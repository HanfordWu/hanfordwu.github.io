Google big table Summary

This post is my summary of google's famous paper:

The paper is quite abstract, it only provides parts of the whole architecture, hence here I can only talk about my understanding besides the summary.

Overview of Big-table

There are three core components of big-table: clientLibrary, master, tabletServer.

- clientLibrary is a client library, obviously. It's used to read/write data. clientLibrary doesn't have to interact with Master, it only interact with tabletServer. tips: like GFS, reading doesn't go through Master. Otherwise Master will be a bottleneck.
- tabletServer: tabletServer is responsible for concrete data reading/writing. There are often few tabletServers, and can be added and deleted dynamically. One tabletServer has up to thousands of tablet, and tablets can be dispatched dynamically. tabletServer also do split of the tablet when the tablet is too big.
- master: master is responsible for distribution of table, detect the increase or decrease of tabletServer, load balance of tabletServer, GC of GFS file.

In general, Big-table is an ordered big table, each tablet stores one part of the data. At the beginning, there is only one tablet, with the data increasing, tablet keep splitting, more and more tablet evenly distributed inside of tabletServers. In order to speed up data read/write, some tablet is responsible for index, which is called METADATA tablet. Big-table restricts the number of index can be three at most, hence, there is only one index above METADATA tablet, which is called rootTablet. rootTablet is the top index, it is the entry of data accessing.


My understanding of Big-table architecture:

![alt](https://niceaz.com/images/post/bigtable_arch.png)

Chubby:

Big-table relies on Chubby. Chubby is a component similar to ZK. Big-table relies on chubby to do three things:

1. Record the meta information of rootTablet. rootTablet is the entry of data accessing, so the entry information is documented in chubby.
2. Record the active tabletServer currently. Each tabletServer will create an exclusive lock. tabletServer keep the lock by session(upload heartbeat periodically in a long session). If tabletServer got down or network partition happen, the lock can be time out and invalid. By listening to this catalog, master will know the active tabletServer currently.
3. master selecting. master creates a master lock in chubby, main master obtain the lock. If master got down, the lock got invalid, then switch to another master.


master:

When the master is starting, master will do the following:

1. Obtain master lock from chubby.
2. Scan chubby server to get active tabletServer.
3. Obtain tablets of active tabletServer.
4. Scan all METADATA tablet to get all tablet, comparing to 3, to get un-dispatched tablet, then dispatch the tablet.


Feature envy is about method:

> A method is more interested in a class other than the one it belongs to.

So there is too much coupling.

Usually, the envied data is accessed through a parameter of the method or a field of the class. 

Therefore, to find this smell, checkout their parameter, whether the method is manipulating a lot of the parameter's fields and calling its methods.

Or, checkout the fields of current class, if there is a method is changing the field's fields or calling the method of the fields, more than manipulating those of itself.

Eg:
```java
getFileTarHeader( TarHeader hdr, File file )
		throws InvalidHeaderException
		{
		this.file = file;

		String name = file.getPath();
		String osname = System.getProperty( "os.name" );
		if ( osname != null )
			{
			// Strip off drive letters!
			// REVIEW Would a better check be "(File.separator == '\')"?

			// String Win32Prefix = "Windows";
			// String prefix = osname.substring( 0, Win32Prefix.length() );
			// if ( prefix.equalsIgnoreCase( Win32Prefix ) )

			// if ( File.separatorChar == '\\' )

			// Per Patrick Beard:
			String Win32Prefix = "windows";
			if ( osname.toLowerCase().startsWith( Win32Prefix ) )
				{
				if ( name.length() > 2 )
					{
					char ch1 = name.charAt(0);
					char ch2 = name.charAt(1);
					if ( ch2 == ':'
						&& ( (ch1 >= 'a' && ch1 <= 'z')
							|| (ch1 >= 'A' && ch1 <= 'Z') ) )
						{
						name = name.substring( 2 );
						}
					}
				}
			}

		name = name.replace( File.separatorChar, '/' );

		// No absolute pathnames
		// Windows (and Posix?) paths can start with "\\NetworkDrive\",
		// so we loop on starting /'s.
		
		for ( ; name.startsWith( "/" ) ; )
			name = name.substring( 1 );

 		hdr.linkName = new StringBuffer( "" );

		hdr.name = new StringBuffer( name );

		if ( file.isDirectory() )
			{
			hdr.mode = 040755;
			hdr.linkFlag = TarHeader.LF_DIR;
			if ( hdr.name.charAt( hdr.name.length() - 1 ) != '/' )
				hdr.name.append( "/" );
			}
		else
			{
			hdr.mode = 0100644;
			hdr.linkFlag = TarHeader.LF_NORMAL;
			}

		// UNDONE When File lets us get the userName, use it!

		hdr.size = file.length();
		hdr.modTime = file.lastModified() / 1000;
		hdr.checkSum = 0;
		hdr.devMajor = 0;
		hdr.devMinor = 0;
		}
```

Essentially, this method have two things to do, first get the `name`, second, changing hdr's properties. So it's nothing to do with the current class. We can move this method to the class `TarHeader`, then just call `hdr.getFileTarHeader(file)`

### God Class

A class should have only one responsibility.

Be suspicious for:
- huge classes treating many other classes as data classes
- Classes with names containing words like “System”, “Subsystem”, “Manager”, “Driver”, or “Controller”
- classes with unrelated sets of methods working on separate instance variables (low cohesion)

**Cohesion Graph**
In one class, if a method is calling another method, or they are referring a common field of the class, we connect them with a non-direction line.

Something like this:

**Case study 1:**

![alt](https://i.imgur.com/0d06lve.png)

If two methods are referring one common field, we connect them with red line, which has more weight.

We can put those three redline connected, we note that the `insert()` method has more relationship with current class, so we don't move it to another class, but we will find the common field that it is referring: `connection`. We extract a new method: `isClosed()`

![alt](https://i.imgur.com/lTU008R.png)

Now we move `isClosed`, `createStatement`, and `closeConnection` to a new class, and add field and delegation of the methods to original class.



**Case study 2**

We can also get the idea from *Dependency graph*:

![alt](https://i.imgur.com/EFa0H33.png)

*Dependency graph* is different from *cohesion graph*, circles represent fields, square represent methods.

There are two clear responsibilities of the class.

We can put one of them to another class. Because of the common reference: `maxStackCap`, is initialized in the constructor with `newUndoStackSize`, so we put the field with undo related methods, and provide a getter for redo class.

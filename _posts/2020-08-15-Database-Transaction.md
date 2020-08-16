This note is talking about four important properties of transaction.

Transaction means one unit of database operations, it only exist in one database connection.

### 1. Atomicity

Just like operations of JMM, Atomicity in transaction means all activities in one transaction, if all activities succeed, the transaction succeed. If any activity failed, all other activities are invalid, the database is not be affected.


### 2. Consistency

Before and after the transaction, database must have the consistency.
For example, before a money transfer from A to B, they have total 1000$, after the transaction activities, they must be 1000\$ total.

### 3. Isolation

Isolation property focus on concurrent connection of database.
Transaction T1 and T2 are accessing table 1 at the same time. Isolation means T1 and T2 must be isolated with each other.

From the view of T1, T2 will start after T1 is done, or has been done before T2 starts.

They should not know they happen at the same time.

### 4. Durability

Once a transaction is submitted, the change must be permanent.

### Level of Isolation

If there no isolation, the problems will be:

- Dirty read

T1 is changing data, but has not submitted, then T2 come to read the data of T1.

If T1 finally submit the change, T2's reading is correct, but if T1 roll back, T2's reading is incorrect. This is Dirty read.

Dirty read: T2 read T1's change that has not been submitted.

- Non-repeatable read

In T1 transaction, read one data from database get value 1, then T2 change the data from value 1 to value 2, and submit. Then T1 read the data again, get value 2.

Non-repeatable read means in one transaction, twice reading get two different value for the same item.

- Phantom read

Similar to Non-repeatable read, but T2 insert an item between the two read(Count) of T1.

For example, T1 first select all item that value = 1, then T2 add one more item of value = 1, T1's second select all item that value = 1 will be different.


**Level of Isolation**


1. Read uncommitted: Allow dirty read, but don't allow drop write. If T1 start to write a table, T2 is not allowed to write. 
2. Read committed
3. Repeatable read
4. Serializable

Highest is Serializable, lowest is Read uncommitted.

The higher level, the slower execution.

Serializable is using a lock to monopoly table or tables. Just like thread concurrency's lock. 

**Different level remain different problems:**

![alt](https://kwpqsq.ch.files.1drv.com/y4mZxxmogs3QEZeCe8SoyWDuymVMHFoyosGlsSKJufY8u_TAUjBuv-jIcLwQUaAtk09_mNrV1u1HWhPIGaTKNEXBXS1Vd1jqhH4CZoEikyMu0ksfWJJXkTMBktMgGwdRu_QCjq5qoBjhKGT_hKrvzgqD2deQk3WFZouYNWS30vuV0D7I308yJelkvmqJrvNAda-Q586wBKA8mDsBKQjEY0Yfg?width=554&height=186&cropmode=none)




MySQL support four level of isolation, the default isolation level is Repeatable read. 

Oracle support Serializable and Read committed, the default is Read committed.



The higher isolated, the more consistency and correctness, but the slower execution. For most application, isolation level could be Read committed. It can avoid dirty read, and has a good concurrency performance. For the possible Non-repeatable read and Phantom read, we can make use of thread concurrency(optimistic/pessimistic lock)

### What about Java's transaction?

JDBC only has three API to achieve transaction:
```java
Connection.setAutoCommit(boolean);
Connection.commit();
Connection.rollback();
```

JDBC only focus on how to start a transaction, commit, roll back, in one connection, it's about Atomicity. It doesn't focus on isolation level.

**Local Transaction and Distributed Transaction**

Local Transaction: There is only one data source, to provide multiple connection concurrency.

Distributed Transaction: There are more than one data source, multiple connection concurrency.

The following example focus on Local Transaction.

**A failed transaction management:**
A BankService transfer money from user's BankAccount(Bank table) to InsuranceAccount(Insurance table).

```java
public interface BankService {
    public void transfer(int fromId, int toId, int amount);
}
```

BankDAO:
```java
public class FailureBankDao {
    private DataSource dataSource;

    public FailureBankDao(DataSource dataSource) {
        this.dataSource = dataSource;
    }


    public void withdraw(int bankId, int amount) throws SQLException {
        Connection connection = dataSource.getConnection();
        PreparedStatement selectStatement = connection.prepareStatement("SELECT BANK_AMOUNT FROM BANK_ACCOUNT WHERE BANK_ID = ?");
        selectStatement.setInt(1, bankId);
        ResultSet resultSet = selectStatement.executeQuery();
        resultSet.next();
        int previousAmount = resultSet.getInt(1);
        resultSet.close();
        selectStatement.close();


        int newAmount = previousAmount - amount;
        PreparedStatement updateStatement = connection.prepareStatement("UPDATE BANK_ACCOUNT SET BANK_AMOUNT = ? WHERE BANK_ID = ?");
        updateStatement.setInt(1, newAmount);
        updateStatement.setInt(2, bankId);
        updateStatement.execute();

        updateStatement.close();
        connection.close();

    }
}
```
InsuranceDAO:
```java
public class FailureInsuranceDao {
    private DataSource dataSource;

    public FailureInsuranceDao(DataSource dataSource){
        this.dataSource = dataSource;
    }

    public void deposit(int insuranceId, int amount) throws SQLException {
        Connection connection = dataSource.getConnection();
        PreparedStatement selectStatement = connection.prepareStatement("SELECT INSURANCE_AMOUNT FROM INSURANCE_ACCOUNT WHERE INSURANCE_ID = ?");
        selectStatement.setInt(1, insuranceId);
        ResultSet resultSet = selectStatement.executeQuery();
        resultSet.next();
        int previousAmount = resultSet.getInt(1);
        resultSet.close();
        selectStatement.close();


        int newAmount = previousAmount + amount;
        PreparedStatement updateStatement = connection.prepareStatement("UPDATE INSURANCE_ACCOUNT SET INSURANCE_AMOUNT = ? WHERE INSURANCE_ID = ?");
        updateStatement.setInt(1, newAmount);
        updateStatement.setInt(2, insuranceId);
        updateStatement.execute();

        updateStatement.close();
        connection.close();
    }
}
```
Implementing BankService:

```java
public class FailureBankService implements BankService{
    private FailureBankDao failureBankDao;
    private FailureInsuranceDao failureInsuranceDao;
    private DataSource dataSource;

    public FailureBankService(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public void transfer(int fromId, int toId, int amount) {
        Connection connection = null;
        try {
            connection = dataSource.getConnection();
            connection.setAutoCommit(false);

            failureBankDao.withdraw(fromId, amount);
            failureInsuranceDao.deposit(toId, amount);

            connection.commit();
        } catch (Exception e) {
            try {
                assert connection != null;
                connection.rollback();
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
        } finally {
            try
            {
                assert connection != null;
                connection.close();
            } catch (SQLException e)
            {
                e.printStackTrace();
            }
        }
    }

    public void setFailureBankDao(FailureBankDao failureBankDao) {
        this.failureBankDao = failureBankDao;
    }

    public void setFailureInsuranceDao(FailureInsuranceDao failureInsuranceDao) {
        this.failureInsuranceDao = failureInsuranceDao;
    }
}
```

In above implementation, we withdraw money from bank, deposit it to insurance account.
Note two DAO and the service are using same dataSource, but they may have three different connection.

Therefore, the `rollBack` in BankService only works on the connection of BankService, the operations in BankDAO and InsuranceDAO will not rollback successfully.

**An Ugly Solution regarding above problem**

Because they are using three different connection, we didn't rollback successfully, so we can pass one same connection to each operation, then rollback will work.

```java
public class UglyBankDao {
    public void withdraw(int bankId, int amount, Connection connection) throws SQLException {
        PreparedStatement selectStatement = connection.prepareStatement("SELECT BANK_AMOUNT FROM BANK_ACCOUNT WHERE BANK_ID = ?");
        selectStatement.setInt(1, bankId);
        ResultSet resultSet = selectStatement.executeQuery();
        resultSet.next();
        int previousAmount = resultSet.getInt(1);
        resultSet.close();
        selectStatement.close();


        int newAmount = previousAmount - amount;
        PreparedStatement updateStatement = connection.prepareStatement("UPDATE BANK_ACCOUNT SET BANK_AMOUNT = ? WHERE BANK_ID = ?");
        updateStatement.setInt(1, newAmount);
        updateStatement.setInt(2, bankId);
        updateStatement.execute();

        updateStatement.close();
    }
}
```

```java
public class UglyInsuranceDao {
    public void deposit(int insuranceId, int amount, Connection connection) throws SQLException {
        PreparedStatement selectStatement = connection.prepareStatement("SELECT INSURANCE_AMOUNT FROM INSURANCE_ACCOUNT WHERE INSURANCE_ID = ?");
        selectStatement.setInt(1, insuranceId);
        ResultSet resultSet = selectStatement.executeQuery();
        resultSet.next();
        int previousAmount = resultSet.getInt(1);
        resultSet.close();
        selectStatement.close();


        int newAmount = previousAmount + amount;
        PreparedStatement updateStatement = connection.prepareStatement("UPDATE INSURANCE_ACCOUNT SET INSURANCE_AMOUNT = ? WHERE INSURANCE_ID = ?");
        updateStatement.setInt(1, newAmount);
        updateStatement.setInt(2, insuranceId);
        updateStatement.execute();

        updateStatement.close();
    }
}
```


In BankService, we get a connection, then pass it to DAOs:

```java
public class UglyBankService implements BankService {
    private DataSource dataSource;
    private UglyBankDao uglyBankDao;
    private UglyInsuranceDao uglyInsuranceDao;

    public UglyBankService(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public void transfer(int fromId, int toId, int amount) {
        Connection connection = null;
        try {
            connection = dataSource.getConnection();
            connection.setAutoCommit(false);

            uglyBankDao.withdraw(fromId, amount, connection);
            uglyInsuranceDao.deposit(toId, amount, connection);

            connection.commit();
        } catch (Exception e) {
            try {
                assert connection != null;
                connection.rollback();
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
        } finally {
            try
            {
                assert connection != null;
                connection.close();
            } catch (SQLException e)
            {
                e.printStackTrace();
            }
        }
    }

    public void setUglyBankDao(UglyBankDao uglyBankDao) {
        this.uglyBankDao = uglyBankDao;
    }

    public void setUglyInsuranceDao(UglyInsuranceDao uglyInsuranceDao) {
        this.uglyInsuranceDao = uglyInsuranceDao;
    }
}
```

In this case, the `rollBack` will work, but the solution is ugly, because there are too many coupling.


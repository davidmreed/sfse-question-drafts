# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

I'm testing asynchronous code - like Batch Apex, Queueable Apex, `@future` methods, or Scheduled Apex - and I'm getting unexpected results. It seems like portions of my code aren't running, or I'm getting confusing errors about batch invocations. How do I effectively test this kind of code?

# Answer

Asynchronous Apex includes all of the methods for executing code on the Salesforce platform outside a synchronous transaction. These tools make it possible to perform operations like making a callout after DML, processing millions of records in batches, chaining sequences of operations, or running code on a schedule. Asynchronous Apex includes:

 - `@future` methods.
 - Batch Apex
 - Queueable Apex
 - Schedulable Apex

Because these constructs are by nature asynchronous, do not come with an SLA, and can be executed by Salesforce based upon overall system load and other considerations, we cannot typically guarantee exactly when they will be executed. This requires some changes to how we build and structure unit tests for code that is built within or makes use of any of the four asynchronous code types mentioned above.

### Use of `Test.startTest()` and `Test.stopTest()` 

Because of the way Asynchronous Apex works, any asynchronous code - a future method is a useful example - will not be executed during the confines of an Apex unit test. A unit test forms a single transaction, and asynchronous code enqueued within that transaction cannot be executed until the transaction commits successfully. 

For this reason, Salesforce has provided a framework to force asynchronous code to execute synchronously for testing. 

We enclose our test code between `Test.startTest()` and `Test.stopTest()`. The system collects all asynchronous calls made after `startTest()`. When `stopTest()` is executed, these collected asynchronous processes are then run synchronously. 

Following `Test.stopTest()`, our code can evaluate the results of the executed asynchronous code and make assertions to validate its behavior.

    Test.startTest();
    AsyncUtil.executeFutureMethod();
    Test.stopTest();

    System.assertEquals(expected, actualChangesInAsync);

### Nested Asynchronous Code

The collection and synchronous execution of Asynchronous Apex applies only between `Test.startTest()` and `Test.stopTest()`. Any further asynchronous code that's enqueued *by* the asynchronous operations that are executed at `Test.stopTest()` is *not* executed synchronously in the context of the unit test. For example, if we're working with the following code:

    public class MySchedulable implements Schedulable { 
        private Account a;
        
        public MySchedulable(Account a) {
            this.a = a;
        }
        
        public void execute(SchedulableContext sc) {
            a.Description = 'Contacted the customer');
            update a;

            Database.executeBatch(new ContactsUpdaterBatch(a), 200);
        }
    }

    public class ContactsUpdaterBatch implements Database.Batchable<sObject> { 
        // ContactsUpdaterBatch updates the Description field on Contact (elided for brevity).
    }
   
A unit test structured like this will not work:

    @isTest
    public static void updating_accounts_updates_contacts() {
        Account a = [SELECT Id FROM Account LIMIT 1];

        Test.startTest();
        System.schedule('TEST_MySchedulable', '0 0 * * * ?', new MySchedulable(a));
        Test.stopTest();

        a = [SELECT Id, Description, (SELECT Id, Description FROM Contacts) FROM Account WHERE Id = :a.Id];

        System.assertEquals('Contacted the customer', a.Description, 'found correct description');
        for (Contact c : a.Contacts) {
            System.assertEquals('Account has been contacted', c.Description, 'found correct Contact description');
        }
    }
    
The second assertion will fail, because the batch class `ContactsUpdateBatch`, fired from *within* the asynchronous `MySchedulable`, will not execute during test context - even though the first layer, `MySchedulable.execute()`, is called at `Test.stopTest()`.

The same pattern applies to other multi-layer asynchronous code, including `@future` methods and Queueables.

There's no work-around to allow multi-level asynchronous code to execute in test context. Instead, the tests must be constructed to validate functionality without requiring this, by decomposing the tests to validate smaller units and/or using techniques like dependency injection to validate the connections between different asynchronous code units.

### Batch Class Execution

Unit test context permits only one batch execution (call to `execute()`) to occur in a single unit test. While in most cases your unit tests would not insert more than one batch's worth of test data, it is possible to do so. This will result in an exception being thrown. Your unit test needs to guarantee that only one batch executes, either by controlling the batch size of the test data set or both.

Batches that execute across metadata objects, such as `User`, are especially vulnerable to this challenge. While unit tests for these Batch classes may succeed in developer orgs or scratch orgs that have tiny record sets, they will fail when deployed to a larger production org. In many cases, these batches need at least a light dependency injection strategy to allow the unit test to control the queries executed by `start()`. For example, the query might be exposed in a `@TestVisible` instance variable to allow a unit test to inject a more restricted query, or adding an `Id` set to restrict the query results.

# Resources

## Trailhead Modules

- [Asynchronous Apex](https://trailhead.salesforce.com/en/content/learn/modules/asynchronous_apex)
  - Includes modules on Future, Batch, Queueable, and Scheduled Apex, each of which describes testing approaches.

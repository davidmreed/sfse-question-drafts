
# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

My code includes exception handling logic. How can I effectively unit test this code to obtain full coverage, while validating that exception handlers work correctly and exceptions are thrown where expected?

# Answer

## Testing for a Thrown Exception

Let's start with a simple example: a method that throws an exception based on its input values. Here, we throw a custom exception if asked to divide by zero.

    public static Decimal divide(Integer numerator, Integer denominator) {
        if (denominator == 0) {
            throw new CalculatorException('Division by zero is not allowed');
        }

        return numerator / denominator;
    }
    
We can test *whether or not we caught an exception* using a pattern like this

    @isTest
    public static void divide_throws_exception_for_division_by_zero() {
        Boolean caught = false;
        try {
            Calculator.divide(1, 0);
        } catch (Exception e) {
            caught = true;
        }
        System.assert(caught, 'threw expected exception');
    }
    
We use a `try/catch` block to trap the exception, set a Boolean to `true` if caught, and finally assert that the catch happened.

But this example's simplicity is actually deceptive. Suppose our `divide()` method had a logic error in its parameter checking:

        if (denominator < 0) {
        
Our test case is still going to pass, because an exception *will* be thrown if we call `Calculator.divide(1, 0)`. It just won't be a `CalculatorException`. Instead, we'll get a `MathException`, because the underlying system exception we were trying to guard against *wasn't* blocked by our logic. Our unit test misses this functional failure, because its exception handler is too broad and doesn't guarantee that it caught an exception that was actually thrown from the code path we're aiming to test.

We need to do three things to test an exception-throwing code path safely:

 1. Catch the exception, so we can assert that it was thrown at all.
 1. Validate that we caught the correct exception type.
 1. Validate that the exception message is the one expected for the tested code path.
 
The third point here guards against yet another potential false positive/false negative case: what if there were two code paths inside `divide()` that threw `CalculatorException`? If all our unit test does is check that we caught a `CalculatorException`, we cannot validate that it was thrown in the right code path for the right reason.

We extend our unit test to add these steps, then, and get to this point:

    @isTest
    public static void divide_throws_exception_for_division_by_zero() {
        Boolean caught = false;
        try {
            Calculator.divide(1, 0);
        } catch (Calculator.CalculatorException e) {
            System.assertEquals('Division by zero is not allowed', e.getMessage(), 'caught the right exception');
            caught = true;
        }
        System.assert(caught, 'threw expected exception');
    }

Now, we guarantee that we are passing the test only with the right exception being thrown for the right reason at the right time. Should a `MathException` or some other exception be thrown, the test would fail. If desired, we can add another `catch` block on a generic `Exception` to trap these and write a failing assertion to provide a clear failure reason for this case:

        } catch (Calculator.CalculatorException e) {
            System.assertEquals('Division by zero is not allowed', e.getMessage(), 'caught the right exception');
            caught = true;
        } catch (Exception e) {
            System.assert(false, 'caught an incorrect exception: ' + e.getMessage());
        }
        
This is a cosmetic choice, however; we're secure in the unit test functionality either way.
  
## Testing an Exception Handler (Generating Exceptions in Test Context)

The other side of exception testing is validating the behavior of, and getting code coverage for, exception handlers in production code. Noting at the outside that this is a complex and case-specific area, let's look again at a simple example.

    public static void performUpdates(List<Account> accounts) {
        for (Account a : accounts) {
            if (a.Industry == 'Finance') {
                a.Description = 'NOTE: client in the finance industry.';
            }
        }

        try {
            update accounts;
        } catch (DMLException e) {
            LoggingUtil.postErrorLogs(accounts, e);
        }
    }
    
Now, this is not very good code from an enterprise pattern standpoint, but it's representative of real Apex that's used in Salesforce orgs every day. So how do we approach testing this exception handler, and getting to that 100% code coverage metric?

### Is the exception handler necessary?

If there's no catchable exception that will actually be thrown by the code in the `catch()` block, remove the exception handler entirely.

    List<Accounts> acts;
    try {
        acts = [SELECT Id, Name FROM Account WHERE Name = 'Amalgamated Industries'];
    } catch (Exception e) {
        // do something
    }
    
This code

 1. Cannot be tested, because we cannot cause the query to fail in test context.
 2. Is useless, because there are no catchable exceptions that will be thrown from this query. The only exception this query could cause is a `LimitsException`, which cannot be caught.
 
Here, the solution is to delete the exception handler.

Similarly, if the exception handler can be removed by implementing logic to prevent an exceptional condition, it's in many cases superior to do so. For example, a coder more familiar with a an exception-heavy language like Python might write a pattern like this:

    try {
        return o.get('Parent__r').get('Name');
    } catch (NullPointerException e) {
        return null;
    }
 
 This isn't idiomatic Apex - we shouldn't throw an exception here. We should simply check for `null` to Look Before We Leap.
 
     return (o.get('Parent__r') != null ? o.get('Parent__r').get('Name') : null);

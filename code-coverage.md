# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

This canonical question is intended to address several classes of common questions by providing a quick summary and links to comprehensive resources:

 - How is code coverage obtained and calculated?
 - How do I increase my coverage? 
 - Why can't I cover these lines?
 - Why isn't the body of my loop or conditional statement covered?

# Answer

Code coverage is a measurement of how many unique lines of your code are executed while the automated tests are running. Code coverage percentage is the number of covered lines divided by the sum of the number of covered lines and uncovered lines.

For purposes of calculating code coverage, only executable lines of code are counted, whether covered or uncovered. Comments and blank lines are not counted, and `System.debug()` statements and curly braces that appear alone on one line are also not counted.

In order to perform a deployment that includes Apex or install an AppExchange package in production, the minimum code coverage metric of 75% coverage must be achieved. 

While writing unit tests the main emphasis should be given on actually testing the logic and behaviour of the block by adding  appropriate assertions to make sure the code's behaviour is same as expected. Ideally, a system should strive to have 100% code coverage. 

To increase your code coverage you have to write functional unit-tests for the existing classes which have uncovered block of code or for the new classes you have added. 

## Commonly Encountered Scenarios

### Control Structures

You may find that your unit tests only cover one branch of a control statement, like an `if`/`else` or `switch on` construct.

(FIXME: include a code coverage screenshot example)

In many cases, you should expect to cover different branches of your logic using *multiple* unit tests, each of which performs different setup and data creation steps to execute and validate a different code pathway. Trying to test too much in a single test method tends to make unit tests more difficult to understand and more fragile.

### Loops

Code coverage that appears to "stop" and not enter the body of a loop, such as a `for` or `while`, stems from the same cause as with control structures: the test environment doesn't meet the preconditions for the loop body to execute. Here's an example:

    for (Account a : [SELECT Id, Name FROM Account WHERE Name LIKE 'ACME%']) {
        a.Description = 'Here are some more details.';
    }
    
After running unit tests, if the line starting with `a.Description` is not covered, it's an indication that the loop never begins iterating, because the query returns no records. This is a failure of the test data setup: records weren't created that would exercise the functionality of this specific code path.

# Resources

- [How Is Code Coverage Calculated?](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_code_coverage_intro.htm)


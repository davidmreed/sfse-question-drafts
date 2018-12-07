# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

This canonical question is intended to address several classes of common questions by providing a quick summary and links to comprehensive resources:

 - How is code coverage obtained and calculated?
 - How do I increase my coverage? 
 - Why can't I cover these lines?
 - Why isn't the body of my loop or conditional statement covered?

# Answer

Code coverage is a measurement of how many unique lines of your code are executed while the automated tests are running. Code coverage percentage is a calculation of the number of covered lines divided by the sum of the number of covered lines and uncovered lines. Only executable lines of code are included. (Comments and blank lines aren’t counted.) System.debug() statements and curly brackets are excluded when they appear alone on one line. Multiple statements on one line are counted as one line for the purpose of code coverage. If a statement consists of multiple expressions that are written on multiple lines, each line is counted for code coverage.

In order to deploy in Production or install an app-exchange package , the minimum code coverage requirement is 75% . While writing unit tests the main emphasis should be given on actually testing the logic /behaviour of the block by adding proper approriate assert statements to make sure the code's behaviour is same as expected. Ideally a system should strive to have 100% code coverage. 

To increase your code coverage you have to write functional unit-tests for the existing classes which have uncovered block of code or for the new classes you have added. 


## Commonly Encountered Scenarios

# Resoures

[`How Is Code Coverage Calculated?`](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_code_coverage_intro.htm)


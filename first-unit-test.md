# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

This canonical question is intended to address several classes of common questions by providing a quick summary and links to comprehensive resources:

 - How do I start writing my first unit test? 
 - How do I unit test this code? 
 - I need help writing this unit test.
 
Salesforce Stack Exchange looks for detailed, specific questions that the community can help you with, and can't write tests on your behalf. We feel that working with the resources below can help you get started, and we encourage you to make an attempt to write your test and return to SFSE with your specific questions when you encounter challenges you can't resolve.

# Answer

This answer is not intended to teach you everything about writing unit tests, nor to specifically answer every question, but to provide a quick summary and links to the resources that will help you move forward.

## Overview

Unit (and integration) testing is a big topic, but it starts with a small set of principles. If you've never written a unit test before, we strongly encourage you to complete the [Apex Testing Trailhead module](https://trailhead.salesforce.com/content/learn/modules/apex_testing) and read at least the Month of Testing series and [How to Write Good Unit Tests](https://developer.salesforce.com/page/How_to_Write_Good_Unit_Tests) articles. All of these materials are linked under Resources below.

It's a common mistake to think of unit tests, and code coverage, as exchangeable resources. But unit tests aren't off-the-shelf components. Each unit test is designed for a particular piece of code, and validates and documents specific aspects of its operation.

Fundamentally, testing comprises three steps: 

 1. Designing inputs to your code - test data - to ensure that a specific logical path is executed. 
 1. Executing that code in a test context, meaning that the code runs within a method annotated with the `@isTest` annotation.
 1. Writing assertions to demonstrate that the results of the code are correct and as expected for the given inputs.

Then, all code that is executed under step (2) is counted as *covered* under Salesforce's code coverage metrics. Code coverage is a *side effect* of high quality unit tests. Salesforce uses code coverage as a proxy to measure the presence of unit tests in your deployments.

Unit testing principles are quite general, and most Apex code is not special in the sense of requiring unique approaches to create a successful test. Techniques for implementing tests that perform all three steps are taught in the resources we include below.

On Salesforce, all unit tests are executed in an isolated context. In this context, your code cannot see data in your organization, including ordinary records as well as Custom Settings. However, metadata records, including Users and Custom Metadata, are visible. An older annotation, `seeAllData=true`, allows tests to see all data in the Salesforce org. Use of this annotation is **strongly discouraged** for new unit tests, and is considered a very bad practice. It's important instead to follow the first step above, by designing test data as input for your code.

Unit tests that don't contain assertions are often called *smoke tests*. These tests have very limited value, because they show nothing other than that your code does not crash under a specific set of circumstances. They don't prove the code works or does what it's intended to do.

## Resources

### Trailhead

 - [Apex Testing](https://trailhead.salesforce.com/content/learn/modules/apex_testing)
 - [Apex REST Callouts](https://trailhead.salesforce.com/en/content/learn/modules/apex_integration_services/apex_integration_rest_callouts) and [Apex SOAP Callouts](https://trailhead.salesforce.com/content/learn/modules/apex_integration_services/apex_integration_soap_callouts) cover the use of mocking to test callout code.
 
 ### Apex Developer Guide
 
  - [Testing Apex](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing.htm)
 
 ### Blogs and Articles
 
  - [How to Write Good Unit Tests](https://developer.salesforce.com/page/How_to_Write_Good_Unit_Tests)
  - Month of Testing series from the Salesforce Developers blog.
     - Part 1: [Why We Test](https://developer.salesforce.com/blogs/2018/05/why-we-test.html)
     - Part 2: [Apex Testing in Depth](https://developer.salesforce.com/blogs/2018/05/month-of-testing-apex-testing-in-depth-part-2-of-3.html)
     - Part 3: [Advanced Topics in Salesforce Unit Testing](https://developer.salesforce.com/blogs/2018/05/month-of-testing-advanced-topics-in-salesforce-unit-testing-part-3-of-3.html)
     
## Third-Party Testing Frameworks (Advanced Topics)

 - [ApexMocks](https://github.com/financialforcedev/fflib-apex-mocks), an open-source mocking framework for Apex.
 - [Force-DI](https://github.com/afawcett/force-di), a framework for pervasive dependency injection.


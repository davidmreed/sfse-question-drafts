# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

I'm testing asynchronous code - like Batch Apex, Queueable Apex, `@future` methods, or Scheduled Apex - and I'm getting unexpected results. It seems like portions of my code aren't running, or I'm getting confusing errors about batch invocations. How do I effectively test this kind of code?

# Answer

Briefly summarize and link to documentation regarding the machinery of Test.stopTest() and Test.startTest() as they relate to testing asynchronous code. Summarize issue surrounding testing functionality that could fire multiple batch invocations and multi-level asynchrony (schedulable calls batch, etc), and point towards options for structuring tests for this type of code.

# Resources

*[Testing Batch Apex](https://trailhead.salesforce.com/en/content/learn/modules/asynchronous_apex/async_apex_batch#Tdxn4tBK-heading7)
*[Testing Future Methods](https://trailhead.salesforce.com/content/learn/modules/asynchronous_apex/async_apex_future_methods#Tdxn4tBK-heading5)
*[Testing Queueable Apex](https://trailhead.salesforce.com/content/learn/modules/asynchronous_apex/async_apex_queueable#Tdxn4tBK-heading6)
*[Testing Scheduled Apex](https://trailhead.salesforce.com/content/learn/modules/asynchronous_apex/async_apex_scheduled#Tdxn4tBK-heading7)


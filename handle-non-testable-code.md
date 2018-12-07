# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

I'm trying to test code that sends email or uses the Connect API. I'm getting errors that tell me what I want to do is not supported. How do I test this type of code?

# Answer

Across the Salesforce API, there are a few types of code that cannot be executed in test context at all, or that requires special test configuration to execute. Three of the most common of these situations are

 - Performing callouts, which is handled with a Mock (see [this canonical question]()).
 - Sending email, which must be gated in test context.
 - Calling the Connect API, which must *either* be gated in test context or utilize the `seeAllData=true` annotation.
 
Code that needs to not be executed in test context can be gated by checking for `Test.isRunningTest()`. Because it's important for tests to reflect the real execution pathways of the code - and because code that's not executed in test context cannot obtain coverage - it's a best practice to limit the use of this type of gating as much as possible.

## Sending Email

Any use of `Messaging.sendEmail()` needs to be gated in test context. Outbound emails cannot be sent in a test.

By wrapping the `Messaging.sendEmail()` call in a one-line `if` statement, you can still maintain full coverage:

    if (!Test.isRunningTest()) Messaging.sendEmail(myListOfEmails);
    
Unfortunately, this doesn't allow you to validate the behavior of your code in constructing the list of outbound messages. To do so, you'd need to adopt a dependency injection pattern, where your code calls a delegate object rather than calling `Messaging.sendEmail()` directly. Then, in test context, you can supply a test-only delegate that validates the messages generated, while in production use your code calls a delegate that actually calls through to `Messaging.sendEmail()`. The production delegate itself can then include a line such as the above, allowing it too to be covered.

## Connect API

The Connect API is slightly different from email sending. The Connect API can be invoked in test context, but only if the `seeAllData=true` annotation is present. Absent that annotation, calls to the Connect API must be guarded just like email sending.


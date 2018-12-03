# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the `canonical-qa` tag.*

How do I unit test code that includes a SOAP or REST-based callout, or indirectly calls code that makes a callout? I'm getting an error saying that callouts are not supported.

# Answer

As part of the isolation of the test context, Salesforce does not allow your code to make REST or SOAP callouts during test execution. This includes all code that's executed in test context, even if it is executed indirectly by the code you're explicitly testing, and it includes callouts to Salesforce itself.

To test code that makes callouts, you must develop a Mock class, which mocks the remote server during the test and constructs an appropriate response that's returned to your code.

To learn how to develop Mock classes, complete these Trailhead modules:

 - [Apex REST Callouts](https://trailhead.salesforce.com/en/content/learn/modules/apex_integration_services/apex_integration_rest_callouts) 
 - [Apex SOAP Callouts](https://trailhead.salesforce.com/content/learn/modules/apex_integration_services/apex_integration_soap_callouts) 

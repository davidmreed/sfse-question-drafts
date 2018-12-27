# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

How do I unit test code that includes a SOAP or REST-based callout, or indirectly calls code that makes a callout? I'm getting an error saying that callouts are not supported.

# Answer

As part of the isolation of the test context, Salesforce does not allow your code to make REST or SOAP callouts during test execution. This includes all code that's executed in test context, even if it is executed indirectly by the code you're explicitly testing, and it includes callouts to Salesforce itself.

To test code that makes callouts, you must develop a Mock class, which mocks the remote server during the test and constructs an appropriate response that's returned to your code. Mock classes may generate a response in code (for example, by constructing objects, serializing them, and returning the resulting JSON), or by returning a result stored in a Static Resource.

Salesforce makes available two Mock interfaces (`HttpCalloutMock`, for REST calls, and `WebServiceMock`, for SOAP calls), as well as the `StaticResourceCalloutMock` and `MultiStaticResourceCalloutMock` implementations. You can use these built-in implementations to test your callouts by providing result data in a Static Resource, which the mock will return to your code in test context. Keep in mind that you must call `Test.setMock()` to configure your mock in the test context prior to invoking the code that makes a callout.

In many cases, you'll need to use multiple Mock classes, returning different response bodies or status codes to exercise different logic paths in your code. Each unit test would supply an appropriate Mock for the code path whose behavior it's intended to test.

Mock classes are not required to test *inbound* REST and web service classes. Instead, the classes may be called directly (with appropriate inputs), in a way similar to testing other Apex code.

## Resources

To learn how to develop Mock classes, complete these Trailhead modules:

 - [Apex REST Callouts](https://trailhead.salesforce.com/en/content/learn/modules/apex_integration_services/apex_integration_rest_callouts) 
 - [Apex SOAP Callouts](https://trailhead.salesforce.com/content/learn/modules/apex_integration_services/apex_integration_soap_callouts)
 
 For more in-depth information, explore these sections in the Apex Developer Guide:

 - [Testing HTTP Callouts](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_classes_restful_http_testing.htm)
 - [Test Web Service Callouts](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_callouts_wsdl2apex_testing.htm)
 - [`HttpCalloutMock` Interface](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_interface_httpcalloutmock.htm#apex_interface_httpcalloutmock)
 - [`WebServiceMock` Interface](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_interface_webservicemock.htm#apex_interface_webservicemock)
 - [`StaticResourceCalloutMock` Class](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_methods_system_staticresourcecalloutmock.htm#apex_methods_system_staticresourcecalloutmock)
 - [`MultiStaticResourceCalloutMock` Class](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_methods_system_multistaticresourcecalloutmock.htm#apex_methods_system_multistaticresourcecalloutmock)

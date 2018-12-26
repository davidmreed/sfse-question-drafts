# Question

*This is a **canonical question and answer** developed by the community to help address common questions. If you've been directed here, or your question has been closed as a duplicate, please look through the resources here and use them to shape more specific questions. To browse all canonical questions and answers, including more unit test resources, navigate to the [`canonical-qa`](https://salesforce.stackexchange.com/questions/tagged/canonical-qa) tag.*

This canonical question is intended to address several classes of common questions by providing a quick summary and links to comprehensive resources:

- What are the code coverage requirements?
- How are they applied during a deployment with different test run settings?
- What are some of the techniques one uses to manage coverage during the deployment process?

# Answer

Salesforce imposes specific requirements for *code coverage* that must be met when deploying new or changed code to a production org. Code coverage requirements help to ensure that your code includes high quality tests that validate its behavior; in this sense they are a proxy metric.

The basic code coverage metric is **75% total coverage**. This means that 75% of the executable lines of Apex across your org are covered when unit tests are run, and the tests pass. You must meet this metric when performing a deployment to your production Salesforce org that includes Apex. 



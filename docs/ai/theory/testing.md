# testing with AI tools
[🔙README](/README.md)

The latest versions of AI tools are incredibly powerful when it comes to creating and running tests. This is an area where developers can save a huge amount of time using these tools

### generation of test data
When generating test data, your tool needs sufficient context. This requires either detailed and precise prompts, or at least existing sample data on which the generation can be based. Interface descriptions alone often lead to results that are not realistic (although, of course, data that deviates from real-world scenarios should also be tested).

### TDD
It is possible to write a test that explicitly tests the new features to be developed and instruct the tool to adapt the application so that the test passes. It is important that all necessary context is provided for this adaptation. This can be achieved, for example, by using comments.
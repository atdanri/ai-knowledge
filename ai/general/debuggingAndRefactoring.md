# debugging and refactoring with AI Tools
[🔙README](/README.md)

## debugging
AI development tools can be a great help when debugging. From assisting with implementation (e.g. remote debugging) to identifying problems, these tools can also provide support in many other areas

### Rubber duck debugging – verification through explanation
Explain your changes to the tool as if you were explaining them to an outsider, step by step, including what they are intended to achieve (see rubber duck debugging). This often enables the AI to identify errors in the changes. This is, of course, one of the most basic ways in which the dev tools can help you with debugging

### webapps
Many AI tools already come with agents or/and the necessary tools to run and analyse web applications. Testing front-end and back-end combinations should also be straightforward in most tools with the right prompts and the necessary context. If required, there are browser extensions and other tools(eg. Jam) that can be integrated with the actual AI tool(often with MCP-services).
If necessary, the appearance of a web app can also be adjusted using a snapshot as a reference (this naturally applies to other UI solutions as well)

## refactoring
AI tools can assist you with refactoring in a variety of ways. Identifying code smells, anti-patterns... and even fixing them is usually not a major problem.

### test coverage first
Make sure you have test coverage in place before you start refactoring. That way, you can be fairly certain later on that your changes haven’t broken anything. A good general tip for making any changes to existing code.

### additional tools
Combine several tools for refactoring. Static analysis tools such as SonarQube or linters can complement AI-powered development tools effectively. AI-powered development tools struggle particularly when it comes to providing an overall view of a system.

### pattern
When refactoring, LLMs look for patterns in their training data.  They compare the implementation with best practices, look for anti-patterns, and finally attempt to generate a suitable solution based on successful approaches found in the training data

### restricted view
When refactoring via a prompt, the AI development tool usually only has a small portion of the component as context. The lack of context—for example, regarding architecture, dependencies or configuration—can lead to hallucinations or incomplete results. The context can be supplemented either through files provided for the tool itself (e.g. steering and specs files for kiro) or, for instance, a well-maintained README file that can be provided to the tool as context.

### step by step
Proceed step by step. Ask the tool to suggest refactoring changes and have it implement them one by one. This way, they can be tested separately, and if a problem arises, the changes required to revert to the last working version will be minimal(this approach is also recommended for many other types of modifications).

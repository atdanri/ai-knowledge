# good to know
[🔙README](/README.md)

### knowledge cutoff 
If the model’s training data is not up to date, it may struggle to cope with the latest libraries, for example. This can lead to hallucinations (the model then often tries to continue working with its outdated data). In most tools, it helps to include a reference to the latest documentation

### permissions
All AI development tools will ask for permission to execute commands, run programmes, etc., when running with agents. Be strict about this, even if it can be tedious. You can certainly grant permanent permission for read-only actions within a session, but be cautious with everything else. Act responsibly!

### niche areas 
the less information there is on a topic in the web, the less effective the model tends to be in most cases. Consider whether AI is the right choice for this topic

### image formats
When providing images as context for AI tools (including directly in chat), these two most common image formats are virtually the standard. JPG should be the quickest to process, but can result in a loss of detail with large, complex images (as images are scaled to fit a maximum window, even though this is often very large). PNG does not have this problem, but is processed slightly more slowly.
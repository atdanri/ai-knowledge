# economize
[🔙README](/README.md)

## tool operating

### Inline chat(less context, fewer tokens)
use the inline chat in AI developer tools where possible, local issues that don’t require much context can be dealt with using far fewer tokens

### clear sessions(discard unneccessary context)
use new chat sessions for differnt tasks with different context. Otherwise, the old context would cause you to use up more tokens and might have a negative impact on the responses. If you still need the session context for another task, you can reopen it in most tools

### regulation
There are various ways to harden tools against token wastage. For example, in some tools you can limit the maximum number of tokens per window (useful if the model you’re using goes haywire), in theory, you can use hooks to limit the file sizes for the context... Decide for yourself whether this makes sense for you

### model selection
If you’re using the tool to carry out simple, routine tasks, you shouldn’t use the most expensive (and often most powerful) model. Smaller, more cost-effective models usually cope just fine with this. The auto-mode on most tools is already quite good. If you want to control this yourself, apart from manual selection, most AI tools allow you to do so via agent hooks or rules. However, you’ll have to decide for yourself whether the effort is worth it.

## user behavior

### plan before implementation
I’m a fan of carrying out an analysis before making any major adjustments and generating or writing down a list of the necessary steps (tasks in a md file usually - I probably picked this up from kiros’s spec mode). Often, this process reveals things that need to be adjusted, added or at least considered. Once I’ve optimised these lists, I have the tool work through them step by step. This often saves you the work of having to do the same thing twice, either manually or via the tool (which means more tokens). If, on the other hand, you’re absolutely certain of what you’re doing, a single good prompt is of course more token-effective

### Use additional sources
when tokens need to be saved, use freely available AI chatbots or a standard search engine for simple separate tasks and researches which are not directly related to the tasks your are using the AI development tool for.

### document context optimisation 
It can be very useful to keep documents(e.g. documentation) required for context strictly separate by topic and to keep them small. This helps to minimise the overhead involved in loading context

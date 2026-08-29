# Python Code Style
Guiding principles in code style, organized by importance - as perceived by me.
With a clear focus on data science and machine learning projects.

## The Newspaper Metaphor
Think of a well-written newspaper article.
At the top you expect a headline that will tell you what the story is about and allows you to decide whether it is something you want to read.
The first paragraph gives you a synopsis of the whole story, hiding all the details while giving you the broad concepts.
As you continue downward, the details increase until you have all relevant information.
A source file should be like a newspaper article.
The [name](#naming) should be simple but explanatory so that it is sufficient to tell us whether we are in the right module or not.
The topmost parts of the source file should provide the high-level concepts and algorithms.
Detail should increase as we move downward, until at the end we find the lowest level functions and details in the source file.

This is can be be achived by following these hints:

+ Concepts that are closely related should be kept vertically close to each other.
+ Variables should be declared as close to their usage as possible.
+ If one function calls another, they should be vertically close, and the caller should be above the callee.

In general we want function call dependencies to point in the downward direction.
That is, the definition of a function that should be below a function that does the calling.
This creates a flow down the source code module from high level to low level.
As in newspaper articles, we expect the most important concepts to come first, and we expect them to be expressed with the least amount of polluting detail.
We expect the low-level details to come last.
This allows us to skim source files, getting the gist from the first few functions, without having to immerse ourselves in the details.

We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions.


## Naming
Above all else, names have to be descriptive and exlanatory.
The name of a variable, function, or class, should answer all the big questions.
It should tell you why it exists, what it does, and how it is used.
If a name requires a comment, then the name does not reveal its intent.
Do not be afraid to make a name long.
A long descriptive name is better than a comment. 

Use pronounceable names: If you can’t pronounce it, you can’t discuss it.
Avoid abbreviations and single letter variables.
This has the side effect of making the code more searchable.
(The only exception is, if there is strong convention every developer will be familier with such as `model.fit(X, y)` from `scikit-learn`.)

Names also have to follow these rules:
1. Class names have to be nouns.
2. Method and function names have to be verbs or phrases.
(`@property` methods can be nouns).
3. Pick one distinct word per concept.
For example `fetch`, `get`, and `retrieve` all mean the same.
Pick one.
4. Be consistent in your names.
Use the same phrases, nouns, and verbs in the function names you choose for your modules.


## Functions
There are 2 rules that can not be broken:
1. Functions should do one thing.
2. They should do it only.

The next sections will focus on how to do this.


### Do one thing
Since they only do one thing functions also be small.
If a function does not fit on your screen, it is too long.
If you devide a function into sections, this is an obvious symptom of doing more than one thing.
Functions that do one thing cannot be reasonably divided into sections.

To achieve this, it helps to keep one level of abstraction per function.
High level concepts should not be mixed minute details.

```py
X, y = generate_training_dataset()
X = X.loc[~X["customer_id"].str.startswith("XYZ")] # this should not be here
model = train_model(X, y)
```

Another hint that your function might do more than one thing is having too many arguments.
Up to 3 arguments is usually fine, but anything above 5 needs a good reason.


#### Blocks
Blocks (`if`, `for`, `while`, `with`) within these functions should be tiny, ideally just containing a function call.
Not only does this keep the enclosing function small, but it also adds documentary value because the function called within the block can have a nicely descriptive name.

Having small and simple functions also implies that functions should not be nesting blocks within blocks.
Therefore in Python, the indent level of a function should not be greater than one or two.
This makes the functions easier to read, but also helps to keep the level ob abstraction.


#### Logging
Try to log via decorators.
```py
@log("Deleting file")
def delete(file):
    pass
```
A positive side effect:
If you feel like you would like to add some logging within a function, this can be an indicator that it is a candidate to be refactored into separate functions.


### Do it only
Your function should not have side effects.
Side effects are lies.
Your function promises to do one thing, but side effects also do other hidden things.
This can be an unexpected change to a hidden variables.
Side effects often result in strange temporal couplings and order dependencies.
In the worst case your function would to different things with the same inputs, depending on when or how often you ran it.

Furthermore, functions should either do something or answer something, but never both.
Either your function should change the state of an object, or it should return some information about that object.


## Keep it simple and stupid
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.


## Don't repeat yourself
If see yourself writing the same (or very similar) code twice, make it a [function](#functions).


## Explicit is better than implicit
This is especially true for data science code and it includes:

+ Replace Magic Numbers with Named Constants
+ Test for values and value ranges instead of relying on assumptions.
+ Validate keys and dimensions before and after joins.
This is the most common source of both involuntary duplications and missing values.
+ Do not pivot tables.
It makes the resulting tables dimensions dependent on the previous tables contents, which almost guarantees failure.


## Comments
The older a comment is, and the farther away it is from the code it describes, the more likely it is to be just plain wrong.

If they can not be avoided, comments should only explain **why** something is done a certain way - if the reason for it is not apparent from the names of the variables and functions.
Comments can explain intent, but should **never** explain **what** happens.
If they do it is most often a sign that the naming of the function or variables is insufficient.
Generally, if a comment can be avoided by better naming or making a new function, it should be.

Commented-out code is never allowed, ever.


## Docstrings
Docstrings focus on **exactly what** a function does, **not** the **how** and **why**.
Technical details about the implementation and it's design decisions should be kept out of the docstring.
It should never describe what a function does not do.

Consider the docstring the most detailed instruction manual for the code.
The rules about [naming](#naming) are most important here, especially 3 (one word per concept) and 4 (consistent naming).


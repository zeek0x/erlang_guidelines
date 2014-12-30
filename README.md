Erlang Coding Standards & Guidelines
====================================

Suggested reading material: http://www.erlang.se/doc/programming_rules.shtml

***

Table of Contents:
* [Contact Us](#Contact-Us)
* [Conventions & Rules](#conventions--rules)
  * [Source Code Layout](#source-code-layout)
    * [Project Structure](#project-structure)
    * [Spaces over tabs](#spaces-over-tabs)
    * [Use your spacebar](#use-your-spacebar)
    * [No Trailing Whitespace](#no-trailing-whitespace)
    * [80 column per line](#80-column-per-line)
    * [Maintain existing style](#maintain-existing-style)
    * [Avoid deep nesting](#avoid-deep-nesting)
    * [More, smaller functions over case expressions](#more-smaller-functions-over-case-expressions)
    * [Group functions logically](#group-functions-logically)
    * [No God modules](#no-god-modules)
    * [Simple unit tests](#simple-unit-tests)
    * [Honor DRY](#honor-dry)
    * [Avoid dynamic calls](#avoid-dynamic-calls)
    * [Group modules in subdirectories by functionality](#group-modules-in-subdirectories-by-functionality)
    * [Don't write spaghetti code](#dont-write-spaghetti-code)
  * [Syntax](#syntax)
    * [Avoid if expressions](#avoid-if-expressions)
    * [Avoid nested try...catches](#avoid-nested-try-catches)
  * [Naming](#naming)
    * [Be consistent when naming](#be-consistent-when-naming-concepts)
    * [Explicit state should be explicitly named](#explicit-state-should-be-explicitly-named)
    * [Don't use _Ignored variables](#dont-use-_ignored-variables)
    * [Avoid boolean parameters](#avoid-boolean-parameters)
    * [Stick to one convention for naming modules](#stick-to-one-convention-for-naming-modules)
    * [Lowercase atoms](#lowercase-atoms)
  * [Strings](#strings)
    * [IOLists over string concatenation](#iolists-over-string-concatenation)
  * [Macros](#macros)
    * [No Macros](#no-macros)
    * [Uppercase Macros](#uppercase-macros)
    * [No module or function name macros](#no-module-or-function-name-macros)
  + [Records](#records)
    * [Record names](#record-names)
    * [Records go first](#records-go-first)
    * [Don't share your records](#dont-share-your-records)
    * [Avoid records in specs](#avoid-records-in-specs)
    * [Types in records](#types-in-records)
  * [Misc](#misc)
    * [Write function specs](#write-function-specs)
    * [Use -callback attributes over behaviour_info/1](use--callback-attributes-over-behaviour_info1)
    * [No nested header inclusion](#no-nested-header-inclusion)
    * [No types in include files](#no-types-in-include-files)
  * [Tools](#tools)
    * [Lock your dependencies](#lock-your-dependencies)
    * [Loud errors](#loud-errors)
    * [Properly use logging levels](#properly-use-logging-levels)
    * [Prefer the git protocol when specifying dependency locations](#prefer-the-git-protocol-over-others-when-specifying-dependency-urls)
* [Suggestions & Great Ideas](#suggestions--great-ideas)
  * [CamelCase over Under_Score](#camelcase-over-under_score)
  * [Prefer shorter (but still meaningful) variable names](#prefer-shorter-but-still-meaningful-variable-names)
  * [Comment levels](#comment-levels)
  * [Keep functions small](#keep-functions-small)
  * [Use behaviours](#use-behaviours)
  * [When programming defensively, do so on client side](#when-programming-defensively-do-so-on-client-side)
  * [Avoid unnecesary calls to length/1](#avoid-unnecesary-calls-to-length1)
  * [Move stuff to independent applications](#move-stuff-to-independent-applications)
  * [Use the facade pattern on libraries](#use-the-facade-pattern-on-libraries)

## Contact Us

For **questions** or **general comments** regarding the use of this library, please use our public
[hipchat room](https://www.hipchat.com/gpBpW3SsT).

If you find any **bugs** or have a **problem** while using this library, please [open an issue](https://github.com/inaka/erlang_guidelines/issues/new) in this repo (or a pull request :)).

And you can check all of our open-source projects at [inaka.github.io](http://inaka.github.io)

## Conventions & Rules

"Things that may be used as reason to reject a Pull Request."

### Source Code Layout

***
##### Project Structure
> Follow the OTP and community standards for project folder structure.

An individual Erlang application has the following structure when it is meant to conform to OTP conventions:

```
    $APP_ROOT
     |
     -- ebin/          % Contains all generated .beam files.
     -- include/       % Contains all public .hrl files.
     -- priv/          % Contains all other artifacts.
     -- src/           % Contains all .erl files and potentially private
     |                 % .hrl files.
     -- test/          % Contains all EUnit test files.
```

You should use this "flat" directory structure if your project is a simple Erlang application (e.g. a generic component which might be used in other projects) or a library application.

If your system consists of several applications working together, the umbrella project structure should be used,  in which the system root contains a folder named `apps` which will contain the individual application folders. Each application can then have its own `ebin`, `include`, `src` and `test` folders, and the entire project can share resources in the `priv` folder, and dependencies can be downloaded and compiled once for the entire project.
 
```
    $PROJECT_ROOT
     |
     -- $SYSTEM_ROOT/               % Main system directory.
          |
          -- config/                % Contains all system configuration
          |                         % files and templates.
          -- deps/                  % Contains external applications which
          |    |                    % are used as part of the system.
          |    |
          |    -- some_dep_app/
          -- apps/                  % Contains all applications which are
          |    |                    % part of the system.
          |    |
          |    -- some_app/
          |    -- another_app/
          -- patches/               % Contains all patches to Erlang/OTP
          |                         % which are custom to this system.
          -- priv/                  % Contains all other artifacts.
```

*Reasoning*: All erlang tooling assumes these folder structures, and not following them will cause many errors, especially when working with releases. 

An Erlang system consists of many applications and needs to follow various guidelines in order to be OTP-compliant. This ensures that it is compatible with various utility tools from OTP, e.g. for release generation. In the following lines the directory structure of a project is shown.

***
##### Spaces over tabs
> Spaces over tabs, 2 space indentation.

*Examples*: [indent](src/indent.erl)

*Reasoning*: This is *not* intended to allow deep nesting levels in the code. 2 spaces are enough if the code is clean enough, and the code looks more concise, allowing more characters in the same line.

***
##### Use your spacebar
> Surround operators and commas with spaces.

*Examples*: [spaces](src/spaces.erl)

*Reasoning*: Again, easier to find / read / etc.

***
##### No Trailing Whitespace
> Remove trailing whitespaces from your lines

*Examples*: [trailing_whitespace](src/trailing_whitespace.erl)

*Reasoning*: It's commit noise. Also [this long argument](https://programmers.stackexchange.com/questions/121555/why-is-trailing-whitespace-a-big-deal).

### Syntax

Erlang syntax is horrible amirite? So you might as well make the best of it, right? _Right_?

***
##### 80 column per line
> Stick to 80 chars per line, some of us still have to use vi sometimes, specially when editing code via ssh. Also, it allows showing more than one file simultaneously on a wide screen or laptop monitor.

*Examples*: [col_width](src/col_width.erl)

*Reasoning*: Not having to scroll horizontally while editing is a HUGE gain. Also, in wider screens you can open two files: one beside the other.

***
##### Maintain existing style
> When editing a module written by someone else, stick to the style in which it was written. If a project has an overall style, stick to that when writing new modules as well.

*Examples*: [existing_style](src/existing_style.erl)

*Reasoning*: It's better to keep a module that just looks ugly to you than to have a module that looks half ugly to you, half ugly to somebody else.

***

##### Avoid deep nesting
> Try not to nest more than 3 levels deep.

*Examples*: [nesting](src/nesting.erl)

*Reasoning*: Nested levels indicate deep logic in a function, too many decisions taken or things done in a single function. This hinders not only readability, but also maintainability (making changes) and debugging, and writing unit tests.

***
##### More, smaller functions over case expressions
> Use pattern-maching in clause functions rather than case's. Specially important if the case is:
> - the top-level expression of the function
> - huge

*Examples*: [smaller_functions](src/smaller_functions.erl)

*Reasoning:* it is usually the case that a case in a function body represents some sort of decision, and functions should be as simple as possible. If each branch of a decision's outcome is implemented as a function clause instead of as a case clause, the decision may be given a meaningful name. In other words, the case is acting as an 'anonymous function', which unless they are being used in the context of a higher-order function, merely obscure meaning.

***
##### Group functions logically
> Try to always separate **unexported** and **exported** functions in groups, with the exported ones first, unless it helps readability and code discovery.

*Examples*: [grouping_functions](src/grouping_functions)

*Reasoning*: Well structured code is easier to read/understand/modify.

***
##### No God modules
> Don't design your system using **god**  modules (modules that have a huge number of functions and/or deal with very unrelated things)

*Examples*: [god](src/god.erl)

*Reasoning*: God modules, like god objects, are modules that do too much or know too much. God modules usually come into existence by feature accretion. A beautiful, to-the-point module with one job, one responsibility done well, gains a function. Then another, which does the same thing but with different parameters. Then one day, you have a 6000-line module with 500 functions. Having modules (and functions) that do one and only one thing well makes it easy to explore and reason about code, and thus maintain it.

***
##### Simple unit tests
> Single responsibility applies to tests as well. When writing **unit** tests, keep them short and don't put more than 1 or 2 asserts per test

*Examples*: [test_SUITE](src/test_SUITE.erl)

*Reasoning*: Multiple tests can identify multiple errors in one run, if you put all the things you want to test into one test you'll have to fix one thing at a time until the test passes.

***
##### Honor DRY
> Don't write the same code in many places, use functions and variables for that

*Examples*: [dry](src/dry.erl)

*Reasoning*: This convention is specifically put in this list (instead of treat it as a [great idea](#great-ideas)) so that reviewers can reject PRs that include the same code several times or PRs that re-implement something that they know it's already done somewhere else.

***
##### Avoid dynamic calls
> If there is no specific need for it, don't use dynamic function calling.

*Examples*: [dyn_calls](src/dyn_calls.erl)

*Reasoning*: Dynamic calls can't be checked by ``xref``, one of the most useful tools in the Erlang world.

***
##### Group modules in subdirectories by functionality
> When having lots of modules, use subdirectories for them, named with a nice descriptive name for what that "package" does.

*Reasoning*: That way it's easier to find what you need and determine what a certain module does.

*Note*: Remember to properly configure your ``Emakefile`` and ``rebar.config`` to handle that

***
##### Don't write spaghetti code
> Don't write spaghetti code (A list comprehension with a case inside, or blocks with begin/end, and nested stuff)

*Examples*: [spaghetti](src/spaghetti.erl)

*Reasoning*: Spaghetti code is harder to read, understand and edit. The function callgraph for your program should strive to be a directed acyclic graph.

### Syntax

Erlang syntax is horrible amirite? So you might as well make the best of it, right? _Right_?

***
##### Avoid if expressions
> Don't use `if`.

*Examples*: [no_if](src/no_if.erl)

*Reasoning*: In some circumstances `if` introduces static boolean logic in your code, reducing code flexibility. In other cases, a `case` or a function call with pattern matching in its clauses is just more declarative. For newcommers (that have learned to use `if` in other languages), Erlang's `if` can be either hard to understand or easily abused.

*Debate*:
- [From OOP world](http://antiifcampaign.com/)
- [In this repo](issues/14)
- [In erlang-questions](http://erlang.org/pipermail/erlang-questions/2014-September/080827.html)

***
##### Avoid nested try...catches
> Don't nest `try…catch` clauses

*Examples*: [nested_try_catch](src/nested_try_catch.erl)

*Reasoning*: Nesting `try…catch` blocks defeats the whole purpose of them, which is to isolate the code that deals with error scenarios from the nice and shiny code that deals with the expected execution path.

### Naming

***
##### Be consistent when naming concepts
> Use the same variable name for the same concept everywhere (even in different modules).

*Examples*: [consistency](src/consistency.erl)

*Reasoning*: When trying to figure out all the places where an ``OrgID`` is needed (e.g. if we want to change it from ``string`` to ``binary``), it's way easier if we can just grep for ``OrgID`` instead of having to check all possible names.

***
##### Explicit state should be explicitly named
> Name your state records ``#state`` and use ``-type state():: #state{}`` in all your OTP modules.

*Examples*: [state](src/state)

*Reasoning*: OTP behaviours implementations usually require a state, and if it always have the same name it makes it more clearly recognizable. Defining a type for it, helps _dialyzer_ detect leaks (where an internal type as the state is used outside of the module).

***
##### Don't use _Ignored variables
> Variables beginning with _ are still variables, and are matched and bound, the _ just keeps the compiler from warning when you don't use them. If you add the _ to a variable's name, don't use it.

*Examples*: [ignored_vars](src/ignored_vars.erl)

*Reasoning*: They are **not** supposed to be used.

***
##### Avoid boolean parameters
> Don't use boolean parameters (i.e. `true` and `false`) to control clause selection.

*Examples*: [boolean_params](src/boolean_params.erl)

*Reasoning*: Clarity of intention and not requiring the reader to check the function definition to understand what it does.

***
##### Stick to one convention for naming modules
> Stick to one convention when naming modules (i.e: ik_something vs iksomething vs something).

*Examples*: [naming_modules](src/naming_modules)

*Reasoning*: It gives coherence to your system.

***
##### Lowercase atoms
> Atoms should use only lowercase characters. Words in atom names should be separated with `_`. Special cases are allowed (like `'GET'`, `'POST'`, etc.) but should be properly justified.

*Examples*: [atoms](src/atoms.erl)

*Reasoning*: Adhering one convention makes it easier not to have "duplicated" atoms all around the code. Also, not using caps or special characters reduces the need for `'` around atoms.

### Strings

***
##### IOLists over string concatenation
> Use iolists instead of string concatenation whenever possible

*Examples*: [iolists](src/iolists.erl)

*Reasoning*: Performance

### Macros

***
##### No Macros
> Don't use macros, except for very specific cases, that include
> * Predefined ones: ``?MODULE``, ``?MODULE_STRING`` and ``?LINE``
> * Magic numbers: ``?DEFAULT_TIMEOUT``

*Examples*: [macros](src/macros.erl)

*Reasoning*: Macros make code harder to debug. If you're trying to use them to avoid repeating the same block of code over and over, you can use functions for that.

***
##### Uppercase macros
> Macros should be named in ALL_UPPER_CASE:

*Examples*: [macro_names](src/macro_names.erl)

*Reasoning*: It makes it easier not to duplicate macro names, to find them using grep, etc.

***
##### No module or function name macros
> Don't use macros for module or function names

*Examples*: [macro_mod_names](src/macro_mod_names.erl)

*Reasoning*: Copying lines of code to the console for debugging (something that happens *a lot*) becomes a really hard task if we need to manually replace all the macros.

### Records

***
##### Record names
> Record names must use only lowercase characters. Words in record names must be separated with `_`. Same rule applies to record field names

*Examples*: [record_names](src/record_names.erl)

*Reasoning*: Record and field names are atoms, they should follow the same rules that apply to them.

***
##### Records go first
> Records that are used within a module should be defined before any function bodies.

*Examples*: [record_placement](src/record_placement.erl)

*Reasoning*: Records are used to define data types that will most likely be used by multiple functions on the module, so their definition can not be tied to just one. Also, since records will be associated to types, it's a good practice to place them in code in a similar way as the documentation does (and edoc puts types at the beginning of each module documentation)

***
##### Don't share your records
> Records should not be shared among multiple modules. If you need to share _objects_ that are represented as records, use opaque exported types and provide adequate accesor functions in your module.

*Examples*: [record_sharing](src/record_sharing.erl)

*Reasoning*: Records are used for data structure definitions. Hiding those structures aids encapsulation and abstraction. If a record structure needs to be changed and it's definition is written in a .hrl file, the developer should find all the files where that .hrl and verify that his change hasn't broken anything. That's not needed if the record structure is internal to the module that manages it.

***
##### Avoid records in specs
> Avoid using records in your specs, use types.

*Examples*: [record_spec](src/record_spec.erl)

*Reasoning*: Types can be exported, which aids documentation and, using ``opaque`` types it also helps with encapsulation and abstraction.

***
#####  Types in records
> Always add type definitions to your record fields

*Examples*: [record_types](src/record_types.erl)

*Reasoning*: Records define data structures, and one of the most important parts of that definition is the type of the constituent pieces.

### Misc

***
##### Write function specs
> Write the **-spec**'s for your exported fun's, and for unexported fun's when it adds real value for documentation purposes. Define as many types as needed.

*Examples*: [specs](src/specs.erl)

*Reasoning*: Dialyzer output is complicated as is, and it is improved with good type names. In general, having semantically loaded type names for arguments makes reasoning about possible type failures easier, as well as the function's purpose.

***
##### Use -callback attributes over behaviour_info/1.
> Unless you know your project will be compiled with R14 or lower, use ``-callback`` instead of ``behavior_info/1`` for your behavior definitions.

*Examples*: [callbacks](src/callbacks)

*Reasoning*: Avoid deprecated functionality

***
##### No nested header inclusion
> When having many _nested_ "include files", use -ifndef(HEADER_FILE_HRL) .... -endif so they can be included in any order without conflicts.

*Examples*: [nested](include/nested.hrl)

*Reasoning*: ``-include`` directives in included headers may lead to duplication of inclusions and/or other conflicts and it also hides things from the developer view.

***
##### No types in include files
> No `-type` in hrl files

*Examples*: [types](src/types.erl)

*Reasoning*: Defining types in public header files (especially those intended for inclusion via `-include_lib()`) might lead to type name clashes between projects and even modules of a single big project.
Instead, types should be defined in modules which they correspond to (with `-export_type()`) and this way take advantage of the namespacing offered by module names.
In other words, "no type definitions in header files" rule means that we will always need to use `some_mod:some_type()` unless referring to a type from the same module it's defined in.
Following this rule you also get the benefits that `-opaque` types provide, for instance, to dialyzer.

### Tools

***
##### Lock your dependencies
> In your rebar.config or Erlang.mk, specify a tag or commit, but not master.

*Examples*:
- [erlang.mk](priv/Makefile)
- [rebar.config](priv/rebar.config)

*Reasoning*: You don't want to be suddenly affected by a change in one of your dependencies. Once you've found the right version for you, stick to it until you *need* to change.

***
##### Loud errors
> Don't let errors and exceptions go unlogged. Even when you handle them, write a log line with the stack trace.

*Examples*: [loud_errors](src/loud_errors.erl)

*Reasoning*: The idea is that somebody watching the logs has enough info to understand what's happening.

***
##### Properly use logging levels
> When using lager, use the different logging levels with the following meanings:

*Meanings*:
  * ``debug``: Very low-level info, that may cover your screen and don't let you type in it :P
  * ``info``: The system's life, in some detail. Things that happen usually, but not all the time. You should be able to use the console with acceptable interruptions in this level.
  * ``notice``: Meaningful things that are worth noticing, like the startup or termination of supervisors or important gen_servers, etc…
  * ``warning``: Handled errors, the system keeps working as usual, but something out of the ordinary happened
  * ``error``: Something bad and unexpected happen, usually an exception or error (**DO** log the **stack trace** here)
  * ``critical``: The system (or a part of it) crashed and somebody should be informed and take action about it
  * ``alert``: _There is no rule on when to use this level_
  * ``emergency``: _There is no rule on when to use this level_

##### Prefer the git protocol over others when specifying dependency URLs
> When specifying dependencies in erlang.mk Makefils or rebar.config, prefer using the git protocol to download the dependency repository.

*Examples*: [makefile example](src/dependency_protocol/dep_protocol.makefile) [rebar example](src/dependency_protocol/dep_protocol.config)

*Reasoning*: SSH requires authentication.
https may require authentication (bitbucket does, github doesn't), but sometimes doesn't.
In addition, https is chatty compared to the git protocol, optimized for cloning repos.
The git protocol's main disadvantage is that it doesn't support authentication, which is a plus for CI systems.

* [Git on the Server - The Protocols](http://git-scm.com/book/ch4-1.html)

## Suggestions & Great Ideas

Things that should be considered when writing code, but do not cause a PR rejection, or are too vague to consistently enforce.

***
##### CamelCase over Under_Score
> Symbol naming: Use variables in CamelCase and atoms, function and module names with underscores.

*Examples*: [camel_case](src/camel_case.erl)

*Reasoning*: It helps a lot with the next issue in this list ;)

***
##### Prefer shorter (but still meaningful) variable names
> As long as it's easy to read and understand, keep variable names short

*Examples*: [var_names](src/var_names.erl)

*Reasoning*: It helps reducing line lengths, which is also described above

***
##### Comment levels
> Module comments go with **%%%**, function comments with **%%**, and code comments with **%**.

*Examples*: [comment_levels](src/comment_levels.erl)

*Reasoning*: It clearly states what the comment is about, also helpful to search for specific comments, like "%% @".

***
##### Keep functions small
> Try to write functions with a small number of expressions, and that do only one thing. **12** expressions per function except for integration tests is a good measure.

*Examples*: [small_funs](src/small_funs.erl)

*Reasoning*: From 3 different sources:
- Small functions aid readability and composeability. Readability aids maintainability. This cannot be stressed enough. The smaller your code, the easier it is to fix and change.
- A small function allows one to see its purpose clearly, so that you need to only understand the small subset of operations it performs, which makes it very simple to verify it works correctly.
- These are all compeling reasons:
  + a function should do one thing, if it's too large you are likely to be doing work better suited for multiple functions
  + clarity, it's easier to see what a function does when it's short and concise
  + reuse, keeping them short means you can use them later for something else (specially true for Erlang)
  + screen size: you want to be able to see the whole function if you want to connect via ssh to a server for whatever reason

*Notes*:

This guideline, together with **[Avoid deep nesting](#avoid-deep-nesting)** and **[More, smaller functions over case expressions](#more-smaller-functions-over-case-expressions)**, can be well followed by structuring your functions as follows:

```erlang
some_fun(#state{name=foo} = State) ->
  do_foo_thing(),
  continue_some_fun(State);
some_fun(#state{name=bar} = State) ->
  do_bar_thing(),
  continue_some_fun(State).

continue_some_fun(State) ->
  ...,
  ok.

```

Remember:

- There is no cost for a tail call like that.
- This pattern is efficient, compact, clear.
- It "resets" indentation so the code doesn't wander off the right edge of the screen.

Most importantly:

- It's easier to test because the functions delineate the testing hinge points.
- It gives more surface for tracing, so one can get very specific about where the computation goes off the rails. Nested cases are opaque at runtime.

***
##### Use behaviours.
> Encapsulate reusable code in behaviors.

*Examples*: [behavior](src/behavior.erl)

*Reasoning*: It's the OTP way ;)

***
##### When programming defensively, do so on client side
Do validations on the outmost layers of your code.

*Examples*: [validations](src/validations.erl)

*Reasoning*: One aspect of choosing where want you to crash is how you design your API: A function that checks the input before calling the gen_server behind it will avoid a full roundtrip to the gen_server and maybe even a gen_server crash.
do_it(Pid, X) when is_integer(X) -> gen_server:call(Pid, {do_it, X}).
If you design this way, the caller crashes if the arg is wrong.
If you don't tighten up the function head, the gen_server will crash.

***
##### Avoid unnecesary calls to length/1
> Lots of use cases of length/1 can be replaced by pattern matching, this is specially true when checking if the list has at least one element.

*Examples*: [pattern matching](src/pattern_matching.erl)

*Reasoning*: Pattern matching is one of the core aspects of Erlang and as such it's both performant and readable. Pattern matching is also more flexible so changes to the logic get simpler.

***
##### Move stuff to independent applications
> When you identify a block of functionality that is self-contained (it may be several modules or just a big one) and actually independent of the main purpose of your application, place that in a separate application. And consider open-sourcing it.

*Reasoning*: It's easier to share among apps. If open-sourced, you're sharing it with the community and you get the benefits of the community being involved in it.

*Note*: Do **not** create highly specific libraries that are too coupled with the project you're working on. Use this rule for libraries that will likely be reused in other projects.

***
##### Use the facade pattern on libraries
> [The facade pattern](http://en.wikipedia.org/wiki/Facade_pattern) is great to simplify library usage and serves as a form of self-documentation.

*Examples*: [kafkerl](https://github.com/inaka/kafkerl/blob/master/src/kafkerl.erl)

*Reasoning*: Having the relevant functions in a single module means that the end user doesn't have a hard time figuring out which functions to call. Note that to avoid making it too complex, you probably want to carefully consider which functionality you wish to support here; exposing fewer functions (the ones that show the basic use of the library) as opposed to just creating a dummy module containing every single exported function in the library is prefered.
This greatly reduces the learning curve of the library and therefore makes it more tempting to use.
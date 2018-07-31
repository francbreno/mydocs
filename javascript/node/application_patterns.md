# Note Application Patterns

## Setting up Your Project

### Organization

* Not everything on a single dir
  * Ex.: all logic in a `lib` directory
  * Avoid to create a _Parent Project_
* Create **_independent modules_** in the _root folder_ of your project
  * _subdirectories_ for **each module**
  * dependencies independent for each module
* Avoid _overthinking_
* Slicing this way is _not so that it could be reused later_. It's because development can become **_tightly focused_** and **_better mantainance_**
* DDD
  * Business Services (**_Bounded Contexts_**)
* UNIX mantra:
  > Do **one thing** and do it **well**
*

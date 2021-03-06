---
title: "Trust the Future"
date: 2020-11-04 14:00:00 -0800
categories:
 - R
tags:
 - R
 - package
 - future
 - future.apply
 - doFuture
 - furrr
 - future.batchtools
 - future.callr
 - future.tests
 - parallel
 - parallelly
 - HPC
 - parallel-processing
 - asynchronous
 - testing
 - validation
 - CRAN
---

<center>
<img src="/post/you_dont_have_to_worry_about_your_future.jpg" alt="A fortune cookie that reads 'You do not have to worry about your future'" style="border: solid 1px; max-width: 70%"/>
</center>

Each time we use R to analyze data, we rely on the assumption that functions used produce correct results.  If we can't make this assumption, we have to spend a lot of time validating every nitty detail.  Luckily, we don't have to do this.  There are many reasons for why we can comfortably use R for our analyses and some of them are unique to R.  Here are some I could think of while writing this blog post - I'm sure I forgot something:

* R is a functional language with few side effects ("just like mathematical functions")

* R, and its predecessor S, has undergone lots of real-world validation over the last two-three decades

* Millions of users and developers use and vet R regularly, which increases the chances for detecting mistakes and bugs

* R has one established, agreed-upon framework for validating an R package: `R CMD check`

* The majority of R packages are distributed through a single repository (CRAN)

* CRAN requires that all R packages pass checks on past, current, and upcoming R versions, across operating systems (MS Windows, Linux, macOS, and Solaris), and on different compilers

* New checks are continuously added to `R CMD check` causing the quality of new and existing R packages to improve over time

* CRAN asserts that package updates do not break reverse package dependencies

* R developers spend a substantial amount of time validating their packages

* R has users and developers with various backgrounds and areas of expertise

* R has a community that actively engages in discussions on best practices, troubleshooting, bug fixes, testing, and language development

* There are many third-party contributed tools for developing and testing R packages

I think [Jan Vitek] summarized it well in the 'Why R?' panel discussion on ['Performance in R'](https://youtu.be/uiEhmKN1RJo?t=1917) on 2020-09-26:

> R is an ecosystem.  It is not a language.  The language is the little bit on top.  You come for the ecosystem - the books, all of the questions and answers, the snippets of code, the quality of CRAN. ... The quality assurance that CRAN brings ... we don't have that in any other language that I know of.


Without the above technical and social ecosystem, I believe the quality of my own R packages would have been substantially lower.  Regardless of how many unit tests I would write, I could never achieve the same amount of validation that the full R ecosystem brings to the table.



When you use the [future framework for parallel and distributed processing](https://cran.r-project.org/package=future), it is essential that it delivers a corresponding level of correctness and reproducibility to that you get when implementing the same task sequentially.   Because of this, validation is a _top priority_ and part of the design and implementation throughout the future ecosystem.  Below, I summarize how it is validated:

* All the essential core packages part of the future framework,
**[future]**, **[globals]**, **[listenv]**, and **[parallelly]**, implement a rich set of package tests.
These are validated regularly across the wide-range of operating
systems (Linux, Solaris, macOS, and MS Windows) and R versions available
on CRAN, on continuous integration (CI) services ([GitHub Actions], [Travis CI],
and [AppVeyor CI]), an on [R-hub].

* For each new release, these packages undergo full reverse-package
dependency checks using **[revdepcheck]**.
As of October 2020, the **future** package is tested against more than 140
direct reverse-package dependencies available on CRAN and Bioconductor,
including packages **[future.apply]**, **[furrr]**, **[doFuture]**,
**[drake]**, **[googleComputeEngineR]**, **[mlr3]**, **[plumber]**,
**[promises]** (used by **[shiny]**), and **[Seurat]**.
These checks are performed on Linux with both the default settings and
when forcing tests to use multisession workers (SOCK clusters), which further
validates that globals and packages are identified correctly.

* A suite of _Future API conformance tests_ available in the
**[future.tests]** package validates the
correctness of all future backends.  Any new future backend developed must
pass these tests to comply with the _Future API_.
By conforming to this API, the end-user can trust that the backend will
produce the same correct and reproducible results as any other backend,
including the ones that the developer have tested on.
Also, by making it the responsibility of the developer to assert that their
new future backend conforms to the _Future API_, we relieve other
developers from having to test that their future-based software works on all
backends.
It would be a daunting task for a developer to validate the correctness of
their software with all existing backends. Even if they would achieve that,
there may be additional third-party future backends that they are not aware
of, that they do not have the possibility to test with, or that are yet to be developed.
The **future.tests** framework was sponsored by an [R Consortium ISC grant](https://www.r-consortium.org/projects/awarded-projects).

* Since **[foreach]** is used by a large number of essential
CRAN packages, it provides an excellent opportunity for supplementary
validation. Specifically, I dynamically tweak the examples of
**[foreach]** and popular CRAN packages **[caret]**, **[glmnet]**, **[NMF]**,
**[plyr]**, and **[TSP]** to use the **[doFuture]** adaptor.
This allows me to run these examples with a variety of future backends to
validate that the examples produce no run-time errors, which indirectly
validates the backends and the _Future API_.
In the past, these types of tests helped to identify and resolve corner cases
where automatic identification of global variables would fail.
As a side note, several of these foreach-based examples fail when using a
parallel foreach adaptor because they do not properly export globals or
declare package dependencies.  The exception is when using the sequential
_doSEQ_ adaptor (default), fork-based ones such as **[doMC]**, or
the generic **[doFuture]**, which supports any future backend and
relies on the future framework for handling globals and packages.

* Analogously to above reverse-dependency checks of each new release,
CRAN and Bioconductor continuously run checks on all these direct, but
also indirect, reverse dependencies, which further increases the validation
of the _Future API_ and the future ecosystem at large.




May the future be with you!



## Links

* **future** package: [CRAN](https://cran.r-project.org/package=future), [GitHub](https://github.com/HenrikBengtsson/future)
* **future.tests** package: [CRAN](https://cran.r-project.org/package=future.tests), [GitHub](https://github.com/HenrikBengtsson/future.tests)

[future]: https://cran.r-project.org/package=future
[globals]: https://CRAN.R-Project.org/package=globals
[listenv]: https://CRAN.R-Project.org/package=listenv
[parallelly]: https://cran.r-project.org/package=parallelly
[future.apply]: https://cran.r-project.org/package=future.apply
[furrr]: https://cran.r-project.org/package=furrr
[doFuture]: https://cran.r-project.org/package=doFuture
[future.batchtools]: https://cran.r-project.org/package=future.batchtools
[future.callr]: https://cran.r-project.org/package=future.callr
[future.tests]: https://cran.r-project.org/package=future.tests

[revdepcheck]: https://github.com/r-lib/revdepcheck
[drake]: https://cran.r-project.org/package=drake
[googleComputeEngineR]: https://cran.r-project.org/package=googleComputeEngineR
[mlr3]: https://cran.r-project.org/package=mlr3
[plumber]: https://cran.r-project.org/package=plumber
[promises]: https://cran.r-project.org/package=promises
[shiny]: https://cran.r-project.org/package=shiny
[Seurat]: https://cran.r-project.org/package=Seurat

[caret]: https://CRAN.R-Project.org/package=caret
[doMC]: https://CRAN.R-Project.org/package=doMC
[foreach]: https://CRAN.R-Project.org/package=foreach
[glmnet]: https://CRAN.R-Project.org/package=glmnet
[NMF]: https://CRAN.R-Project.org/package=NMF
[plyr]: https://CRAN.R-Project.org/package=plyr
[TSP]: https://CRAN.R-Project.org/package=TSP

[GitHub Actions]: https://github.com/features/actions
[Travis CI]: https://travis-ci.org/
[AppVeyor CI]: https://www.appveyor.com/
[R-hub]: https://builder.r-hub.io/

[Jan Vitek]: https://twitter.com/j_v_66

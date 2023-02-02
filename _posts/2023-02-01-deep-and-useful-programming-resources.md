---
layout: post
title: Deep and Useful Programming Resources
subtitle: A Fictional Job Opening From My Ideal Company
tags: [learning, programming, functional programming, theory, applications]
---

Many programmers read books about new platforms or languages. Perhaps even a book about applying a particular pattern in their favorite language. I like to go deeper and try to understand the fundamental structure that serves as the underpinnings of all programming, that is, the code behind the code, if you will. I have encountered a few books and resources over the last few years that have helped me understand what this thing we call "computation" is really about.

These books and resources go deep; every page has something weighty to chew on. The concepts they cover may seem intimidating, but they are actually quite simple. You need to stick with them and get to the point where there is a shift in your thinking. While I have not (yet) finished any of these, what I have read has already proved fruitful.

## The Structure and Interpretation of Computer Programs
### By Harold Abelson, Gerald Jay Sussman, and Julie Sussman

SICP is famous enough it almost needs no introduction. The book dives deep into fundamental programming principles before moving on to designing and implementing a programming language. The book uses Scheme, a dialect of Lisp. While the Lisp family has mythical status in the programmer community, it is a straightforward language with a basic structure and syntax that can be learned in a few minutes.

This book is very dense, and it will take a while to understand the contents fully, but it is well worth it. Fortunately, video lectures from the authors are available for free on YouTube to help you get through the material. If you don't like the authors' teaching styles, this book has been covered many times on YouTube. The book is available in print and for free under the Creative Commons.

\[[Print](https://www.amazon.com/Structure-Interpretation-Computer-Programs-Engineering/dp/0262510871)\] \[[Web](https://mitp-content-server.mit.edu/books/content/sectbyfn/books_pres_0/6515/sicp.zip/index.html)\] \[[HTML5/epub](https://sicpebook.wordpress.com)\] \[[PDF](https://github.com/sarabander/sicp-pdf)\] \[[Video Lectures](https://www.youtube.com/watch?v=-J_xL4IGhJA&list=PLE18841CABEA24090)\]


## Category Theory for Programmers
### By Bartosz Milewski

Category theory examines the underlying structure of mathematics and logic through "objects" and "arrows" (also called "morphisms"). For programmers, category theory can serve as a roadmap to new ways to manipulate code and how different data structures and functions relate. This book answers what things like a "monad" or a "functor" are and why they are essential concepts. Code examples are in Haskell (more on that below) and, much more verbosely, C++.

Like SICP, this book has a series of free lectures on YouTube, and Milewski is an excellent teacher while demystifying this obscure branch of mathematics. This book was also released under the Creative Commons.

\[[Print](https://www.blurb.com/b/9621951-category-theory-for-programmers-new-edition-hardco)\] \[[Web](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)\] \[[PDF](https://github.com/hmemcpy/milewski-ctfp-pdf)\] \[[Video Lectures Part 1](https://www.youtube.com/watch?v=I8LbkfSSR58&list=PLbgaMIhjbmEnaH_LTkxLI7FMa2HsnawM_)\] \[[Video Lectures Part 2](https://www.youtube.com/watch?v=3XTQSx1A3x8&list=PLbgaMIhjbmElia1eCEZNvsVscFef9m0dm)\] \[[Video Lectures Part 3](https://www.youtube.com/watch?v=F5uEpKwHqdk&list=PLbgaMIhjbmEn64WVX4B08B4h2rOtueWIL)\]

## Learn You a Haskell for Great Good!
### By Miran Lipovaca

Haskell is a reasonably simple functional language that feels more like mathematics than any other language I have seen. In the previous book, Milewski even shows how you can manipulate Haskell functions on paper as though they were algebraic equations!

The Haskell community has actively mined the depths of category theory, making it a good fit for code examples in the previous book. By studying the language, the last book is made more clear. Coincidently, despite being very different languages, [Swift was in part influenced](https://nondot.org/sabre/) by Haskell. It doesn't take long before you see Haskell's imprint on Swift.

The book I have selected here is the famous "Learn You a Haskell for Great Good!" The book is easy to read and has been a joy.

\[[Print](https://nostarch.com/lyah.htm)\] \[[Web](http://learnyouahaskell.com/chapters)\] \[[PDF](http://learnyouahaskell.com/learnyouahaskell.pdf)\]

## PointFreeCo
### By Brandon Williams and Stephen Celis

Diving into these concepts in functional languages is great, but what about applying these ideas to something more "practical" like Swift? Luckily there is a fantastic set of resources from PointFreeCo.

Brandon Williams and Stephen Celis do a fantastic job in their videos, with very well-done transcripts included, discussing functional programming using Swift. They go over the theoretical concepts, how to implement them in Swift, ask, "what's the point?" and use the ideas in practical projects.

They publish the libraries they generate on their GitHub, everything from "missing libraries" such as [Overture](https://github.com/pointfreeco/swift-overture) and [Prelude](https://github.com/pointfreeco/swift-prelude) to opinionated, but powerful, architecture such as [TCA](https://github.com/pointfreeco/swift-composable-architecture).

Some videos and articles are free, and a subscription for access to everything. The content they cover is extensive, so I believe it is well worth the entry price.

\[[Website](https://www.pointfree.co)\] \[[GitHub](https://github.com/pointfreeco)\]
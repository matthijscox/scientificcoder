---
title: "Production-ready code for scientists:  3 lessons learned"
datePublished: Thu May 04 2023 09:43:36 GMT+0000 (Coordinated Universal Time)
cuid: clh8xwgl1000n09jk9g35dbly
slug: production-ready-code-for-scientists-3-lessons-learned
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1683010031069/a33212ab-1534-4842-92cd-a94b4e89785c.png
tags: code, advice, production, lessonslearned

---

*How do you become a great scientific coder? To understand this, I want to ask others about their journey and share their lessons with you. This post is a collaboration with* [Keith Myerscough](https://www.linkedin.com/in/keith-myerscough/)*, a mathematical consultant and senior engineer, who helped me with setting up our internal Julia language ecosystem*.

Matthijs asked me to write a guest post about what skills are needed to become a great scientific coder. He asked me because I assisted a team of scientists in adopting Julia for their research and development work. I am keen to help: I empathize with people who come up with great ideas but have a hard time wrapping these up into something that can be turned into a product. Even under the assumption that code will be extensively improved upon by others with more software knowledge, that first delivery is either a big hurdle or the seed for a smooth project.

Before diving into what I think is most important for scientists developing code, let us take a small step back. A central theme of this scientific coding blog is that we must rid ourselves of hard boundaries between different groups working towards delivering the same product. But removing the divide(s) does not remove the inherent differences between ideation and productization, between divergent and convergent thinking. We will still have to bring “something” from an idea into a product; we just want to make this journey a continuous one. The reality is, however, that larger products require multiple people working on them. So the idea-product continuum will still have to be divided. I see this as creating a chain of several people working in stages. The important thing is that any boundary is permeable, in both directions: new ideas are presented in a way that can be turned into a product and improvements in the product can find their way back to the code used in ideation. This blog post addresses the implications of the first of those requirements on people not used to having their code end up in a production environment.

From my time working with a team of engineers with a physics background generating code that was intended for production, I have found the following suggestions to be most relevant:

* Make your work reproducible
    
* Keep everything as small as possible
    
* Be relentless in asking for help
    

I will discuss these in more detail below.

## Make your work reproducible

This is probably too obvious for many of you, but this is the start. The divide between ideation and productization is also one between “it works now” and “it will work forever”. As a scientific software developer, your code does not have to work forever, it does not have to cover all corner cases and it does not have to be optimized for performance. But if you want to hand it over to the next person in the chain, it will have to at least do (more or less) the same in most cases.

The best way to guarantee this is to have the intended use(s) of your idea encoded in tests and to have these tests run in an automated environment. It might even help to write the tests first. Unfortunately, I see it too often that people are “testing” their code using REPL commands or script runs, but do not include these as parts of delivery, losing the commands/scripts to run the code forever. Running tests in an automated environment, such as Github Actions, Bitbucket Pipelines, Jenkins etc., avoids any sneaky dependencies on your local machine configuration. This infrastructure is a prerequisite for scientific software development.

There are two skills required for this. The first is a solid understanding of the test framework in use for your project. You should feel comfortable in both modifying and creating tests, using the tools available. The second skill is version control. In particular, familiarize yourself with the command-line version. There is some irony in the fact that version control tools like Tortoise and SourceTree themselves lead to irreproducibility, as there’s no way of tracing just what you clicked when; with the command line, you always have your history. Furthermore, the command-line restricts you to using only the commands you know. You can get a long way with the basics.

## Keep everything as small as possible

This is a super-linear advantage, to put it in nerdy terms. Reducing the size of a component makes all the work that has to be done on it easier. It makes every pull request less work, it makes every test run faster and it makes every bug easier to find. But more than that, it reduces the number of people who need to work on it, reducing the amount of communication required.

This is an important advantage of moving from a two-language situation to the one-language paradigm. In the two-language situation, the productized software will always have a modular structure, thanks to the software engineering attention that was spent on the architecture. This modularity would, however, not necessarily exist in the ideation code base. In the one-language paradigm, the same architecture is available to scientists. You, as a scientific software developer, can see this as one of the rewards of switching tools.

One of the easiest yet often overlooked ways to keep things small is to rely on existing (open-source, inner-source or even closed-source) tooling. This immediately reduces the complexity of your code. In Julia, it also aids in the composability of your code to rely on existing implementations.

It’s harder to divine specific skills for this goal. You will need to know a little more about how a language works to know how to split a package into multiple packages, or better still, how to introduce new functionality in a separate package that interacts nicely with existing packages.

*\[Matthijs' comment:\] I can think of a simple tip. If you notice that you often write long scripts or functions, say dozens or hundreds of lines of code inside one function, you can probably cut it into multiple functions with easy-to-understand names.*

## Be relentless in asking for help

It is important to acknowledge that you can not know everything. As a scientific software developer, you will probably need help from the more software-savvy people on your team. Do not hold back in asking them. Again within the framework of an idea-product continuum, there should be no hard boundaries. Whoever is responsible for bringing your delivery to the next level, has an interest in helping you deliver something better, to make your life easier.

## Conclusion

Writing code for product development as a scientist can be a challenging task. Hopefully, my advice can help you find ways to improve your work. Remember, writing good code is not just about solving the problem at hand, but also about helping others, including your future self. By investing in your software skills now, you can set yourself and your team up for success in the long run.

*Let me know if you enjoy this guest post and would like to read more of such posts in the future, by leaving a comment!*
---
title: "Software Testing for Scientists"
datePublished: Sun Jun 11 2023 12:35:26 GMT+0000 (Coordinated Universal Time)
cuid: clirert0k000109me5bxe44fr
slug: software-testing-for-scientists
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1685970744744/3005ed53-27a0-4856-92d6-26ece2b8fe52.png
tags: software-development, tips, testing, best-practices, scientificsoftware

---

I am currently reading the book "Software Engineering for Science." It is one giant complaint about how scientists are terrible at writing maintainable code for themselves. I won't go into all the pain, but I do recognize that pain deeply and have written about it elsewhere. Right now I am reading this book hoping to find solutions. So, what's the proposed solution? The book doesn't provide a simple answer, but one recurring topic is "testing, testing, TESTING!" So, let's talk about testing!

Why don't scientists test their code? Well, it turns out that most scientists do not have a software engineering background, yet they find themselves writing code and software for their work. Alternatively, they may collaborate heavily with software engineers, either in academia or in the industry. If you find yourself in this scenario, you probably don't have the time to suddenly obtain a computer science degree, but it's beneficial to learn a few tricks from the professional software development community. As mentioned, the primary skill to acquire is software testing.

(As always I want to repeat that when I talk about "scientists", this may refer to anyone who uses modeling techniques and computational thinking in their daily job. This can include data scientists, requirements engineers, business analysts, financial quants or anyone else. See [My Target Audience](https://scientificcoder.com/my-target-audience) article.)

I know from experience that scientists often use manual testing strategies. In this article I will share a few simple steps that you can take to improve the correctness of your code, thus making you and all your colleagues trust the results of your work. Without trust, people will stop using the software you develop. That's a shame.

Tests also help to "refactor" the code, you can change the code, quickly check all tests pass, and be confident about your changes. Since your code will change a lot during your work, the peace of mind you gain from knowing everything is working is absolutely worth the effort of learning about a few software test strategies.

Some people even believe that writing tests will make the code more usable, more modular and improve the overall software architecture. So plenty of reasons to test.

There was a time that I didn't write any tests for my code. Several times it was pointed out to me that software testing is a good practice. I never really started, or started only half-heartedly. At some point I was working with a lot of software engineers, and they all got a course in test-driven development (TDD), so I promised to try TDD in a new project. At the start it was painful to change my way of working, but after a while I got the hang of it and I have been writing tests ever since. The main benefit for me is that I do not have to keep the whole codebase in my mind anymore. I can just focus on the a small portion, make it work, and then see if I didn't break anything by running the tests. Before, when I didn't have any tests, I would have to consider all dependent code in my mind, and start checking manually whether those other pieces of code still worked. The tests help me relax and save literal headaches.

## Semi-Automated Testing

So the first advice is simple: if you ever find yourself running the same manual tests, for example by executing a function with example inputs and checking that the output matches your expectation, then it's time to automate that manual work by writing explicit tests!

There are many other benefits to automated testing, but the primary reason to get started as an individual is simply to save yourself the effort of running endless scenarios by hand from memory. Manual testing is un-scalable as the code base grows. How can you be sure you didn't break something a colleague of yours is using?

## Fully automated testing

Writing tests and running your tests manually is a big step up from having no tests at all. Unfortunately people can forget to run the tests. To increase the confidence in your code, you can automate the tests for every change that is made to your software, and only allow changes that pass the tests.

In our modern age, a junior scientist can single handedly setup an automated testing system, at least for open source projects on Github. In the Julia ecosystem, which is mostly written by scientists, 89% of packages have automated tests.

Unless you are forced to setup infrastructure inside your own organization, automating your test suite should be relatively low effort, yet high reward.

To get you started, you can read my previous article about [how to automate your tests and code quality](https://scientificcoder.com/automate-your-code-quality-in-julia).

## Regression Testing

The rest of this article will mostly be about the types of tests you can write. Consider them as best practices if you like.

Regression testing is probably the simplest form of testing, and typically what people do intuitively already in manual testing. It's all about checking that the results of your functions reproduce. Run the code with known inputs and check that the outputs match with expected values. If you have no well known reference, these expected values can come from historical runs of your own code.

Basically all you do is this: `f(x,y) == expected_value`

Stochastic processes are harder to test this way. You may set a seed to keep the code deterministic. Or check that the output falls within some expected distribution. Or focus on testing the non-stochastic components of your code.

## Boundary Testing

Difficult, or rare, uses of the code are often called "corner cases" or "boundary cases", as they exist somewhere on the boundary of what your code can do. People often forget to test these cases, focusing all effort on verifying typical use cases.

Sometimes people call this "good weather" versus "bad weather" testing. Good weather is the typical use of your code, with input data in some normal operating range. Bad weather happens when less expected input data leads to less expected behavior in your code.

Errors are very common corner cases, or "bad weather". Don't forget to test errors. Errors and their messages are extremely important for users and developers of your software to figure out what went wrong and how to fix the mistakes. Junior developers always underestimate the importance of good error messages.

Other corner cases will depend on the domain you are modeling with your code. If you are simulating fluid dynamics in metal pipes typically ranging from 10 cm to 50 cm, but the user may input 500 cm, then you have to consider that corner case. Do you throw an error beyond a certain range, or provide a warning that the behavior may be incorrect, or test the behavior properly even though most users will never go there? These are all decisions to be made by you, the programmer.

Extreme input values can also lead to numerical instabilities, which brings us to the next section.

## Numerical instabilities

You may have the most beautiful math and science, but when you write code, you'll have to understand some of the limitations of computer hardware. Mathematical problems may be poorly conditioned, but the numerical algorithms can also be a source errors and mistakes. Numerical instability is about poorly conditioned computer algorithms, even though the math behind it is well conditioned.

For example, a common source of mistakes happens with floating point arithmetic. Be careful with math that uses very big and very small numbers. For example when using 64-bit floating points in Julia, we can get:

```python-repl
>>> 10^10 + 10^-6 - 10^10
1.9073486328125e-6

>>> 10^10 + 10^-7 - 10^10
0.0
```

In both cases we expect to return the small value in the middle, since `x + y - x = y` , but that's not what we get. We can find the wrong value of `y` or even obtain a zero. This kind of issues happen because numbers in computers are represented with a finite accuracy, as a trade-off to limit the amount of allocated memory.

This example may seems silly, but if you do any kind of linear algebra with matrices that contain a wide range of values, you may quickly run into such problems without noticing.

For our testing strategy, one simple take-away from floating point arithmetic is to use approximate equalities instead of identical equality checks, so test that `x ≈ 5.0` instead of `x == 5.0`. What tolerances you find acceptable in your comparisons is another big decision you will have to make.

In general, read a good book like [Fundamentals of Numerical Computation](https://tobydriscoll.net/fnc-julia/frontmatter.html) to get an idea of the interplay between math and computers.

## Toy examples

If you have some complicated, multi-dimensional, multi-physics simulation software, you do not really know how it behaves. Actually you are using the software to figure out how your system behaves. So how can you test the behavior?

Well, you can probably compare your code to simpler problems that are well known, like toy models or analytical solutions. Cases where you do know the answer, your code should behave accordingly. If it doesn't match, then you know you have a fundamental error in your code somewhere.

For the more complex cases you are researcher, you cannot check the end result, but you can test all the smaller components of your code. The unknown, untestable behavior probably resides in the interplay between all kinds of known smaller parts. As long as you know the smaller components behave according to known physical and mathematical principles, you have more trust in the aggregate.

## Reference datasets

Instead of finding simple toy examples, you can also look for reference code and datasets. Either by looking in the literature or by testing against alternative software packages. Your code should do something novel, else you would be using existing software, but there is probably overlap in behavior with other software packages. That overlap in functionality is the part you can check automatically to look for errors in the behavior of your code.

## Coverage metrics

Measuring how well your tests cover your source code is not really a testing strategy, but it is really helpful to learn where you can improve your testing strategy. Code that has no corresponding tests yet is low hanging fruit. And while code coverage is no guarantee that your testing strategy is perfect, it is a good first indication for others about how serious you are in your testing. This increases the trust they (and you yourself) may have in your code.

Once you get into code coverage metrics, you can slowly expand to other [code quality metrics and tools](https://scientificcoder.com/automate-your-code-quality-in-julia), to further increase that trust.

## Common Sense

A simple way to invent tests is to use your common sense. Let's say you were given a piece of code from a colleague. How would you verify that the code is working properly? What would make you trust that code? Now figure out a way to codify that common sense check into an automated test code. Done!

Most testing strategies are simply common sense. They are best practices found by legions of software developers around the world over the last decades. Stand on the shoulders of all that experience, but don't forget to use your own mind.

## Objections to testing

A common objection from scientists, against writing tests, is that their code evolves too fast. They do not really know what to test, because they are using the code to figure out the physics and science. So they say that there is no need to test.

This is not a valid argument I am afraid. Scientists are not really that special. Most professional software developers do not know exactly what their users want. They expect their code to evolve. In [The Lean Startup](https://theleanstartup.com/), the whole process of building a (software) company is described as a scientific cycle. Build a product based on assumptions, measure if users want it, learn from that, build some more and continue onwards. It's like a social science experiment, trying to figure out what humans want by building the code. Yet in professional software companies there is always a heavy focus on testing the code.

So I believe that the uncertainty and evolution of the code is no reason against testing. I believe the main reason scientists forgo testing is simply because they are never trained to think about the benefits of software testing. They code as a side project. But if the code is critical to your scientific results, you will be very happy to have the tests to prove the correctness of the code.

## Conclusion

I strongly advise to adopt software testing strategies to improve the correctness and reliability of your code. By starting to use some of the techniques I described, researchers can build trust in their software and ensure its quality. Embracing these best practices will not only save time and effort but also enhance the long term research process.
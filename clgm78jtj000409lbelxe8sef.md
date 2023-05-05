---
title: "My Target Audience"
datePublished: Tue Apr 18 2023 11:46:15 GMT+0000 (Coordinated Universal Time)
cuid: clgm78jtj000409lbelxe8sef
slug: my-target-audience
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1681823799101/87d9ae58-3e5c-428e-a6fc-554e35d68c10.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1681818346405/b3103eb1-9008-42ce-a071-5c9a927b4ce0.png
tags: culture, julia, people, scientific-computing, numerical-computing

---

What kind of people do I have in mind while writing this blog? People who share my professional mission of course! What is that mission you ask? Let me elaborate.

## From Research to Engineering

I am a scientist who [danced with startups](https://medium.com/symbionic-project) and moved into industry. I've spend years doing research, and years doing software product development. During those years I have gotten slightly frustrated with the process of moving ideas from research to production, primarily in my favorite field of scientific computing.

The typical problem is that the time from research to production can be long, often spanning multiple years per project, and the process can be error prone. Some common smaller issues I see arising in many of our projects:

1. Unreproducible, unexplainable scripts or notebooks in research
    
2. Effort to translate from one technology to another
    
3. Difficulty optimizing the idea for direct product integration
    

One typical constraint problem I observed is the following. The researchers quickly write a prototype, management wants it turned into a product, the software engineers then spend ages deciphering the prototype and converting it for the specifics of the production systems. Because the researchers are not the bottleneck they continue tinkering with the prototype and bothering the software engineers with new insights, which further reduces the overall efficiency. Meanwhile other researchers find new prototypes and hand them over to these software engineering teams. This can result in a never ending cycle of downwards productivity.

While many improvements in this scenario are possible and needed, one aspect attracts a lot of attention: the technology differences. For many years it was taken for granted that we need different tools in the separate phases between research and product development. For quick research and exploration you use an easy to use programming language, for example Python. For high performance, reliable, scalable production environments, you use a language like C++, sacrificing ease-of-use in favor of these important utilities. This two language problem immediately creates a bottleneck in handing over ideas smoothly for development.

## The Two Culture Problem

The “two language problem” was globally accepted for several decades. You just learn to live with it. Until one day it was challenged by the [Julia language](https://julialang.org/blog/2012/02/why-we-created-julia/), a programming language that promises both speed and ease of use. As I feel the pain of the two language problem deeply, I wanted to try out this new solution. So together with several allies I went on a mission to adopt this new technology at work, and remove the bottleneck.

While we had initial success and attention, we quickly stumbled into resistance from the existing groups of researchers/scientists and developers. Over time I have named this the “two culture problem”. In the beginning I didn’t see the cultures clearly, which limited our success. I was too focused on the technological problem itself.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681735971356/91b6e886-7ce1-41a3-9d9f-29b7b096e7f2.png align="center")

I will refer to the two cultures as “scientists” versus “developers”. However, the “scientists” group generalizes to anyone who codes quick and dirty to explore, such as domain experts, data analysts and others like that. I do hope everyone is doing their exploration somewhat scientifically, so the generalization should makes sense. Scientists typically want to get their stuff done, perhaps with code, but they don't care about the code. Software developers care deeply about the code craftmanship, sometimes obsessively so, but often developers barely understand the business domain or science. There are people near the middle, trying to balance both, but they are a rare breed.

The benefit of these two names "scientist" versus "developer" is that I can call the middle ground "scientific developers", or "scientific coders", or "scientific software engineers", which is the kind of people I have in mind to form the bridge between these two cultures. I want more of these super engineers and give them the tools they need to succeed.

## Bridging The Two Cultures

I have discussed some of my attempted changes in my blog post [Organizational Refactoring](https://www.functionalnoise.com/pages/2022-12-29-org-refactor/). People who walk between cultures feel friction, their skills not always acknowledged, yet they get to see the problems and the opportunities from multiple perspectives. Often an individual can become immensely valuable by learning the skills that people stuck in a single mindset are unwilling to pick up.

So let's start a new culture right there in the middle, by connecting people who share this passion for improvement. The Julia language is a tool designed for scientific coders, though not necessarily available to use in their current organization which may force upon them the tools of the dominant cultures. I have spoken with many who see the bottleneck between science and production, but are frustrated that they are not allowed to solve it. This is a good sign! There is a desire for bottom-up change!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681735992315/62fdd58f-4630-4120-8eb4-5238740543e8.png align="center")

I am not saying the Julia language is the answer to everything. You can do fine with a combination of tools, especially if you do not write your own high performance algorithms. Many data scientists I worked with only use off-the-shelf Python packages with a C/Fortran/C++ backend. They barely look under the hood of their favorite packages, and never try to write a performant algorithm themselves. So they are dependent on the small group of interested software developers who can juggle multiple technologies.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681804812222/15f30443-2301-42d4-96a6-d2b09e1d4cc0.png align="center")

Numerical computing professionals are a different breed, they want to write their own high performant code. They are often unsatisfied with the features of the libraries embedded in Python. These professionals will run into the problems I described, especially in larger organizations. It's the same struggles I have run into over the years. So there you have my target audience: scientific software engineers. In the end it's not about betting on a single technology, but about betting on the right kind of people.

This audience is a smaller group of people than either scientists or pure software engineers. Yet I believe that if we band together there are plenty of us. Unfortunately, it can be difficult to find each other.

Do you feel the pains I describe? Do you feel a desire for improvement? Do you share my professional mission for better numerical product development? Then this blog is for you! Subscribe to the newsletter and let me know what kind of knowledge would help you in your personal struggles!
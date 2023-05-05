---
title: "Why this blog?"
datePublished: Sun Apr 16 2023 17:27:18 GMT+0000 (Coordinated Universal Time)
cuid: clgjojfza000109mgayefbmaj
slug: why-this-blog
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1681655524424/8208e14f-0765-4eb2-a301-4d9470f26e45.png
tags: software-engineering, julia, scientific-computing, numerical-computing

---

My goal in life is to follow my curiosity and help others with the knowledge I uncover. A solid way to help people is to reflect on the last years and think about what would have helped your past self. In my case, I have been busy trying to develop and deploy numerical computing applications with the Julia language. One thing that I found lacking was good material on how to do numerical computing professionally in the industry, something I learned is sometimes called "scientific software engineering". A book or blog about large-scale development and deployment into production systems would have helped me tremendously. I had to learn most things the hard way through trial and error.

Since what I need doesn't exist, we will have to make it exist! That's this blog!

I have been blogging about my journey on my private website [functionalnoise.com](http://www.functionalnoise.com), but I will separate the professional software content from my private musings there. That's why I am starting this new blog for you, including a newsletter to easily subscribe to my content (something people have been asking me for a while now).

In the future, I would like to write a book, but I first freely share my current knowledge here and get your feedback. I am also interested to interview people in the industry or doing collaborative blogging with people (Let me know if that's you! Don't underestimate yourself, few people are experts in this field). Once I have a good amount of posts, I'd like to polish those and bundle them into a book.

Side note: if you prefer podcasts about numerical computing software, head over to [inspiringcomputing.com](http://www.inspiringcomputing.com). This an initiative from a fellow scientific traveler, which I encourage even if I prefer blogs and books over audio content.

## **The blog name**

I came up with a few options and asked for feedback on LinkedIn. These are the poll results:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681665368097/ce138a4f-bf2f-428e-b2e5-0a6355932a4a.jpeg align="center")

I was personally doubting between "The Numerical Mind" and "The Scientific Coder". They both sound more personal than the other two. "The Numerical Mind" had my slight personal preference because there is a hint to my open-source work with brain waves and biosensors. The Scientific Coder matches better with the target audience I have in mind: my kind of people. So I opted for the latter.

## Who are my people?

I will write a longer post about who I consider my ideal audience, but in short: scientific software engineers. Scientists who love to code and want to learn professional software development. Software engineers with a scientific background or interest in scientific computing. And the magical unicorns who can already do both, but want to improve their skills further.

## What do my people want?

I asked in a [LinkedIn post](https://www.linkedin.com/posts/matthijscox_i-sometimes-joke-i-should-write-a-book-activity-7046024343025258496-GVkD) what people wanted to read in a book about my work.

I summarize the requests as:

* Pure Julia deployment options.
    
* Deploying into old/reliable systems such as C++/Python.
    
* Compare development and deployment options to Python/Matlab/R deployment.
    
* Environment setup
    
* The best way to write the code
    
* (Large scale) code organization
    
* Development workflow (alone, or with multiple people and teams?)
    
* Examples of applications from start to finish, and in all stages of development.
    
* How to overcome the two-culture problem (scientists vs developers). I want to blog about this in more detail.
    

Plenty of material for a blog and book!

## How is this blog created?

If anyone is interested, or for my future reference, this blog is created and hosted on hashnode.com. I choose HashNode because it promises optimal tech writer tools, unlike other blogging sites like SubStack or Ghost.

Here's a quick experiment with a code block and Julia syntax highlighting:

```julia
function example(x::String)
    return x * "foo"
end
```

Everything works fine!

I bought the domain on namecheap.com and set up the DNS redirect to HashNode.

HashNode blogs come with an automatic email newsletter. I am curious how that will play out. Let me know if you (dis)like the newsletter.

*Don't forget to subscribe!* That way you will be informed about new blog posts. And you'll encourage me to write my book when I know I have a decent audience.
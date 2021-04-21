# How (some) good corporate engineering blogs are written

Created: Mar 29, 2020 8:56 AM
URL: https://danluu.com/corp-eng-blogs/

I've been comparing notes with people who run corporate engineering blogs and one thing that I think is curious is that it's pretty common for my personal blog to get more traffic than the entire corp eng blog for a company with a nine to ten figure valuation and it's not uncommon for my blog to get an order of magnitude more traffic.

I think this is odd because tech companies in that class often have hundreds to thousands of employees. They're overwhelmingly likely to be better equipped to write a compelling blog than I am and companies get a lot more value from having a compelling blog than I do.

With respect to the former, employees of the company will have done more interesting engineering work, have more fun stories, and have more in-depth knowledge than any one person who has a personal blog. On the latter, my blog helps me with job searching and it helps companies hire. But I only need one job, so more exposure, at best, gets me a slightly better job, whereas all but one tech company I've worked for is desperate to hire and loses candidates to other companies all the time. Moreover, I'm not really competing against other candidates when I interview (even if we interview for the same job, if the company likes more than one of us, it will usually just make more jobs). The high-order bit on this blog with respect to job searching is whether or not the process can take significant non-interview feedback or if [I'll fail the interview because they do a conventional interview](https://danluu.com/algorithms-interviews/) and the marginal value of an additional post is probably very low with respect to that. On the other hand, companies compete relatively directly when recruiting, so being more compelling relative to another company has value to them; replicating the playbook Cloudflare or Segment has used with their engineering "brands" would be a significant recruiting advantage. The playbook isn't secret: these companies broadcast their output to the world and are generally happy to talk about their blogging process.

Despite the seemingly obvious benefits of having a "good" corp eng blog, most corp eng blogs are full of stuff engineers don't want to read. Vague, high-level fluff about how amazing everything is, content marketing, handwave-y posts about the new hotness (today, that might be using deep learning for inappropriate applications; ten years ago, that might have been using "big data" for inappropriate applications), etc.

To try to understand what companies with good corporate engineering blog have in common, I interviewed folks at three different companies that have compelling corporate engineering blogs (Cloudflare, Heap, and Segment) as well as folks at three different companies that have lame corporate engineering blogs (which I'm not going to name).

At a high-level, the compelling engineering blogs had processes that shared the following properties:

- Easy approval process, not many approvals necessary
- Few or no non-engineering approvals required
- Implicit or explicit fast [SLO](https://en.wikipedia.org/wiki/Service-level_objective) on approvals
- Approval/editing process mainly makes posts more compelling to engineers
- Direct, high-level (co-founder, C-level, or VP-level) support for keeping blog process lightweight

The less compelling engineering blogs had processes that shared the following properties:

- Slow approval process
- Many approvals necessary
- Significant non-engineering approvals necessary
    - Non-engineering approvals suggest changes authors find frustrating
    - Back-and-forth can go on for months
- Approval/editing process mainly de-risks posts, removes references to specifics, makes posts vaguer and less interesting to engineers
- Effectively no high-level support for blogging
    - Leadership may agree that blogging is good in the abstract, but it's not a high enough priority to take concrete action
    - Reforming process to make blogging easier very difficult; previous efforts have failed
    - Changing process to reduce overhead requires all "stakeholders" to sign off (14 in one case)
        - Any single stakeholder can block
        - No single stakeholder can approve
    - Stakeholders wary of approving anything that reduces overhead
        - Approving involves taking on perceived risk (what if something bad happens) with no perceived benefit to them

One person at a company with a compelling blog noted that a downside of having only one approver and/or one primary approver is that if that person is busy, it can takes weeks to get posts approved. That's fair, that's a downside of having centralized approval. However, when we compare to the alternative processes, at one company, people noted that it's typical for approvals to take three to six months and tail cases can take a year.

While a few weeks can seem like a long time for someone used to a fast moving company, people at slower moving companies would be ecstatic to have an approval process that only takes twice that long.

Here are the processes, as described to me, for the three companies I interviewed (presented in `sha512sum` order, which is coincidentally ordered by increasing size of company, from a couple hundred employees to nearly one thousand employees):

### Heap

- Someone has an idea to write a post
- Writer (who is an engineer) is paired with a "buddy", who edits and then approves the post
    - Buddy is an engineer who has a track record of producing reasonable writing
    - This may take a few rounds, may change thrust of the post
- CTO reads and approves
    - Usually only minor feedback
    - May make suggestions like "a designer could make this graph look better"
- Publish post

The first editing phase used to involve posting a draft to a slack channel where "everyone" would comment on the post. This was an unpleasant experience since "everyone" would make comments and a lot of revision would be required. This process was designed to avoid getting "too much" feedback.

### Segment

- Someone has an idea to write a post
    - Often comes from: internal docs, external talk, shipped project, open source tooling (built by Segment)
- Writer (who is an engineer) writes a draft
    - May have a senior eng work with them to write the draft
- Until recently, no one really owned the feedback process
    - Calvin French-Owen (co-founder) and Rick (engineering manager) would usually give most feedback
    - Maybe also get feedback from manager and eng leadership
    - Typically, 3rd draft is considered finished
    - Now, have a full-time editor who owns editing posts
- Also socialize among eng team, get get feedback from 15-20 people
- PR and legal will take a look, lightweight approval

Some changes that have been made include

- At one point, when trying to establish an "engineering brand", making in-depth technical posts a top-level priority
- had a "blogging retreat", one week spent on writing a post
- added writing and speaking as explicit criteria to be rewarded in performance reviews and career ladders

Although there's legal and PR approval, Calvin noted "In general we try to keep it fairly lightweight. I see the bigger problem with blogging being a lack of posts or vague, high level content which isn't interesting rather than revealing too much."

### Cloudflare

- Someone has an idea to write a post
    - Internal blogging is part of the culture, some posts come from the internal blog
- John Graham-Cumming (CTO) reads every post, other folks will read and comment
    - John is approver for posts
- Matthew Prince (CEO) also generally supportive of blogging
- "Very quick" legal approval process, SLO of 1 hour
    - This process is so lightweight that one person didn't really think of it as an approval, another person didn't mention it at all (a third person did mention this step)
    - Comms generally not involved

One thing to note is that this only applies to technical blog posts. Product announcements have a heavier process because they're tied to sales material, press releases, etc.

One thing I find interesting is that Marek interviewed at Cloudflare because of their blog ([this 2013 blog post on their 4th generation servers caught his eye](https://blog.cloudflare.com/a-tour-inside-cloudflares-latest-generation-servers/)) and he's now both a key engineer for them as well as one of the main sources of compelling Cloudflare blog posts. At this point, the Cloudflare blog has generated at least a few more generations of folks who interviewed because they saw a blog post and now write compelling posts for the blog.

My opinion is that the natural state of a corp eng blog where people [get a bit of feedback](https://danluu.com/p95-skill/) is a pretty interesting blog. There's [a dearth of real, in-depth, technical writing](https://twitter.com/rakyll/status/1043952902157459456), which makes any half decent, honest, public writing about technical work interesting.

In order to have a boring blog, the corporation has to actively stop engineers from putting interesting content out there. Unfortunately, it appears that the natural state of large corporations tends towards risk aversion and blocking people from writing, just in case it causes a legal or PR or other problem. Individual contributors (ICs) might have the opinion that it's ridiculous to block engineers from writing low-risk technical posts while, simultaneously, C-level execs and VPs regularly make public comments that turn into PR disasters, but ICs in large companies don't have the authority or don't feel like they have the authority to do something just because it makes sense. And none of the fourteen stakeholders who'd have to sign off on approving a streamlined process care about streamlining the process since that would be good for the company in a way that doesn't really impact them, not when that would mean seemingly taking responsibility for the risk a streamlined process would add, however small. An exec or a senior VP willing to take a risk can take responsibility for the fallout and, if they're interested in engineering recruiting or morale, they may see a reason to do so.

One comment I've often heard from people at more bureaucratic companies is something like "every company our size is like this", but that's not true. Cloudflare, a $6B company approaching 1k employees is in the same size class as many other companies with a much more onerous blogging process. The corp eng blog situation seems similar to situation on giving real interview feedback. [interviewing.io claims that there's significant upside and very little downside to doing so](http://blog.interviewing.io/no-engineer-has-ever-sued-a-company-because-of-constructive-post-interview-feedback-so-why-dont-employers-do-it/). Some companies actually do give real feedback and the ones that do generally find that it gives them an easy advantage in recruiting with little downside, but the vast majority of companies don't do this and people at those companies will claim that it's impossible to do give feedback since you'll get sued or the company will be "cancelled" even though this generally doesn't happen to companies that give feedback and there are even entire industries where it's common to give interview feedback. It's easy to handwave that some risk exists and very few people have the authority to dismiss vague handwaving about risk when it's coming from multiple orgs.

Although this is a small sample size and it's dangerous to generalize too much from small samples, the idea that you need high-level support to blast through bureaucracy is consistent with what I've seen in other areas where most large companies have a hard time doing something easy that has obvious but diffuse value. While this post happens to be about blogging, I've heard stories that are the same shape on a wide variety of topics.

### Appendix: examples of compelling blog posts

Here are some blog posts from the blogs mentioned with a short comment on why I thought the post was compelling. This time, in reverse sha512 hash order.

### Cloudflare

- [https://blog.cloudflare.com/how-verizon-and-a-bgp-optimizer-knocked-large-parts-of-the-internet-offline-today/](https://blog.cloudflare.com/how-verizon-and-a-bgp-optimizer-knocked-large-parts-of-the-internet-offline-today/)
    - Talks about a real technical problem that impacted a lot of people, reasonably in depth
    - Timely, released only eight hours after the outage, when people were still really interested in hearing about what happened; most companies can't turn around a compelling blog post this quickly or can only do it on a special-case basis, Cloudflare is able to crank out timely posts semi-regularly
- [https://blog.cloudflare.com/the-relative-cost-of-bandwidth-around-the-world/](https://blog.cloudflare.com/the-relative-cost-of-bandwidth-around-the-world/)
    - Exploration of some data
- [https://blog.cloudflare.com/the-story-of-one-latency-spike/](https://blog.cloudflare.com/the-story-of-one-latency-spike/)
    - A debugging story
- [https://blog.cloudflare.com/when-bloom-filters-dont-bloom/](https://blog.cloudflare.com/when-bloom-filters-dont-bloom/)
    - A debugging story, this time in the context of developing a data structure

### Segment

- [https://segment.com/blog/when-aws-autoscale-doesn-t/](https://segment.com/blog/when-aws-autoscale-doesn-t/)
    - Concrete explanation of a gotcha in a widely used service
- [https://segment.com/blog/gotchas-from-two-years-of-node/](https://segment.com/blog/gotchas-from-two-years-of-node/)
    - Concrete example and explanation of a gotcha in a widely used tool
- [https://segment.com/blog/automating-our-infrastructure/](https://segment.com/blog/automating-our-infrastructure/)
    - Post with specific details about how a company operates; in theory, any company could write this, but few do

### Heap

- [https://heap.io/blog/engineering/basic-performance-analysis-saved-us-millions](https://heap.io/blog/engineering/basic-performance-analysis-saved-us-millions)
    - Talks about a real problem and solution
- [https://heap.io/blog/engineering/clocksource-aws-ec2-vdso](https://heap.io/blog/engineering/clocksource-aws-ec2-vdso)
    - Talks about a real problem and solution
    - In HN comments, engineers (malisper, kalmar) have technical responses with real reasons in them and not just the usual dissembling that you see in most cases
- [https://heap.io/blog/analysis/migrating-to-typescript](https://heap.io/blog/analysis/migrating-to-typescript)
    - Real talk about how the first attempt at driving a company-wide technical change failed

One thing to note is that these blogs all have different styles. Personally, I prefer the style of Cloudflare's blog, which has a higher proportion of "deep dive" technical posts, but different people will prefer different styles. There are a lot of styles that can work.

Thanks to Marek Majkowski, Kamal Marhubi, Calvin French-Owen, John Graham-Cunning, Michael Malis, Matthew Prince, Yuri Vishnevsky, Julia Evans, Wesley Aptekar-Cassels, Nathan Reed, an anonymous commenter, plus sources from the companies I didn't name for comments/corrections/discussion; none of the people explicitly mentioned in the acknowledgements were sources for information on the less compelling blogs
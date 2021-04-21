# Algorithms interviews: theory vs. practice

Created: May 23, 2020 11:28 AM
URL: https://danluu.com/algorithms-interviews/

When I ask people at trendy big tech companies why algorithms quizzes are mandatory, the most common answer I get is something like "we have so much scale, we can't afford to have someone accidentally write an `O(n^2)` algorithm and bring the site down"[1](https://danluu.com/algorithms-interviews/). One thing I find funny about this is, even though a decent fraction of the value I've provided for companies has been solving phone-screen level algorithms problems on the job, I can't pass algorithms interviews! When I say that, people often think I mean that I fail half my interviews or something. It's more than half.

When I wrote a draft blog post of my interview experiences, draft readers panned it as too boring and repetitive because I'd failed too many interviews. I should summarize my failures as a table because no one's going to want to read a 10k word blog post that's just a series of failures, they said (which is good advice; I'm working on a version with a table). I’ve done maybe 40-ish "real" software interviews and passed maybe one or two of them (arguably zero)[2](https://danluu.com/algorithms-interviews/).

Let's look at a few examples to make it clear what I mean by "phone-screen level algorithms problem", above.

At one big company I worked for, a team wrote a core library that implemented a resizable array for its own purposes. On each resize that overflowed the array's backing store, the implementation added a constant number of elements and then copied the old array to the newly allocated, slightly larger, array. This is a classic example of how not to [implement a resizable array](https://en.wikipedia.org/wiki/Dynamic_array) since it results in linear time resizing instead of [amortized constant time](https://en.wikipedia.org/wiki/Amortized_analysis) resizing. It's such a classic example that it's often used as the canonical example when demonstrating amortized analysis.

For people who aren't used to big tech company phone screens, typical phone screens that I've received are one of:

- an "easy" coding/algorithms question, maybe with a "very easy" warm-up question in front.
- a series of "very easy" coding/algorithms questions,
- a bunch of trivia (rare for generalist roles, but not uncommon for low-level or performance-related roles)

This array implementation problem is considered to be so easy that it falls into the "very easy" category and is either a warm-up for the "real" phone screen question or is bundled up with a bunch of similarly easy questions. And yet, this resizable array was responsible for roughly 1% of all GC pressure across all JVM code at the company (it was the second largest source of allocations across all code) as well as a significant fraction of CPU. Luckily, the resizable array implementation wasn't used as a generic resizable array and it was only instantiated by a semi-special-purpose wrapper, which is what allowed this to "only" be responsible for 1% of all GC pressure at the company. If asked as an interview question, it's overwhelmingly likely that most members of the team would've implemented this correctly in an interview. My fixing this made my employer more money annually than I've made in my life.

That was the second largest source of allocations, the number one largest source was converting a pair of `long` values to byte arrays in the same core library. It appears that this was done because someone wrote or copy pasted a hash function that took a byte array as input, then modified it to take two inputs by taking two byte arrays and operating on them in sequence, which left the hash function interface as `(byte[], byte[])`. In order to call this function on two longs, they used a handy `long` to `byte[]` conversion function in a widely used utility library. That function, in addition to allocating an `byte[]` and stuffing a `long` into it, also reverses the endianness of the long (the function appears to have been intended to convert `long` values to network byte order).

Unfortunately, switching to a more appropriate hash function would've been a major change, so my fix for this was to change the hash function interface to take a pair of longs instead of a pair of byte arrays and have the hash function do the endianness reversal instead of doing it as a separate step (since the hash function was already shuffling bytes around, this didn't create additional work). Removing these unnecessary allocations made my employer more money annually than I've made in my life.

Finding a constant factor speedup isn't technically an algorithms question, but it's also something you see in algorithms interviews. As a follow-up to an algorithms question, I commonly get asked "can you make this faster?" The answer is to these often involves doing a simple optimization that will result in a constant factor improvement.

A concrete example that I've been asked twice in interviews is: you're storing IDs as ints, but you already have some context in the question that lets you know that the IDs are densely packed, so you can store them as a bitfield instead. The difference between the bitfield interview question and the real-world superfluous array is that the real-world existing solution is so far afield from the expected answer that you probably wouldn’t be asked to find a constant factor speedup. More likely, you would've failed the interview at that point.

To pick an example from another company, the configuration for [BitFunnel](https://danluu.com/bitfunnel-sigir.pdf), a search index used in Bing, is another example of an interview-level algorithms question[3](https://danluu.com/algorithms-interviews/).

The full context necessary to describe the solution is a bit much for this blog post, but basically, there's a set of bloom filters that needs to be configured. One way to do this (which I'm told was being done) is to write a black-box optimization function that uses gradient descent to try to find an optimal solution. I'm told this always resulted in some strange properties and the output configuration always resulted in non-idealities which were worked around by making the backing bloom filters less dense, i.e. throwing more resources (and therefore money) at the problem.

To create a more optimized solution, you can observe that the fundamental operation in BitFunnel is equivalent to multiplying probabilities together, so, for any particular configuration, you can just multiply some probabilities together to determine how a configuration will perform. Since the configuration space isn't all that large, you can then put this inside a few for loops and iterate over the space of possible configurations and then pick out the best set of configurations. This isn't quite right because multiplying probabilities assumes a kind of independence that doesn't hold in reality, but that seems to work ok for the same reason that [naive Bayesian spam filtering](https://en.wikipedia.org/wiki/Naive_Bayes_spam_filtering) worked pretty well when it was introduced even though it incorrectly assumes the probability of any two words appearing in an email are independent. And if you want the full solution, you can work out the non-independent details, although that's probably beyond the scope of an interview.

Those are just three examples that came to mind, I run into this kind of thing all the time and could come up with tens of examples off the top of my head, perhaps more than a hundred if I sat down and tried to list every example I've worked on, certainly more than a hundred if I list examples I know of that someone else (or no one) has worked on. Both the examples in this post as well as the ones I haven’t included have these properties:

- The example could be phrased as an interview question
- If phrased as an interview question, you'd expect most (and probably) all people on the relevant team to get the right answer in the timeframe of an interview
- The cost savings from fixing the example is worth more annually than my lifetime earnings to date
- The example persisted for long enough that it's reasonable to assume that it wouldn't have been discovered otherwise

At the start of this post, we noted that people at big tech companies commonly claim that they have to do algorithms interviews since it's so costly to have inefficiencies at scale. My experience is that these examples are legion at every company I've worked for that does algorithms interviews. Trying to get people to solve algorithms problems on the job by asking algorithms questions in interviews doesn't work.

One reason is that even though big companies try to make sure that the people they hire can solve algorithms puzzles they also incentivize many or most developers to avoid deploying that kind of reasoning to make money.

Of the three solutions for the examples above, two are in production and one isn't. That's about my normal hit rate if I go to a random team with a diff and don't persistently follow up (as opposed to a team that I have reason to believe will be receptive, or a team that's asked for help, or if I keep pestering a team until the fix gets taken).

If you're very cynical, you could argue that it's surprising the success rate is that high. If I go to a random team, it's overwhelmingly likely that efficiency is in neither the team's objectives or their org's objectives. The company is likely to have spent a decent amount of effort incentivizing teams to hit their objectives -- what's the point of having objectives otherwise? Accepting my diff will require them to test, integrate, deploy the change and will create risk (because all deployments have non-zero risk). Basically, I'm asking teams to do some work and take on some risk to do something that's worthless to them. Despite incentives, people will usually take the diff, but they're not very likely to spend a lot of their own spare time trying to find efficiency improvements(and their normal work time will be spent on things that are aligned with the team's objectives)[4](https://danluu.com/algorithms-interviews/).

Hypothetically, let's say a company didn't try to ensure that its developers could pass algorithms quizzes but did incentivize developers to use relatively efficient algorithms. I don't think any of the three examples above could have survived, undiscovered, for years nor could they have remained unfixed. Some hypothetical developer working at a company where people profile their code would likely have looked at the hottest items in the profile for the most computationally intensive library at the company. The "trick" for both isn't any kind of algorithms wizardry, it's just looking at all, which is something incentives can fix. The third example is less inevitable since there isn't a standard tool that will tell you to look at the problem. It would also be easy to try to spin the result as some kind of wizardry -- that example formed the core part of a paper that won "best paper award" at the top conference in its field (IR), but the reality is that the "trick" was applying high school math, which means the real trick was having enough time to look at places where high school math might be applicable to find one.

I actually worked at a company that used the strategy of "don't ask algorithms questions in interviews, but do incentivize things that are globally good for the company". During my time there, I only found one single fix that nearly meets the criteria for the examples above (if the company had more scale, it would've met all of the criteria, but due to the company's size, increases in efficiency were worth much less than at big companies -- much more than I was making at the time, but the annual return was still less than my total lifetime earnings to date).

I think the main reason that I only found one near-example is that enough people viewed making the company better as their job, so straightforward high-value fixes tended not exist because systems were usually designed such that they didn't really have easy to spot improvements in the first place. In the rare instances where that wasn't the case, there were enough people who were trying to do the right thing for the company (instead of being forced into obeying local incentives that are quite different from what's globally beneficial to the company) that someone else was probably going to fix the issue before I ever ran into it.

The algorithms/coding part of that company's interview (initial screen plus onsite combined) was easier than the phone screen at major tech companies and we basically didn't do a system design interview.

For a while, we tried an algorithmic onsite interview question that was on the hard side but in the normal range of what you might see in a BigCo phone screen (but still easier than you'd expect to see at an onsite interview). We stopped asking the question because every new grad we interviewed failed the question (we didn't give experienced candidates that kind of question). We simply weren't prestigious enough to get candidates who can easily answer those questions, so it was impossible to hire using [the same trendy hiring filters that everybody else had](https://danluu.com/programmer-moneyball/). In contemporary discussions on interviews, what we did is often called "lowering the bar", but it's unclear to me why we should care how high of a bar someone can jump over when little (and in some cases none) of the job they're being hired to do involves jumping over bars. And, in the cases where you do want them to jump over bars, they're maybe 2" high and can easily be walked over.

When measured on actual productivity, that was the most productive company I've worked for. I believe the reasons for that are cultural and too complex to fully explore in this post, but I think it helped that we didn't filter out perfectly good candidates with algorithms quizzes and assumed people could pick that stuff up on the job if we had a culture of people generally doing the right thing instead of focusing on local objectives.

If other companies want people to solve interview-level algorithms problems on the job perhaps they could try incentivizing people to solve algorithms problems (when relevant). That could be done in addition to or even instead of filtering for people who can whiteboard algorithms problems.

### Appendix: how did we get here?

Way back in the day, interviews often involved "trivia" questions. Modern versions of these might look like the following:

- What's MSI? MESI? MOESI? MESIF? What's the advantage of MESIF over MOESI?
- What happens when you throw in a destructor? What if it's C++11? What if a sub-object's destructor that's being called by a top-level destructor throws, which other sub-object destructors will execute? What if you throw during stack unwinding? Under what circumstances would that not cause `std::terminate` to get called?

I heard about this practice back when I was in school and even saw it with some "old school" companies. This was back when Microsoft was the biggest game in town and people who wanted to copy a successful company were likely to copy Microsoft. The most widely read programming blogger around (Joel Spolsky) was telling people they need to adopt software practice X because Microsoft was doing it and they couldn't compete adopting the same practices. For example, in one of the most influential programming blog posts of the era, Joel Spolsky advocates for what he called the Joel test in part by saying that you have to do these things to keep up with companies like Microsoft:

> A score of 12 is perfect, 11 is tolerable, but 10 or lower and you’ve got serious problems. The truth is that most software organizations are running with a score of 2 or 3, and they need serious help, because companies like Microsoft run at 12 full-time.

At the time, popular lore was that Microsoft asked people questions like the following (and I was actually asked one of these brainteasers during my on interview with Microsoft around 2001, along with precisely zero algorithms or coding questions):

- how would you escape from a blender if you were half an inch tall?
- why are manhole covers round?
- a windowless room has 3 lights, each of which is controlled by a switch outside of the room. You are outside the room. You can only enter the room once. How can you determine which switch controls which lightbulb?

Since I was interviewing during the era when this change was happening, I got asked plenty of trivia questions as well plenty of brainteasers (including all of the above brainteasers). Some other questions that aren't technically brainteasers that were popular at the time were [Fermi problems](https://en.wikipedia.org/wiki/Fermi_problem). Another trend at the time was for behavioral interviews and a number of companies I interviewed with had 100% behavioral interviews with zero technical interviews.

Anyway, back then, people needed a rationalization for copying Microsoft-style interviews. When I asked people why they thought brainteasers or Fermi questions were good, the convenient rationalization people told me was usually that they tell you if a candidate can really think, unlike those silly trivia questions, which only tell if you people have memorized some trivia. What we really need to hire are candidates who can really think!

Looking back, people now realize that this wasn't effective and cargo culting Microsoft's every decision won't make you as successful as Microsoft because Microsoft's success came down to a few key things plus network effects, so copying how they interview can't possibly turn you into Microsoft. Instead, it's going to turn you into a company that interviews like Microsoft but isn't in a position to take advantage of the network effects that Microsoft was able to take advantage of.

For interviewees, the process with brainteasers was basically as it is now with algorithms questions, except that you'd review [How Would You Move Mount Fuji](https://www.amazon.com/gp/product/0316778494/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0316778494&linkCode=as2&tag=abroaview-20&linkId=bcc3345d4905164e75b79f2cd2a0f5fb) before interviews instead of [Cracking the Coding Interview](https://www.amazon.com/gp/product/0984782850/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0984782850&linkCode=as2&tag=abroaview-20&linkId=381539624231e0e40b66f2695c925536) to pick up a bunch of brainteaser knowledge that you'll never use on the job instead of algorithms knowledge you'll never use on the job.

Back then, interviewers would learn about questions specifically from interview prep books like "How Would You Move Mount Fuji?" and then ask them to candidates who learned the answers from books like "How Would You Move Mount Fuji?". When I talk to people who are ten years younger than me, they think this is ridiculous -- those questions obviously have nothing to do the job and being able to answer them well is much more strongly correlated with having done some interview prep than being competent at the job. Hillel Wayne has discussed how people come up with interview questions today (and I've also seen it firsthand at a few different companies) and, outside of groups that are testing for knowledge that's considered specialized, it doesn't seem all that different today.

At this point, we've gone through a few decades of programming interview fads, each one of which looks ridiculous in retrospect. Either we've finally found the real secret to interviewing effectively and have reasoned our way past whatever roadblocks were causing everybody in the past to use obviously bogus fad interview techniques, or we're in the middle of another fad, one which will seem equally ridiculous to people looking back a decade or two from now.

Without knowing anything about the effectiveness of interviews, at a meta level, since the way people get interview techniques is the same (crib the high-level technique from the most prestigious company around), I think it would be pretty surprising if this wasn't a fad. I would be less surprised to discover that current techniques were not a fad if people were doing or referring to empirical research or had independently discovered what works.

Inspired by [a comment by Wesley Aptekar-Cassels](https://twitter.com/danluu/status/925728187375595521), the last time I was looking for work, I asked some people how they checked the effectiveness of their interview process and how they tried to reduce bias in their process. The answers I got (grouped together when similar, in decreasing order of frequency were):

- Huh? We don't do that and/or why would we do that?
- We don't really know if our process is effective
- I/we just know that it works
- I/we aren't biased
- I/we would notice bias if it existed, which it doesn't
- Someone looked into it and/or did a study, but no one who tells me this can ever tell me anything concrete about how it was looked into or what the study's methodology was

### Appendix: training

As with most real world problems, when trying to figure out why seven, eight, or even nine figure per year interview-level algorithms bugs are lying around waiting to be fixed, there isn't a single "root cause" you can point to. Instead, there's a kind of [hedgehog defense](https://en.wikipedia.org/wiki/Hedgehog_defence) of misaligned incentives. Another part of this is that [training is woefully underappreciated](https://danluu.com/programmer-moneyball/).

We've discussed that, at all but one company I've worked for, there are incentive systems in place that cause developers to feel like they shouldn't spend time looking at efficiency gains even when a simple calculation shows that there are tens or hundreds of millions of dollars in waste that could easily be fixed. And then because this isn't incentivized, developers tend to not have experience doing this kind of thing, making it unfamiliar, which makes it feel harder than it is. So even when a day of work could return $1m/yr in savings or profit (quite common at large companies, in my experience), people don't realize that it's only a day of work and could be done with only a small compromise to velocity. One way to solve this latter problem is with training, but that's even harder to get credit for than efficiency gains that aren't in your objectives!

Just for example, I once wrote a moderate length tutorial (4500 words, shorter than this post by word count, though probably longer if you add images) on how to find various inefficiences (how to use an allocation or CPU time profiler, how to do service-specific GC tuning for the GCs we use, how to use some tooling I built that will automatically find inefficiencies in your JVM or container configs, etc., basically things that are simple and often high impact that it's easy to write a runbook for; if you're at Twitter, you can read this at [http://go/easy-perf](http://go/easy-perf)). I've had a couple people who would've previously come to me for help with an issue tell me that they were able to debug and fix an issue on their own and, secondhand, I heard that a couple other people who I don't know were able to go off and increase the efficiency of their service. I'd be surprised if I’ve heard about even 10% of cases where this tutorial helped someone, so I'd guess that this has helped tens of engineers, and possibly quite a few more.

If I'd spent a week doing "real" work instead of writing a tutorial, I'd have something concrete, with quantifiable value, that I could easily put into a promo packet or performance review. Instead, I have this nebulous thing that, at best, counts as a bit of "extra credit". I'm not complaining about this in particular -- this is exactly the outcome I expected. But, on average, companies get what they incentivize. If they expect training to come from developers (as opposed to hiring people to produce training materials, which tends to be very poorly funded compared to engineering) but don't value it as much as they value dev work, then there's going to be a shortage of training.

I believe you can also see training under-incentivized in public educational materials due to the relative difficulty of monetizing education and training. If you want to monetize explaining things, there are a few techniques that seem to work very well. If it's something that's directly obviously valuable, selling a video course that's priced "very high" (hundreds or thousands of dollars for a short course) seems to work. Doing corporate training, where companies fly you in to talk to a room of 30 people and you charge $3k per head also works pretty well.

If you want to reach (and potentially help) a lot of people, putting text on the internet and giving it away works pretty well, but monetization for that works poorly. For technical topics, I'm not sure the non-ad-blocking audience is really large enough to monetize via ads (as opposed to a pay wall).

Just for example, Julia Evans can support herself from her [zine income](https://jvns.ca/blog/2019/10/01/zine-revenue-2019/), which she's said has brought in roughly $100k/yr for the past two years. Someone who does very well in corporate training can pull that in with a one or two day training course and, from what I've heard of corporate speaking rates, some highly paid tech speakers can pull that in with two engagements. Those are significantly above average rates, especially for speaking engagements, but since we're comparing to Julia Evans, I don't think it's unfair to use an above average rate.

### Appendix: misaligned incentive hedgehog defense, part 3

Of the three examples above, I found one on a team where it was clearly worth zero to me to do anything that was actually valuble to the company and the other two on a team where it valuable to me to do things that were good for the company, regardless of what they were. In my experience, that's very unusual for a team at a big company, but even on that team, incentive alignment was still quite poor. At one point, after getting a promotion and a raise, I computed the ratio of the amount of money my changes made the company vs. my raise and found that my raise was 0.03% of the money that I made the company, only counting easily quantifiable and totally indisputable impact to the bottom line. The vast majority of my work was related to tooling that had a difficult to quantify value that I suspect was actually larger than the value of the quantifiable impact, so I probably recieved well under 0.01% of the marginal value I was producing. And that's really an overestimate of how much I was incentivized I was to do the work -- at the margin, I strongly suspect that anything I did was worth zero to me. After the first $10m/yr or maybe $20m/yr, there's basically no difference in terms of performance reviews, promotions, raises, etc. Because there was no upside to doing work and there's some downside (could get into a political fight, could bring the site down, etc.), the marginal return to me of doing more than "enough" work was probably negative.

Some companies will give very large out-of-band bonuses to people regularly, but that work wasn't for a company that does a lot of that, so there's nothing the company could do to indicate that it valued additional work once someone did "enough" work to get the best possible rating on a performance review. From a [mechanism design](https://en.wikipedia.org/wiki/Mechanism_design) point of view, the company was basically asking employees to stop working once they did "enough" work for the year.

So even on this team, which was relatively well aligned with the company's success compared to most teams, the company's compensation system imposed a low ceiling on how well the team could be aligned.

This also happened in another way. As is common at a lot of companies, managers were given a team-wide budget for raises that was mainly a function of headcount, that was then doled out to team members in a zero-sum way. Unfortunately for each team member (at least in terms of compensation), the team pretty much only had productive engineers, meaning that no one was going to do particularly well in the zero-sum raise game. The team had very low turnover because people like working with good co-workers, but the company was applying one the biggest levers it has, compensation, to try to get people to leave the team and join less effective teams.

Because this is such a common setup, I've heard of managers at multiple companies who try to retain people who are harmless but ineffective to try to work around this problem. If you were to ask someone, abstractly, if the company wants to hire and retain people who are ineffective, I suspect they'd tell you no. But insofar as a company can be said to want anything, it wants what it incentivizes.

- [Zvi Mowshowitz's on Moral Mazes](https://thezvi.wordpress.com/2019/05/30/quotes-from-moral-mazes/), a book about how corporations have systemic issues that cause misaligned incentives at every level
- ["randomsong" on on how it's possible to teach almost anybody to program](https://www.jefftk.com/p/programmers-should-plan-for-lower-pay#lw-ftocr9cDfYtyQj9x2). thematically related, the idea being that programming isn't as hard as a lot of programmers would like to believe
- [Tanya Reilly on how "glue work" is poorly incentivized](https://noidea.dog/glue), training being poorly incentivized is arguably a special case of this
- [An anonymous HN commenter on doing almost no work at Google, they say about 10% capacity, for six years and getting promoted](https://news.ycombinator.com/item?id=21961560)

Thanks to Leah Hanson, Heath Borders, Lifan Zeng, Justin Findlay, Kevin Burke, @chordowl, Peter Alexander, Niels Olson, Kris Shamloo, Chip Thien, and Solomon Boulos for comments/corrections/discussion

1. For one thing, most companies that copy the Google interview don't have that much scale. But even for companies that do, most people don't have jobs where they're designing high-scale algorithms (maybe they did at Google circa 2003, but from what I've seen at three different big tech companies, most people's jobs are pretty light on algorithms work). [[return]](https://danluu.com/algorithms-interviews/)
2. Real is in quotes because I've passed a number of interviews for reasons outside of the interview process. Maybe I had a very strong internal recommendation that could override my interview performance, maybe someone read my blog and assumed that I can do reasonable work based on my writing, maybe someone got a backchannel reference from a former co-worker of mine, or maybe someone read some of my open source code and judged me on that instead of a whiteboard coding question (and as far as I know, that last one has only happened once or twice). I'll usually ask why I got a job offer in cases where I pretty clearly failed the technical interview, so I have a collection of these reasons from folks.   [[return]](https://danluu.com/algorithms-interviews/)

    The reason it's arguably zero is that the only software interview where I inarguably got a "real" interview and was coming in cold was at Google, but that only happened because the interviewers that were assigned interviewed me for the wrong ladder -- I was interviewing for a hardware position, but I was being interviewed by software folks, so I got what was basically a standard software interview except that one interviewer asked me some questions about state machine and cache coherence (or something like that). After they realized that they'd interviewed me for the wrong ladder, I had a follow-up phone interview from a hardware engineer to make sure I wasn't totally faking having worked at a hardware startup from 2005 to 2013. It's possible that I failed the software part of the interview and was basically hired on the strength of the follow-up phone screen.

    Note that this refers only to software -- I'm actually pretty good at hardware interviews. At this point, I'm pretty out of practice at hardware and would probably need a fair amount of time to ramp up on an actual hardware job, but the interviews are a piece of cake for me. One person who knows me pretty well thinks this is because I "talk like a hardware engineer" and both say things that make hardware folks think I'm legit as well as say things that sound incredibly stupid to most programmers in a way that's more abbout shibboleths than actual knowledge or skills.

3. This one is a bit harder than you'd expect to get in a phone screen, but it wouldn't be out of line in an onsite interview (although a friend of mine once got [a Google Code Jam World Finals question](https://code.google.com/codejam/contest/2437491/dashboard#s=p2) in a phone interview with Google, so you might get something this hard or harder, depending on who you draw as an interviewer).  [[return]](https://danluu.com/algorithms-interviews/)

    BTW, if you're wondering what my friend did when they got that question, it turns out they actually knew the answer because they'd seen and attempted the problem during Google Code Jam. They didn't get the right answer at the time, but they figured it out later just for fun. However, my friend didn't think it was reasonable to give that as a phone screen questions and asked the interviewer for another question. The interviewer refused, so my friend failed the phone screen. At the time, I doubt there were more than a few hundred people in the world who would've gotten the right answer to the question in a phone screen and almost all of them probably would've realized that it was an absurd phone screen question. After failing the interview, my friend ended up looking for work for almost six months before passing an interview for a startup where he ended up building a number of core systems (in terms of both business impact and engineering difficulty). My friend is still there after the mid 10-figure IPO -- the company understands how hard it would be to replace this person and treats them very well. None of the other companies that interviewed this person even wanted to hire them at all and they actually had a hard time getting a job.

4. Outside of egregious architectural issues that will simply cause a service to fall over, the most common way I see teams fix efficiency issues is to ask for more capacity. Some companies try to counterbalance this in some way (e.g., I've heard that at FB, a lot of the teams that work on efficiency improvements report into the capacity org, which gives them the ability to block capacity requests if they observe that a team has extreme inefficiencies that they refuse to fix), but I haven't personally worked in an environment where there's an effective system fix to this. Google had a system that was intended to address this problem that, among other things, involved making headcount fungible with compute resources, but I've heard that was rolled back in favor of a more traditional system for [reasons](https://en.wikipedia.org/wiki/Incentive_compatibility). [[return]](https://danluu.com/algorithms-interviews/)
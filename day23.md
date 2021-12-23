# Day 23: Housekeeping

Today's diary entry would be a bit meta. There is last two days of my self-imposed deadline left, and I decided to spend today's time chunk on cleaning up the existing diary entries. ...while documenting it with one more diary entry.

So, what I am doing to prepare it all to be [at least somewhat] readable:

1\. Push everything to private (for now) GitHub repo. Fortunately, GitHub's Markdown renderer understands my "naive" `[](image.png)` images (done by inserting it from the clipboard with a custom Sublime plugin, BTW!), so everything renders as it should be.

2\. ...then, just start looking at them from day 1, and adjust formatting, introduce context etc. _Technical note: before today, I strived to maintain a clean commit log (sometimes cheating with the next day rereading `--fixup` commits), so day's work can be seen in a singular commit. Today, I'll just do one huge "edit" commit for all days texts, to make it easier for me, instead of playing with day-by-day fixups._

Ugh, now I notice I had a timestamp at the bottom of day 1, as a way of making it more diary-alike... But then I apparently forgot the whole idea :(

I decided to add a "contextual" header to all days, looking like this (for Day 2):

> * **Commit:** [d2ac581](https://github.com/zverok/grok-shan-shui/commit/d2ac5814aff6beca016df1e67ac38ce55cebec91)
> * **Function:** [`Tree.tree01`](https://github.com/zverok/grok-shan-shui/blob/main/original.html#L744) (generate one tree; link is to the original implementation)
> * **Days about this function:** [02](day02.md)—[03](day03.md)-[04](day04.md)—[05](day05.md)

Should be easier to go back and fro, hopefully!

Also, I am replacing links to the original author's repo (which I used sometimes in text in first days, and will use in day headers) with links to `original.html` in my own repo—not out of disrespect (I'll specify what what and who's the author very clearly), just for links not to rot eventually.

Nothing to report about the tedious today's reviewing work, actually. It seems the diary is quite comprehensive by now, if not complete (all in all, I've covered like 30% of code paths probably?), and _hopefully_ will give me an ability to write an incomplete, but "giving the idea" explanation tomorrow, and that would be that!

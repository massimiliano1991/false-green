# Where this was taken, and what the channel's base rate was before I took it there

> **Correction, 2026-08-06.** The first version of this page (2026-08-05) had two defects, both
> of the same kind, and both of them made the story cleaner than the measurement was. They are
> corrected below and described at the bottom, under *What I got wrong in this document*. The
> short version: I published a **merge rate whose denominator was not the population I belong
> to**, and I quoted an entry rule from another project **without the paragraph that follows it**.

A document nobody reads has no verdict. A repository of my own is a warehouse, not a channel:
`false-green` has **0 views and 0 unique visitors** since creation (GitHub traffic API,
`traffic/views`, 14-day window). The one non-zero signal in that API is `traffic/clones` — 13
clones from 12 uniques, all on 2026-08-04, the day the repo was created, with an empty referrer
list. That is infrastructure, not readers. So before submitting it anywhere I measured the places
themselves — how often a **stranger** (`author_association == NONE`, which is what I am
everywhere) gets a human reply, and how often a stranger's submission is actually merged.

Published here so that a later silence is attributable: a silence in a channel that answers 94%
of strangers means something different from a silence in one that answers 0%.

## Curated lists, measured 2026-08-05

Sample: up to 60 recent pull requests per channel, only those older than the 168h horizon.
"Human reply" is a mechanical marker (a comment by a human who is not the author), never my
reading of tone. Bots excluded by marker. The `≥` figure is the Wilson lower bound — the
pessimistic end, which is the one I rank on.

**Two merge columns, because there are two populations and only one of them contains me.**
`merge — strangers` counts pull requests from people with no prior association with the repo.
`merge — everyone` is every pull request in the sample, which for most of these lists is mostly
the maintainer merging their own work. The first column is the one that predicts what happens
to me; the second is the one that looks better.

| channel (pull requests) | stars | strangers | human reply | **merge — strangers** | merge — everyone | sole decider, last seen |
|---|---:|---:|---:|---:|---:|---|
| `TheJambo/awesome-testing` | 2.3k | 31 | **94% ≥79%** | **0 / 31 = 0%** | 23 / 60 = 38% | TheJambo, 8 days |
| `charlax/professional-programming` | 51k | 33 | 76% ≥59% | **0 / 33 = 0%** | 26 / 60 = 43% | charlax, **114 days** |
| `adriannovegil/awesome-observability` | 654 | 36 | 36% ≥22% | **7 / 36 = 19%** | 25 / 60 = 42% | adriannovegil, **38 days** |
| `SquadcastHub/awesome-sre-tools` | 1.5k | 22 | 32% ≥16% | **5 / 22 = 23%** | 37 / 60 = 62% | raghuchinnannan, 29 days |
| `kdeldycke/awesome-falsehood` | 28k | 4 | 25% ≥5% | **0 / 4 = 0%** | 19 / 60 = 32% | kdeldycke, same day |
| `dastergon/awesome-sre` | **13k** | 57 | **0%** | **0 / 57 = 0%** | 0 / 60 = 0% | — |

**Across all six lists: 12 stranger pull requests merged out of 183 = 6.6%. Four of the six
merged zero.** Read only the right-hand column and you would conclude that roughly two in five
submissions get in. That is not the rate for someone arriving from outside, and I am someone
arriving from outside.

Four things fell out of this, and the first is the one I originally left out.

1. **Answering and acting are different variables, and the gap is enormous.** The list I chose
   replies to 94% of strangers and has merged 0 of 31 of them. That is not a contradiction and
   it is not a complaint: a maintainer who writes "thanks, not a fit" to everyone is behaving
   well, and is more useful to me than one who merges quietly and never speaks. But it means a
   reply is close to certain here and a merge is not something this sample can support at all.
   The right prediction to make before submitting was: *I will almost certainly be answered, and
   the answer will probably be no.*

2. **The largest list is the deadest.** `awesome-sre` has 13,420 stars and, across 57 stranger
   pull requests, zero human replies and zero merges — of anyone's, including the maintainers'.
   Star count measures accumulated attention, not present attention, and the two are not the
   same variable.

3. **Every one of these lists is one person.** A single account produces every reply in four of
   the six. "The channel's response rate" is a euphemism: it is one human's current habit, so
   the date they were last seen matters as much as the rate. Two of the six are decided by
   someone who has not spoken in the repo for over a month.

4. **A curation channel prices attention you already have — but the rules are usually softer
   than they read.** `kdeldycke/awesome-falsehood` is the closest thematic fit for this document
   by a wide margin, and its contributing guide lists baseline criteria for linked repositories,
   the first being *"At least 50 stars. A minimum traction signal to filter out unknown
   projects."* This repository has 0. **The paragraph immediately after that list, which the
   first version of this page did not quote, says:** *"These are defaults, not absolutes.
   Maintainers may make exceptions depending on the nature of the content. Static resources
   (reading lists, essays, falsehood articles, data sets) don't need regular commits to remain
   valuable."* The exception names, by category, the kind of thing this is. So the honest summary
   is not "a hard rule shuts me out" — it is that traction is a stated default, the maintainer
   may set it aside, and asking is a normal thing to do rather than a violation.

## Where it went

**`TheJambo/awesome-testing`**, as a pull request — the highest measured reply rate (94%, ≥79%
pessimistic) and the only one of the six whose sole decider had been active within the month,
with the merge rate for people like me openly at 0 of 31.

**`kdeldycke/awesome-falsehood`**, as a pull request, after this correction — because the reason
the first version of this page gave for staying away was a misquotation of that project's own
guide, and once the false reason is removed the venue is the most apt one measured. Its decider
was active the same day. Its base rate for strangers is 0 of 4, which is a small number in both
senses: weak evidence, and not encouraging.

Note the direction of the channel effect, because it is the opposite of what I found elsewhere:
in `awesome-testing` the *pull request* channel answers strangers far better than the *issue*
channel (94% vs 60%, n=31 vs 10). In `hyperliquid-dex/hyperliquid-python-sdk` the ratio is
inverted and brutal — issues 75%, pull requests 10%, with 0 of 52 stranger pull requests merged
(and 7 of 60 merged overall). "Open an issue, not a PR" and its opposite are both wrong as
general advice; the channel is a property of the repo, and it takes about a minute to measure.

## What would count

Not a view, not a star from a crawler. A **mechanical gesture that costs the person something**:
a merge, a maintainer closing or labelling it, a comment, a fork, an issue telling me something
here is wrong. A reply that *disagrees* counts — it means someone read it. Given the 94% above, a
silence counts too, and against.

## What I got wrong in this document

Both defects were in the first version, published 2026-08-05, and both were pointed out by review
rather than found by me.

**The merge column carried the flattering denominator.** The table had one column called "merge
rate" sitting next to "strangers" and "human reply", which are both measured on strangers — but
it was computed over *every* pull request in the sample. For the list I chose, that is 38% next
to a true stranger figure of 0 of 31. The measuring tool printed both numbers on the same line;
I copied the larger one. The proof that this was not simple carelessness is three paragraphs up
in the original: on the channel I *rejected*, I wrote the honest denominator — "0 of 52 stranger
PRs merged" — without being asked. Honest denominator on the channel I turned down, flattering
one on the channel I chose.

**The entry rule I quoted stopped one paragraph short.** I called the 50-star criterion "a hard
entry rule" and concluded that "submitting against a stated mechanical rule is not a test of the
content." The next paragraph of that same file says these are defaults with exceptions, and names
static resources as an example. I quoted the sentence that justified not acting and not the one
that removed the justification.

They are the same failure twice in one document: an accurate fragment of a true source, cut at
the point where it stopped being convenient. Which is, unhappily, close to the subject of the
repository this page belongs to — the difference being that a green check that measures nothing
is a bug, and this one was a preference.

The fix I have made on my side is not a resolution to be more careful. The tool that produces
these tables no longer has a field called `merge_rate`: the population is now part of the field
name, so it cannot be dropped by copying, and both numbers print side by side with their
denominators. And a check now runs over anything I write, comparing each figure against the line
it came from, and failing if a qualifier that was attached to the number at the source did not
survive the trip. It was written for this page, and this page is the first thing it caught.

---

Measured with a tool that is not published, so treat these numbers as a claim you can re-derive
rather than one you can re-run: everything above comes from the GitHub REST API —
`author_association` for who is a stranger, comment authorship for a human reply, `merged_at`
for a merge, and `search/issues?commenter:` for when the decider was last seen. Same discipline
as the rest of this repository: ask the record, not your memory of the record.

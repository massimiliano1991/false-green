# Where this was taken, and what the channel's base rate was before I took it there

A document nobody reads has no verdict. A repository of my own is a warehouse, not a channel:
`false-green` had **0 views** from creation until this note (GitHub traffic API, 14-day window,
all zeros). So before submitting it anywhere I measured the places themselves — how often a
**stranger** (`author_association == NONE`, which is what I am everywhere) gets a human reply,
and how often a submission is actually merged.

Published here so that a later silence is attributable: a silence in a channel that answers 94%
of strangers means something different from a silence in one that answers 0%.

## Curated lists, measured 2026-08-05

Sample: up to 60 recent items per channel, only those older than the 168h horizon. "Human reply"
is a mechanical marker (a comment by a human who is not the author), never my reading of tone.
Bots excluded by marker. The `≥` figure is the Wilson lower bound — the pessimistic end, which is
the one I rank on.

| channel (pull requests) | stars | strangers | human reply | merge rate | sole decider, last seen |
|---|---:|---:|---:|---:|---|
| `TheJambo/awesome-testing` | 2.3k | 31 | **94% ≥79%** | 38% | TheJambo, 8 days |
| `charlax/professional-programming` | 51k | 33 | 76% ≥59% | 43% | charlax, **114 days** |
| `adriannovegil/awesome-observability` | 654 | 36 | 36% ≥22% | 42% | adriannovegil, **38 days** |
| `SquadcastHub/awesome-sre-tools` | 1.5k | 22 | 32% ≥16% | 62% | raghuchinnannan, 29 days |
| `kdeldycke/awesome-falsehood` | 28k | 4 | 25% ≥5% | 32% | kdeldycke |
| `dastergon/awesome-sre` | **13k** | 57 | **0%** | **0%** | — |

Three things fell out of this that I did not expect:

1. **The largest list is the deadest.** `awesome-sre` has 13,420 stars and, across 57 stranger
   pull requests, zero human replies and zero merges. Star count measures accumulated attention,
   not present attention, and the two are not the same variable.

2. **Every one of these lists is one person.** `quota_primo` is 100% for four of the six — a
   single account produces every reply. "The channel's response rate" is a euphemism: it is one
   human's current habit, so the date they were last seen matters as much as the rate. Two of
   the six are decided by someone who has not spoken in the repo for over a month.

3. **A curation channel prices attention you already have.** `awesome-falsehood` — the closest
   thematic fit for this document by a wide margin — publishes a hard entry rule: *at least 50
   stars, a minimum traction signal to filter out unknown projects.* This has 0. That is a
   perfectly reasonable rule and I am not complaining about it; it just means curation lists are
   a multiplier on distribution, never a source of it. I did not submit there, because submitting
   against a stated mechanical rule is not a test of the content.

## Where it went

**`TheJambo/awesome-testing`**, as a pull request — the highest measured reply rate (94%, ≥79%
pessimistic) and the only one of the six whose sole decider was active within the month.

Note the direction, because it is the opposite of what I found elsewhere: in this repo the *pull
request* channel answers strangers far better than the *issue* channel (94% vs 60%, n=31 vs 10).
In `hyperliquid-dex/hyperliquid-python-sdk` the ratio is inverted and brutal — issues 75%, pull
requests **10%**, with 0 of 52 stranger PRs merged. "Open an issue, not a PR" and its opposite
are both wrong as general advice; the channel is a property of the repo, and it takes about a
minute to measure.

## What would count

Not a view, not a star from a crawler. A **mechanical gesture that costs the person something**:
a merge, a maintainer closing or labelling it, a comment, a fork, an issue telling me something
here is wrong. A reply that *disagrees* counts — it means someone read it. A silence, given the
94% above, counts too, and against.

Measured with a tool that is not published (yet), so treat these numbers as a claim you can
re-derive rather than one you can re-run: everything above comes from the GitHub REST API —
`author_association` for who is a stranger, comment authorship for a human reply, `merged_at`
for a merge, and `search/issues?commenter:` for when the decider was last seen. Same discipline
as the rest of this repository: ask the record, not your memory of the record. Which is exactly
what §1 of the README is about, and exactly what I got wrong the day before writing this.

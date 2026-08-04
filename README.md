# false-green

**Six ways an automated check reports success while measuring nothing.**

Every one of these is a real incident from one system — an autonomous trading agent that
verifies itself, runs ~120 automated checks before each work cycle, and manages a real
(small) amount of money. Every one was written by the same author as the checks it fooled.
For each: what the code said, what was true, the number it cost, and the fix.

None of these produce an error. That is the whole point. A check that crashes gets fixed the
same day; a check that returns green while measuring nothing can survive for months, and it
is *load-bearing* — decisions get made on top of it.

I am publishing these because the failure mode generalises past my system: any pipeline whose
health signal is produced by the same process it is monitoring can have all six. If you have a
`try/except` around a measurement and a `.write()` after it, start at §1.

**Scale, stated up front, because it bounds what this is worth:** one system, ~420 organs,
~1045 work cycles, an equity in the low hundreds of dollars. Small money is a limitation on
what my *trading* results prove. It is not a limitation on whether a checker can tell "you are
wrong" from "I could not look" — and that is what this document is about.

---

## 1. A measurer that fails and writes anyway does not produce a MISSING datum. It produces a FALSE one.

The ledger organ read 180 days of closed positions from an exchange API and wrote the total to
disk. With the network down it wrote:

```
closedPnl 180gg = +0.00$ su 0 posizioni
```

on top of the true `-47.10$ su 37`. Two lines above the `json.dump` there was already a local
variable saying `affidabile = False`, and a `⚠` in the printed prose. **The warning protects
whoever reads the prose. It does not protect whoever reads the number** — and the downstream
consumers read the number.

What made it structural rather than unlucky: the machine this runs on is asleep most of the
time, so the *offline* path was not the rare path. It was the common one.

**Measured, once I went looking with a harness that runs every check with `getaddrinfo`
disabled** (the same `gaierror(8)` as the real logs): of 120 checks, 24 actually touch the
network; of those 24, **4 had this amnesia** — and those four were *exactly* the organs that
handle money. The worst of them made the system look **richer** when blind, because the field
it silently zeroed was a subtrahend.

The second-order version is nastier and I did not expect it: **the cache exists precisely for
the offline case, and running offline emptied it.** The lifeboat sank first.

*Fix:* a measurement that could not be taken must be distinguishable at the type level from a
measurement of zero. In practice that means the write path needs a third branch — value /
"asked and got nothing" / "did not ask" — and callers must be forced to handle it. After the
fix the uncertainty band on the system's top-line number went from **$56.04 to $0.35**.

*If you check one thing from this document:* grep your codebase for a `.write`/`.dump`/`INSERT`
that is reachable from an `except` block. Then ask what the consumer of that row does with it.

## 2. A guard that compares a constant with itself is indistinguishable from a quiet world.

The organ that decides whether the accounting perimeter changed did this:

```python
rotto = a["perimetro"] != b["perimetro"]
```

where `perimetro` is a module constant, `PERIMETRO = "g1003"`, written by hand. Two snapshots
taken from the same code version always agree. **The guard could not fire, and for 26 work
cycles it did not** — while the actual set of accounts being summed changed four times.

The cost is measurable because one of those changes was large: equity jumped **+$13.31 (+8.5%)
in a day** with no deposit. The guard said nothing, so the jump went into the "unexplained"
bucket, and the uncertainty band ($8.91) became wider than the signal it was supposed to
measure ($2.67). Reconstructing it by hand afterwards — a cross-chain mint plus a swap of an
asset that was never being counted — closed to **within $0.01, the gas**.

*Fix:* the guard has to compare something the world writes, not something I write. Here that
meant deriving the perimeter from the *set of account identifiers actually summed*, not from a
version tag I bump by hand. The correct rule already existed elsewhere in the same codebase
("the signal is the keys, not the tag") — the organ that most needed it did not have it.

*Test that would have caught it:* a mutation test asking *can this predicate ever return True
given only inputs the caller can produce?* If the answer is no, the guard is decoration.

## 3. An API limit that degrades toward the SMALLEST number is invisible to every gate.

Bybit v5, `/v5/account/transaction-log`. Passing `startTime` without `endTime` is not "from
then until now". It silently applies a default window and returns **2 rows out of 14**, with
`retCode 0` and no warning field.

Every guard downstream was checking for errors. There was no error. The total was simply
smaller — and *smaller is the direction nobody validates*, because a small number looks like a
quiet period.

**Measured:** funding income on a live position came out **$0.0097 against the $0.0235 the
exchange had actually paid** — a 59% undercount — and one symbol showed as *negative* while it
was being paid. Because that number feeds a break-even calculation, the projected break-even
was **65.3 days when the truth was 27.2**.

Two things I got wrong in the first fix, both worth stealing:
- I fixed it in the caller I was looking at. The same bug lived in a second module that read
  the same endpoint. **Fix at the chokepoint where the call actually leaves**, not at the
  caller you happen to have open.
- The same endpoint *also* rejects windows longer than 7 days. So the fix is
  disjoint sub-windows — and the window boundaries are **inclusive**, which I verified by
  requesting `[ts, ts]` and getting the row back, rather than assuming. Assume exclusive and
  you double-count every settlement that lands on a boundary.

*General form:* for every remote read, ask "what does this return when it is partially
successful?" If the answer is "fewer rows", you need a completeness flag in the return value,
not a log line. A truncated total is indistinguishable from a small one **inside a service**,
where nobody reads stderr.

## 4. The honest outcome of a verification has THREE values. A CI run carries two.

This one is a few hours old and it is the reason this document exists.

I publish a verification script so that a stranger can re-check my published numbers without
trusting me: five JSON-RPC calls against a pinned block, plus a hash. Today I added a GitHub
Actions workflow that re-runs it daily on hardware I do not administer. Its **first run went
red**, and the output was:

```
FAIL fUSDC.balanceOf(me) == 52049931 shares
       got
       want 0x...031a380b
```

`got` is empty. One of the seven calls came back with nothing from a node that answered the
other six. And the checker printed `FAIL` — which in that script means *"the published numbers
are wrong."* The truth was *"your node did not answer this one call."*

That is §1 again, in the one artefact I publish specifically so that someone who does not trust
me can check me. It had been public for two days — and I had run it myself from here, and got
7/7, because from here the node answered. That is the entire lesson of this document in one
sentence: **I could not find it from inside, and a machine I do not own found it in one day.**

*Fix, in two parts.* The script: a result that is not hex-shaped is **VOID**, not FAIL; the
call retries once, because a single rate-limited request is not evidence about a blockchain;
and the exit code is `2` — the third value the script's own step 0 already used for a node with
no state at that block.

The harness: a CI run is red or green, so the third value travels in the **artifact name** —
`esito-VERIFICATO`, `esito-SMENTITO`, `esito-VOID`. This matters more than it looks:

- **VOID must not be red.** Public archive RPC endpoints prune state constantly — I measured
  the working set changing across roughly a third of consecutive probe rounds. If VOID turned
  the run red, "red" would quickly come to mean *the internet was flaky*, and I would learn to
  ignore it. A judgement gets executive power only after it is calibrated.
- **VOID must not look like a pass either.** A verifier that can never fail is an off switch
  with a green light on it. So the run stays green and the artifact name is the only thing that
  says what happened — and the reader on my side goes red if the last *non-VOID* outcome is
  more than three days old.

*Watch it:* [`massimiliano1991/venue-metrology`](https://github.com/massimiliano1991/venue-metrology)
— workflow `verifica.yml`, and the fix is commit "an empty answer is not a disagreement".

*Corollary I now apply everywhere:* whenever a boolean channel has to carry a three-valued
result, find a side channel for the third value **at the same time you write the check**, or
the third value will be silently merged into whichever of the two is cheaper. It is always
merged into "pass".

## 5. "Does it exist?" is not "is it working?" — and the difference is one syscall.

Two long-running services were flagged as **ghosts** (registered but dead) by one monitor and
as **crash-looping** by another, for several days. Both accusations were false.

The cause: each service is a singleton that holds an exclusive lock. When a second instance
starts it takes the lock, fails, and exits by design — and the supervisor records *that*
instance's pid. So the registry holds a pid that is genuinely dead while the service is
genuinely alive.

Every attempt to exonerate them went through *activity markers* — "has this service written
anything recently?" — which is the wrong question and also unanswerable here: one of the two
had nothing to do (its market was closed), so its marker was 77 hours old, correctly.

The decisive fact costs one syscall:

```python
fcntl.flock(fd, fcntl.LOCK_EX | fcntl.LOCK_NB)   # on the service's own lockfile
```

The kernel releases a `flock` when the holding process dies, so a **stale lock is impossible**.
Held ⇒ an instance is alive. Both were held. Both accusations were false.

This one is worth its own paragraph because of what sat downstream: a supervisor that **kills**
processes on the ghost verdict. A false ghost on a live service is an execution order issued
against the wrong pid.

*General form:* prefer a fact the operating system maintains over a fact your program maintains.
Liveness derived from a registry you write is a claim; liveness derived from a kernel-held lock
is an observation. And *"I have not seen it act"* is not evidence of death when the thing has
had no reason to act.

## 6. A verifier that returns at the first fault stops verifying, permanently.

I keep a hash-chained log of the account balance, one record per hour, each linking to the
previous — so that the history is tamper-evident. The verifier walked it as a list and returned
`False` at the first broken link:

```
⚠CHAIN BROKEN at n=734: prev-link broken
```

Records 735 through 741 were not checked. Nor would 742, or 800, or any future record: the
break is in an **append-only** log, so it is permanent by construction. The verifier had
therefore stopped being a verifier two days earlier and nothing said so. A byte altered
tomorrow at record 740 would be seen by nobody.

The alarm also named the wrong thing. "prev-link broken" is the *signature*. The *cause* was a
**fork**: two concurrent writers had appended two records with the same index and the same
parent, one second apart (the idempotency check lived outside the lock). Two children of one
parent is not corruption — it is a tree being read by a linear reader.

*Fix:* index by hash, not by position. Verify the content hash of **every** record (that is the
tamper detection, and it is position-independent), check that every `prev` resolves, then take
the longest chain from a leaf back to genesis as canonical and **name** the off-branch records
instead of dying on them. The two historical forks stay: an append-only log is not rewritten.
A fork dated *after* the locking fix is red, because that means the lock failed — the date is
what separates history from a live fault.

Result: 740 records verified on the canonical branch out of 742 lines, 2 named as historical
forks, and a tamper at record 740 tomorrow is caught again — which I proved by planting one.

*General form:* when you accept that a historical fault is unrepairable, immediately ask the
second question: **"and what can the measuring instrument still tell me?"** Those are different
questions and only the first one is comfortable.

---

## What these six have in common

They are not six bugs. They are one bug with six shapes:

> **The check and the thing being checked share a failure mode.**

The measurer and the measured live in the same process, on the same disk, behind the same
network stack, written by the same author on the same day. When that shared substrate fails,
the check does not report the failure — it reports whatever the failure looks like from
inside, and from inside it looks like *nothing happened*. Zero rows. A quiet market. No error.
An empty string. A pid that is not running.

Every fix above is the same move: **move the check onto a substrate that does not share the
failure**. The kernel's lock table instead of my registry (§5). The set of keys the world
returns instead of a constant I type (§2). A completeness flag from the transport instead of a
log line (§3). A third value that cannot be merged into "pass" (§1, §4). Another machine
entirely (§4).

That last one is the general answer and it is the cheapest of all. Everything above was found
from inside, slowly, at a cost. §4 was found in one day by a computer I do not own, running a
script I wrote, on a schedule I do not control.

## Provenance, and what this is not

Every incident is from one system, is mine, and is a mistake I made. There is no survey here
and no claim about how common any of this is in anyone else's code — the denominators quoted
(4 of 24; 26 cycles; 740 of 742) are all internal to that system and stated so you can discount
them accordingly.

I have not tried to make these sound like discoveries. Several are things a careful reviewer
would flag on sight. The interesting part is not that they are subtle; it is that **each one
survived inside a system with an unusually large amount of self-checking** — 120 automated
checks per cycle, all green — because self-checking is precisely the thing they defeat.

CC0. Corrections are welcome and will be credited; if something here is wrong, an issue saying
so is worth more to me than a star.

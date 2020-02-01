---
title: 'Time in Progress (TIP) and Systems Thinking'
date: 2019-02-01T12:00:00.001+10:00
draft: true
tags : [kanban, agile]
---

Work in Progress (WIP) limits are enforced as part of a delivery flow in order
to maximise the throughput that a team produces. From a systems thinking
perspective, every time a WIP limit is decreased, further stress is put on the
system. In the case of Kanban delivery this stress may break the flow
completely, or allow the system to adapt and improve. I recently had a bit of a
rant about this on Twitter and wish to expand my thinking further:

## A Time in Progress (TIP) Limit

A TIP limit is a constraint that is placed on the system in order to limit the
time an item stays in a particular state. Where WIP is the number of items being
worked on concurrently in one part of the flow. TIP is how long a particular
item has been in that part of the flow. Controlling WIP is something that many
people can agree is a good thing. TIP is something that is often unheard of,
or ignored all together.

## How to use TIP

Initial TIP and WIP limits are difficult to enforce because there is no
baseline metric to begin with. After setting initial limits, the team can
become more aggressive with the limits over time. Placing more aggressive
limits, means more stress on the system, which may mean more productivity.

Initial WIP limits should be set according to the number of people on a team,
TIP limits can be set according the length of an iteration. For example:
suppose your iteration length is 10 days, placing an initial TIP limit of 13
days would initially seem to be a liberal limit. Over time, and through
inspection, if your system (that is your flow) is able to take the stress,
decrease your TIP limits as you go.

## Dealing with Violations

A WIP violation occurs when the team pulls in work from an upstream stage in
the process. A TIP violation occurs when an item has remained longer than the
limit itself. The former can be controlled easier than the latter. A few
questions arise when a TIP limit is violated: Why has the TIP limit been
violated in the first place and What should a team do if a TIP limit is
violated?

The answer to the first question can be a variety of things, technical or
non-technical including: framework issues, compatibility issues, refined
business requirements, people availability issues, or external team dependency
issues.

An answer to the second question could be user story splitting, this action
also depends on the item itself and whether it makes sense to split in the
first place. Unfortunately, what’s commonly seen as user story splitting in
agile delivery isn’t conducive to a proper understanding of a flow system
end-to-end. When a backlog item that’s almost done is split into two, a
behaviour commonly seen in teams, the act of splitting itself hides several
nuances including: the complexity of the requirements, the delta of the
backlog, and most importantly the unintended stress that that particular work
item has caused. When a user story is split into two, one  item is marked as
closed (or "done") and another is added into the backlog, this hides many
things. The delta of the overall backlog now becomes zero, even though work
has been performed and more uncovered. This is why I always advocate for
splitting a user story (or backlog item) into at least three items if a TIP
limit is ever violated.

Splitting a user story into at least three other user stories is a more
difficult task than simply splitting a user story into two. Splitting a user
story into two, means that the residue work goes back into the backlog, and
the completed work is marked as closed. Whereas when a user story is split
into at least three items, more though needs to go into what work has been
completed and what work is remaining. This limits the common error of simply
putting the residue work back into the backlog without properly examining what
the nature of that work actually is. Splitting into at least three items also
captures the stress caused on the overall system, as the delta of the backlog
is now positive not zero. Suggesting instantly that more work has been
uncovered during the act of working on the user story that has just been closed.

Finally, I'd encourage you to try TIP limits with user story splitting in your
delivery process. It will put stress on your delivery process for an iteration
or two, however, it may also help you by increasing your throughput and by
capturing previously uncovered work. If TIP limits to end up working for you,
I'd like to hear your stories and learnings. Please feel free to contact me.

---
title: 'Make a Channel Meeting in Microsoft Teams Appear in People''s Calendar'
date: 2020-12-17T00:00:00.001+11:00
draft: false
tags : [productivity]
---

The default behaviour when creating a meeting on Microsoft Teams is that
it will not appear on individual people's calendar, instead it will appear
in the channel and people will then need to add it to their calendars
individually.

!["Tasting Meat Pies Channel Invite"](/images/channel-invite.png)

!["Add to Calendar"]("/images/add-to-calendar.png")

The advantage to keeping an invite on a channel is that the
conversations related to a meeting are encapsulated within the
Microsoft Team where all other conversations about a specific topic are made.
The disadvantage is: the lack of visibility of the meeting invite on
an individual's calendar.

The work around to this, is to create a meeting invite in the channel, and
add a the channel email address in the `required attendees` fields.

The first step is to retrieve the channel email address. To do this
click the ellipses (...) on the channel where
the invite is required, and then `Get email address`.

!["Get channel email"]("/images/channel-email.png")

The second step is to create a channel meeting and add the channel
email address on the `required attendees` field.

!["Invite Channel Email"]("/images/invite-channel-email.png")

After following the above steps, all members of your Microsoft Team should
have the calendar meeting in their calendars. The posts that appear in the
channel will be both a channel email and a post notifying the channel that
an email has been sent.

!["Meeting Invites Into Calendars"]("/images/meeting-invite-into-calendars.png")

---
layout: post
title:  "Fix for 'Output Device' being stuck on 'Dummy Output' on Ubuntu 22.04"
date:   2023-11-24 15:03:55 -0800
tags: [Ubuntu, Pulse Audio, Dummy Output]
---

Another day, another Ubuntu bug. This time it has to do with the (pulse) audio not working after unplugging and replugging the audio cable.

I have a YouTube video playing in Firefox, and a colleague asks me a question. I turn around on my swanky swivel chair and roll over to their
desk. \*boink\* the 3.5mm audio cable unplugs from my machine. No biggie, I'll just plug it back in after I'm done conversing with my colleague.

5 minutes later, I return to my machine and plug the audio cable back into the machine. No sound. Nani?! I go into "Settings" -> "Sound" and
I see that "Dummy Output" is selected for "Output Devices". No biggie, I click on the dropdown and proceed to select "Line Out - Builtin Audio".

And... nothing. Silence is golden, I suppose. I should say, if you're having issues with Ubuntu 22.04 (or later) _not even showing_ your audio
devices, you have a different problem.

For me, I could select my other audio devices (yes, plural, including the S/PDIF and HDMI Audio out) but when I navigate away from the "Sound"
page, it reverts to Dummy Output. I can only assume it's not letting me change, but the UI doesn't reflect that fact.

## Time to fix "Dummy Output"!

For the savvy reader, you'll have noticed `(pulse) audio` within the first paragraph. This is indeed the culprit. For some reason, if the audio
is player and gets disconnected, there is a bug wherein pulseaudio gets stuck on "Dummy Output".

The fix then, is quit simple:
1) Reboot your machine (ahaha, who does this anymore? This is 2023, and we're going for 20 years of uptime baby!)
2) Restart `pulseaudio` service.

Let's go with option 2:
`pulseaudio --kill && pulseaudio --start`

... and we're back 🎉🎧!

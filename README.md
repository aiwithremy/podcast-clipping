# Podcast Clipping

A Claude skill that turns a long podcast or interview into short vertical clips you can post.

Most clipping tools find a loud moment, crop it to the speaker and stop. This one works like an editor. It reads the whole transcript, picks the moments where you make one clear point, and reorders your own lines so each clip opens on a hook and ends on a line that lands. It cuts between every camera so the picture keeps moving and the other person is seen, removes every pause and breath, adds captions and quiet music, and puts the batch on a preview page you can tap through on your phone. Then it writes title hook options and makes one version per hook you pick. It was built from a real 56-minute, three-camera podcast that became 14 approved clips, and every correction from those review rounds is written into it.

Made by [Remy Gaskell](https://www.instagram.com/aiwithremy).

## Install

**The easy way:** paste this repo's link into Claude Code (or any AI agent that can install skills) and say:

> Install this skill for me: https://github.com/aiwithremy/podcast-clipping

**Claude Code, by hand:** copy the `podcast-clipping` folder into `~/.claude/skills/`.

**Claude.ai or the Claude app:** download this repo as a ZIP (green **Code** button, then **Download ZIP**), then go to Settings, then Capabilities, and upload it as a skill.

## Use it

Say "clip my podcast" and tell it where the footage is: a folder on your computer, or a Drive or Dropbox link. It finds the moments, cuts the clips and sends you a preview page. Reply with notes on any clip ("03 needs a stronger ending") and it recuts only that one. When you approve the batch, ask for title hooks.

## What it needs

- Python 3.10 or newer, and ffmpeg.
- The first run sets up its tools with `scripts/setup.sh`. That takes about 1 GB of disk and a few minutes. Transcription runs locally with an open-source speech model, so there is no transcription service to pay for.
- A bold sans font file for the captions (any one you are allowed to use).
- It works best in Claude Code, or any agent that can run commands. Without a shell it can still find the moments and write the scripts and hooks.
- Separate camera and microphone files give the best clips. One finished video works too, with less movement.
- English recordings only, for now.

## More

I send a weekly newsletter on practical AI for business: [aiwithremy.com](https://www.aiwithremy.com)

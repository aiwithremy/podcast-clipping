# Music

Every clip gets one quiet song under it. This file is the method: how to pick the song, where it starts, how loud it sits, and how to check it.

## What the host asked for

> Add "some subtle background music" to every clip.

> A "bank of soundtrack options", with Claude able to "classify what types of soundtracks are best for what type of clip".

The references were podcast clip reels the host liked. What those reels had in common:

- One song for the whole clip.
- No build and no drop.
- Dark and low. Nothing bright.
- Music 5 to 14 dB under the voice. Louder on emotional stories, barely there on arguments.

## Keep your own bank

Keep a short list of songs you are allowed to use (licensed for your platform, or royalty-free). Five to ten is enough. For each song, record:

| Song | Artist | Use it for | Start the song at | Notes |
|---|---|---|---|---|
| (title) | (artist) | What kind of clip it suits, in a few words | Seconds into the song where it becomes audible | Licence, explicit, very soft, sub-bass only at the start, and so on |

Keep the bank in one place (a file in the job folder, or wherever the user keeps notes) and add to it when the user likes a new track. If the user has no bank yet, ask them for three to five songs they like under talking clips, and build it from those.

## Pick by what the clip is doing

Pick by what the clip is doing, not by its topic. The kinds of rows that worked on the first job:

- **A long story or thought experiment:** a flat, steady bed.
- **A warm personal moment, gentle advice:** soft and warm.
- **The most vulnerable, emotional story:** swelling strings. Big, so use it rarely.
- **Rock bottom, then the comeback:** something with more drive. Vocals are fine if they stay under the voice.
- **A hard truth, a cost, a contrarian take:** dark and low.
- **A calm argument that questions the default:** steady and slightly tense.
- **Calm mindset or philosophy:** slow and spacious.

Rules for a batch:

- Hook versions cut from the same answer share one song.
- Spread the songs across a batch. If six clips in a row are contrarian takes, they should not all get the same track.
- Many songs exist in several versions (covers, slowed, sped up, instrumental). Check the title and the artist.

## The rule that got clips sent back

> "The start is very quiet and there's no real music playing... the track only starts to be audible at around 5 to 6 seconds in."

> "The actual song doesn't seem to kick in audibly until 3 seconds in."

**The music must be audible from the clip's first second.** Most songs open quietly. Start the song past its intro.

To find a song's start point: export or extract the music alone and read its level second by second, above 250 Hz (roughly what a phone speaker plays). Start the song where that level reaches its normal running level. A song that opens on sub-bass only is silent on a phone until the rest comes in. One song on the first job opened with 5.5 seconds of sub-bass.

```
ffmpeg -v error -i SONG.mp3 -af "aresample=48000,highpass=f=250,asetnsamples=n=48000,astats=metadata=1:reset=1,ametadata=print:key=lavfi.astats.Overall.RMS_level:file=-" -f null -
```

It prints one level per second (`pts_time` is the second). The start point is the first second near the song's typical level. Write it in the bank.

## Levels

The approved clips had the music about **11 to 16 dB under the voice**. That matches the quieter end of the reference reels.

- Keep one steady level across a batch. A clip that is louder than its neighbours stands out. Do not turn a song up because the clip is emotional.
- A soft song that measures more than 16 dB under: raise it a little.
- A song that measures less than 11 dB under: lower it.
- About 19 dB under was tried and felt very subtle. The host chose the louder setting.
- Fade in 0.5 seconds. Fade out 1.0 second. No ducking: the level is already low enough.
- The clip's own sound stays as it is.

Your ear (or the user's) decides. An agent cannot hear. Mood notes in the bank come from measurements and from what the reference reels were about.

## Adding the music

Two routes. Both keep a copy of every clip without music.

**In the user's editor of choice** (CapCut, Premiere, DaVinci Resolve, Final Cut and so on). This is the route to take when the song comes from the platform's own licensed library, which usually lives inside its app. One song per clip, starting at the song's start point, at the level above. Export at the clip's size and frame rate (1080x1920, and 24 for a 23.976 clip, 30 for a 29.97 clip). Check that the export is the same length as the clip without music.

**With ffmpeg, for a song file you are allowed to use.** The kit has no music step of its own, so use this command. It starts the song at `START` seconds, sets the music to about -28 LUFS (about 13 dB under a -15 LUFS voice), fades it in and out, and mixes it under the clip:

```
CLIP=out/closeup/01_my-clip.mp4; SONG=music/song.mp3; START=3.0; OUT=music/01_my-clip_music.mp4
DUR=$(ffprobe -v error -show_entries format=duration -of csv=p=0 "$CLIP")
FADE_AT=$(python3 -c "print(max(0, $DUR - 1))")
ffmpeg -y -i "$CLIP" -ss "$START" -i "$SONG" -filter_complex \
 "[1:a]loudnorm=I=-28:TP=-6,aresample=48000,afade=t=in:d=0.5,afade=t=out:st=$FADE_AT:d=1[m];[0:a][m]amix=inputs=2:duration=first:normalize=0,alimiter=limit=0.84[a]" \
 -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 192k "$OUT"
```

Measure the result (below). Move `I=-28` up or down a dB or two to land 11 to 16 dB under the voice. Use the same value for the whole batch unless a song measures outside that range.

Do the music before the first delivery. The approved clips all had music, and redoing it on a recut clip takes about a minute.

## Checking the result

Check every clip before delivering:

```
python scripts/music_check.py --music WITH_MUSIC.mp4 --voice WITHOUT_MUSIC.mp4 --highpass 250
```

It lines the two files up, subtracts the voice, and reports how far the music sits under the voice and the music level of each of the first seconds.

- Same length and same picture as the clip without music.
- Music 11 to 16 dB under the voice.
- The first two seconds at about the same music level as the rest. If they are quieter, move the song start later.

Check the numbers before telling the user it is fixed. On the first job, the fix for a quiet start was confirmed with a second measurement: the song went from 37 to 44 dB down for the first six seconds to 30 to 33 dB down from the first second.

## Licensing and where clips can be posted

- A song from a platform's own music library is usually cleared only on that platform, and often only when posted from its app.
- The same file posted elsewhere can get a copyright claim.
- That is why every batch also keeps a `Without music` set. Tell the user in one line which set is for where.
- Never take music out of someone else's video to use in a clip.

## If music cannot be added yet

Deliver the clips without music on the preview page. Say plainly that music is still to come and why. Add it when you can and update the page.

# lp2flac
##### A compilation of notes, software, and workflows used in converting some vinyl records (mostly classical music) to a digital format.

### Motivation

I like my vinyl records. I also like being able to listen to music anywhere and without needing to change albums. I'm not a nut about either vinyl or digital - they each have use cases where they excel.

One reason vinyl is great, apart from the sound, which, if not superior, is certainly unique, is that it can be had far cheaper than digital music (unless you cheat and _steal_ from artists). Nowhere else do I know of such a deflation in cost. Records that were produced by world-class musicians, engineers, and facilities in the 50s to 80s can be had for a few dollars today.

Another reason is that many gems, especially in the world of classical music, are completely out of print and can not be found on iTunes or elsewhere. Thus, their analog counterparts provide a means of appreciating genius and artistry otherwise lost to obsolescence and poorly planned technological maturation.

And of course, the whole reason for transferring to digital is that digital files are infinitely easier to maintain, manipulate, and enjoy than records.

This repo is intended to serve as a compilation of my notes, software used, and workflows. It is not a trivial task, and this will in all likelihood fall shamefully to greater priorities, but one can always make a good go of it. it

### Challenges
In general, I feel that GUIs are evil. Anything worth doing in the world of computers is doing from the command line, with a few exceptions. The current tool for people like, who want to digitize their LPs and use open software, is Audacity. Audacity is great for its audience and a good tool for most people.

### Design Approach and Goals

- Attempt to maximize quality of sound
    o Minimize effects of processing, both hardware and software.
    o Minimize unwanted artifacts of the physical medium - clicks, noise, pitch warping, etc.
- Reduce the amount of recurring effort as much as possible through automation.
- Accurate/Organized import of all tags

### Signal Chain

#####  Turntable
[Rega rp1](http://www.rega.co.uk/rp1.html)

##### Equalizer/Phono PreAmp
[Rega Brio](http://www.rega.co.uk/brio-r.html)

##### A/D
[Focusrite Scarlett 2i4](https://us.focusrite.com/usb-audio-interfaces/scarlett-2i4)

##### Recording Engine (aka DAW)
[Raspberry Pi 3] (https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)

##### Miscelany http://www.musicdirect.com/p-8523-ultimate-analog-test-lp-analogue-productinos-test-vinyl-lp.aspx
### Workflow

The below assumes an initial hardware/software setup is complete.

- Choose the record to digitize - it's a lot of work, so pick one worthy of the honor

- Clean the record

- Optimize Recording Levels

    o _NOTE:  This is a challenging but necessary step. On one hand, the signal quality is maximized by recording at the highest
    possible gain level, where "highest" is defined (in this case) by the dynamic range of the A/D card. Because of transients, it is
    desirable to leave a margin of "head room" between the greatest amplitude signal and the Full Scale level (defined, for 24-bit
    sampling as 2^24). However, obtaining "perfect" levels, especially with mechanical knobs, is an exercise in exhaustion, so
    moderation is needed._

    o The goal of this step is achieve maximum SNR levels for each channel throughout the duration of the recording. Given software
    control of gain amplifiers, this would be straightforward, but given the set used in the project, only manual gain adjustment
    is available.

    o Conduct level test recording

    o Evaluate levels of channels avg'd over each side by importing the recorded wav to Matlab and measuring the difference
    between the loudest portions of the recording and a chosen reference below Full Scale (e.g. -6dB). Note the delta per channel
    for gain knob adjustment.

    o Using the test record, play a tone through the signal chain. In real-time, stream the music to Matlab and display the level
    of the signal in dB. Apply the adjustment noted in the previous step to each channel by observing real-time feedback level.

- Record the LP

    o Since I am using an RPi with limited storage resources, I've chosen to save the files over an NFS mount to the computer that
    will be doing the heavy lifting. In effect, the RPi is simply running the environment that interfaces with the A/D.

    o Teach program how to recognize stylus to disc event
        * This step allows the program that performs writing the wav to disk to run as a daemon, automatically initiating a new
        record based on sensing an event.
        * Record some signal (a few seconds) where the stylus is free floating, not touching anything.
        * Record some signal where the stylus is transferring over the beginning band of a record.
            > There should be enough difference between these signals, both in energy and statistics that they are
            easily distinguished.
            > Then, a threshold can be set to recognize when a stylus has been placed on the record.

    o Teach program to recognize stylus to middle band (end of a side) event.
        * Similar, but a little harder, than the above step. Can maybe be improved using _a priori_ knowledge of the album, especially
        track lengths, number of tracks.
        * Records always have two sides, so two "middle-band" events signal end of record.

    o Post-record process
        * The files should be finalized and cached to disk and master backups created.
        Pre-cached file tag information should be applied, in the least a title, for post-processing.

        * The program should then begin waiting for another stylus-to-disc event

- Post-Processing

    o Removal of Unwanted Artifacts
        * DC offset
        * Subsonic noise (rumble, bumps)
        * Cracks and Pops
        * Hiss and High-Frequency noise

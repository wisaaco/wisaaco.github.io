---
layout: post
title: "Animations from matplotlib figures"
categories: 
    - "Plot"
    - "Python"
    - "Matplotlib"
    - "Animations"
    - "ffmpeg"

---



# Make animations with matplotlib figures and ffmepg

<figure>
  <img
  src="/assets/img/animation_matplotlib.gif"
  alt="Gif Animation" 
  style="height:90%">	
  <figcaption>From YAFS project and MARIOII </figcaption>
</figure>


Take snaps from your execution and enumerate the files. Ffmpeg command does the next... 

```bash
ffmpeg -framerate 10 -i images/snap_%05d.png -c:v libx264 -pix_fmt yuv420p -crf 23 video.mp4

ffmpeg -t 20 -i video.mp4 -vf "fps=10,scale=520:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" -loop 0 output.gif

```
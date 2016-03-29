+++
title = "ImageMagick and the GRUB splash image"
date = "2013-10-04T14:37:00+08:00"
tags = ["linux"]
+++

Sometimes we have to make our own image for GRUB splash background. But there
are some restrictions on that image for GRUB bootloader to accept and display
it. By ImageMagick, we have an easy way to convert any image to satisfy that:

```bash
convert oldsplash.jpg -resample 72 -sampling-factor 2x2 -resize 640x480\! newsplash.jpg
```

*Hint:* The `\!` means *FORCE*, so it maybe breaks original ratio.

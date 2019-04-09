SwampCTF - "Ghidra Release"
======
##### CTF Name: SwampCTF 2019
##### Challenge Name: Ghidra Release
##### Challenge Category: Forensics
##### Difficulty: Medium
##### Points: 310
##### Challenge Description: 

>[Meanwhile at the NSA on a Friday afternoon]
Manager: Hey, we're going to be releasing our internal video training for Ghidra and we need you to watch it all to flag any content that needs to be redacted before release.
Manager: The release is next Monday. Hope you didn't have any weekend plans!
You: Uhhh, sure bu-
Manager: Great! Thanks. Make sure nothing gets out.
You: ... [looks at clock. It reads 3:45PM]
You: [Mutters to self] No way am I watching all of this: https://static.swampctf.com/ghidra_nsa_training.mp4
-= Created by DigitalCold =-`

#### Solution
After downloading the video the first thing I noticed was the massive length (>15 hrs).   Watching for a minute or two revealed the video was scrolling text, suggesting Optical Character Recognition (OCR) would be an ideal solution.  
Quick googling of OCR suggested video OCR is not well developed, and would be very slow, and the video would have to be converted to images.
I extracted frames of the video using a tool named Video to Image Converter, but upon reading other writeups ffmpegcould be used: `ffmpeg -i ghidra_nsa_training.mp4 -vf "fps=1" out%0d.png` [credit Ripp3rs](https://ctftime.org/writeup/14500).
After extracting the images pytesseract can be used to convert the images to a searchable/greppable text file:
```
$ FILES=./images/* 
$ for f in $FILES; pytesseract >> solve.txt; done;
```

Searching the file for "flag" reveals : 
![Ghidra Image 1]("https://github.com/joshuahaddad/CTF_WriteUps/blob/master/firstImage.jpg" "Ghidra Image One")

This shows the images are in the form "FLAG(x/4)" and searching through the rest of the file gives:
Flag 1 flag{l34
Flag 2 kfr33_n4
Flag 3 tion4l_s
Flag 4 3cur1ty}
Complete Flag: flag{l34kfr33_n4tion4l_s3cur1ty}
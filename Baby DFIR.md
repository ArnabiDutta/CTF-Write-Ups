# CTF Writeup: Digital Forensics - .ad1 File Analysis

## Challenge Overview
The challenge was an introductory digital forensics task where I was given a `.ad1` file. My goal was to extract and retrieve the hidden flag from this forensic image.

## Approach

1. **Understanding the File Type**  
   - The given file had an `.ad1` extension, which I was unfamiliar with.  
   - A quick research revealed that `.ad1` files are forensic image files created by AccessData's FTK (Forensic Toolkit). These files are used to store evidence in forensic investigations.

2. **Finding the Right Tool**  
   - Further research led me to **FTK Imager**, a forensic imaging tool that can open and analyze `.ad1` files.
   - I downloaded and installed FTK Imager from AccessData’s official site.

3. **Extracting the Flag**  
   - I launched **FTK Imager** and loaded the `a.ad1` file.
   - Browsing through the file system, I explored the directory structure.
   - I found a file that contained the **flag**.

4. **Retrieving the Flag**  
   - I opened the file to check its contents.
   - The flag was visible, and I successfully captured it!

## Conclusion
This challenge was a great introduction to digital forensics, specifically dealing with forensic image files. The key takeaway was learning how to handle `.ad1` files using FTK Imager. If you ever come across such files in a CTF, FTK Imager is the go-to tool for analysis!



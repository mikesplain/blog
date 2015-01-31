---
layout: post
title: "Compiling Textual"
date: 2014-06-01 11:37:58 -0400
comments: true
categories:
---

[Textual](http://www.codeux.com/textual/) is a great up and coming IRC client for mac (a modified version of [Limechat](https://github.com/psychs/limechat)).  They offer a nice free trial and a purchase version in the mac app store however they also release their code online for free.

They say it's free to compile yourself but compiling the app in OSX 10.9.3 has changed a little than I found on other sites.

Here's the process that worked for me:

## Pre-req's

Latest version of xcode and command line tools installed.


## Compiling

```
git clone https://github.com/Codeux/Textual.git
cd Textual;
open .
```

Then in finder open `Main Project (Textual).xcodeproj`

Here's the trick, if you aren't a member of Apple's Mac Developer program, you'll need to generate your own Code Signing self signed cert.  This is simple enough in Keychain Access:

* Open Keychain access
* Open Keychain Access Menu > Certificate Assistant > Create a Certificate...
  * Name: Developer ID Application
  * Certificate Type: Code Signing
* Click create
* Agree to whatever Keychain access requests.

_Modified as of 1/30/15:_

> Once you have your code signing certificate, do not modify the Build Settings of Textual through Xcode. Instead, modify the file at the path: Resources ➜ Build Configurations ➜ Code Signing Identity.xcconfig — The contents of this file define the name of the certificate which will be used for code signing.
>
> <cite>[Textual Docs](https://github.com/Codeux-Software/Textual#building-textual)</cite>

To the right of the stop button Click on the Textual Release and make sure "Textual (Standard Release)" is selected.

Compile and you're all set.

Enjoy!

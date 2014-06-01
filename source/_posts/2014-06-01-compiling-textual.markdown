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
cd Textual && open Main Project (Textual).xcodeproj
```

Here's the trick, if you aren't a member of Apple's Mac Developer program, you'll need to generate your own Code Signing self signed cert.  This is simple enough in Keychain Access:

* Open Keychain access
* Open Keychain Access Menu > Certificate Assistant > Create a Certificate...
  * Name: Developer ID Application
  * Certificate Type: Code Signing
* Click create
* Agree to whatever Keychain access requests.

Go back to xcode:

* Click Build Settings
* Search for "CODE_SIGN_IDENTITY"
* For the master "Code Signing Identity" click the dropdown and select the cert you just created named "Developer ID Application"
* Dropdown will revert to "Developer ID: *"

To the right of the stop button Click on the Textual Release and make sure "Textual (Standard Release)" is selected.

Compile and you're all set.

Enjoy!

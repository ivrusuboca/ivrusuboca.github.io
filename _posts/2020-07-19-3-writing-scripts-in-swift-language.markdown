---
layout: post
title:  "3/3 Writing scripts in Swift language"
date:   2020-07-19 00:00:00 +0800
categories: swift
---

This third and final part of the series is the most interesting one yet — my take. It involves transferring the source code, almost as is, from the script to the library.

This is the beauty of writing scripts in Swift using [swift-sh](https://github.com/mxcl/swift-sh), or java 11's [JEP 330](https://openjdk.java.net/jeps/330) feature — the code re-use between the script and the supporting application or library.

So then — here’s the [pull request](https://github.com/ivrusuboca/abc/pull/5) that shows the code transfer and here are some notes for each of the commits involved in it :

First commit, [b97d350](https://github.com/ivrusuboca/abc/pull/5/commits/b97d35068a560c01c904e330453dfd6151b37352), does the most interesting part as it removes the function `getPrice`’s source code located in `Scripts/binancePrice.swift` and adds it to `Sources/abc/abc.swift` . Now all that’s left is for the script to `import abc // ../../abc` and so making swift-sh `import` the local library’s code, then call `getPrice` function accordingly .

This also involves removing from the script the `Alamofire` and `SwiftyJSON` imports and adding them as dependencies to the abc library, inside `Package.swift` file. Recompiling the entire ensemble gave an error, however, as `Alamofire` complained it only supports `macOS` versions higher than `10.12` and so an adjustment had to be made in `Package.swift` in order to accommodate this. 

One more recompilation proved all’s in place this time :

![3-writing-scripts-in-swift-language-1.png](/assets/img/3-writing-scripts-in-swift-language-1.png)

Now — just because the function has been transferred into the library as is, does not mean that the job is completely done from the overall project point of view; this is because:
- Xcode is now more strict with types and other source code quality issues, and 
- the library source code, taken as a full-fledged piece of software, is just not close enough to a decent level of quality.

Second commit, [c479631](https://github.com/ivrusuboca/abc/pull/5/commits/c4796310479a271cc4020d87a4f5b6bc1b861675), deals with cleaning up the Xcode warnings — what’s easy as it’s actually only one.

Third commit, [d118882](https://github.com/ivrusuboca/abc/pull/5/commits/d118882aed7d1016627ecacb9eff3ce76a41a55e), however, is relatively more complex :

A new file, `Sources/abc/binance.swift` has been created and the `getPrice` function has been moved inside it — this is for the reason of code structuring and isolation; `getPrice` was really not that close in functionality with `abc.swift`'s `Hello World` …

Another new file, `Tests/abcTests/binanceTests.swift` has been created and two test units `testGetPriceValid` and `testGetPriceInvalid` have been written in order to make sure function `getPrice` works as per the original implementation.

These changes required re-issuing `swift package generate-xcodeproj` in the command line in order for Xcode take into consideration the newly introduced two files.

This is pretty much it . Enjoy !


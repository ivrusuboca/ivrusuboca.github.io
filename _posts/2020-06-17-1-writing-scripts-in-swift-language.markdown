---
layout: post
title:  "1/3 Writing scripts in Swift language"
date:   2020-06-17 00:00:00 +0800
categories: swift
---

I write [bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) scripts to automate things. I was writing scripts in java using [BeanShell](https://en.wikipedia.org/wiki/BeanShell) and now I am using java 11's [JEP 330](https://openjdk.java.net/jeps/330) feature of doing pretty much the same; the aim is to start with a small piece of code that, as it grows larger and more complex, it can change into a full fledged application program or library, at zero translation costs. 

For Swift, however, I use [swift-sh](https://github.com/mxcl/swift-sh): the easiest way — my take — to write scripts in Apple’s Swift language, in other words to write some simple code that in future might turn into a more complex set of classes, methods etc. as part of a larger project .

Here’s an example : create a library then write various scripts that would make use of it; it should be possible to transfer the code from these scripts to the underlying library’s source code as the scripts’ code becomes more stable and less likely to change on a whim .

Here’s how to accomplish this : install swift-sh via [Homebrew](https://en.wikipedia.org/wiki/Homebrew_(package_manager)), as per the instructions found on swift-sh’s page : `brew install mxcl/made/swift-sh`. To check all installed fine, issue `brew info swift-sh` :

![1-writing-scripts-in-swift-language-1.png](/assets/img/1-writing-scripts-in-swift-language-1.png)

Create a new folder `abc/` then inside it initialise the local git repository via `git init` .

Create a new swift library project inside folder `abc/` via `swift package init --type library` then add and commit everything into git :
```
> git add .gitignore Package.swift README.md Sources/ Tests/
git commit -m "created abc library via swift package init --type library"
```
Create a new directory `./Scripts` and a new file inside, it `./Scripts/sayHello.swift` :
```
>pwd
/Users/dev/work/public/abc
>ls
Package.swift README.md Sources  Tests 
>mkdir -p ./Scripts
>touch ./Scripts/sayHello.swift
>chmod u+x ./Scripts/sayHello.swift
```
Edit the `./Scripts/sayHello.swift` file with the following content :
```
>cat ./Scripts/sayHello.swift
#!/usr/bin/swift sh
import abc // ../../abc
print("saying : \(abc.text)")
```
Change the library file content to make its `struct abc` and `var` public :
```
>cat ./Sources/abc/abc.swift
public struct abc {
    public static var text = "Hello, World!"
}
```
Build the swift library then execute the `sayHello.swift` script :
```
>swift build
[2/2] Merging module abc
>
>./Scripts/sayHello.swift
saying : Hello, World!
```
And this is it. `./Scripts/sayHello.swift` script calls the library’s content.
Now — writing source code via [vi](https://en.wikipedia.org/wiki/Vi) or [vim](https://en.wikipedia.org/wiki/Vim_(text_editor)) is probably not ideal ; to get this done in Xcode generate an Xcode project via command `swift package generate-xcodeproj` .

![1-writing-scripts-in-swift-language-2.png](/assets/img/1-writing-scripts-in-swift-language-2.png)

And here’s the [source code pull request](https://github.com/ivrusuboca/abc/tree/3fae246ee7dafa36130be0864b904952736e8cf3) for the above. Enjoy !



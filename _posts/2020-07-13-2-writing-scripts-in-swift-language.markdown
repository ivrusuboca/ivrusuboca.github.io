---
layout: post
title:  "2/3 Writing scripts in Swift language"
date:   2020-07-13 00:00:00 +0800
categories: swift
---
First part of this series introduced how scripts can be written in Apple’s Swift programming language while also having referenced a library of our own. `sayHello.swift` script was implemented by calling a public field, `abc.text`, located in this library.

This second part is showing how a script evolves from writing a few short and dirty lines to a rather complex ensemble of writing functions and calling 3rd party library tools — all under `swift-sh`’s umbrella .

The scope of this second script is to write some code that retrieves the price of a cryptocurrency pair, say [`BTCUSDT`](https://www.binance.com/en/trade/BTC_USDT), published by the cryptocurrency exchange [Binance](https://www.binance.com/en)
.
The script life-cycle is described in [this pull request](https://github.com/ivrusuboca/abc/pull/3/commits) :

1. [First commit](https://github.com/ivrusuboca/abc/pull/3/commits/7148e196e823896c5aaa90865f36bdc4f0ce5574) introduces the command line arguments that the script is expecting, namely that the cryptocurrency pair like `BTCUSDT` must be specified as the only required input parameter.

2. [Second commit](https://github.com/ivrusuboca/abc/pull/3/commits/30d0ba966ba2a4c3c1d2a2ccfb19b953eb6877e0) introduces the 3rd party libraries that the script will be using : [Alamofire](https://github.com/Alamofire) and [SwiftyJSON](https://github.com/SwiftyJSON) . Their versions are specified as per the pattern introduced by swift-sh tool:

    ```
    import Alamofire // @Alamofire ~> 5.2
    import SwiftyJSON // @SwiftyJSON ~> 5.0.0
    ```
    Then it follows a very quick and dirty approach of getting the work done, assuming that everything will work just fine and no errors will ever happen. Running the script first time , without any input parameters, will make `swift-sh` tool fetch the 3rd party libraries then execute the script code; running the script the second time, with the correct input parameter `BTCUSDT` will make the script output the expected price for the cryptocurrency pair; finally, running the script with an invalid input parameter such as `BTCUSDTX` will make the script output some nasty error — which is expected, considering that the code does not handle any kind of error. Here’s the output of these three runs :

    ![2-writing-scripts-in-swift-language-1.png](/assets/img/2-writing-scripts-in-swift-language-1.png)

    What comes next are several steps that would improve the source code sufficiently such that it can be used in a production setting :

3. [Third commit](https://github.com/ivrusuboca/abc/pull/3/commits/5f725790debce4ccde5872b52a6353fd7266135f) introduces the synchronisation via `DispatchGroup` as well as the [`cURL`](https://en.wikipedia.org/wiki/CURL) debug-like output so we’d know what kind of request is actually being sent to the exchange

4. [Fourth commit](https://github.com/ivrusuboca/abc/pull/3/commits/9f447e30fb91afb4135a1d2ba9d822dcb2357dbd) introduces the error handling of the exchange response in case of various scenarios : invalid pair, wrong response etc.

5. [Fifth commit](https://github.com/ivrusuboca/abc/pull/3/commits/e99a719b1a7b89778722858a81715eb2c7854e5e) replaces the entire logic written thus far with a function with signature :

    ```
    func getPrice(ofPair pair:String) throws -> (String, Decimal)
    ```
    It returns the a tuple of (pair,price) upon successful processing or it throws an exception of type Failure in case of an error .

6. Finally, the [sixth commit](https://github.com/ivrusuboca/abc/pull/3/commits/acab8482b096408a2d976cdb9061e0fb8165d42c) decides to replace `DispatchGroup` with `DispatchSemaphore` for better and easier understanding of the source code.

All in all, the script is now doing a rather decent job of retrieving the price of a cryptocurrency pair while also dealing with potential errors that might come to its way :

![2-writing-scripts-in-swift-language-2.png](/assets/img/2-writing-scripts-in-swift-language-2.png)

That's it for now. Enjoy !



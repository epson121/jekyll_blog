---
layout: post
title: Bitcoinj dev - generate address and receive funds
date: 2016-07-17 21:28:09.000000000 +00:00
categories:
- ssh
- server
- linux
author: luka
---

## Introduction
[Bitcoinj](https://bitcoinj.github.io/#introduction) is a library designed specifically to interact with Bitcoin network. You can use it to interact with real bitcoin blockchain, or play around in TestNet - bitcoin's testing network. It allows us to do some pretty cool stuff. This post (and hopefully a series of them) is my way of trying to learn more about bitcoin, and its inner workings by using this library. For starters, I'd like to focus on simple tasks - creating a wallet, an address, and receiving some funds to it. This introduction assumes you'll be able to download the library, and include it into Java project, either using Maven, or manually. Either way is fine.

## Initial setup
I'll be using Java programming language to interact with the library, but there are a few [other possibilities](https://bitcoinj.github.io/using-from-other-languages): Javascript, Python, Ruby, etc.
Download the library from the official website. At the time of writing, newest version is 0.14.3. Include it into your project.

Every Java application has a starting point - a main class, with famous `public static void main` method. Lets create a new `Main` class:
```Java
public class Main {
    public static void main(String[] args) {
        // code here
    }
}
```

## Network, Wallet and WalletAppKit
First off, we have to select a network we'll be using. Network can be either real bitcoin network, working with real bitcoin, on the current blockchain, or one of bitcoin's test networks. Since I am using the library for learning purposes, I'll use test network - __TestNet3__ - current bitcoin test network.
In order to generate address for ourselves, we need a wallet. To quote the documentation:

_"A Wallet stores keys and a record of transactions that send and receive value from those keys. Using these, it is able to create new transactions that spend the recorded transactions, and this is the fundamental operation of the Bitcoin protocol"_

In other words, we use wallet to keep track of our addresses (keys), and use it to send transactions to other addresses. Since most of the applications needs a lot of stuff to be instantiated before starting, bitcoinj offers us a higher level object that create most of the stuff for us __WalletAppKit__. It wraps a lot of boilerplate code we would need to write. Let's get back to our code. In our `Main` class, we'll have to create a new `WalletAppKit`. Since it will prepare configuration for network connection, we need to tell it what network to use. Also, SPV downloads block headers from the network, so we'll have to specify where those files should be downloaded, as well as what the file name would look like. Here is the code:

```java
// we're using TestNet3
NetworkParameters params = TestNet3Params.get();

// WalletAppKit creates 2 files - .wallet file and .spvchain file
// use any string you want
String filePrefix = "forwarding-service-testnet";

// and finally, we create WalletAppKit, providing it with network parameter
// location of where to download block headers, and a file prefix
WalletAppKit kit = new WalletAppKit(params, new File("."), filePrefix);
```

Ok, we've created a WalletAppKit, but nothing has been started yet. We can start the kit, by invoking

```java
kit.startAsync()
```
in our main method. If you inspect library code, you'll see that `WalletAppKit` class extends `AbstractIdleService` class. `AbstractIdleService` is a part of Google's [Guava Service](https://github.com/google/guava/wiki/ServiceExplained#abstractidleservice). Calling `startAsync()` on an object extending `AbstractIdleService` will call the `startUp` method of that object (in this case `WalletAppKit`'s `startUp()` asynchronously. Reasons for starting this asynchronously are many, but the most obvious one is the UI blocking. Many wallet applications will use some kind of UI, and it might not be a good idea to have everything blocked while block headers are being downloaded. Since we're building a console application, we'll have to wait for everything to be downloaded, and then use the WalletAppKit object. We do this by calling

```java
kit.awaitRunning();
```

This will halt our application, until the asynchronous download of block headers is finished.

## Balance and addresses
`WalletAppKit` has plenty of methods we could use to get information about our wallet, network, connected peers, blockchain etc. We're currently interested only in our wallet. Let's generate an address and print it out
```java
kit.wallet().addWatchedAddress(kit.wallet().freshReceiveAddress());
System.out.println("New address created");
List<Address> list = kit.wallet().getWatchedAddresses();
System.out.println("You have " + list.size() + " addresses!");

for (Address a: list) {
    System.out.println(a.toString());
}
```

This will generate an address, print out the number of addresses we have, load them and print each of them out. Keep in mind that, whenever you start the program, a new address will be generated and added to your wallet. Let's print out the balance. Easy way to get balance is this:

```java
String balance = kit.wallet().getBalance().toFriendlyString();
System.out.println(balance);
```
Since we haven't put any bitcoin in our address, balance will be 0.0 BTC. Let's add some BTC. Remember, we're using TestNet, and bitcoin there is free. Head over to the testnet3 [free faucet](https://testnet.coinfaucet.eu/en/), enter your newly generated address, and receive some bitcoin.
You can start the application again, and hopefully your balance will be changed (you'll also have get a new address in your wallet). You might need to wait until the next block is mined - check blocks [here](http://tbtc.blockr.io/)

## Conclusion
We've covered quite a lot of stuff in this post. Next time, we'll return the BTC received by the faucet by creating a transaction of our own, and send the coins back. Why send them back? This is a test network, and other people might want to use them to test their applications, so keep the faucet running.
Here is the whole class
```java
// various imports
public class Main {

    public static void main(String[] args) {

        NetworkParameters params = TestNet3Params.get();

        String filePrefix = "forwarding-service-testnet";

        WalletAppKit kit = new WalletAppKit(params, new File("."), filePrefix);

        // Download the block chain and wait until it's done.
        kit.startAsync();
        kit.awaitRunning();

        List<Address> list = kit.wallet().getWatchedAddresses();
        if (list.size() < 2) {
            kit.wallet().addWatchedAddress(kit.wallet().freshReceiveAddress());
            System.out.println("New address created");
        }

        System.out.println("You have " + list.size() + " addresses!");
        for (Address a: list) {
            System.out.println(a.toString());
        }

        String balance = kit.wallet().getBalance().toFriendlyString();
        System.out.println(balance);
    }
}
```
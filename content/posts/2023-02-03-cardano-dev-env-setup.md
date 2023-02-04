---

title: "Setting Up for Plutus Development"
date: 2023-02-03 12:45:00 -0000
draft: true

---

##Introduction
Smart contracts for the Cardano blockchain are created using Plutus. Plutus is a platform that provides a programming language based on Haskell and an SDK that contains all the necessary tools to make smart contract development possible.

To get started developing Plutus smart contracts, you need to setup your environment first and the most important thing to understand is that Plutus development
is based on Haskell and therefore the platform itself is made available as a Haskell library. Generally, you will need to have some experience in Haskell to be able
to write smart contracts in Plutus.

The aim of this guide is to show you how to setup the entire environment from scratch such that you understand how all the pieces are tied together. In the end you will have a ready environment for contract development.


### Setting Up
####The Plutus Application Framework

This provides the basic set of libraries needed for Plutus development. It is the set of libraries your project will be complied and built on. The best way to set this up is using the nix method explained in the repository docs. This allows you to switch to different versions of the platform easily.
In general to set up plutus-apps, you will:
    1. Setup Nix on your machine. [Nix setup intructions can be found here](https://nixos.org/download.html).
    2. Clone the [plutus-apps](https://github.com/input-output-hk/plutus-apps) repository to a local directory.  The best way to set this up the using the nix method explained in the repository docs. This allows you to switch to different versions of the platform easily.
    3. From the local repo, check-out a recent tagged release. At the time of writting this, I am using v1.1.0.
    4. Enter into the nix-environment by typing `nix-shell` inside the repository. This command will start fetching and buidlding all the necessary dependencies that
    will be necessary when you build you smart contracts. Note that for the first time, this may take quite some time to build everything up. Just make sure you set up the IOHK binary cache to avoid compiling ALOT of tools. 

#### The Plutus Project
Once the plutus-apps tools are all built, you can now proceed to the setting up your project for smart contracts development. Here you have the option of copying the  [plutus-starter](https://github.com/input-output-hk/plutus-starter) project and using it as a template. However, the repository does not seem to have been updated lately and therefore you may have some issues getting it to work with the latest plutus framework version (mentioned above as v.1.10) which provides for Plutus version 2 development.

An easier option, at least for me, is to setup a basic Haskell project and then configure it to be ready for use with Plutus. So that's the process I'll explain here.
1. On your local machine, ensure you have installed the Haskell complier (GHC) atleast version 8.10.7, cabal and stack. A nice way to set up the entire suite of tools can be found [here](https://www.haskell.org/ghcup/install/)
2. 











---

For me, one of the hardest parts I've experienced is setting up the environment such that I could get started writting my first smart contract. I have come 
accross different approaches which try and the process less painfull. One of these is the [plutus-starter](https://github.com/input-output-hk/plutus-starter) template which is created by IOHK. It provides a simple project which you can use as a template to get started with development. Unfortunately, it does not seem to be updated to create PlutusV2 contracts. The other alternative is using custom templates from [demeter.run](https://demeter.run) which provides a cloud computing service. All you need to do is select a template and get started with development. While both are great at showing how a starter project looks like, they do not show how to setup the environment on which they are run and deployed.

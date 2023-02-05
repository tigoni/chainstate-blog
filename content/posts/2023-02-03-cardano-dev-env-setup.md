---

title: "Setting Up for Plutus Development from Scratch"
date: 2023-02-03 12:45:00 -0000
draft: true

---

##Introduction
Smart contracts for the Cardano blockchain are created using the Plutus which is a platform that provides a programming language based on Haskell (Plutus) and an SDK that contains all the necessary tools to make smart contract development possible.

At the moment, there are new ways of writting Cardano smart contracts without haaving to learn Haskell. Projects such as Aiken, Helios, and make it possible to delevelop smart contracts without using Plutus and therefore you do not need to learn Haskell.
The aim of this guide is to show you how to setup the entire environment from scratch such that you understand how all the pieces are tied together. In the end you will have a ready environment for contract development.

To get started developing Plutus smart contracts, you need to setup your environment first and the most important thing to understand is that Plutus development
is based on Haskell and therefore the platform itself is made available as a set of Haskell libraries. 

#### Haskell Compiler, Nix and Cabal
The tools we want to setup first are Nix, GHC and Cabal.
Nix is a tool which allows us to build and manage software packages in a reproducible way in diffrent environments. It also comes with a programming language to use for building packages. I tend to think of it like Docker but much better and reproducability. We need to set it up so that its much easier managing versions of the Plutus packages. You can find instructions on how to set it up [here](https://nixos.org/download.html). 

Next, we need to install GHC, a Haskell compiler and Cabal which is a tool used for managing Haskell libraries/packages. Think of it loosely like npm or Maven for Java development. According to IOG, the currently recommended GHC version is 8.10.7 and Cabal should be at least 3.8. While you could install these tools individually, a better way is to use GHCUp which will allow you to set up both from one place. Also note you could use stack which is a new Haskell build tool intended to replace Cabal. [Installation instructions for GHCUp](https://www.haskell.org/ghcup/install/). Once you have installed it you can run the command `ghcup tui` on your terminal to get a simple interface that will enable you install the right versions of GHC and Cabal 


#### The Plutus Application Framework
This provides the basic set of libraries needed for Plutus development. It is a set of libraries your project will be complied and built on and is available on the IOHK repository as `plutus-apps`. The best way to set this up is using the nix method explained as earlier as this allows you to switch to different versions of the platform libraries easily. Before you proceed make sure Nix is installed on your machine.

In general to set up plutus-apps, you will:
* Clone the IOHK  [plutus-apps](https://github.com/input-output-hk/plutus-apps) repository to a local directory on your machine.
* From the local repo, check-out a recent tagged release. At the time of writting this is release v1.1.0.
* While still in that directory, use the command `nix-shell` to enter into the nix-environment. This command will start fetching and building all the required Plutus packages. Note that for the first time, this may take quite some time to build everything up. Just make sure you set up the IOHK binary cache to avoid compiling a huge number of the packages and tools. 

#### The Plutus Project
When the `nix-shell` command completes building the Plutus packages you should get a nix shell where all those packages are available as dependencies for your Plutus project. Keep this shell open, we shall come back to it in a few.

Now we need to create an empty haskell project in a local directory. This will be the project that will house all your smart contracts. To create the project, do `cabal init` from the terminal. This will prompt for some preferences and you can use the defaults for all the questions. 
Once the project is ready, we need to add a `cabal.project` file which will allow us to list packages we need in our project but are not on Hackage (Haskell's package repo. Similar to npm repo for Javascript). So go ahead and create a new file and name it cabal.project. Inside the file, put in the contents below:
```
-- Custom repository for cardano haskell packages
-- See https://github.com/input-output-hk/cardano-haskell-packages on how to use CHaP in a Haskell project.
repository cardano-haskell-packages
  url: https://input-output-hk.github.io/cardano-haskell-packages
  secure: True
  root-keys:
    3e0cce471cf09815f930210f7827266fd09045445d65923e6d0238a6cd15126f
    443abb7fb497a134c343faf52f0b659bd7999bc06b7f63fa76dc99d631f9bea1
    a86a1f6ce86c449c46666bda44268677abf29b5b2d2eb5ec7af903ec2f117a82
    bcec67e8e99cabfa7764d75ad9b158d72bfacf70ca1d0ec8bc6b4406d1bf8413
    c00aae8461a256275598500ea0e187588c35a5d5d7454fb57eac18d9edb86a56
    d4a35cd3121aa00d18544bb0ac01c3e1691d618f462c46129271bccf39f7e8ee



-- See CONTRIBUTING.adoc for how to update index-state
index-state: 2023-01-26T17:52:04Z

-- See CONTRIBUTING.adoc for how to update index-state
index-state:
  , hackage.haskell.org 2023-02-05T14:22:36Z
  , cardano-haskell-packages 2023-01-26T17:52:04Z

packages:
  ./

-- We never, ever, want this.
write-ghc-environment-files: never

-- Always build tests and benchmarks.
tests: true
benchmarks: true

-- The only sensible test display option, since it allows us to have colourized
-- 'tasty' output.
test-show-details: direct

allow-newer:
  -- cardano-ledger packages need aeson >2, the following packages have a
  -- too restictive upper bounds on aeson, so we relax them here. The hackage
  -- trustees can make a revision to these packages cabal file to solve the
  -- issue permanently.
  , ekg:aeson
  , ekg-json:aeson
  , openapi3:aeson
  , servant:aeson
  , servant-client-core:aeson
  , servant-server:aeson

constraints:
  -- cardano-prelude-0.1.0.0 needs
  , protolude <0.3.1

  -- cardano-ledger-byron-0.1.0.0 needs
  , cardano-binary <1.5.0.1

  -- plutus-core-1.0.0.1 needs
  , cardano-crypto-class >2.0.0.0
  , algebraic-graphs <0.7

  -- cardano-ledger-core-0.1.0.0 needs
  , cardano-crypto-class <2.0.0.1

  -- cardano-crypto-class-2.0.0.0.1 needs
  , cardano-prelude <0.1.0.1

  -- dbvar from cardano-wallet needs
  , io-classes <0.3.0.0

  -- newer typed-protocols need io-classes>=0.3.0.0 which is incompatible with dbvar's constraint above
  , typed-protocols==0.1.0.0

  , aeson >= 2

  , hedgehog >= 1.1

-- The plugin will typically fail when producing Haddock documentation. However,
-- in this instance you can simply tell it to defer any errors to runtime (which
-- will never happen since you're building documentation).
--
-- So, any package using 'PlutusTx.compile' in the code for which you need to
-- generate haddock documentation should use the following 'haddock-options'.
package plutus-ledger
  haddock-options: "--optghc=-fplugin-opt PlutusTx.Plugin:defer-errors"
package plutus-script-utils
  haddock-options: "--optghc=-fplugin-opt PlutusTx.Plugin:defer-errors"
package plutus-contract
  haddock-options: "--optghc=-fplugin-opt PlutusTx.Plugin:defer-errors"

-- These packages appear in our dependency tree and are very slow to build.
-- Empirically, turning off optimization shaves off ~50% build time.
-- It also mildly improves recompilation avoidance.
-- For dev work we don't care about performance so much, so this is okay.
package cardano-ledger-alonzo
  optimization: False
package ouroboros-consensus-shelley
  optimization: False
package ouroboros-consensus-cardano
  optimization: False
package cardano-api
  optimization: False
package cardano-wallet
  optimization: False
package cardano-wallet-core
  optimization: False
package cardano-wallet-cli
  optimization: False
package cardano-wallet-launcher
  optimization: False
package cardano-wallet-core-integration
  optimization: False


-- Waiting for plutus-apps CHaP to be published
source-repository-package
  type: git
  location: https://github.com/input-output-hk/plutus-apps.git
  tag: v1.0.0 
  subdir:
    cardano-streaming
    doc
    freer-extras
    marconi
    marconi-mamba
    playground-common
    pab-blockfrost
    plutus-chain-index
    plutus-chain-index-core
    plutus-contract
    plutus-contract-certification
    plutus-example
    plutus-ledger
    plutus-ledger-constraints
    plutus-pab
    plutus-pab-executables
    plutus-script-utils
    plutus-tx-constraints
    plutus-use-cases
    rewindable-index

-- Direct dependency.
-- Compared to others, cardano-wallet doesn't bump dependencies very often.
-- Making it a good place to start when bumping dependencies.
-- As, for example, bumping the node first highly risks breaking API with the wallet.
-- Unless early bug fixes are required, this is fine as the wallet tracks stable releases of the node.
-- And it is indeed nice for plutus-apps to track stable releases of the node too.
--
-- The current version is dated 2022/08/10
source-repository-package
    type: git
    location: https://github.com/input-output-hk/cardano-wallet
    tag: 18a931648550246695c790578d4a55ee2f10463e
    subdir:
      lib/cli
      lib/core
      lib/core-integration
      lib/dbvar
      lib/launcher
      lib/numeric
      lib/shelley
      lib/strict-non-empty-containers
      lib/test-utils
      lib/text-class

-- Should follow cardano-wallet.
source-repository-package
    type: git
    location: https://github.com/input-output-hk/cardano-addresses
    tag: b7273a5d3c21f1a003595ebf1e1f79c28cd72513
    subdir:
      -- cardano-addresses-cli
      command-line
      -- cardano-addresses
      core

-- This is needed because we rely on an unreleased feature
-- https://github.com/input-output-hk/cardano-ledger/pull/3111
source-repository-package
    type: git
    location: https://github.com/input-output-hk/cardano-ledger
    tag: da3e9ae10cf9ef0b805a046c84745f06643583c2
    subdir:
      eras/alonzo/impl
      eras/alonzo/test-suite
      eras/babbage/impl
      eras/babbage/test-suite
      eras/byron/chain/executable-spec
      eras/byron/crypto
      eras/byron/crypto/test
      eras/byron/ledger/executable-spec
      eras/byron/ledger/impl
      eras/byron/ledger/impl/test
      eras/shelley/impl
      eras/shelley/test-suite
      eras/shelley-ma/impl
      eras/shelley-ma/test-suite
      libs/cardano-ledger-core
      libs/cardano-ledger-pretty
      libs/cardano-protocol-tpraos
      libs/cardano-data
      libs/vector-map
      libs/set-algebra
      libs/small-steps
      libs/small-steps-test
      libs/non-integral
```
This file generally instructs Cabal to include the listed packages as dependencies when it builds you project. 

We are almost done. Now, do `cabal update` to fetch these dependencies into the project. Finally, do `cabal build`. This should build your project although there is no code that uses the packages for now. 

When you get a successful build, you are ready to start writting Plutus code for your smart contracts. Here is a minimal Plutus contract to test if everything is set up correctly. Create a file called `TestPlutus.hs` in the `src` directory of your project and paste in the contents below:
```
```
Finally, update the .cabal file in the project to add this file under the `exposed-modules` section. Do `cabal build` and if it compiles, you have successfully setup the environment to get started with Plutus. 

Simple t-of-n remote signing and key management protocol for nostr, using the powers of FROST.

This project was originally hacked together for entry in the TABCONF 2024 hack-a-thon event.

## Features

* Break up an existing secret key or **nsec** into parts, or "shares".
* Create any t-of-n multi-signature setup using your shares and signing devices.
* If one of your shares is compromised, your secret key is still safe.
* Key rotation is simple: Destroy existing shares, replace with a new set.
* Your **npub** does not change. Bring your existing identity to frostr.
* Your signatures do not change. Nobody even knows you are using a multi-sig.

## Core Architecture

* **Bifrost** [[link]](https://github.com/FROSTR-ORG/bifrost)  
  Reference client implementation of FROSTR protocol. Each client custodies a share and acts as a node within your FROSTR network. Uses nostr relays to communicate peer-to-peer and coordinate signing. All traffic is end-to-end encrypted between nodes.  

## Signing Clients

- **Igloo** [[link]](https://github.com/FROSTR-ORG/igloo)  
  Desktop-based key management and signing device. Options to import an existing **nsec**, or generate a new one. Allows you to manage and rotate shares, plus recover your **nsec** using shares. Can be used online for remote signing, or offline for key management only.  

- **Frost2x** [[link]](https://github.com/FROSTR-ORG/frost2x)  
  Browser signing extension (forked from nos2x). Works will all existing NIP-07 supporting clients (including encryption). Bitcoin wallet features coming soon!  

- **Permafrost** [[link]](https://github.com/FROSTR-ORG/permafrost)  
  Server-based signing device and personal ephemeral relay. Includes a NIP-07 based web portal for managing your server. Options to run as a node service (using bun) or inside a docker environment. WIP.  

- **Frostbite** [TBA]  
  Mobile signing device and wallet, using NIP-46 remote signing.  

- **Heimdall** [TBA]  
  Server-less API gateway and signing device. Sign using state-less API calls (and running nodes on-demand).  

## How it Works

The protocol uses Shamir Secret Sharing to break up your nsec into "shares", and a hyper-optimized version of FROST to coordinate signing of messages. Each FROSTR signing client contains a **bifrost** node, and can participate in signing rounds with other nodes. Nodes communicate over the nostr network, and all traffic is end-to-end encrypted. The flow of the protocol is relatively simple:

* Nostr application makes a request to the user's FROSTR signing device.
* FROSTR device verifies the request, then makes a request for a signature to other nodes (over nostr).
* Other devices / nodes verify the request, then respond with a partial signature.
* User's FROSTR device collects the responses, combines the partial signatures, and returns a completed signature.

Nostr apps and websites can use existing protocols (NIP-07 and NIP-46) to interact with FROSTR signing devices. Nothing is changed about the user's **nsec**, **npub**, or signatures.

## Getting Started

If you are new to FROSTR, the best way to get started is by downloading **Igloo** for your desktop of choice. Generate a new **nsec** (for testing), and create a basic 2/3 multi-signature setup. Then you can experiment with using **frost2x** for browser-based signing (using NIP-07). The steps are as follows:

* Download and run the [igloo desktop](https://github.com/FROSTR-ORG/igloo/releases) app from github, for your platform of choice.
* Download and install the [frost2x extension](https://github.com/FROSTR-ORG/frost2x/releases) from github, or (soon) the chrome web store. 
* Use Igloo to generate a new **nsec** and 2/3 multi-signature setup (with 3 shares).
* Load the first share into Igloo, configure your relays, and start the signing server.
* Load the second share (along with **bfgroup**) into the **frost2x** extension.
* (optional) store the last share in a safe place (for use in recovery).

Once **igloo** and **frost2x** are configured, visit any NIP-07 enabled nostr website as usual. The **frost2x** extension will reach out and connect to **igloo** automatically. You should not notice any difference logging in and using nostr clients.

*Note: make sure to configure **frost2x** and **igloo** to use the same relays!*

## Rotating Shares

If one of your shares is lost or compropmised, you can abandon it by replacing your existing shares with a new set. This renders the compromised share useless. You can generate new sets of shares using your **nsec** (in igloo). There is no limit to how many new sets of shares your **nsec** can generate, so feel free to rotate often! For the truly paranoid, this can be done off-line on an air-gapped device.

The rotation process is very simple:

* Re-import your **nsec** into **igloo** and generate a new set of shares.
* Destroy any existing shares in **igloo** and **frost2x**. Replace them with your new shares.

## Questions / Reporting Issues

If you run into any bugs or problems using FROSTR, or have any kind of questions, please feel free to submit an issue ticket. Each project has it's own issues page, and our team monitors all of them. We really appreciate your feedback!

> **Alpha software:** FROSTR is still alpha software. You should not be using it yet for real keys or production workflows. V2 is coming out soon.

Simple k-of-n remote signing and key management protocol for nostr, using the powers of FROST.

This project was originally hacked together for the TABCONF 2024 hackathon competition by @cmdruid and @austinkelsay.

## Features

* Break up an existing secret key or **nsec** into fragments, called "shares".
* Create any kind of multi-signature setup using your shares and signing devices.
* If one of your shares is compromised, your secret key is still safe.
* Key rotation is simple: Destroy existing shares, replace with a new set.
* Your **npub** does not change. Bring your existing identity to frostr.
* Your signatures do not change. Nobody even knows you are using a multi-sig.

![frostr-turning-your-nsec-into-a-multisig](https://github.com/user-attachments/assets/9cf23683-e06a-48f5-856f-76b3231f34f5)

![frostr-running-signers](https://github.com/user-attachments/assets/6686cd9f-73ee-40e2-acce-0a9a13121842)

![frostr-signing-flow](https://github.com/user-attachments/assets/29b52c47-d4da-48f7-9b47-e3e86c785b47)

![frostr-what-are-signers](https://github.com/user-attachments/assets/385329e4-f99f-480b-ada3-2d73b3511aea)

![frostr-lost-a-key](https://github.com/user-attachments/assets/c14c06e6-e15c-44fa-9d47-b676b5b7553e)

![frostr-destroy-and-recreate](https://github.com/user-attachments/assets/8bd75d2c-a9d6-4b53-bf59-23da412b0894)

## Signing Clients

- **Igloo Desktop** [[link]](https://github.com/FROSTR-ORG/igloo-desktop)  
  Desktop-based key management and signing device. Options to import an existing **nsec**, or generate a new one. Allows you to manage and rotate shares, plus recover your **nsec** using shares. Can be used online for remote signing, or offline for key management only.  

- **Frost2x** [[link]](https://github.com/FROSTR-ORG/frost2x)
[[chrome web store]](https://chromewebstore.google.com/detail/frost2x/gpbndcgoaehgeckcfmmbmaaaeljnaiof)  
  Browser signing extension (forked from nos2x). Works will all existing NIP-07 supporting clients (including encryption). Bitcoin wallet features coming soon!  

- **Igloo Server** [[link]](https://github.com/FROSTR-ORG/igloo-server)  
  Lightweight self-hostable Frostr server & signing client with NIP46 support, Frostr capable API, runs lightweight in 'headless' mode off of single .env or in fully featured 'database' mode with static frontend, sqlite db, and user management.

- **Igloo CLI** [[link]](https://github.com/frostr-org/igloo-cli)
  A simple CLI for interacting with all frostr functionality including keys, signing, peering, and recovery.

- **Igloo Web** [[link]](https://github.com/frostr-org/igloo-web)
  Browser-first FROSTR signer built with React/Vite. Runs entirely in your tab; your share stays encrypted in localStorage.

- **Igloo Android** [[link]](https://github.com/FROSTR-ORG/igloo-android)
  A NIP-55 and FROSTR signing device, wrapped in a hybrid android application (coming soon)

## Libraries

* **Bifrost** [[link]](https://github.com/FROSTR-ORG/bifrost)  
  Reference client implementation of FROSTR protocol. Each client custodies a share and acts as a node within your FROSTR network. Uses nostr relays to communicate peer-to-peer and coordinate signing. All traffic between nodes is end-to-end encrypted.

- **Igloo Core** [[link]](https://github.com/frostr-org/igloo-core)
  A TypeScript library providing core functionality for FROSTR/Bifrost distributed key management and remote signing. This library abstracts the complexity of threshold signatures and provides a clean, strongly-typed API for building secure distributed applications.

- **nostr-connect** [[link]](https://github.com/frostr-org/nostr-connect)
  Lightweight opinionated implementation of NIP46 (nostr connect) signing standard

## How it Works

The protocol uses Shamir Secret Sharing to break up your nsec into "shares", and a hyper-optimized version of FROST to coordinate signing of messages. Each FROSTR signing client contains a **bifrost** node, and can participate in signing rounds with other nodes. Nodes communicate over the nostr network using end-to-end encrypted notes. The flow of the protocol is relatively simple:

* Nostr application makes a request to the user's FROSTR signing device.
* Signing device verifies the request, then forwards it to other FROSTR nodes.
* Other nodes verify the request, then respond with a partial signature.
* Signing device collects the responses, then returns a completed signature.

Nostr apps can use existing protocols (NIP-07 and NIP-46) to interact with a FROSTR signing device. FROSTR is meant to be a drop-in replacement for existing signing devices. Nothing is changed about the user's **nsec**, **npub**, or signatures.

## Demo Walkthrough
Demo walkthrough setting up a basic 2/3 with [igloo desktop](https://github.com/FROSTR-ORG/igloo/releases) and [frost2x extension](https://github.com/FROSTR-ORG/frost2x/releases) signing a multisig note, and recovering.

[![Watch the video](https://plebdevs-bucket.nyc3.cdn.digitaloceanspaces.com/images/frostr-demo-6-10-2024.png)](https://plebdevs-bucket.nyc3.cdn.digitaloceanspaces.com/videos/random/frostr-demo-6-10-2024.mp4)

## Getting Started

If you are new to FROSTR, the best way to get started is by downloading **Igloo** for your desktop of choice. Generate a new **nsec** (for testing), and create a basic 2/3 multi-signature setup. Then you can experiment with using **frost2x** for signing in the browser. The steps are as follows:

* Download and run the [igloo desktop](https://github.com/FROSTR-ORG/igloo/releases) app from github, for your platform of choice.
* Download and install the [frost2x extension](https://github.com/FROSTR-ORG/frost2x/releases) from github, or the [chrome web store](https://chromewebstore.google.com/detail/frost2x/gpbndcgoaehgeckcfmmbmaaaeljnaiof)  
* Use Igloo to generate a new **nsec** and 2/3 multi-signature setup (with 3 shares).
* Load the first share into Igloo, configure your relays, and start the signing server.
* Load the second share (along with **bfgroup**) into the **frost2x** extension.
* (optional) store the last share in a safe place (for use in recovery).

Once **igloo** and **frost2x** are configured, visit any NIP-07 enabled nostr website as usual. The **frost2x** extension will reach out and connect to **igloo** automatically. You should not notice any difference logging in and using nostr clients.

*Note: make sure to configure **frost2x** and **igloo** to use the same relays!*

## Rotating Shares

If one of your shares is lost or compropmised, you can abandon it by replacing your existing shares with a new set. This renders the compromised share useless. You can generate a new set of shares using your **nsec** (and igloo). For the truly paranoid, this can be done offline on an air-gapped device.

The rotation process is very simple:

* Re-import your **nsec** into **igloo** and generate a new set of shares.
* Destroy any existing shares in **igloo** and **frost2x**. Replace them with your new shares.

 There is no limit to how many sets of shares your **nsec** can generate, and each new set is random. Rotate as much as you like.

*Note: when you rotate to a new set, make sure to delete all the shares from the previous set!*

## Questions / Reporting Issues

If you run into any bugs or problems using FROSTR, or have any kind of questions, please feel free to submit an issue ticket. Each project has it's own issues page, and our team monitors all of them. We really appreciate your feedback!

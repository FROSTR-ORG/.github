# FROSTR

Simple t-of-n remote signing and key management protocol for nostr, using the powers of FROST.

This project was originally hacked together for entry in the TABCONF 2024 hack-a-thon event.

## Key Features

* Break up your secret key into decentralized, distributable shares.

* Sign messages using t-of-n signing devices (multiple devices available).

* If one share is compromised, your secret key is still safe.

* Discard and replace shares without changing your secret key (and identity).

## Architecture

**Bifrost**: Reference p2p client and implementation of FROSTR protocol.

## Clients

**Igloo:** Desktop key management app & signing device.  
**Frost2x:** Browser signing extension (forked from nos2x).  
**Frostbite:** (TBA) Mobile signing device using NIP-46.  
**Permafrost:** Reference node application for server environments.  
**Heimdall:** API gateway and signer for server-less environments.  

## Dependencies

**Frost:** Core cryptography library that implements FROST primitives.  
**Nostr-P2P:** Reference node and SDK for communicating peer-to-peer over nostr.

## TODO:

There are a few things that need to be finished before a version 1.0 release:

* ~~Finish refactoring Bifrost library.~~
* ~~Add a project kanban board for tracking updates.~~
* ~~Implement ECDH and signature protocol negotiation.~~
* ~~Add demo examples and test vectors to Bifrost.~~
* ~~Launch alpha implementation of frost2x and igloo.~~
* Finish updating Igloo for release.
* Finish updating frost2x for release.

The code cleanup is needed in order to graduate this from a hackathon project to a real application.

## How it Works

The protocol uses FROST in order to coordinate the signing of a message between multiple signing devices owned by a single user.

* Website or application makes a request to the user's signing device (to sign a note).

* User's device makes a signed request to the remote signing device(s).

* Each remote device verifies the request, then responds with a partial signature.

* User's device verifies each partial signature, then adds their own.

* The signatures are combined, and the complete signature is returned to the website / app.

## Setting up your Devices

* Use Igloo to generate a set of FROST shares from your secret key.

* Import a share into Igloo to start the remote signing server.

* Import a share into each of your other devices.

* (optional) store the remaining shares for use in recovery.

## Rotating your Shares

* Re-run the import process using your secret key to generate new shares.

* Destroy any existing shares, and replace them with your new shares.

## Resources

**Frost**  
Multi-platform Typescript FROST Library   
https://github.com/cmdruid/frost

**Nostr-P2P**  
Nostr client SDK for creating peer-to-peer protocols.  
https://github.com/cmdruid/nostr-p2p

**Bifrost**  
Core library for implementing the FROSTR signing protocol.  
https://github.com/frostr-org/bifrost

**Igloo**  
Electron-based key-management app and remote signing server.  
https://github.com/frostr-org/igloo

**Frost2x**  
Web extension signing device (fork of nos2x).  
https://github.com/frostr-org/frost2x


# Product Overview

SoberFlare is a Web3 social app centered around address-based identity. What we want to build is not simply traditional social media moved on-chain, but a product where identity, relationships, content, chat, and capital flow all live inside the same structure, so users can express themselves, connect with others, and act on-chain in one place. At the same time, we are also exploring new ways information should be distributed in the AI era, including what kinds of content deserve to be seen and how they can continue to be seen over time.

The audio and visuals in the demo may not always match perfectly on the exact same timeline, and they can complement each other when needed.

## Home

Home is the content distribution layer, and it is also one of the pages we most want to highlight in this hackathon.

This is not just a reverse-chronological feed, and it is not organized around KOLs or centralized recommendation slots. What we care about more is why valuable content gets seen, and whether it can keep being seen over time. To support that, we have already built a multi-channel feed, post creation, comments, post detail pages, and a complete interaction loop around each post.

More importantly, we have connected on-chain incentive mechanics to the content system. Posts can be liked and commented on, but they can also continue to receive additional "fuel." In SoberFlare, users can use real assets to boost content they believe deserves more visibility. Our current Tempered Blaze mechanism supports starting a post with either COAL or USDT, and other users can continue adding fuel to the same post afterward. This means visibility is no longer driven only by emotion or attention farming. It becomes tied to real commitment.

What we want Home to eventually carry is a different content logic: valuable posts should not only be published, but continue gaining energy, recirculation, and redistribution through later interactions.

## Network

Network is the second core pillar. It is not a follower list, and it is not a static social graph. It is an interpretable relationship network built around address identity.

We gradually project multi-chain transfer behavior, together with encrypted chat exchange counts between addresses, into relationship strength, relationship state, and role labels. In other words, users do not just see "who I know." They see what has actually happened between them and others, which connections have become stable, and which relationships are still heating up.

In product expression, Network feels more like a dynamic field than a simple list of nodes. It helps users understand their own social structure, and it also allows them to look into other people's relationship networks to discover more trustworthy connections worth going deeper on. Compared with the vague follow relationships of traditional Web2 social products, we want this page to present a relationship map grounded in real behavior.

The design goal here is especially important: this relationship expansion model is possible precisely because communication privacy is protected at the foundation. Since direct chats and private groups are encrypted, users do not need to worry that their private communication will be exposed in plaintext. Instead of exposing message content, the system uses relationship signals built from real exchange and real interaction counts to help users expand trusted human relationships more confidently.

## Contacts

Contacts is the middle layer where relationships are organized and turned into action. It takes abstract address relationships and turns them into a contact system that is manageable, revisit-able, and actionable.

On this page, users can view saved contacts as well as the groups they have joined. Each contact can be opened into a more detailed profile page, where users can save the address, edit remarks, copy the address, start a direct chat, jump directly into a transfer flow, or continue exploring that person's relationship network.

The point of this layer is to turn an address from a cold string into something recognizable, maintainable, and socially active over time. Contacts is not just an address book. It is the layer where on-chain identity becomes part of real social relationships.

## Chat

Chat is where relationships actually happen. We have already connected end-to-end encrypted direct messaging, end-to-end encrypted private groups, and regular group chat flows.

Users can add contacts directly by address, start direct chats, create groups, join groups, and manage daily communication from the conversation list. For more sensitive communication scenarios, both direct chats and private groups use E2EE, while regular group chats are not encrypted in the same way. Private keys and decryption material stay on the local device and are never uploaded to the cloud. The goal here is very clear: we want relationship networks to be understandable, without turning private communication itself into platform-readable data.

So in SoberFlare, Chat is not just a messaging feature. It is the place where relationships are verified, maintained, and strengthened, while also forming a loop together with Contacts and Network.

## Me

Me is the combined entry point for personal identity and wallet functionality, and it is also the page most closely tied to transfers in this navigation structure.

On one side, this page carries the user's profile, including avatar, display name, and tags. On the other side, it acts as the main entry point into wallet actions. Right now, we have already placed several core functions here: `Asset Display`, `Transfer`, `Transfer History`, `Receive`, and `Swap`.

The transfer flow supports sending assets across multiple chains. The current pipeline already covers Ethereum, Bitcoin, Solana, BNB Smart Chain, and Tron. Users hold their keys locally and sign on-device, while the server helps construct unsigned transactions and broadcast signed transactions. This preserves self-custody while also making transfers feel native inside a social product.

The receive flow can directly show the user's wallet address and payment QR code, which makes face-to-face or cross-app receiving easier. The Swap page already connects to cross-chain routing, allowing users to quickly inspect swap paths across chains and assets.

In addition, the Me page also shows recent transfer records, so users can quickly look back at on-chain transfers that were initiated and broadcast from the current device. This matters because we do not want wallet functionality to feel like a detached utility. We want it to sit naturally inside identity and social behavior.

## Summary

The problem SoberFlare wants to solve is straightforward: today, on-chain identity, social relationships, content distribution, and capital actions are still fragmented, and in the AI era, we also need to rethink how information should be distributed and what kinds of content deserve to be seen.

Our approach is to reorganize them into one unified product structure. From Home's incentive-driven content layer, to Network's relationship graph, to the address-social and transfer capabilities inside Contacts, Chat, and Me, we want SoberFlare to become a social experience that is truly native to on-chain identity.

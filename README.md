# The NFT Gateway Protocol

The NFT Gateway Protocol (NFTGP) is a mini protocol designed for privacy preserving access to NFT data stored on- and off-chain.

It is comprised of two specifications:

- [NFT URI Scheme Specification](./NFT_URI_SCHEME.md)
- [SVG Resource Inlining Specification](./SVG_RESOURCE_INLINING.md)

## Goals

The protocol is designed to address a set of concerns about the way current web3 apps and wallets handle digital media files that are tokenized as [non-fungible tokens](https://eips.ethereum.org/EIPS/eip-721) (NFTs) on [EVM](https://ethereum.org/en/developers/docs/evm/) blockchains.

### Addressability

While sharing the token URI is certainly possible, this URI allows no inference of the NFT's on-chain record and the ownership claim it expresses.
Moreover, for NFT data that is stored or generated on-chain there is no way for websites to link to it in a compact and reliable way since [data URIs](https://en.wikipedia.org/wiki/Data_URI_scheme) are long and not supported by many apps.

The NFT Gateway Protocol defines an address schema for canonic links to NFTs that are concise, work reliably, and allow tracing to the ownership claim on the blockchain.

### Privacy Protection

There are [serious privacy concerns](https://medium.com/@alxlpsc/critical-privacy-vulnerability-getting-exposed-by-metamask-693c63c2ce94) with fetching the token URI directly from the browser.
This can be alleviated by proxying such requests through a gateway.

### Interoperability

Gateways implementing the NFT Gateway Protocol will be interoperable and can be used interchangeably by different wallets or other apps for displaying NFT media.

### Trust Minimization

Specifying a protocol rather than merely implementing a gateway, the NFTGP project aims at fostering a diverse ecosystem of gateways.
This is crucial for enabling trust minimized access to NFT data where URIs to NFT must not be tied to any one central authority.

### Composability

[SVG](https://www.w3.org/TR/SVG/) lends itself as a format for generating images on a blockchain due to its compact vector-based representation.
Importantly, it can use external resources, like fonts, other SVG files, or raster images via URI references.
This facilitates on-chain generation and storage of complex images at a small file size by composing larger image layers that are stored off-chain, e.g., on [IPFS](https://ipfs.io).

The NFT Gateway Protocol defines a mechanism for the resolution and inlining of external resources in SVG--guaranteeing consistent image rendering in any environment.

## Gateway implementations

### Public gateways

The NFTGP project maintains a free public http gateway service at https://nftgp.io.

The source code of an http gateway reference implementation can be found here:
https://github.com/nftgp/nft-http-gateway

With adoption of the standard we hope to see more instances of both, alternative gateway implementations as well as third-party public gateways.

### Private & local gateways

The NFT Gateway Protocol can also be implemented by private gateways, for example as part of a web3 application's back-end infrastructure.

Finally, an NFT gateway can even run locally on the end-user's machine, for example bundled within a web app or implemented as a browser extension.
Such client-local gateway must take extra measures to ensure that privacy is preserved, which can be achieved by routing http requests to media files through a trusted http proxy server, for example.

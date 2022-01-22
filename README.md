# NFT Gateway Protocol Specification

The NFT Gateway Protocol (NFTGP) is a mini protocol designed for privacy preserving access to NFT data stored on- and off-chain.

## Motivation

Non-fungible token (NFT) contracts are popularly used to represent ownership of digital media files on blockchains.
The [ERC-721](https://eips.ethereum.org/EIPS/eip-721) open standard prescribes that the token stores the URI of the media file (`tokenURI`) as part of its metadata.

### Addressability

While sharing the token URI is certainly possible, this URI allows no inference of the actual NFT and the ownership claim it expresses.
Moreover, for NFT data that is stored or generated on-chain there is no way for websites to link to it in a compact and reliable way as [data URIs](https://en.wikipedia.org/wiki/Data_URI_scheme) are long and not supported by many apps.

The NFT Gateway Protocol defines an address schema enabling direct links to NFT data that are concise, work reliably, and allow inferring ownership.

### Privacy

There are [serious privacy concerns](https://medium.com/@alxlpsc/critical-privacy-vulnerability-getting-exposed-by-metamask-693c63c2ce94) with fetching the token URI directly from the browser.
This can be alleviated by routing these requests through a proxy gateway.

### Interoperability

Gateways implementing the NFT Gateway Protocol will be interoperable and can be used interchangeably by different wallets or other apps for displaying NFT media.

### Decentralization

Specifying a protocol rather than only implementing a gateway, the NFTGP project aims at fostering a diverse ecosystem of gateways.
This is crucial for enabling trust minimized access to NFT data where NFT URIs must not be tied to any one central authority.

While the project includes a reference implementation and maintains a free gateway service, its principal goal is promoting adoption by wallets and apps as well as encouraging alternative gateway implementations and deployments.

### Composition

[SVG](https://www.w3.org/TR/SVG/) lends itself as a format for generating images on a blockchain due to its compact vector-based representation.
Importantly, it can use external resources, like fonts, other SVG files, or raster images, via URI references.
This facilitates on-chain generation and storage of complex images with small size, that are composed of larger image layers stored off-chain, e.g., on [IPFS](https://ipfs.io).

The NFT Gateway Protocol defines a mechanism for the resolution and embedding of resources referenced in SVG to make a self-contained SVG that displays consistently in all environments.

### Caching

By using a standardized, decentralized caching layer, different gateways will be able to share a cache.
This means a higher chance of cache hits, and hence improved caching effectiveness.


## NFT Address Schema

This specification introduces the `nft` URI scheme.
The `nft:` URI prefix signals that the URI at hand must be resolved according to the NFT Gateway Protocol.

The protocol considers NFTs on EVM blockchains, on which each token is uniquely identified by the following attributes:

<dl>
  <dt><code>&lt;CHAIN_ID&gt;</code> (hexadecimal)</dt>
  <dd>The <a href="https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md">chain ID</a> of EVM chain storing the NFT contract</dd>
  <dt><code>&lt;CONTRACT_ADDRESS&gt;</code> (hexadecimal)</dt>
  <dd>The Ethereum address of the NFT smart contract</dd>
  <dt><code>&lt;TOKEN_ID&gt;</code> (hexadecimal)</dt>
  <dd>The <a href="https://eips.ethereum.org/EIPS/eip-721">EIP-721 token ID</a></dd>
  <dt><code>&lt;BLOCK&gt;</code> (hexadecimal or `latest`)</dt>
  <dd>The block number (height) or tag</dd>
</dl>

The block number is an optional attribute identifying a snapshot state in case of NFTs with mutable data URIs.
A value of `latest` addresses the most recently mined block.
If omitted, the gateway can choose to query from any block on which the data URI function returns a non-empty string. 

These attributes are combined into a URI according to the following scheme:

> nft://`<CHAIN_ID>`\[:`<BLOCK>`\]/`<CONTRACT_ADDRESS>`/`<TOKEN_ID>`\[/`<FILENAME>`\]

Sections in square brackets are optional and can be omitted.

The optional `<FILENAME>` suffix does not serve any addressing purposes.
It shall usually be composed of the [URI component encoded](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent) [EIP-721 name metadata](https://eips.ethereum.org/EIPS/eip-721) and the canonical file extension.

Note: The protocol specifies only an absolute addressing scheme.
It could potentially be extended in future versions to include relative addresses.

## SVG URI resolution

Values of any SVG [href attribute](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/href) are subject to resolution by the gateway.

Additionally, the gateway must resolve the arguments of [css url() functions](https://developer.mozilla.org/en-US/docs/Web/CSS/url).

### URI schemes for resolution

#### http(s)

URIs with the `http` or `https` schemes (starting with `http://` or `https://`) are resolved with via http.

#### ipfs

URIs with the `ipfs` scheme (starting with `ipfs://`) are resolved via [IPFS](https://ipfs.io).

#### nft

URIs with the `nft` scheme (starting with `nft://`) are resolved by recursively invoking the NFT gateway, allowing for composition of on-chain images.

URIs of any other scheme are not resolved but kept unmodified.

### Resolution process

Resolving a URI means fetching the identified resource via the appropriate protocol (http, ipfs, or nftgp) and embedding the resolved data as [data URI](https://en.wikipedia.org/wiki/Data_URI_scheme) in the SVG replacing the resolved original URI.

In case of unsuccessful fetch requests the original URI shall be left untouched.

## IPFS Caching

WIP

Rough idea is to use IPFS as a decentral cache storage.
Open question is how to retrieve the IPFS CID (content hash of the resolved SVG file).
Could be provided by some sort of decentralized key value store (mapping nft:// URIs to IPFS CIDs).
What options do exist for decentralized key value stores?

Probably a good idea to scope this part out of the inital version.

## Gateway implementations

### Public gateways

The NFTGP project maintains a public gateway at https://nftgp.todo.
The hope is that with adoption of the standard there will be alternative public gateways provided by third-parties.

### Private gateways

A gateway can also be maintained privately by app providers.

### Local gateways

An NFT gateway implemented to run locally on the end-users machine must make sure that privacy is preserved.
This can be achieved by routing all http requests through a generic proxy.


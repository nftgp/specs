# NFT URI Scheme Specification

This specification introduces the NFT URI scheme.
This new [generic URI scheme](https://datatracker.ietf.org/doc/html/rfc3986#section-3.1) is identified with the `nft` scheme name.

The goal of the NFT URI scheme is to make the data of any non-fungible token (NFT) representing digital media files addressable with a Uniform Resource Identifier (URI).

The specification considers NFTs on [EVM](https://ethereum.org/en/developers/docs/evm/) blockchains, on which each token is uniquely identified by the following attributes:

<dl>
  <dt><code>&lt;CHAIN_ID&gt;</code> (decimal)</dt>
  <dd>The <a href="https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md">chain ID</a> of EVM chain storing the NFT contract</dd>
  <dt><code>&lt;CONTRACT_ADDRESS&gt;</code> (hexadecimal)</dt>
  <dd>The Ethereum address of the NFT smart contract</dd>
  <dt><code>&lt;TOKEN_ID&gt;</code> (adecimal)</dt>
  <dd>The <a href="https://eips.ethereum.org/EIPS/eip-721">EIP-721 token ID</a></dd>
  <dt><code>&lt;BLOCK&gt;</code> (decimal or <code>latest</code>)</dt>
  <dd>The block number (height) or <code>latest</code> tag</dd>
</dl>

The block number is an optional attribute identifying a snapshot state in case of NFTs with mutable data URIs.
A value of `latest` addresses the most recently mined block.
If omitted, the gateway can choose to query from any block on which the data URI function returns a non-empty string.

To form an URI of the NFT scheme the attributes above are combined according to this structure:

> nft://`<CHAIN_ID>`\[:`<BLOCK>`\]/`<CONTRACT_ADDRESS>`/`<TOKEN_ID>`\[/`<FILENAME>`\]

Sections in square brackets are optional and can be omitted.

The optional `<FILENAME>` suffix does not serve any addressing purposes.
It shall usually be composed of the [URI component encoded](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent) [EIP-721 name metadata](https://eips.ethereum.org/EIPS/eip-721) and the canonical file extension.

#### Example URI

The infamous Beeple NFT is identified by the following nft scheme URI:

> nft://1/0x2a46f2ffd99e19a89476e2f62270e0a35bbf0756/40913/EVERYDAYS%3A%20THE%20FIRST%205000%20DAYS.jpg

## URI Resolution

### Retrieve the token data URI

- Query the addressed blockchain via an archive node
- read dataUri of the token on the specified block

### Respond on-chain data

If the retrieved URI is of the `data` scheme and the data is in SVG format it is subject to [SVG resource inlining](./SVG_RESOURCE_INLINING.ms).
If the data is in any other format it is written to the response body without modification.

### Respond data from decentralized storage

If the retrieved data URI is of the `ipfs` scheme, its must be resolved via [IPFS](https://ipfs.io).

### Respond data from http servers

If the retrieved data URI is of the `http` scheme, the content is fetched via http and written to the response body without modification.

## Content Caching

The content of NFT URIs can be cached according to the following rules:

- If the `<BLOCK>` URI component is specified as a block number, caches can treat the content as [immutable](https://datatracker.ietf.org/doc/html/rfc8246).
- If the `<BLOCK>` URI component has the value of `latest` and the retrieved content has some cache control information attached to it, e.g., via a cache-control http header, caches have to obey these rules.
- If the `<BLOCK>` URI component has the value of `latest` and there is no cache control information available, caches must assign an expiration time lower or equal to the average block time of the token's host chain.
- If no `<BLOCK>` URI component is specified and the retrieved content has some cache control information attached to it, these rules have to obeyed.
- If no `<BLOCK>` URI component is specified and there is no cache control information available, caches may assign a heuristic expiration time to the content.

_Note:_ The `<FILENAME>` URI component has no relevance for caching.

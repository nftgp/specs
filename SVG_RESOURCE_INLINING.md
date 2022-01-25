# SVG Resource Inlining Specification

This specification describes simple transformations for [SVG](https://www.w3.org/TR/SVG/) documents with the goal of ensuring consistent SVG image rendering results across different environments.

The SVG format allows use of external resources, like fonts, other SVG files, or raster images, via URI references.
This enables composition and allows for compact SVG documents that reference external resources rather than including the entire graphic information in a single large file.

Unfortunately, browsers do not resolve external resources for SVGs that are rendered with HTML `<img>` elements due to security restrictions.
Similarly, most server-side image thumbnail generates do not support SVGs with external references either.
This situation renders the use of external resources in SVG impractical for a large range of scenarios.

The specification first defines the locations within an SVG document at which URIs to external resources subject to inlining may appear.
Afterwards, it restricts the inlining candidate URIs based on their scheme.
Finally, it describes the process of inlining data of a resource identified by a URI.

## URI locations that are subject to resolution

#### `href` attributes

Values of any SVG [href attribute](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/href) are subject to resolution by the gateway.

#### css `url()` functions

Additionally, the gateway must resolve the arguments of [css url() functions](https://developer.mozilla.org/en-US/docs/Web/CSS/url).

URIs appearing anywhere else in the SVG document are not resolved but kept unmodified.

## URI schemes that are subject to resolution

#### http(s)

URIs with the `http` or `https` schemes (starting with `http://` or `https://`) are resolved with via http.

#### ipfs

URIs with the `ipfs` scheme (starting with `ipfs://`) are resolved via [IPFS](https://ipfs.io).

#### nft

URIs with the `nft` scheme (starting with `nft://`) are resolved according to the [NFT URI specifications](./NFT_URI_SCHEME.md).

URIs of any other scheme are not resolved but kept unmodified.

## Resolution and inlining process

Any URI that is subject to resolution as per the previous sections must be replaced with a new [data scheme](https://en.wikipedia.org/wiki/Data_URI_scheme) URI.

This happens by retrieving the linked resource data using the associated protocol (http, ipfs, or nft).
The fetched resource data must represented by a data URI with base64 encoding.
This data URI is inserted into the SVG document replacing the original external resource URI.

In case of unsuccessful retrieval of an external resource, the corresponding URI must be kept unmodified in SVG document.

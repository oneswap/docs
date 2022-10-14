# NFT Metadata Standards

How to add rich metadata to your CRC721 or CRC1155 NFTs?

Providing asset metadata allows applications like `OneSwap` to pull in rich data for digital assets and easily display them in-app. Digital assets on a given smart contract are typically represented solely by a unique identifier (e.g., the `token_id` in CRC721), so metadata allows these assets to have additional properties, such as a name, description, and image.


## Implementing Token URI

For `OneSwap` to pull in off-chain metadata for `CRC721` and `CRC1155` assets, your contract will need to return a `URI` where we can find the metadata. To find this `URI`, we use the `tokenURI` method in `CRC721` and the `uri` method in `CRC1155`. First, let's look closely at the `tokenURI` method in a demo contract.

```solidity
/**
 * @dev Returns an URI for a given token ID
 */
function tokenURI(uint256 _tokenId) public view returns (string) {
  return Strings.strConcat(
      baseTokenURI(),
      Strings.uint2str(_tokenId)
  );
}
```

The `tokenURI` function in your `CRC721` or the `uri` function in your `CRC1155` contract should return an `HTTP/HTTPS URL` or `IPFS URL`. When queried, this `URL` should in turn return a `JSON` blob of data with the metadata for your token.

See the section on **IPFS URIs** below for how to handle decentralized metadata URIs.


## Metadata Structure

`OneSwap` supports metadata that is structured according to the [official CRC721 metadata standard](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md) or the [Enjin Metadata suggestions](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1155.md#erc-1155-metadata-uri-json-schema).

Here's an example of metadata for one collection on `OneSwap`:

```
{
  "name": "Asset Metadata",
  "description": "This is metadata of your NFT token", 
  "external_url": "https://www.oneswap.net/images/common/logo-all-white.svg", 
  "image": "", 
  "attributes": [ ... ], 
}
```

Here's how each of these properties work:

| property         | description                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|:---------------- |:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| image            | This is the URL to the image of the item. Can be just about any type of image, and can be `IPFS URL` or paths. We recommend using a 350 x 350 image.                                                                                                                                                                                                                                                                                                           |
| image_data       | Raw `SVG` image data, if you want to generate images on the fly (not recommended). Only use this if you're not including the `image` parameter.                                                                                                                                                                                                                                                                                                                |
| external_url     | This is the `URL` that will appear below the asset's image on `OneSwap` and will allow users to leave `OneSwap` and view the item on your site.                                                                                                                                                                                                                                                                                                                |
| description      | A human readable description of the item. Markdown is supported.                                                                                                                                                                                                                                                                                                                                                                                               |
| name             | Name of the `NFT`.                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| attributes       | These are the attributes for the `NFT`, which will show up on the `OneSwap` page for the `NFT`. (See below)                                                                                                                                                                                                                                                                                                                                                    |
| background_color | Background color of the `NFT` on `OneSwap`. Must be a six-character hexadecimal *without* a pre-pended #.                                                                                                                                                                                                                                                                                                                                                      |
| animation_url    | A `URL` to a multi-media attachment for the `NFT`. The file extensions GLTF, GLB, WEBM, MP4, M4V, OGV, and OGG are supported, along with the audio-only extensions MP3, WAV, and OGA. Animation_url also supports HTML pages, allowing you to build rich experiences and interactive NFTs using JavaScript canvas, WebGL, and more. Scripts and relative paths within the HTML page are now supported. However, access to browser extensions is not supported. |
| youtube_url      | A URL to a YouTube video.                                                                                                                                                                                                                                                                                                                                                                                                                                      |


## Attributes

To add a little bit of highlight to your items, we also allow you to add custom "attributes" to your metadata that will show up underneath each of your assets.

To generate those attributes, the following array of attributes was included in the metadata:

```
... {
"attributes": [
    {
      "trait_type": "Base", 
      "value": "Starfish"
    }, 
    {
      "trait_type": "Eyes", 
      "value": "Big"
    }, 
    {
      "trait_type": "Mouth", 
      "value": "Surprised"
    }, 
    {
      "trait_type": "Level", 
      "value": 5
    }, 
    {
      "trait_type": "Stamina", 
      "value": 1.4
    }, 
    {
      "trait_type": "Personality", 
      "value": "Sad"
    }
  ]
}
```

Here `trait_type` is the name of the trait, while `value` is the value of the trait.

*A couple of important notes when coming up with your attributes! You should include string attributes as strings (remember the quotes), and numeric properties as either floats or integers so that OneSwap can display them appropriately. String properties should be human-readable strings.*


## Metadata Storage

You're welcome to deploy your metadata API however you see fit: you can host it on IPFS, cloud storage, or your own servers.


### IPFS

OneSwap supports the storage of NFT metadata in decentralized file networks, so that they can’t be modified by a central party.

If you use [IPFS](https://ipfs.io/) to host your metadata, your URL should be in the format like this: `ipfs://<hash>`

For example, `ipfs://QmTy8w65yBXgyfG2ZBg5TrfB2hPjrDQH3RCQFJGkARStJb`.

If you plan to store on IPFS, we recommend [Pinata](https://pinata.cloud/) for easily storing data. Here's a [tutorial](https://blog.chain.link/build-deploy-and-sell-your-own-dynamic-nft/) by Chainlink for getting started.

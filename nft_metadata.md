# NFT元数据标准

如何在你的CRC721或CRC1155 NFT 中添加丰富的元数据？

为了让`OneSwap`能够方便的拉取、展示你的NFT Token，你的CRC721 NFTs或者CRC1155 NFTs除了提供唯一的标识符（例如：CRC721 中的`token_id`）之外，还需要提供额外的元数据属性，比如：名称、描述和图像。


## 实现token URI

对于`OneSwap`来说，要拉取`CRC721`和`CRC1155`资产的链外元数据，你的合约需要返回一个我们可以找到元数据的`URI`。我们调用`CRC721`的`tokenURI`方法和`CRC1155`的`uri`方法去查找这个 `URI`。首先，让我们看一下一个demo合约的`tokenURI`方法。

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

你的`CRC721`中的`tokenURI`函数或`CRC1155`合约中的`uri`函数应该返回一个`HTTP/HTTPS URL`或`IPFS URL`。调用这个`URL`应该返回你的NFT token的元数据，数据格式必须是`JSON`格式。

关于如何处理去中心化的元数据 URI，见下面关于 **IPFS URI** 的部分。


## 元数据结构

`OneSwap`支持根据 [官方 CRC721 元数据标准](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md) 或  [Enjin 元数据建议](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1155.md#erc-1155-metadata-uri-json-schema) 进行结构化的元数据。

下面是`OneSwap`上一个系列的元数据的例子。

```
{
  "name": "Asset Metadata",
  "description": "This is metadata of your NFT token", 
  "external_url": "https://www.oneswap.net/images/common/logo-all-white.svg", 
  "image": "", 
  "attributes": [ ... ], 
}
```

以下是这些属性的各自工作原理：

| 属性             | 工作原理                                                     |
| :--------------- | :----------------------------------------------------------- |
| image            | 这是该NFT图像的`URL`。可以是几乎任何类型的图片，可以是`IPFS URL`或其他路径。我们建议使用 350 x 350 的图片。 |
| image_data       | 原始的`SVG`图像数据，如果你想即时生成图像（不推荐）。只有在不包含`image`参数时才使用这个。 |
| external_url     | 这是出现在`OneSwap`上资产的图片下面的`URL`，将允许用户离开`OneSwap`，在你的网站上查看该项目。 |
| description      | 该`NFT`的可读描述。支持 Markdown。                           |
| name             | 该`NFT`的名称。                                              |
| attributes       | 这些是该`NFT`的属性，它们将显示在`OneSwap`上`NFT`的详情页面上。(见下文) |
| background_color | 该`NFT`在`OneSwap`上的背景颜色。必须是六位数的十六进制，*没有*预留 #。 |
| animation_url    | 一个指向该`NFT`的多媒体附件的`URL`。支持GLTF、GLB、WEBM、MP4、M4V、OGV 和 OGG等文件扩展名，以及纯音频扩展名MP3、WAV 和 OGA。Animation_url 也支持HTML页面，允许你使用 JavaScript 画布、WebGL 等建立丰富的体验和互动的NFTs。现在支持HTML页面内的脚本和相对路径。然而，不支持对浏览器扩展的访问。 |
| youtube_url      | 一个指向YouTube视频的 URL。                                |


## 属性

为了让您的项目更有魅力，我们支持在每个资产下方展示元数据中添加自定义“属性”。

为了生成这些属性，在元数据中包含了以下属性列表。

```
...
{
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

这里`trait_type`是属性的名称，`value`是属性的值。

*在设置属性时需要注意几点！你应该把字符串属性写成字符串（记住引号），
把数字属性写成浮点数或整数，这样 OneSwap 就可以正确地显示它们。
字符串属性应该是人类可读的字符串。*


## 元数据存储

你可以把元数据托管在IPFS、云存储或你自己的服务器上。


### IPFS

OneSwap 支持在去中心化的文件网络中存储NFT元数据，这样它们就不会被某个中心化的机构修改。

如果你使用 [IPFS](https://ipfs.io/) 来托管你的元数据，你的 URL 应该是 `ipfs://<hash>` 这样的格式。

例如，`ipfs://QmTy8w65yBXgyfG2ZBg5TrfB2hPjrDQH3RCQFJGkARStJb`。

如果你打算在 IPFS 上存储，我们推荐使用 [Pinata](https://pinata.cloud/) 来轻松存储数据。这里有一个 Chainlink 的[入门教程](https://blog.chain.link/build-deploy-and-sell-your-own-dynamic-nft/)。

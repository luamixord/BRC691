# BRC691

We propose a new standard for ordinals collections to combine traditional pfp and on-chain rune miner nft with multi layered recursive inscriptions. This standard provides a more intriguing on-chain features combining the rune miner. The pfp uses a method similar to BRC69 but is more flexible which suport dynamic trait, such as gif format. With the recursive inscriptions, BRC691 integrates the on-chain rune miner with pfp nft. The miner hashrate is determined by the attributes value of pfp nft and the blockchain data, such as blockheight, inscription id and so on.

---

## Context

With the upcoming rune protocol, some rune mining collection have been launched, but these nft collections are all the same pictures with no features and boring patterns. At the same time, some protocols such as BRC69/BRC333 have reduced the inscription fee of pfp nft using recursive inscriptions. To make rune nft more interesting and reduce inscription fee, we can improve the current standard for launching pfp-rune ordinals collections.

---

## Idea

We propose a new standard for launching pfp-rune ordinals collection. This standard combine traditional pfp and on-chain rune miner nft while achieving a 90%+ optimization of block space. The process involves six steps:

1. Inscribing the images of the traits on-chain
2. Inscribing the BRC691 collection traits deployment JSON
3. Inscribing the BRC691 collection pfp iframe & rune miner html
4. Inscribing the BRC691 collection  iframe & miner js
5. Inscribing the BRC691 collection main css
6. Inscribing the BRC691 asset with the mint operation
   
All these processes can be conducted without needing an external indexer, as long as the collection creators release the official list of inscriptions for their collections, as currently required. Moreover, the images and miner data will be automatically rendered on all front-end interfaces that have already implemented [Recursive Inscriptions](https://github.com/ordinals/ord/pull/2167), eliminating the need for additional steps.

---

## Operations

### Deploy BRC691

Once the images of the traits that comprise the collection are inscribed on-chain, we can inscribe the collection deploy JSON in the deploy operation.

The deploy operation is a JSON/Text inscription that contains general information and rune miner components hashrate about the collection and an array of the inscription IDs of the traits. The deploy inscription serves as the reference and the definitive source for the traits.

*Example of a collection deploy json:*

```javascript
{
   "p":"BRC691",
   "op":"deploy",
   "collection":{
      "slug":"runedoge",
      "name":"RuneDoge",
      "description":"RuneDoge Testing Collection",
      "creator":"lumixord",
      "supply":10000
   },
   "attributes":[
      "hand": [
        ["c778a8aced63179dd753ec919479f7bfa334a89c758c6cc14594d416e83fab00i0", 20]
      ],
      "head": [
        ["f5f27185fb342d4d4ef7f1edbd8332c5d86c7c6eabb6d5d434545ab0b6aeef2ei0", 14]
      ],
      "eye": [
        ["88b27c320551339c8ac0ea30a3eede6bcc31d00f6f8b66a49bd6595f6b6274a4i0", 10]
      ],
      "mouse": [
        ["c376d49911a19cd4492ffddfe1ef03695bd2aec9f55dc3299286b36ed7feefc6i0", 15]
      ],
      "rune": [
        ["9aad7f774d248e575bca71870ab9953042791926c354dbe9738b1fd8c5f7c1b6i0", 16]
      ],
     // ... More traits inscription IDs
   ]
}
```

| Key        | Required | Description                                                  |
| ---------- | -------- | ------------------------------------------------------------ |
| p          | YES      | Protocol: Helps other systems identify and process BRC691 events |
| op         | YES      | Operation: Type of event (Deploy, Mint)             |
| slug       | YES      | Slug: Identifier of the collection. Not enforced if no indexer implemented |
| name       | NO       | Name: Human readable name of the collection                  |
| supply     | NO       | Supply: Supply of the collection. Not enforced if no indexer implemented |
| attributes | YES      | Array of the inscription IDs & hashrate of the traits that will generate the final assets |

### iframe html of BRC691

The iframe html inscription is a js file which render the pfp canvas html and on-chain rune miner container html and the logic connecting them. The iframe html inscription inscription is a recursive inscription that points back to the Deploy inscription to receive the traits' inscription IDs and ultimately render the asset. And contains 2 sub-inscriptions which is main.css inscription and main.js inscription.

The main.css inscription is an external style sheet which render the iframe html inscription, both for pfp gif image & dynamic rune miner.

The main.js inscription is an external javescript which compile the pfp trait layers to combine a pfp image and provide the mint logic and data for the on-chain rune miner.

Down belove we propose a simple render image logic and rune miner parameter acquisition. The mining algorithm needs to be implemented according to the specific design of the miner project.

``````javascript
/*
{
  "p": "BRC691",
  "s": "runedoge"
}
*/

// render doge pfp
function drawDoge(box, urls) {
    let ctx = box.getContext("2d");
    for (let i = 0; i < urls.length; i++) {
        let img = new Image();
        img.src = urls[i];
        img.onload = () => {
            ctx.drawImage(img, 0, 0);
        }
    }
}

drawDoge(document.getElementById('canvas'), [trait7, trait6, trait5, trait4, trait3, trait2, trait1, trait0]);

// fetch blockheight
async function getBlockHeight() {
	try {
		let response = await fetch("/blockheight");
		if (!response.ok) throw new Error('Network error on /blockheight');
		let blockHeight = await response.text();
		return blockHeight;
	} catch (error) {
		console.error('Fetch error:', error);
		return '840000';
	}
}

// get inscription id
let insId = window.location.pathname.split("/")[2]?window.location.pathname.split("/")[2]:"runerunerunerunerunerunerunerunerunerunerunerunerunerunerune";

``````

### Mint BRC69

The Mint operation uses an HTML type inscription that stores the index of the traits used to generate the final asset and points back to the iframe inscription, in a single line. This approach allows any front-end with Recursive Inscription to automatically render the image and miner data using on-chain inscribed data.

``````html
<script t="7,6,5,4,1,2,3" src="/content/<compile inscription id>" m='{"p":"BRC691" "op":"mint" "s":"runedoge" "id":"0"}'></script>
``````

| Key  | Required | Description                                                  |
| ---- | -------- | ------------------------------------------------------------ |
| t    | YES      | Traits: index of the traits used to generate the asset found in the "attributes" array of the deploy inscription |
| src  | YES      | Source: Recursive inscription pointer to the iframe inscription |
| m    | NO       | Metadata: metadata used to track BRC691 operations. Optional if you want your mints operation to be stealth |

---

## Impact

Implementing the BRC691 standard can add dynamic effects for the BRC69 protocol collections and provide integration of on-chain mining functions with pfp. Because the unique feature images are written only once in the Deploy transaction, the asset consists of an HTML file that only references the features in a single line of approximately 80 bytes. Any frontend with a recursive inscription implementation can use on-chain deployment of inscriptions to render images without any additional steps.

---

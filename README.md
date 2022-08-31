# Level 9 - NFT-Tutorial 

Déployer un projet NFT sur Ethereum

## Vous préférez une vidéo ?
Si vous préférez apprendre à partir d'une vidéo, nous avons un enregistrement disponible de ce tutoriel sur notre YouTube. Regardez la vidéo en cliquant sur la capture d'écran ci-dessous, ou allez-y et lisez le tutoriel !

[![Cryptocurrency Tutorial](https://i.imgur.com/klHysek.png)](https://www.youtube.com/watch?v=uwnAXAsd428 "NFT Tutorial")

## Conditions préalables

- Configurer un métamasque (Beginner Track - [Level-4](https://github.com/LearnWeb3DAO/Crypto-Wallets))
- Vérifiez si votre ordinateur dispose de Node.js. Si ce n'est pas le cas, téléchargez-le à partir de [here](https://nodejs.org/en/download/)

---

## Build

### Smart Contract

Pour construire le contrat intelligent, nous utiliserons [Hardhat](https://hardhat.org/).
Hardhat est un environnement et framework de développement Ethereum conçu pour un développement complet. En d'autres termes, vous pouvez écrire vos contrats intelligents, les déployer, exécuter des tests et déboguer votre code.

- Pour configurer un projet Hardhat, ouvrez un terminal et exécutez ces commandes

  ```bash
  mkdir NFT-Tutorial
  cd  NFT-Tutorial
  npm init --yes
  npm install --save-dev hardhat
  ```

- Dans le même répertoire où vous avez installé Hardhat exécutez :

  ```bash
  npx hardhat
  ```

  - Sélectionnez `Créer un projet Javascript`.
  - Appuyez sur la touche Entrée pour `Hardhat Project root` la racine du projet Hardhat déjà spécifiée.
  - Appuyez sur entrée pour la question sur si vous voulez ajouter un `.gitignore`.
  - Appuyez sur la touche Entrée pour `Do you want to install this sample project's dependencies with npm ? (@nomicfoundation/hardhat-toolbox)?`

Vous avez maintenant un projet de hardhat prêt à être réalisé !

Si vous êtes sous Windows, veuillez faire cette étape supplémentaire et installer également ces bibliothèques :)

```bash
npm install --save-dev @nomicfoundation/hardhat-toolbox
```

---

## Écrire le code du contrat NFT

Installons les contrats Open Zeppelin, dans la fenêtre du terminal exécutez cette commande

```
npm install @openzeppelin/contracts
```

- Dans le dossier des contrats, créez un nouveau fichier solidity appelé NFTee.sol
- Maintenant, nous allons écrire du code dans le fichier NFTee.sol. Nous importons [Openzeppelin's ERC721 Contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol). ERC721 est la norme la plus courante pour créer des NFT's. Dans le cursus freshman, nous n'utiliserions que ERC721. Dans le cursus sophomore, vous en sauriez plus sur ERC721's. Alors ne vous inquiétez pas, si vous ne comprenez pas tout:)

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Import the openzepplin contracts
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

// GameItem est  ERC721 signifie que le contrat que nous créons importe ERC721 et suit le contrat ERC721 de openzeppelin
contract GameItem is ERC721 {

    constructor() ERC721("GameItem", "ITM") {
        // mint an NFT to yourself
        _mint(msg.sender, 1);
    }
}
```

- Compilez le contrat, ouvrez un terminal et exécutez ces commandes

```bash
npx hardhat compile
```

S'il n'y a pas d'erreur, vous êtes prêt. :)

## Configuration du déploiement

Déployons le contrat sur le réseau de test `rinkeby`. Pour ce faire, nous allons écrire un script de déploiement, puis configurer le réseau. D'abord, créez un nouveau fichier/remplacez le fichier par défaut nommé `deploy.js` dans le dossier `scripts`, et écrivez-y le code suivant:

```js
// Importez les éthers du paquet Hardhat
const { ethers } = require("hardhat");

async function main() {
  /*
Un ContractFactory dans ethers.js est une abstraction utilisée pour déployer de nouveaux smart contracts,
donc nftContract ici est une usine pour les instances de notre contrat GameItem.
*/
  const nftContract = await ethers.getContractFactory("GameItem");

  // ici nous déployons le contrat
  const deployedNFTContract = await nftContract.deploy();
  
  // attendre que le contrat soit déployé
  await deployedNFTContract.deployed();

  // imprimer l'adresse du contrat déployé
  console.log("NFT Contract Address:", deployedNFTContract.address);
}

// Appelle la fonction principale et attrape/catch les erreurs s'il y en a une.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

- Créez maintenant un fichier `.env`  dans le dossier `NFT-Tutorial`  et ajoutez les lignes suivantes. Utilisez les instructions dans les commentaires pour obtenir votre Alchemy API Key et Rinkeby Private Key. Assurez-vous que le compte à partir duquel vous obtenez votre clé privée rinkeby est approvisionné en Rinkeby Ether. Vous pouvez vous en procurer ici: [https://www.rinkebyfaucet.com/](https://www.rinkebyfaucet.com/)

```

# Aller à https://www.alchemyapi.io, sign up, create
# une nouvelle application est dans le tableau de bord et sélectionner le réseau comme Rinkeby, et remplacer "add-the-alchemy-key-url-here" avec sa key url
ALCHEMY_API_KEY_URL="ajoutez-la-alchemy-key-url-ici"

# Remplacez cette clé privée par la clé privée de votre compte RINKEBY
# Pour exporter votre clé privée depuis Metamask, ouvrez Metamask et
# aller à Account Details > Export Private Key
# Soyez conscient de ne JAMAIS mettre de l'Ether réel dans les comptes de test
RINKEBY_PRIVATE_KEY="add-the-rinkeby-private-key-here"

```

Vous pouvez penser à Alchemy comme l'AWS EC2 pour la blockchain. C'est un fournisseur de node.Il nous aide à nous connecter à la blockchain en nous fournissant des nodes afin que nous puissions lire et écrire sur la blockchain.  est ce qui nous aide à déployer le contrat sur rinkeby.

- Maintenant, nous devons installer le package `dotenv` pour pouvoir importer le fichier env et l'utiliser dans notre configuration.
  Dans votre terminal, exécutez les commandes suivantes.
  ```bash
  npm install dotenv
  ```
- Ouvrez maintenant le fichier hardhat.config.js, nous ajouterons le réseau `rinkeby` ici pour que nous puissions déployer notre contrat sur rinkeby. Remplacez toutes les lignes du fichier `hardhat.config.js` par les lignes suivantes

```js
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const ALCHEMY_API_KEY_URL = process.env.ALCHEMY_API_KEY_URL;
const RINKEBY_PRIVATE_KEY = process.env.RINKEBY_PRIVATE_KEY;

module.exports = {
  solidity: "0.8.9",
  networks: {
    rinkeby: {
      url: ALCHEMY_API_KEY_URL,
      accounts: [RINKEBY_PRIVATE_KEY],
    },
  },
};
```

- Pour déployer dans votre terminal, tapez :
  ```bash
      npx hardhat run scripts/deploy.js --network rinkeby
  ```
- Enregistrez l'adresse du contrat NFT qui a été imprimée sur votre terminal dans votre bloc-notes, vous en aurez besoin.

## Vérifier sur Etherscan

- Aller à [Rinkeby Etherscan](https://rinkeby.etherscan.io/) et rechercher l'adresse qui a été imprimée.
- Si l'`address` s'ouvre sur etherscan, vous avez déployé votre premier NFT 🎉
- Accédez aux détails de la transaction en cliquant sur le hash de la  transaction, vérifier qu'un jeton a été transféré à votre adresse

# **Lancer et vérifier un NFT avec Foundry**

Dans le tutoriel vidéo ci-dessus, vous avez mis un NFT sur Opensea en utilisant l'environnement Remix. C'est bien, mais Remix n'est pas idéal pour une utilisation en production. Dans ce chapitre, nous vous montrerons comment:

  1. Configurer Foundry pour le NFT
  2. Déployez-le sur le réseau de test Sepolia et vérifiez-le sur Etherscan

Si vous avez fait les exercices, vous devriez déjà avoir installé Foundry, alors Créez un nouveau dossier ; appelez-le foundry-nft, accédez-y et exécutez la commande 
```bash 
forge init 
``` 
dans le dossier vide. **Copiez et collez** le code Voici le code pour créer le NFT. Renommez le fichier **_Counter.sol_** dans le répertoire « src » en FoundryNFT.sol et collez ce code.

```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "openzeppelin-contracts/contracts/token/ERC721/ERC721.sol";

contract FoundryNFT is ERC721 {
    uint256 public totalSupply = 0;

    constructor() ERC721("FoundryNFT", "FNFT") {}

    function mint() 
            external 
            payable {
                    // you can add a price check here if you like
                    totalSupply++;
                    _mint(msg.sender, totalSupply);
    }
}

```

**Installation d'Openzeppelin** Nous importons et héritons du contrat Openzepplin ERC721, donc pour l'installer, utilisez la commonde qui suit :

```bash

forge install Openzeppelin/openzeppelin-contracts --no-commit

```

**Utilisation des remappages** Venant d'un environnement Remix, vous remarquerez que le chemin d'importation d'OpenZeppelin diffère ici. Remix ne stocke pas les bibliothèques au même endroit que Foundry. Cependant, Foundry fournit des remappages pour permettre aux recherches de fichiers lors de l'importation d'être redirigées vers un emplacement différent. Pour voir tous les remappages disponibles, nous exécutons  **forge remappings**. Nous obtenons ceci :

```bash

ds-test/=lib/forge-std/lib/ds-test/src/
forge-std/=lib/forge-std/src/
openzeppelin-contracts/=lib/openzeppelin-contracts/

```

Nous pouvons voir le remappage de OpenZepplin, nous n'avons donc pas besoin de spécifier le chemin complet vers les contrats OpenZeppelin. Nous pouvons utiliser  **openzeppelin-contracts/contracts/token/ERC721/ERC721.sol**  au lieu de spécifier d'abord le répertoire « _lib/_ ». **Ne pas utiliser de remappages** Si nous n'utilisions pas de remappages de forge, nous devrions spécifier le chemin complet vers nos fichiers ou répertoires. Par exemple, pour le fichier ERC721.sol, nous ferions quelque chose comme ceci pour l'importer.

```solidity
import "lib/openzeppelin-contracts/contracts/token/ERC721/ERC721.sol";
```
Pour confirmer que cela est toujours valide, exécutez  
```bash
forge build
```  
et voyez s'il compile. Et c'est le cas

```bash

[⠰] Compiling...
[⠃] Compiling 1 files with 0.8.17
[⠊] Solc 0.8.17 finished in 1.38s
Compiler run successful

```
Annulez maintenant les modifications pour revenir à ce qu'elles étaient auparavant.

# Générer le fichier remappings.txt en une seule étape

Cela peut être fait avec l'opération suivante

```bash
forge remappings > remappings.txt
```
**Changer les noms de fichiers** Nous permettons de configurer davantage l'endroit où pointent les remappages en créant un fichier remappings.txt dans le répertoire racine du projet et en ajoutant cette ligne  **@openzeppelin=lib/openzeppelin-contracts/contracts**  au fichier. Après avoir fait cela, nous pouvons utiliser  **import “@openzeppelin/token/ERC721/ERC721.sol”** ; pour importer le jeton ERC721 au lieu d'écrire explicitement le chemin d'accès complet au fichier. Ce remappage peut être configuré dans le fichier remappings.txt s'il pointe vers le bon chemin. 

**Obtenir une clé Etherscan** Lorsque nous déployons notre NFT, nous avons besoin d'une API Etherscan pour vérifier le contrat. Cela nous permettra de nous connecter facilement à Etherscan pour vérifier le contrat intelligent à l'aide de forge sans aller sur le site Web d'Etherscan et suivre le processus. Rendez-vous sur  [Etherscan](https://etherscan.io), connectez-vous et créez une clé API.
![alt](https://www.rareskills.io/wp-content/uploads/2024/09/FFZD9yO.png)

Nous avons créé _U3D9IS6Z5E872VFS7M7AWR1SBA8786ZZ3Y_ comme clé API. Nous utiliserons cette clé API plus tard.

## Créer un portefeuille jetable

**N'utilisez jamais les clés privées d'un portefeuille matériel ou de tout portefeuille contenant une valeur significative.** Pour déployer le contrat NFT sur le réseau de test, nous avons besoin d'une clé privée avec de l'Ether de test pour signer la transaction. Par souci de simplicité, nous avons créé un portefeuille jetable et l'avons financé avec de l'Ether sepolia. Voici la clé privée du portefeuille;
##### **787ea4ec95ab4f4e66c4c4c387cd0b5fbbec84a9293db485fa5f86f490c157d4** 

(Cela ne devrait pas être utilisé car cela est considéré comme déjà compromis.)

## Placez la clé Etherscan et la clé privée du portefeuille dans vos variables d'environnement

Maintenant que nous avons à la fois notre clé API et notre clé privée, l’étape suivante consiste à créer un fichier .env dans le répertoire racine du projet et à y ajouter celui-ci.

```bash

ETHERSCAN_KEY=U3D9IS6Z5E872VFS7M7AWR1SBA8786ZZ3Y #you can replace this with your API key
PRIVATE_KEY=787ea4ec95ab4f4e66c4c4c387cd0b5fbbec84a9293db485fa5f86f490c157d4

```
**Assurez-vous que votre fichier .env est inclus dans votre .gitignore afin de ne pas publier accidentellement vos clés privées !**


## Exécutez ce script
Exécutez ce script pour déployer et vérifier le contrat NFT sur sepolia.
```bash

source .env #load the environment variables

forge create --rpc-url https://rpc2.sepolia.org --private-key $PRIVATE_KEY --etherscan-api-key $ETHERSCAN_KEY --verify src/FoundryNFT.sol:FoundryNFT 

```
**Vue sur Etherscan** Nous avons déployé avec succès notre contrat NFT sur le testnet sepolia.
* [Le hachage de la transaction](https://sepolia.etherscan.io/tx/0x292d6d15a8e7131306d52ad54b9c09464cc17cd2db94a2a707c4dd494d6382af)
* [Le contrat vérifié](https://sepolia.etherscan.io/address/0xfd967dc8e5a7451e7a1f1ee9321fb1b38eb0dcd6#code)





# **Modificateurs**

 la construction de **_onlyOwner_** est si courante que nous allons lui consacrer une section.

considérons les éléments suivants

```solidity

contract Ownable {

    address public owner;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "onlyOwner");
        _;
    }

    function changeOwner(
        address newOwner
    ) 
        public 
        onlyOwner {
            owner = newOwner;
    }

}

contract HoldFunds is Ownable {
    
    function withdrawFunds() 
        public 
        onlyOwner {
            (bool ok, ) = owner.call{
                value: address(this).balance
            }("");
            require(ok, "transfer failed");
    }

    receive() 
        external 
        payable {

    }
}

```

Regardons la star du spectacle ici :
```solidity

modifier onlyOwner() {
    require(msg.sender == owner, "onlyOwner");
    _;
}

```
Cela signifie simplement **_« l'exécuter le code qui est avant le trait de underscore (_), puis exécuter la fonction »_**.

Il s’agit d’un moyen pratique de « modifier » le comportement d’une fonction, d’où le nom de « modificateurs ».

Notez que même si HoldFunds a hérité d'Ownable, il n'a remplacé aucune fonction. L'héritage dans Solidity est plus souvent un mécanisme permettant d'inclure un comportement que de définir une sorte de polymorphisme (ne vous inquiétez pas si vous ne savez pas ce que c'est).

Donc, dans ce cas, si vous souhaitez que votre contrat intelligent dispose de fonctions pratiques et agréables à l'intérieur, vous pouvez importer d'un autre contrat qui fournit les fonctionnalités dont vous avez besoin.

Ceci n'est pas strictement nécessaire. Vous pouvez mettre tout votre code dans un seul gros contrat. Mais ce code serait moins lisible.

Les modificateurs peuvent être utilisés pour d'autres choses que la vérification de la propriété, mais la vérification des propriétés sont le cas d'utilisation le plus courant.

**Ne modifiez pas l'état à l'intérieur des modificateurs.**  Bien que Solidity vous permette de le faire, cela rend le code plus difficile à comprendre. Ceci est considéré comme une mauvaise pratique.






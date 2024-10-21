# **Chaine de Caractéré (String)**


Les chaînes de caractére sont des types de taille de tableau dynamique d'UTF-8  encode en  bytes qui constituent également un tableau de bytes de taille dynamique. Les deux sont interchangeables en utilisant simplement string() pour convertir les chaînes cararactéré en bytes et bytes() pour convertir les bytes en chaînes caractére respectivement. Cela nous aide grandement à effectuer des opérations sur des chaînes caractére comme nous pouvons le faire avec d'autres langages de programmation. Cependant, comme les chaînes sont codées en UTF-8, si un tel caractère nécessite plus d'un octet, cela augmente la difficulté de manipulation des chaînes caractére.

Étant donné que les chaînes sont des tableaux, elles ont besoin du modificateur **calldata** ou **memory** lorsqu'elles sont transmises aux **fonctions**, et du modificateur **memory** lorsqu'elles sont renvoyées.

Pour vérifier la longueur d’une chaîne :

```solidity

function stringLength(
    string memory input
) 
    public 
    pure 
    returns (uint256) {
        return bytes(input).length;
        // input.length won't work
}


```

Cela ne signifie **PAS** combien de caractères se trouvent dans la chaîne, mais quelle est la longueur du tableau en byte. Les caractères Unicode occupent plus d'un byte.

Pour accéder au caractère d'une chaîne :

```solidity


function characterOfString(
    string memory input, 
    uint256 index
) 
    public 
    pure 
    returns (string memory) {
        bytes memory char = new bytes(1);
        char[0] = bytes(input)[index];
        return string(char);
}


```

Gardez à l'esprit que cela ne fonctionnera que si la chaîne entière de caractére est au format ASCII. Si nous transmettons des caractères Unicode qui occupent plus d'un byte, par exemple « **你好** », le code plantera.

Obtenir un caractère à partir d'une chaîne caractère est un peu plus difficile que de simplement l'indexer comme JavaScript ou Python, car nous devons initialiser un tableau de chaînes de longueur 1, puis insérer le caractère que nous voulons obtenir dans le nouveau tableau de chaînes. C'est ce que fait le code ci-dessus.

Solidity prend en charge les chaînes Unicode :

```solidity


contract Message {
    // no need for a getter function, this is public
    // note the "unicode" modifier
    string public message = unicode"你好👋ℝ𝔸ℝ𝔼𝕊𝕂𝕀𝕃𝕃𝕊";
}

```

Ce qui est légèrement trompeur dans Solidity, c'est que nous utilisons des « chaînes caractére» pour représenter des données hexadécimales si le modificateur hex est utilisé.les caractéres suivantes convertissent l'encodage hexadécimal de « helloworld » en helloworld.

```solidity

contract HexData {
    // not a string!
    bytes hexData = hex"68656C6C6F776F726C64";
    // returns "helloworld"
    function getMessage() 
        public 
        view 
        returns (string memory) {
            return string(hexData);
    }
}


```
La concaténation des chaînes caractéres est facilitée dans **Solidity 0.8.12** avec l'ajout de string.concat(). En dessous de cette version, string.concat() n'est pas disponible.

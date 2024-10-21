# **Les Variables immuables**

Si vous n'avez pas l'intention de modifier une variable, il est préférable d'être explicite sur vos intentions. Solidity a un mot-clé pour cela.

```solidity

contract ExampleContract {

    string immutable public name;

    constructor(string memory _name) {
        name = _name;
    }
}

```
**Si une variable est définie, modifié uniquement dans le constructeur et ne serai jamais mise à jour, elle doit être immuable**

Si vous essayez d'écrire dans une variable immuable, le code ne sera pas compilé.

```solidity

contract ExampleContract {

    string immutable public name;

    constructor(string memory _name) {
        name = _name;
    }

    // ERROR: Cannot compile
    function cannotChangeTheName(string calldata _newName) 
            external {
                name = _newName;
    }
}

```
**Exercices pratiques**

[Immuable](https://github.com/RareSkills/Solidity-Exercises/tree/main/Immutable)


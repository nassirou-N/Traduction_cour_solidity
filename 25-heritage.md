# **Héritage**

Implémenter un contrat ERC20 à partir de zéro à chaque fois serait sans doute fastidieux. Solidity se comporte aussi comme un langage orienté objet et permet l'héritage. Voici un exemple minimal.
```solidity

contract Parent {
    function theMeaningOfLife() 
        public 
        pure 
        returns (uint256) {
            return 42;
    }
}

contract Child is Parent {

}

```

Déployez sur Remix, mais dans la liste déroulante, choisissez le contract Child à déployer, pas le contract Parent.

![alt](https://www.rareskills.io/wp-content/uploads/2024/09/Inheritance1.png)

Même si contract Child est vide, nous voyons les fonctions dans le contract Child
![alt](https://www.rareskills.io/wp-content/uploads/2024/09/Inheritance2.png)

lorsqu'un contract hérite d'un autre contract avec le mot cle << **is** >>, il hérite de toutes ses fonctionnalités.

Comme d'autres langages de programmation orientés objet, les fonctions peuvent être surchargées (overridden). Voici la construction permettant de modifier la valeur.

```solidity

contract Parent {
    function theMeaningOfLife() 
        public 
        pure 
        virtual 
        returns (uint256) {
            return 42;
    }
}

contract Child is Parent {
    function theMeaningOfLife() 
        public 
        pure 
        override 
        returns (uint256) {
            return 43;
    }
}

```
![alt](https://www.rareskills.io/wp-content/uploads/2024/09/Inheritance3.png)

Notez que tous les fonctions **virtual (_virtuelles_)**  peuvent être modiffier par le contract qui l'herité mais en respectant certains régle. Si vous essayez de remplacer une fonction qui n'est pas virtuelle dans le contract parent, le code ne sera pas compilé.

De plus, lorsqu'une fonction est modiffier par le contract qui herite du contract parent, elle doit correspondre exactement, à la fois au niveau du nom, des arguments et du type de retour.

```solidity

contract Parent {
    function theMeaningOfLife()
        public
        pure
        virtual
        returns (uint256) {
            return 42;
    }
}

contract Child is Parent {

    // INVALID: has different arguments
    function theMeaningOfLife(uint256 x)
        public
        pure
        override
        returns (uint256) {
            return 42 + x;
    }

    // INVALID: has different return type
    function theMeaningOfLife(uint256 x)
        public
        pure
        override
        returns (bool) {
            return true;
    }

    // INVALID: has different name
    function theMeaningOfLif3(uint256 x)
        public
        pure
        override
        returns (uint256) {
            return 42;
    }
}

```
Solidity prend en charge l'héritage multiple

```solidity

contract Parent1 {
    function theMeaningOfLife()
        public
        pure
        virtual
        returns (uint256) {
            return 42;
    }
}

contract Parent2 {
    function hackerFavoriteNumber()
        public
        pure
        virtual
        returns (uint256) {
            return 1337;
    }
}

contract Child is Parent1, Parent2 {

}


```
Si vous vous posez la question de savoir, le cas ou les deux parents ont une fonction avec le même nom, l'enfant doit **override** sinon le comportement sera ambigu. Si vous vous retrouvez dans cette situation, vous avez probablement fait quelque chose de mal dans la conception de votre design logiciel. Ne nous engageons donc pas dans cette voie.

## Private vs Internal (privé vs interne.)

Il existe deux manières de rendre une fonction inaccessible depuis le monde extérieur: en lui donnant une visibilite privé ou interne. La distinction est simple.

Les fonctions privées et les variables ne peuvent pas être « vues » par les contrats enfants ou le contract qui herité.

Les fonctions et les variables internes peuvent être « vues » par les contrats enfants ou le contract qui herité.

```solidity

contract Parent {
    function foo()
        internal
        pure
        virtual
        returns (string memory) {
            return "foo";
    }

    // error! private functions cannot be overriden, 
    // so no point in making them virtual!
    function bar()
        private
        pure
        virtual
        returns (string memory) {
            return "bar";
    }
}


```

## The super keyword


Le mot-clé **super** signifie « appeler la fonction du parent ». Voici comment cela peut être utile.

```solidity

contract Parent {
    function foo() 
        internal 
        pure 
        virtual 
        returns (string memory) {
            return "foo";
    }
}

contract Child is Parent {

        // we have overriden foo and made it public
    function foo() 
        public 
        pure 
        override 
        returns (string memory) {
            return super.foo();
    }
}

```

Si nous n'avions pas inclus le mot-clé super ici, foo() s'appellerait lui-même et entrerait en récursivité infinie. Essayez de supprimer super et d'exécuter le code dans Remix. La transaction sera rétablie en raison de la récursivité infinie (Ethereum ne laisse pas le code s'exécuter indéfiniment, il le termine de force. Le mécanisme exact est un sujet intermédiaire pour une discussion ultérieure).

Super signifie « appeler le foo du parent, pas le mien ». Cela nous permet d'obtenir toutes les fonctionnalités de foo sans avoir à copier et coller le code.

### Appel du constructeur du parent

Solidity ne vous permettra pas d'hériter d'un contrat parent sans initialiser son constructeur. Considérez cette situation.

```solidity

contract Parent {
    string private name;

    constructor(string memory _name) {
        name = _name;
    }

    function getName() public view virtual returns (string memory) {
        return name;
    }
}

contract Child is Parent {

    // error, name hasn't been set!
    function getName() public view override returns (string memory) {
        return super.getName();
    }
}


```
La solution consiste à appeler le constructeur parent au point d'héritage.

```solidity


contract Parent {

    string private name;

    constructor(string memory _name) {
        name = _name;
    }

    function getName() 
        public 
        view 
        virtual 
        returns (string memory) {
            return name;
    }
}

//fixed
contract Child is Parent("The Beatles") {
    function getName() 
        public 
        view 
        override 
        returns (string memory) {
            return super.getName();
    }
}


```
Résumons ce que nous avons appris

* **Seules les fonctions virtuelles peuvent être surdéterminé(overriden)**
* **Les fonctions qui sont override, la fonction d'un parent doivent avoir un modificateur de override**
* **La fonction de overriding doit correspondre exactement, en termes de nom, d'arguments et de type de retour**
* **Au lieu de copier et coller le code de la fonction parent, vous pouvez utiliser le mot-clé _<< super >>_**
* **Vous pouvez hériter de plusieurs contrats**
* **Vous devez appeler explicitement le constructeur d'un parent lors de l'héritage.**

## Créez un jeton ERC20 avec peu d'effort

L'héritage, combiné à importation des etats, nous permet d'utiliser facilement les bibliothèques créées par d'autres personnes. Déployez ce contrat dans Remix et vous verrez que toutes les fonctions ERC20 qui ont été implémentées sont visible.

```solidity

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Token is ERC20("SomeToken", "symbol") {

}

```
![alt](Ihttps://www.rareskills.io/wp-content/uploads/2024/09/Inheritance4.png)

## Point à clarifié

Il y a un monde de différence entre un contrat intelligent en tant qu’objet de solidity et un contrat intelligent déployé sur la blockchain.

### Vous ne pouvez pas hériter d’un contrat déployé sur la blockchain.

Ce sont des blobs binaires qui sont en dehors. parce que la terminologie est ambiguë, certains développeurs Solidity craignent que les fonctions et les variables puissent héritées et modiffier par un contrat malveillant.  **Cela ne peut pas se produire** . Même si nous désignons le code déployé par « contrat » et le code Solidity par « contrat », ce n'est pas la même chose.

## Exercices pratiques

[HéritageOverride](https://github.com/RareSkills/Solidity-Exercises/tree/main/InheritanceOverride)

[Multi-héritage](https://github.com/RareSkills/Solidity-Exercises/tree/main/MultiInheritance)

[Super](https://github.com/RareSkills/Solidity-Exercises/tree/main/Super)

[AccesModifiers](https://github.com/RareSkills/Solidity-Exercises/tree/main/AccessModifiers)
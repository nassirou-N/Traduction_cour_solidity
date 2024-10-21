# **Structures (structs)**

Les structures en Solidity se comportent de manière similaire en C. Elles regroupent différentes variables au sein d'un seul type de données compose, ce qui peut être extrêmement utile pour organiser les données et créer des structures de données plus complexes.

Voici comment déclarer une structure dans Solidity.

```solidity
contract StructsExample {

    struct Foo {
        uint256 a;
        uint256 b;
    }
        
    Foo public myFoo;
}

```
**myFoo**  est une variable publique de type struct Foo, elle stocke à la fois  **uint256** *a*  et  **uint256** *b* . Comme vous pouvez le voir si nous le déployons dans remix, myFoo renvoie :
![alt](https://www.rareskills.io/wp-content/uploads/2024/09/myfoo.png)

Pour passer une structure dans Remix à une fonction qui prend une structure comme argument (nous en parlerons plus en détail dans un instant), encodez-la comme suit :
![alt](https://www.rareskills.io/wp-content/uploads/2024/09/Screenshot_2023-05-15_at_3_46_21_PM.png)

La fonction en question prendra par exemple **Foo** ci-dessus, qui se compose de deux variables uint256. Il peut être un peu déroutant de le formater comme un tableau, mais c'est ainsi que cela fonctionne.

Pour créer une nouvelle instance de Foo dans Solidity, encapsulez simplement les valeurs dans struct Foo.

* Foo( a , b )

Pour accéder ou affecter chaque variable individuelle dans la structure  myFoo  , utilisez la notation par points.

* **_monFoo.a_**
* **_monFoo.b_**

Pourquoi utilisons-nous des structures? Supposons que nous ayons un contrat de dépôt qui conserve le  **nom**  et  le **solde** du déposant .

```solidity
contract DepositOnly {
    mapping(address => string) public name;
    mapping(address => uint256) public balance;

    function deposit(
        string memory _name
    ) 
        external 
        payable {
            balance[msg.sender] += msg.value;
            name[msg.sender] = _name;
    }
}


```

Dans le contrat ci-dessus, le nom et le solde du déposant sont stockés dans deux   structures de données **de mappage distinctes**.

La variable d'adresse dans le mappage est répétée deux fois pour le nom et le solde du même _msg.sender_, et n'est donc pas efficace.

C'est donc ici que les structures sont utiles : nous pouvons enregistrer à la fois le nom et le solde sous une variable de structure et stocker cette variable dans un mappage de paires clé-valeur comme celui-ci.

```solidity

contract DepositOnly {
    struct Person {
        string name;
        uint256 balance;
    }
    mapping(address => Person) public depositor;

    function deposit(
        string memory _name
    ) 
        external 
        payable {
            depositor[msg.sender] = Person(_name, msg.value);
    }
}


```

Vous voyez à quel point c'est utile ? Cela rend votre code plus propre et plus efficace.

**Comment utiliser les structures**

Simple, non ? Voici la démonstration.

```solidity

contract StructsExample {

    struct Foo {
        uint256 a;
        uint256 b;
    }
    
    Foo public myFoo;

    function assignMyFoo(
        uint256 _a, 
        uint256 _b
    ) 
        public {
            myFoo = Foo(_a, _b);
    }

    function assignA(
        uint256 _a
    ) 
        public {
            myFoo.a = _a;
    }

    function accessA() 
        public 
        view 
        returns(uint256) {
            return myFoo.a;
    }
    
}


```
Si vous souhaitez passer struct Foo comme argument ou comme valeur de retour, voici quelques règles à suivre :

* **Les structures passées en arguments doivent être déclarées comme une mémoire**
* **Les structures en tant que types de retour doivent également être déclarées comme mémoire.**

Voici à quoi cela ressemble.
```solidity

contract StructsExample {

    struct Foo {
        uint256 a;
        uint256 b;
    }

    Foo myFoo;

    function passStructAsArgument(
        Foo memory foo
    ) 
        public {
            myFoo = foo;
    }

    function returnAStruct() 
        public 
        view 
        returns (Foo memory) {
            return myFoo;
    }
}


```

Il est important de noter que les structures dans Solidity  ne peuvent pas  contenir un membre de type **structs**. Par exemple, cela n'est pas autorisé
```solidity

struct Foo {
    Foo innerFoo; // disallowed
}

```
**Tableaux et mappages**

structures peuvent être utilisées comme type de valeur dans les tableaux et les mappages. Par exemple, vous pouvez créer un tableau dynamique de l'instance Foo comme ceci :
```solidity

contract StructsExample {

    struct Foo {
        uint256 a;
        uint256 b;
    }

    Foo[] public arrayFoo;

}

```
**arrayFoo**  est un tableau composé d'instances Foo.

Pour démontrer :
```solidity

contract StructsExample {

    struct Foo {
        uint256 a;
        uint256 b;
    }

    Foo[] public arrayFoo;

    function addFooToArray(
        uint256 _a, 
        uint256 _b
    ) 
        public {
            arrayFoo.push(Foo(_a, _b));
    }

    function readFooFromArray(
        uint256 _index
    ) 
        public 
        view 
        returns(Foo memory){
            return arrayFoo[_index];
    }

    function readFooA(
        uint256 _index
    ) 
        public 
        view 
        returns(uint256){
            return arrayFoo[_index].a;
    }

    function modifyFooA(
        uint256 _index, 
        uint256 _a
    ) 
        public {
            arrayFoo[_index].a = _a;
    }

    function setFooAtIndex(
        uint256 _index, 
        uint256 _a, 
        uint256 _b
    ) 
        public {
            arrayFoo[_index] = Foo(_a, _b);
    }
}

```

Vous pouvez également créer un mappage où les clés sont des adresses et les valeurs sont des instances Foo :

```solidity


contract StructsExample {
    struct Foo {
        uint256 a;
        uint256 b;
    }

    mapping(address => Foo) public mappingFoo;

    function insertFoo(
        uint256 _a, 
        uint256 _b
    ) 
        public {
            mappingFoo[msg.sender] = Foo(_a, _b);
    }
}
```
Jusqu'à présent, ce qui se passe ici devrait être évident. Nous avons un mappage de address ⇒ struct Foo; mappingFoo.

Pour attribuer une instance Foo à un mappage d'adresse, voici comment procéder
```solidity

function insertFoo(
    uint256 _a, 
    uint256 _b
) 
    public {
        mappingFoo[msg.sender] = Foo(_a, _b);
}
```
et de le modifier
```solidity

function modifyFoo(uint256 _a) public {
    mappingFoo[msg.sender].a = _a;
}
```
**Exemple de la vie réelle**

Un cas d'utilisation plus pratique serait dans un système de ticket. Nous avons un contrat BuyTickets qui vend un ticket au prix de 0,01 Ether. Une adresse ne peut pas acheter plus de 10 tickets et nous avons une fonction qui affiche les informations sur le ticket d'une adresse.

```solidity

contract BuyTickets {

    uint256 public constant TICKET_PRICE = 0.01 ether;

    struct Ticket {
        string name;
        uint256 numberOfTickets;
    }

    mapping(address => Ticket) public tickets;

    function buyTicket(
        string memory _name, 
        uint256 _numberOfTickets
    ) 
        external 
        payable {
            require(msg.value == _numberOfTickets * TICKET_PRICE, "Wrong Value");
            require(_numberOfTickets <= 10, "Maximum Limit Exceeded");
            require(tickets[msg.sender].numberOfTickets + _numberOfTickets <= 10, "Maximum Limit Reached");

            tickets[msg.sender].name = _name;
            tickets[msg.sender].numberOfTickets += _numberOfTickets;
    }

    function displayTicket(
        address _ticketHolder
    ) 
        external 
        view 
        returns (Ticket memory) {
            return(tickets[_ticketHolder]);
    }
}

```
Nous pourrions utiliser des NFT pour les billets, mais si les utilisateurs ne veulent pas transférer leurs billets entre eux, ce serait exagéré.

**Exercices**

[Base de données des étudiants](https://github.com/RareSkills/Solidity-Exercises/tree/ddb9541ee6cad402e498bde51f06308350bfca7d/StudentDB)
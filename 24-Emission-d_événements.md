# **Emission d'événements**

Techniquement, notre token « ERC20 » n'est pas entièrement conforme à la norme ERC20. Il lui manque chose d'importante :  **les événements** .

Règle générale :  **si une fonction provoque un changement d’état, celui-ci doit être consigné.**


---

Pourquoi consigné des choses? N'est-il pas vrai que la blockchain stocke déjà de manière immuable chaque transaction ?

C'est vrai, les événements ne sont pas strictement nécessaires. Cependant, ils facilitent grandement l'audit des événements passés. Plutôt que de parcourir un tas de transactions, l'utilisateur peut filtrer par le journal qui l'intéresse et trouver rapidement les événements (transactions) qui pourraient l'intéresser.

C'est ainsi que votre portefeuille de crypto-monnaie peut rapidement découvrir votre solde ERC20. Il serait assez ennuyeux de devoir examiner chaque transaction ayant eu lieu sur un jeton ERC20 pour découvrir si vous en possédez un. Mais les journaux sont stockés de manière à ce que cette récupération soit efficace.

**Les événements ne peuvent pas être vus par d'autres contrats intelligents**. Ils sont optimisés pour être interrogés hors chaîne.

Regardons un exemple.

```solidity

contract ExampleContract {

    event Deposit(address indexed depositor, uint256 amount);

    receive() 
        external 
        payable {
            emit Deposit(msg.sender, msg.value);
    }
}


```
**Un événement peut avoir jusqu'à 3 types indexés, mais il n'y a pas de limite stricte sur le nombre de paramètres non indexés.**

Si vous avez des connaissances en bases de données, vous pouvez penser aux « index » exactement de la même manière que vous le feriez pour un index de base de données.

Au fait, les noms d'arguments après le type de données sont facultatifs. Nous aurions pu écrire l'événement ci-dessus comme

```solidity

event Deposit(address indexed, uint256);

```
sans aucun effet négatif, sauf que c'est peut-être un peu moins lisible.

Quand une variable doit-elle être indexée ou non ? Si vous souhaitez trouver rapidement cette valeur, par exemple « une adresse a-t-elle été impliquée dans ce contrat de jeton », vous devez l'indexer. La question « quelqu'un a-t-il déjà transféré exactement 1 370 904 jetons dans ce contrat ? » ne vous intéresse probablement pas, alors n'indexez pas le montant. Voici notre jeton ERC20 avec les événements ajoutés. Notez que ces événements sont requis par la [  spécification](https://eips.ethereum.org/EIPS/eip-20)


Faites très attention à l'endroit où les événements ont été ajoutés, en particulier la fonction de mint ! La convention selon laquelle l'adresse(0) est la source signifie que les jetons sont nés de rien, plutôt que d'une autre adresse. Lecture recommandée : 
[https://www.rareskills.io/post/ethereum-events](https://www.rareskills.io/post/ethereum-events)


```solidity

contract ERC20 {
    string public name;
    string public symbol;

    mapping(address => uint256) public balanceOf;
    address public owner;
    uint8 public decimals;

    uint256 public totalSupply;

    // owner -> spender -> allowance
    // this enables an owner to give allowance to multiple addresses
    mapping(address => mapping(address => uint256)) public allowance;

    event Transfer(
        address indexed _from, 
        address indexed _to, 
        uint256 _value
    );
    event Approval(
        address indexed _owner, 
        address indexed _spender, 
        uint256 _value
    );

    constructor(
        string memory _name, 
        string memory _symbol
    ) {
        name = _name;
        symbol = _symbol;
        decimals = 18;

        owner = msg.sender;
    }

    function mint(
        address to, 
        uint256 amount
    ) 
        public {
            require(msg.sender == owner, "only owner can create tokens");
            totalSupply += amount;
            balanceOf[owner] += amount;

            emit Transfer(address(0), owner, amount);
    }

    function transfer(
        address to, 
        uint256 amount
    ) 
        public 
        returns (bool) {
            return helperTransfer(msg.sender, to, amount);
    }

    function approve(
        address spender, 
        uint256 amount
    ) 
        public 
        returns (bool) {
            allowance[msg.sender][spender] = amount;
            emit Approval(msg.sender, spender, amount);

            return true;
    }

    function transferFrom(
        address from, 
        address to, 
        uint256 amount
    ) 
        public 
        returns (bool) {
            if (msg.sender != from) {
                require(
                    allowance[from][msg.sender] >= amount, 
                    "not enough allowance"
                );

                allowance[from][msg.sender] -= amount;
            }

            return helperTransfer(from, to, amount);
    }

    function helperTransfer(
        address from, 
        address to, 
        uint256 amount
    ) 
        internal 
        returns (bool) {
            require(balanceOf[from] >= amount, "not enough money");
            require(to != address(0), "cannot send to address(0)");
            balanceOf[from] -= amount;
            balanceOf[to] += amount;

            emit Transfer(from, to, amount);
            return true;
    }
}


```
## Exercices pratiques

[Emitter](https://github.com/RareSkills/Solidity-Exercises/tree/main/Emitter)



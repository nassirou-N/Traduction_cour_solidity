# **Constantes**

Les variables immuables peuvent être définies une fois dans le constructeur, mais que faire si vous avez un nombre que vous ne souhaitez jamais modifier ?

Comme d'autres langages, Solidity utilise le  mot-clé **constant**. Cela signifie que la valeur est fixe et ne change jamais.

Disons que vous avez un jeton ERC20 qui ne devrait jamais avoir plus de 22 millions de pièces minte.
Ce serait la manière propre de le faire
```solidity
contract ExampleERC20 {

    uint256 public constant MAX_SUPPLY = 22_000_000;
    // erc20 code

    function mint(unint256 amount) external {
        require(totalSupply() + amount <= MAX_SUPPLY, "max supply exceeded");
        balanceOf[msg.sender] += amount;

        emit Transfer(address(0), msg.sender, amount);
    }
    // rest of the erc20 code
}

```

Notez que 22000000 a été écrit 22_000_000. Ils ont le même sens, mais ce dernier est plus lisible. Les traits de soulignement( **_** ) dans les nombres sont simplement ignorés.
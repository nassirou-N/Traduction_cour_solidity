# **Unités d'Ethereum : wei, gwei et ether**

Les deux lignes de code suivantes sont équivalentes

```solidity
(bool ok, ) = recipient.call{value: 1 ether}("");

(bool ok, ) = recipient.call{value: 1_000_000_000_000_000_000}("");

```

Si vous avez envie de compter, cela fait **10^18**. N'oubliez pas qu'il n'y a pas de flottants dans Solidity, donc « 1 Ethereum » correspond en fait à **_10^18_** unités de sa plus petite unité.

**_La plus petite unité d'éther est 1 wei. 10^18 wei est 1 éther._**

Une autre unité couramment utilisée est le **gwei**. Un _gwei_ équivaut à **1 milliard de wei**, soit 1 milliardième d'éther.

**_Remix ne vous permet pas de spécifier une fraction d'un Ether lors de l'envoi d'une valeur, vous devrez donc calculer le montant à partir de la fraction et le convertir en wei ou gwei._**

Au fait, même si les floatant ne sont pas pris en charge dans Solidity, vous pouvez spécifier des fractions d'un Ether. Le compilateur Solidity est suffisamment intelligent pour comprendre qu'une fraction d'un Ether n'est pas une fraction elle-même. Les lignes de code suivantes sont équivalentes.

```solidity

(bool ok, ) = recipient.call{value: 1 gwei}("");

(bool ok, ) = recipient.call{value: 1_000_000_000 wei}("");

(bool ok, ) = recipient.call{value: 0.000000001 ether}("");

```
Au fait, Ether a d'autres noms pour les unités, comme **Finney** et **Sazbo**, mais ces unités sont si rarement utilisées qu'il est préférable de les rechercher sur Google lorsque vous avez besoin de connaître leur valeur. Les valeurs suivantes doivent cependant être mémorisées :

* wei est la plus petite unité d'Ether.
* 10^18 wei est 1 Ether
* Un gwei équivaut à un milliard de wei, soit un milliardième d'Ether.

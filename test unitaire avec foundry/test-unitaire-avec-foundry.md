# **Foundry Unit Tests**

Cet article décrit comment créer des tests unitaires dans Solidity à l'aide de Foundry. Nous verrons comment tester toutes les transitions d'état qui peuvent se produire dans un contrat intelligent, ainsi que certaines fonctionnalités supplémentaires utiles fournies par Foundry. Foundry dispose de capacités de test très étendues, donc plutôt que de réviser la documentation, nous nous concentrerons sur les parties que vous utiliserez la plupart du temps.

Cet article suppose que vous maîtrisez déjà Solidity. Si ce n'est pas le cas, consultez notre tutoriel gratuit [d'apprentissage de Solidity]().

# **Installer Foundry**

Si vous n'avez pas encore installé Foundry, suivez les instructions ici : [https://book.getfoundry.sh/getting-started/installation](https://book.getfoundry.sh/getting-started/installation)

## Paternité

Cet article a été co-écrit par Aymeric Taylor ( [LinkedIn](https://www.linkedin.com/in/aymeric-russel-taylor/) , [Twitter](https://x.com/TaylorAymeric) ), stagiaire de recherche chez RareSkills.
traduit en francais par Arold [linkedIN](https://www.linkedin.com/in/), [Twitter](https://x.com/)


# **Bonjour le Monde avec Foundry**

Exécutez simplement les commandes suivantes et il configurera l'environnement, créera des tests et les exécutera pour vous. (Cela suppose bien sûr que vous avez installé Foundry).

```bash
forge init
```
```bash
forge test
```
# **Bonnes pratiques en matière de tests avec solidity**

Quel que soit le framework, la qualité d'un test unitaire Solidity dépend de trois facteurs :

* **Couverture de ligne**
* **couverture des branch, et**
* **transitions d’état complètement définies.**

en conprenand chacun de ses aspects,  nous motivé a se focalise sur certain aspects de l'API de Foundry.

Il n'est bien sûr pas possible de documenter chaque plage d'entrées pour chaque sortie possible. Cependant, la qualité des tests sera généralement corrélée à la couverture de ligne, à la couverture de branche et à la définition des transitions d'état. Dans notre autre article, nous avons documenté comment [mesurer la couverture de ligne et de branche avec Foundry](https://www.rareskills.io/post/foundry-forge-coverage) . Nous expliquerons ici l'importance des trois mesures :

### 1. **Couverture de ligne**
La couverture de ligne est ce à quoi elle ressemble. Si une ligne de code n'a pas été exécutée pendant les tests, la couverture de ligne n'est pas à 100 %. Si une ligne n'a jamais été exécutée, vous ne pouvez pas être sûr qu'elle fonctionne comme prévu ou qu'elle reviendra en arrière. Il n'y a aucune bonne raison de ne pas avoir une couverture de ligne à 100 % dans un contrat intelligent. Si vous écrivez du code, cela signifie que vous vous attendez à ce qu'il soit exécuté à un moment donné dans le futur, alors pourquoi ne pas le tester ?

### 2. **Couverture des succursales**

Même si chaque ligne est exécutée, cela ne signifie pas que chaque variation de la logique métier du contrat intelligent est testée.

Considérez la fonction suivante
```solidity
function changeOwner(address newOwner) external {
	require(msg.sender == owner, "onlyOwner");
	owner = newOwner;
}

```
Si vous testez cette adresse en l'appelant avec le propriétaire, vous obtiendrez une couverture de ligne à 100 % mais pas une couverture de branche à 100 %. C'est parce que l'instruction require et l'affectation du propriétaire ont été exécutées, mais le cas où l'exigence est rétablie n'a pas été testé.

Voici un exemple plus subtil.
```solidity
// @notice anyone can pay off someone else's loan
// @param debtor the person who's loan the sender is making a payment for
function payDownLoan(address debtor) external payable {
	uint256 loanAmount = loanAmounts[debtor];
	require(loanAmount > 0, "no such loan");

	if (msg.value >= debtAmount {
		loanAmounts[debtor] = 0;
		emit LoanFullyRepaid(debtor);
	} else {
		emit LoanPayment(debtor, debtAmount, msg.value);
		loanAmount -= msg.value;
	}

	if (msg.value > loanAmount) {
		msg.sender.call{value: msg.value - loanAmount}("");
	}
}

```

Combien de branches y a-t-il à tester dans ce cas ?

1. Le cas où le prêt est nul
2. Le cas où quelqu'un paie moins que le montant du prêt
3. Le cas où quelqu'un paie exactement le montant du prêt
4. Le cas où quelqu'un paie plus que le montant du prêt

Il est possible d'obtenir une couverture de ligne de 100 % sur ce test en envoyant plus d'éther que la taille du prêt et moins d'éther que la taille du prêt. Cela exécuterait les deux branches de l'instruction if else et l'instruction if finale à la fin. Mais cela ne testerait pas l'instruction else où le prêt est remboursé exactement à zéro.

Plus vos fonctions ont de branches, plus il devient difficile de les tester unitairement. Le terme technique pour cela est [complexité cyclomatique.](https://en.wikipedia.org/wiki/Cyclomatic_complexity)

### 3. **Transitions d'état entièrement définies**





# **Block.timestamp et Block.number**

Nous avons pu faire des choses intéressantes jusqu'à présent, mais nous n'avons pas pu suivre le passage du temps.

Vous pouvez obtenir l'horodatage Unix(temps des execution) sur le bloc avec  block.timestamp.  Essayons

```solidity

contract WhatTimeIsIt {

    function timestamp() 
        public 
        view 
        returns (uint256) {
            return block.timestamp;
    }
}
```
Essayez-le dans Remix.

Le nombre qui revient est le nombre de secondes depuis le **_1er janvier 1970 UTC_**, l'heure traditionnelle d'Unix. N'oubliez pas qu'il s'agit  de **secondes**  et **non  de millisecondes**,  comme votre bureau Linux ou d'autres langages de programmation pourraient vous répondre.

Ethereum progresse avec des blocs, et quel que soit l'horodatage que vous obtenez, c'est ce que le validateur a mis dans le bloc lorsqu'il l'a produit une bloc. **_Étant donné que les blocs sont produits toutes les 12 secondes, le block.timestamp augmentera à peu près a ce montant la. tu ne dois pas faire confiance au block.timestamp sur des intervalles de l'ordre de la seconde. Il y a trop de variations._** Au fil des minutes mais ses variables sont assez fiable.


Si tu souhaites te assurer que quelqu'un n'appelle pas une fonction plus d'une fois par jour, tu peux utiliser la construction suivante

```solidity

contract ExampleContract {

    uint256 public lastCall;

    function hasCooldown() 
        public {
            uint256 day = 60 * 60 * 24;
            require(
                block.timestamp > lastCall + day, 
                "hasn't been a day"
            );
            lastCall = block.timestamp;
    }
}


```
Solidity a une manière beaucoup plus agréable de représenter le temps au lieu de multiplier les secondes comme ça.

```solidity

contract ExampleContract {

    uint256 public lastCall;

    function hasCooldown() 
        public {
            require(
                block.timestamp > lastCall + 1 days, 
                "hasn't been a day"
            );
            lastCall = block.timestamp; // update the last time the function was called
    }
}


```
En fait, les secondes, les minutes, les heures, les jours et les semaines sont toutes des unités de temps valides, qui ne sont que des raccourcis pratiques pour multiplier le nombre de secondes dont tu as besoin. Au cas où tu te pose la question de savoir pourqui utilise plus les secondes que d'autre mesure?, parce qu'elles offrent une meilleure lisibilité si tu l'utilises comme mesure.


## block.number

tu peux également savoir à quel numéro de bloc tu te trouves grâce à cette variable. J'espère que son rôle est évident. Certaines personnes par erreur multiplie la moyen des blocktime avec le block.number pour mesurer le passage du temps. Ne faites pas ça.

**N'utilisez pas block.number pour suivre le temps, mais uniquement pour appliquer l'ordre des transactions.**

Quand dois-tu appliquer l'ordre des transactions? Pas souvent. Donc, si tu n'êtes pas sûr, utilisez block.timestamp.

Etherscan affiche le numéro de bloc actuel, si vous souhaitez avoir une idée de sa taille actuelle.

![alt](https://www.rareskills.io/wp-content/uploads/2024/09/block_timestamp_number.png)

```solidity

contract ExampleContract {

    function whatBlockIsIt() 
        external 
        view 
        returns (uint256) {
            return block.number;
    }
}


```

Le code ci-dessus vous indiquera sur quel bloc de transaction s'est produite. Dans ce cas, il sera mis à jour de manière dynamique.

Si vous souhaitez imposer qu'une fonction soit appelée après une autre, c'est-à-dire dans un bloc ultérieur, vous devez utiliser la construction suivante.

```solidity

contract ExampleContract {

    // defaults to zero
    uint256 private calledAt;

    function callMeFirst() 
        external {
            calledAt = block.number;
    }

    function callMeSecond() 
        external {
            require(
                calledAt != 0 && block.number > calledAt, 
                "callMeFirst() not called"
            );
    }
}


```

### Exercices pratiques


[Blocage d'une semaine](https://github.com/RareSkills/Solidity-Exercises/tree/main/OneWeekLockup)

[IdiotBetting](https://github.com/RareSkills/Solidity-Exercises/tree/main/IdiotBetting)

[TimeLockEscrow](https://github.com/RareSkills/Solidity-Exercises/tree/main/TimelockEscrow)

[Réduction des paiements](https://github.com/RareSkills/Solidity-Exercises/tree/main/ReducingPayout)

[Numéro de bloc](https://github.com/RareSkills/Solidity-Exercises/tree/main/BlockNumber)
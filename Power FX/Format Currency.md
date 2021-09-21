**Vous trouverez dans cette page quelques exemples d'affichage d'une valeur monétaire**

- [Afficher une valeur monétaire en euros en séparant les milliers](#afficher-une-valeur-monétaire-en-euros-en-séparant-les-milliers)
- [Mettre en forme un champ calculé d'une liste SharePoint pour qu'il s'affiche sous forme de devise](#mettre-en-forme-un-champ-calculé-dune-liste-sharepoint-pour-quil-saffiche-sous-forme-de-devise)


# Afficher une valeur monétaire en euros en séparant les milliers

    Text(ThisItem.MyProperty;"[$-fr-FR]###  ###")&" €"
    Text(ThisItem.MyProperty;"[$-fr-FR]### ### ###0,00";"fr-FR";" €")


# Mettre en forme un champ calculé d'une liste SharePoint pour qu'il s'affiche sous forme de devise

Dans ce cas j'ai un champ calculé qui aditionne 5 colonnes mais qui ne s'affiche pas correctement en mode vue, je vais donc le formater quand nous somme dans ce mode et je vais faire le calcul en temps réel si nous sommes en mode Edit ou New.

Pour le formater je vais transformer le point en virgule puis le champ en Value avant de lui appliquer le formatage vu au dessus.

    If(
        SharePointForm1.Mode = FormMode.View;
        Concatenate(
            Text(
                Value(
                    Substitute(
                        ThisItem.'Montant total TTC';
                        ".";
                        ","
                    )
                );
                "[$-fr-FR]### ### ###0,00";
                "fr-FR"
            );
            " €"
        );
        Concatenate(
            Text(
                DataCardValue7 + DataCardValue11 + DataCardValue15 + DataCardValue19 + DataCardValue23;
                "[$-fr-FR]### ### ###0,00";
                "fr-FR"
            );
            " €"
        )
    )
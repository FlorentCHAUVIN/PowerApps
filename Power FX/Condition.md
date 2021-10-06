**Vous trouverez dans cette page quelques exemples de Condition**

- [Envoyer une notification si la valeur saisie est négative](#envoyer-une-notification-si-la-valeur-saisie-est-négative)

# Envoyer une notification si la valeur saisie est négative

Dans ce cas de figure, je gère des mouvements de budget entre plusieurs services au travers Power Apps et et d'un modèle de donnée géré avec des listes SharePoint.
Et il faut que j'affiche des notifications en fonction du choix effectuée dans une liste déroulante et de la somme saisie selon la logique suivante :

* Si le le choix dans la liste déroulante contient le mot "Débit" et que la somme n'est pas négative j'affiche une notification
* Si le le choix dans la liste déroulante ne contient pas le mot "Débit" et que la somme est négative j'affiche une notification
* Sinon je soumet mon formulaire à SharePoint 

Pour cela on va utiliser la fonction IsMatch qui permet de savoir si une valeur contient un mot (MatchOptions.Contains) ou d'utiliser une expression régulière permettant de vérifier si la valeur saisie est un nombre négatif. 

    If(
        IsMatch(
            Text(DataCardValue6.Selected.Value);
            "Débit";MatchOptions.Contains
        ) And !IsMatch(
            DataCardValue7.Text;
            "\-\d+(\.\d+)?"
        );
        Notify(
            Concatenate("La somme d'origine '";Text(DataCardValue6.Selected.Value);"' doit être négative");
            NotificationType.Warning
        );
        !IsMatch(
            Text(DataCardValue6.Selected.Value);
            "Débit";MatchOptions.Contains
        ) And IsMatch(
            DataCardValue7.Text;
            "\-\d+(\.\d+)?"
        );
        Notify(
            Concatenate("La somme d'origine '";Text(DataCardValue6.Selected.Value);"' doit être positive");
            NotificationType.Warning
        );
        SubmitForm(form_CreateVirement)
    );;

J'aurai pu aussi procéder différemment en détectant au fur et à mesure de la saisie si la logique est respectée et le cas échéant en en affichant dans un label à côté du champ un avertisemment tout en changeant la couleur de la saisie.




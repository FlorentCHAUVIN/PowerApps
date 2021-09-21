**Vous trouverez dans cette page quelques exemples de formule d'update de DataCard**

- [Update d'un champ SharePoint de type Lookup](#update-dun-champ-sharepoint-de-type-lookup)
- [Forcer un champ Lookup à vide si l'utilisateur à désélectionné celui-ci dans un formulaire SharePoint personnalisé](#forcer-un-champ-lookup-à-vide-si-lutilisateur-à-désélectionné-celui-ci-dans-un-formulaire-sharepoint-personnalisé)


# Update d'un champ SharePoint de type Lookup

Si vous avec surcharger la liste des items, il faut alors modifier l'update de la DataCard pour que la sélection soit reconnue.

Exemple avec la propriété Items basé sur une collection de contrat avec un champs spécifique

    Items = ColContrat
    Fields = ["CodeEtTitulaire"]

L'update va utiliser le connecteur SharePoint en faisant référence au champ qui doit être sélectionné. Pour cela on le recherche dans la liste correspondante et on spécifie son Id et sa Value:

    If(!(IsBlank(DataCardValueXX.Selected));{
    '@odata.type':"#Microsoft.Azure.Connectors.SharePoint.SPListExpandedReference";
    Id: LookUp(Contrat; 'N° de contrat' = DataCardValueXX.Selected.'N° de contrat'; ID);
    Value: DataCardValueXX.Selected.'Code Contrat'
    })

La Value pourrait aussi être remontée via un Lookup si elle n'est pas présente dans votre collection.

La méthode de Lookup permet aussi de remonter des valeurs d'une colonne qui est elle-même de type Lookup, par exemple je remonte la valeur de la colonne Structure dans la liste Bureaux en fonction du choix de Bureau :

    LookUp(Bureaux; Bureau = DataCardValueX.Selected.Value; Structure.Value)


# Forcer un champ Lookup à vide si l'utilisateur à désélectionné celui-ci dans un formulaire SharePoint personnalisé

Pour pouvoir remettre un champ à vide il faut activer la fonctionnalité "Gestion d'erreur" en mode experimental ce qui aura aussi pour effet de ne plus hérité des valeurs par défaut définies dans SharePoint mais permettra au champs de supporter cette fonctionnalité non permise par Power Apps.

Pour les champs de type Lookup, il faut aller plus loin en forçant le choix à vide.
Cela ne se jouera pas au niveau de l'update de la datacard mais au niveau de la valeur par défaut.
En effet, il faut détecter le changement de valeur et la non sélection puis définir la valeur par défaut sur une variable spécifique qui sera définie comme tel:


    Set(
    BlankChoice;
    {
        Id: -1;
        Value: Blank()
    }
    );;


Cette variable peut être définie sur le "OnVisible" et "OnHidden" de la "FormScreen" en même temps que les autres variable qui doivent être réinitialisées à chaque ouverture du formulaire SharePoint.
Dans notre exemple nous avons un variable qui nous permetra de savoir si quelqu'un clique sur notre champ Lookup et un autre si on l'a modifié.


    Set(
        HumanSelectMonChampLookup;
        false
    );;
    Set(
        ResetMonChampLookup;
        false
    );;


La première variable est défini à true sur le OnSelect de notre champ Lookup :


    OnSelect : Set(HumanSelectMonChampLookup;true)


La seconde est définie sur le OnChange et elle est dépendante de la première car le chargement d'un objet dans sharePoint provoque un OnChange, il faut donc s'assurer que celui-ci est bien volontaire.
Cette variable passera à true uniquement si quelqu'un a sélectionné le champ et l'a modifié.


    Onchange : If(HumanSelectMonChampLookup= true;Set(ResetMonChampLookup;true))


Le DefaultSelectedItem de notre champ contient la logique de détection :

* Si le champ est modifié et que l'on a une sélection on va chercher les références à notre élement directemment dans la liste correspondante au Lookup. C'est utile notamment si on a surchargé notre champs items pour afficher plus d'information dans notre liste (Concat de deux champs par ex.), dans ce cas ne peut pas utiliser directemment la valeur sélectionné. Il faut utiliser l'ID sélectionnée pour retrouver à minima la Value dans la liste.
* Si le champs est modifié mais que l'on n'a pas de sélection on force le champ à vide via la variable BlankChoice
* Sinon on prend la valeur par défaut  

Ce qui donne :

    DefaultSelectedItems :

    If(
        ResetMonChampLookup = true And !IsBlank(DataCardValue.Selected.ID);
        {
            Id: LookUp(
                'LaListeDuChampLookUp';
                ID = DataCardValue.Selected.ID;
                ID
            );
            Value: LookUp(
                'LaListeDuChampLookUp';
                ID = DataCardValue.Selected.ID;
                'MonChampLookupName'
            )
        };
        ResetMonChampLookup = true And IsBlank(DataCardValue.Selected.ID);
        BlankChoice;
        Parent.Default
    )

Ou en version plus simple si vous n'avez pas de surcharge :

    DefaultSelectedItems :

    If(
        ResetMonChampLookup = true And !IsBlank(DataCardValue.Selected.ID);
        {
            Id: DataCardValue.Selected.ID;
            Value: DataCardValue.Selected.Value;
        };
        ResetMonChampLookup = true And IsBlank(DataCardValue.Selected.ID);
        BlankChoice;
        Parent.Default
    )

Pour aller plus loin on peut aussi surcharger complètement la DataCardValue en la cachant et en la remplaçant par une liste et un label dédié permettant d'afficher des informations beaucoup plus complète de notre liste Lookup sans pour autant intégrer les champs dans notre liste.
En effet Microsoft recommande de ne pas avoir plus de 12 champs lookup/People/Metadata dans une liste, cela provoque d'ailleur une erreur si on essaie d'en afficher plus dans une vue.
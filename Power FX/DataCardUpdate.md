# Exemple de formule d'update de DataCard

## Champ SharePoint de type Lookup 

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
**Vous trouverez dans cette page quelques exemples de Grouper Par**

- [Filtrer, Grouper Par et faire une somme dans une nouvelle colonne formaté pour afficher en euros](#filtrer-grouper-par-et-faire-une-somme-dans-une-nouvelle-colonne-formaté-pour-afficher-en-euros)

# Filtrer, Grouper Par et faire une somme dans une nouvelle colonne formaté pour afficher en euros

    AddColumns(
        GroupBy(
            Filter(
                MyList;
                MyProperty = MyComboBox.SelectedText.Value
            );
            "PropertyToGroupBy";
            "GroupByName"
        );
        "NewColumName";
        Text(Sum(
            GroupByName;
            Somme
        );"[$-fr-FR]###  ###")&" €"
    )


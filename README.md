# Aide-mémoire SQL

Un rappel rapide de toutes les requêtes SQL pertinentes et des exemples sur la façon de les utiliser.

Ce référentiel est mis à jour et enrichi par moi et la communauté. Les Pull requests sont les bienvenues. Bonne lecture !

# Contenu
1. [ Requêtes de sélection. ](#selection)
2. [ Requêtes d'altération des données. ](#alter-data)
3. [ Requêtes d'agrégation. ](#report)
4. [ Requêtes de jointure. ](#joins)
5. [ Requêtes de manipulation de vues. ](#view)
6. [ Requêtes d'altération de tables.](#alter)
7. [ Requêtes de création de tables.](#create)

<a name="selection"></a>
# 1. Requêtes de sélection

### **SELECT**: utilisé pour sélectionner des données dans une base de données
* `SELECT` * `FROM` nom_de_table;

### **DISTINCT**: filtre les valeurs en double et renvoie les lignes de la colonne spécifiée.
* `SELECT DISTINCT` nom_de_colonne;

### **WHERE**: utilisé pour filtrer les enregistrements
* `SELECT` colonne1, colonne2 `FROM` nom_de_table `WHERE` condition;
* `SELECT` * `FROM` nom_de_table `WHERE` condition1 `AND` condition2;
* `SELECT` * `FROM` nom_de_table `WHERE` condition1 `OR` condition2;
* `SELECT` * `FROM` nom_de_table `WHERE NOT` condition;
* `SELECT` * `FROM` nom_de_table `WHERE` condition1 `AND` (condition2 `OR` condition3);
* `SELECT` * `FROM` nom_de_table `WHERE EXISTS` (`SELECT` nom_de_colonne `FROM` nom_de_table `WHERE` condition);

### **ORDER BY**: utilisé pour trier les enregistrements dans l'ordre croissant ou décroissant
* `SELECT` * `FROM` nom_de_table `ORDER BY` colonne;
* `SELECT` * `FROM` nom_de_table `ORDER BY` colonne `DESC`;
* `SELECT` * `FROM` nom_de_table `ORDER BY` colonne1 `ASC`, colonne2 `DESC`;

### **SELECT TOP**: utilisé pour spécifier le nombre d'enregistrements à retourner depuis le haut de la table
* `SELECT TOP` nombre nom_colonnes `FROM` nom_de_table `WHERE` condition;
* `SELECT TOP` pourcentage nom_colonnes `FROM` nom_de_table `WHERE` condition;
* Tous les systèmes de base de données ne prennent pas en charge `SELECT TOP`. L'équivalent PostgreSQL est la clause `LIMIT`.
* `SELECT` nom_de_colonnes `FROM` nom_de_table `LIMIT` offset, count;

### **LIKE**: opérateur utilisé dans une clause WHERE pour rechercher un modèle spécifique dans une colonne
* % (signe de pourcentage) est un caractère générique qui représente zéro, un ou plusieurs caractères.
* _ (underscore) est un caractère générique qui représente un seul caractère
* `SELECT` nom_de_colonnes `FROM` nom_de_table `WHERE` nom_de_colonne `LIKE` modèle;
* `LIKE` ‘a%’ (trouvez toutes les valeurs qui commencent par "a")
* `LIKE` ‘%a’ (trouvez toutes les valeurs qui se terminent par "a")
* `LIKE` ‘%or%’ (trouvez toutes les valeurs qui ont "ou" à n'importe quelle position)
* `LIKE` ‘_r%’ (trouvez toutes les valeurs qui ont "r" en deuxième position)
* `LIKE` ‘a_%_%’ (trouvez toutes les valeurs qui commencent par "a" et qui ont au moins 3 caractères de long)
* `LIKE` ‘[a-c]%’ (trouver toutes les valeurs commençant par "a", "b" ou "c")

### **IN**: qui vous permet de spécifier plusieurs valeurs dans une clause WHERE.
* l'opérateur IN est essentiellement un raccourci pour des conditions OR multiples.
* `SELECT` nom_de_colonnes `FROM` nom_de_table `WHERE` nom_de_colonne `IN` (valeur1, valeur2, …);
* `SELECT` nom_de_colonnes `FROM` nom_de_table `WHERE` nom_de_colonne `IN` (`SELECT STATEMENT`);

### **BETWEEN**: L'opérateur sélectionne les valeurs comprises dans une plage donnée inclusivement
* `SELECT` nom_de_colonnes `FROM` nom_de_table `WHERE` nom_de_colonne `BETWEEN` valeur1 `AND` valeur2;
* `SELECT` * `FROM` Products `WHERE` (nom_de_colonne `BETWEEN` valeur1 `AND` valeur2) `AND NOT` nom_de_colonne2 `IN` (valeur3, valeur4);
* `SELECT` * `FROM` Products `WHERE` nom_de_colonne `BETWEEN` #01/07/1999# AND #03/12/1999#;

### **NULL**: valeurs dans un champ sans valeur
* `SELECT` * `FROM` nom_de_table `WHERE` nom_de_colonne `IS NULL`;
* `SELECT` * `FROM` nom_de_table `WHERE` nom_de_colonne `IS NOT NULL`;

### **AS**: Les alias sont utilisés pour attribuer un nom temporaire à une table ou à une colonne.
* `SELECT` nom_de_colonne `AS` alias `FROM` nom_de_table;
* `SELECT` nom_de_colonne `FROM` nom_de_table `AS` alias;
* `SELECT` nom_de_colonne `AS` alias1, nom_de_colonne2 `AS` alias2;
* `SELECT` nom_de_colonne1, nom_de_colonne2 + ‘, ‘ + nom_de_colonne3 `AS` alias;

### **UNION**: utilisé pour combiner les résultats de deux ou plusieurs instructions SELECT.
* Chaque instruction SELECT dans UNION doit avoir le même nombre de colonnes.
* Les colonnes doivent avoir des types de données similaires
* Les colonnes de chaque instruction SELECT doivent également être dans le même ordre.
* `SELECT` nom_colonnes `FROM` table1 `UNION SELECT` nom_de_colonne `FROM` table2;
* L'opérateur `UNION` ne sélectionne que des valeurs distinctes, `UNION ALL` autorise les doublons.

### **INTERSECT**: utilisé pour retourner les enregistrements que deux instructions SELECT ont en commun.
* Généralement utilisé de la même manière que **UNION** ci-dessus.
* `SELECT` nom_colonnes `FROM` table1 `INTERSECT SELECT` nom_de_colonne `FROM` table2;

### **EXCEPT**: utilisé pour retourner tous les enregistrements de la première instruction SELECT qui ne sont pas trouvés dans la deuxième instruction SELECT.
* Généralement utilisé de la même manière que **UNION** ci-dessus.
* `SELECT` nom_colonnes `FROM` table1 `EXCEPT SELECT` nom_de_colonne `FROM` table2;

### **ANY|ALL**: opérateur utilisé pour vérifier les conditions de sous-requêtes utilisées dans une clause WHERE ou HAVING
* L'opérateur `ANY` renvoie un résultat vrai si toutes les valeurs de la sous-requête remplissent la condition.
* L'opérateur `ALL` renvoie vrai si toutes les valeurs de la sous-requête remplissent la condition
* `SELECT` nom_colonnes `FROM` table1 `WHERE` nom_de_colonne operator (`ANY`|`ALL`) (`SELECT` nom_de_colonne `FROM` nom_de_table `WHERE` condition);

### **GROUP BY**: déclaration souvent utilisée avec les fonctions d'agrégation (COUNT, MAX, MIN, SUM, AVG) pour regrouper l'ensemble des résultats par une ou plusieurs colonnes.
* `SELECT` nom_de_colonne1, COUNT(nom_de_colonne2) `FROM` nom_de_table `WHERE` condition `GROUP BY` nom_de_colonne1 `ORDER BY` COUNT(nom_de_colonne2) DESC;

### **HAVING**: cette clause a été ajoutée à SQL parce que le mot-clé WHERE ne pouvait pas être utilisé avec les fonctions d'agrégats.
* `SELECT` `COUNT`(nom_de_colonne1), nom_de_colonne2 `FROM` table `GROUP BY` nom_de_colonne2 `HAVING` `COUNT(`nom_de_colonne1`)` > 5;

### **WITH**: souvent utilisé pour récupérer des données hiérarchiques ou réutiliser un ensemble de résultats temporaires plusieurs fois dans une requête. Également appelée "Common Table Expression".
* `WITH RECURSIVE` cte `AS` (<br/>
    &nbsp;&nbsp;`SELECT` c0.* `FROM` categories `AS` c0 `WHERE` id = 1 `# Point de départ`<br/>
    &nbsp;&nbsp;`UNION ALL`<br/>
    &nbsp;&nbsp;`SELECT` c1.* `FROM` categories `AS` c1 `JOIN` cte `ON` c1.parent_category_id = cte.id<br/>
  )<br/>
  `SELECT` *<br/>
  `FROM` cte


<a name="alter-data"></a>
# 2. Requêtes d'altération des données

### **INSERT INTO**: utilisé pour insérer de nouveaux enregistrements dans une table
* `INSERT INTO` nom_de_table (colonne1, colonne2) `VALUES` (valeur1, valeur2);
* `INSERT INTO` nom_de_table `VALUES` (valeur1, valeur2 …);

### **UPDATE**: utilisé pour modifier les enregistrements existants dans une table
* `UPDATE` nom_de_table `SET` colonne1 = valeur1, colonne2 = valeur2 `WHERE` condition;
* `UPDATE` nom_de_table `SET` nom_de_colonne = valeur;

### **DELETE**: utilisé pour supprimer les enregistrements existants dans une table
* `DELETE FROM` nom_de_table `WHERE` condition;
* `DELETE` * `FROM` nom_de_table;

<a name="report"></a>
# 3. Requêtes d'agrégation

### **COUNT**: renvoie le nombre d'occurrences
* `SELECT COUNT (DISTINCT` nom_de_colonne`)`;

### **MIN() et MAX()**: renvoie la plus petite/la plus grande valeur de la colonne sélectionnée
* `SELECT MIN (`nom_de_colonnes`) FROM` nom_de_table `WHERE` condition;
* `SELECT MAX (`nom_de_colonnes`) FROM` nom_de_table `WHERE` condition;

### **AVG()**: renvoie la valeur moyenne d'une colonne numérique
* `SELECT AVG (`nom_de_colonne`) FROM` nom_de_table `WHERE` condition;

### **SUM()**: renvoie la somme totale d'une colonne numérique
* `SELECT SUM (`nom_de_colonne`) FROM` nom_de_table `WHERE` condition;

<a name="joins"></a>
# 4. Requêtes de jointure

###  **INNER JOIN**: retourne les enregistrements qui ont une valeur correspondante dans les deux tables
* `SELECT` nom_de_colonnes `FROM` table1 `INNER JOIN` table2 `ON` table1.nom_de_colonne=table2.nom_de_colonne;
* `SELECT` table1.nom_de_colonne1, table2.nom_de_colonne2, table3.nom_de_colonne3 `FROM` ((table1 `INNER JOIN` table2 `ON` relationship) `INNER JOIN` table3 `ON` relationship);

### **LEFT (OUTER) JOIN**: renvoie tous les enregistrements de la table de gauche (table1) et les enregistrements correspondants de la table de droite (table2).
* `SELECT` nom_de_colonnes `FROM` table1 `LEFT JOIN` table2 `ON` table1.nom_de_colonne=table2.nom_de_colonne;

### **RIGHT (OUTER) JOIN**: renvoie tous les enregistrements de la table de droite (table2) et les enregistrements correspondants de la table de gauche (table1).
* `SELECT` nom_de_colonnes `FROM` table1 `RIGHT JOIN` table2 `ON` table1.nom_de_colonne=table2.nom_de_colonne;

### **FULL (OUTER) JOIN**: renvoie tous les enregistrements lorsqu'il y a une correspondance dans la table de gauche ou de droite.
* `SELECT` nom_de_colonnes `FROM` table1 ``FULL OUTER JOIN`` table2 `ON` table1.nom_de_colonne=table2.nom_de_colonne;

### **Self JOIN**: une jointure régulière, mais la table est jointe avec elle-même
* `SELECT` nom_de_colonnes `FROM` table1 T1, table1 T2 `WHERE` condition;

<a name="view"></a>
# 5. Requêtes de manipulation de vues

### **CREATE**: créer une vue
* `CREATE VIEW` nom_de_vue `AS SELECT` colonne1, colonne2 `FROM` nom_de_table `WHERE` condition;

### **SELECT**: consulter une vue
* `SELECT` * `FROM` nom_de_vue;

### **DROP**: supprimer une vue
* `DROP VIEW` nom_de_vue;

<a name="alter"></a>
# 6. Requête d'altération de tables

### **ADD**: ajouter une colonne
* `ALTER TABLE` nom_de_table `ADD` nom_de_colonne definition_colonne;

### **MODIFY**: changer le type de données de la colonne
* `ALTER TABLE` nom_de_table `MODIFY` nom_de_colonne type_colonne;

### **DROP**: supprimer une colonne
* `ALTER TABLE` nom_de_table `DROP COLUMN` nom_de_colonne;

<a name="create"></a>
# 7. Requête de création de tables

### **CREATE**: créer une table
* `CREATE TABLE` nom_de_table `(` <br />
   `colonne1` `datatype`, <br />
   `colonne2` `datatype`, <br />
   `colonne3` `datatype`, <br />
   `colonne4` `datatype`, <br />
   `);`

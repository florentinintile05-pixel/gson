# GSON

## 1 Présentation globale du projet

### 1.1 Utilité du projet

GSON est une librairie Java qui sert de pont entre des objets Java et la structure d'un fichier JSON. En clair, il permet aux utilisateur de créer un objet Java à partir d'une description JSON ou, à contrario, de créer la description JSON d'un objet Java. GSON n'est pas le premier à proposer cette fonctionnalité, mais il se démarque en offrant la possibilité aux utilisateur de passer de Java à JSON sans même avoir besoin du code source Java, là où les autres projets open-sources ayant la même aspiration sont impossible d'utilisation si l'on ne rajoute pas une balise spéciale dans le code source Java.

Le projet ne se lance pas. S'agissant d'une librairie, il doit être importé dans le projet d'un utilisateur, via Gradle ou Maven, qui pourra alors utiliser les méthodes far de GSON, à savoir **toJson()** et **fromJson()** qui permettent de créer une description JSON d'un objet JAVA et inversement.

Le projet ne sort rien en tant que tel.


## 3 Architecture logicielle

### 3.1 Utilisation de bibliothèques extérieures

**(Information trouvées via IntellijIdea > View > Tool Windows > Maven > Dependencies (pour chaque modules))**
Le projet se divise en plusieurs modules et chacun de ces modules utilise des bibliothèques extérieures (des *dependencies*) :
* Gson -> 5 dependencies
* Gson Extras -> 3 dependencies
* Gson Metrics -> 2 dependencies 
* Gson Protobuf Support -> 4 dependencies
* Test : Code shrinking -> 2 dependencies
* Test : GraalVM Native Image -> 3 dependencies 
* Test : Java Platform Module SYstem -> 2 dependencies

Ce qui fait en tout 21 bibliothèques extérieures référencées par les 7 différents  modules. Les 6 derniers modules implémentent également *com.google.code.gson:gson:2.13.3-SNAPSHOT* qui ne peut pas être considérée comme extérieure car elle représente le module *GSON* et est donc interne. En revanche certains modules référencent les mêmes bibliothèques extérieures :
* junit:junit:4.13.2 -> référencée dans 5 modules
* com.google.truth:truth:1.4.5 -> référencée dans 6 modules 
* com.google.guava:guava:33.5.0-jre -> référencée dans 2 modules

Si on retire les doublons des comptes, on obtient **10** bibliothèques extérieures référencées au total.

**(Information trouvée via ~/gson/ mvn dependency:analyze )**
Quand on se penche sur l'utilisation réelle des dépendances, on se rend compte que certains modules n'utilisent pas des dépendances déclarées :
* Test: GraalVM Native Image -> Les dépendances *org.junit.jupiter:junit-jupiter:6.0.1:test* et *org.junit-platform:junit-platform-launcher:6.0.1:test* sont déclarées dans le fichier *pom.xml* mais non utilisée.

Il est aussi des modules qui utilisent des dépendances qu'ils n'ont pas déclarée dans leur fichier *pom.xml* :
* Test: GraalVM Native Image -> utilise *org.junit.jupiter:junit-jupiter-api:jar:6.0.1:test*
* Gson Extras -> utilise *com.google.errorprone:error_prone_annotations:jar:2.45.0:compile*
* Gson Metrics -> utilise *com.google.caliper:caliper-api:jar:1.0-beta-3:compile*, *com.google.guava:guava:jar:30.1.1-jre:compile*, *com.fasterxml.jackson.core:jackson-core:jar:2.20.1:compile* et *com.fasterxml.jackson.core:jackson-annotations:jar:2.20:compile*
* Gson Protobuf Support -> utilise *com.google.errorprone:error_prone_annotations:jar:2.45.0:compile*

**Partie sur l'utilité des librairies A FAIRE**

### 3.2 Organisation en paquetages

**(Information trouvée avec Code > Calculate Metrics ... > Package Number (que j'ai créé))**
Ce projet décompte un nombre de 24 paquetages différents.

**(Information trouvée avec Code > Calculate Metrics ... > Package Dependencies (que j'ai créé))**
**Insérer Image**

Le **Ca** (Afferent Coupling) d'un paquetage représente toutes les dépendances entrantes. C'est à dire que dans les classes des paquetages utilisant ce dît paquetage, il y a **Ca** import vers ce paquetage. C'est exactement l'inverse pour le **Ce** (Efferent Coupling). 

Les données obtenues ci dessus nous montrent plusieurs choses : d'abord on peut observer que le package *com.google.gson* est une dépendance de 18 des paquetages du projet, ce qui représente 5262 dépendances entrantes au total. En sachant que ce projet compte au total 24 paquetages, le fait que 18 d'entres eux ont besoin de ce paquetage montre sa grande importance au sein du projet. Malgrés tout, celui ci n'est pas indépendant et utilise 6 paquetages soit un quart du projet. Cette situation est quelque peu similaire mais moins accentuée pour les paquetages *stream*, *common*, *reflect* et *internal*. Il est d'autres paquetages par contre qui eux sont dépendant d'un grand nombra d'autres, avec un **Ce** trés élevé, comme *functional* et *internal.bind*. Toutes fois la structure du projet est correcte car il n'y a que 3 paquetages, qu'on pourrait appeler paquetages **centraux**, qui sont des paquetages dont dépendent un grand nombre d'autre.

On peut remarquer 3 couches différentes de paquetages (colonne **D** (Distance from the main sequence)) dans les données précédente :
* Couche basse (**D** proche de **0**) -> On y trouve les paquetages peu dépendants comme *com.example* qui sont trés peu voir pas du tout utilisés par les autres paquetages et qu'on peut considérer comme stable
* Couche intermédiaire -> Ce sont des paquetages utilitaires, utilisés par un certain nombre d'autres paquetages.
* Couche haute (**D** proche de **1**) -> On trouve dans cette couche les paquetages dis principaux qui utilisent de nombreux autres paquetages.

On peut observer des cycles entres les paquetages interne de Gson, notamment entre 9 d'entres eux qui ont tous un indice **Cyclic** à 8. Ces cycles concernent les paquetages interne comme *functional*, *internal* et *reflect*. Cela laisse 15 paquetages sans cycles, ce qui laisse présager une structure globalement acyclique ce qui est favorable à une meilleure maintenabilité.

Le niveau maximal de paquetages de ce projet est 6 comme le montre le paquetage *com.google.gson.internal.bind.util* tandis que le niveau minimal est 2 dont fait parti le paquetage *com.example*.

La hiérarchie des tests ne respecte majoritairement pas la hiérarchie du code source. Mis à part le module **Gson Extras**, tous les autres modules ont une hiérarchie de test qui ne respecte pas la hiérarchie de son code source. Exemple concret nous avons le module **Gson Protobuf Support** qui a un paquetage *com.google.gson.protobuf* dans son code source et un paquetage *com.google.gson.protobu.functionnal* sans ses tests. Ce module ne compte aucun autre paquetage, on peut donc se demander pourquoi ces deux paquetages n'ont pas le même nom ce qui permettrait une meilleure compréhension de la hiérarchie du projet. Aussi il y a le module **Test : GraalVM Native Image** qui ne contient qu'un paquetage de test qui ne teste que des élément du module **Gson**, ce qui indique que sa place aurait été dans ce dit module au lieu de créer un nouveau module. Ce ne sont pas les seules coquilles hiérarchiques du projet et il serait justicieux de revoir cette structure lors de la partie dédiée à la "correction" du projet.

Le paquetage *integration* n'a, dans notre tableau, que des valeures égales à 0 : il n'est utilisé nul part et n'utilise rien. Malgrés tout ce paquetage contient la classe **OSGIManifestIT** qui est un test d'intégration qui vérifie les exports des packages Gson. Même si cette classe importe la classe **GsonBuildConfig** du paquetage *internal*, cela n'est pas comptabilisé par les metrics car il n'y a ni appel de fonction, ni intanciation d'objet.

Quand on analyse les noms des paquetages, on comprend assez facilement l'utilité de chacun d'eux :
* *com.google.gson* est le paquetage central qui contient le code principal de la librairie
* *internal* contient uniquement du code interne qui n'est pas destiné à etre utilisé par des utilisateurs extérieurs
* *reflect* gère la réflexion et la manipulation dynamique des classes
* *stream* est utilisée pour l'écriture et la lecture JSON
* *metrics* contient le code lié aux mesures permettant des fonctionnalités optionnelles.
* ect...

En revanche, aucun nom de paquetage ne nous indique l'utilisation d'un design pattern en particulier. 
On sait l'existence et la liaison du projet avec une base de donnée grâce au paquetage au nom explicite *intenal.sql*.


### 3.3 Répartition des classes dans les paquetages

**Insérer image**

On peut voir dans les données ci dessus les informations suivantes :
* Nombre minimum de classes par paquetage -> 2
* Nombre maximum de classes par paquetage -> 511
* Nombre total de classes du projet -> 1081
* Nombre moyen de classes par paquetage -> 45,04
* Nombre de paquetages avec un nombre de classes supérieur à la moyenne -> 4

Les classes sont réparties dans les paquetages de manière inéquitable. On peut voir par exemple que le paquetage *gson.functional* compte 511 classes soit quasimment la moitié des classes du projet. Aussi on peut noter qu'un paquetage n'a pas besoin d'être un paquetage dît "feuille" pour avoir des classes : le paquetage *gson* contient 137 classes alors qu'il contient aussi 22 autres paquetages. Même chose pour *gson.internal* qui contient 77 classes alors qu'il a un paquetage fils et donc n'est pas une "feuille".

#### Analyse du couplage 
Nous étudierons en particulier les paquetages *com.google.gson*, *internal*, *stream* et *integration*.
* *com.google.gson* -> Ce paquetage a un trés fort couplage entrant ce qui confirme son rôle central dans le projet. En revanche son important couplage sortant et la présence de cycle indique une forte interdépendance avec les autres paquetages ce qui peut être amélioré.
* *internal* -> Celui ci montre un couplage élevé aussi bien entrant que sortant et une présece de cycle. Là aussi on a un paquetage interdépendant.
* *stream* -> En ayant un trés fort couplage entrant, ce paquetage reste cohérent et gère correctement sa mission de gestion de flux JSON
* *integration* -> Le couplage ici est nul car c'est un paquetage de test indépendant ce qui le rend trés cohérent.

On observe donc une architecture centralisée autour de *com.google.gson* qui, comme d'autres paquetages internes, présente un couplage élevé et participe à des cycles ce qui démontre une forte interdépendance. Au contraire, les paquetages comme *integration* présentent un faible couplage et une indépendance. Une perspective d'amélioration serait de réduire les cycles internes.



# GSON

## 1 Présentation globale du projet

### 1.1 Utilité du projet

GSON est une librairie Java qui sert de pont entre des objets Java et la structure d'un fichier JSON. En clair, elle fournit une API qui permet aux utilisateur de créer un objet Java à partir d'une description JSON ou, à contrario, de créer la description JSON d'un objet Java. GSON n'est pas le premier à proposer cette fonctionnalité, mais il se démarque en offrant la possibilité aux utilisateur de passer de Java à JSON sans même avoir besoin du code source Java, là où les autres projets open-sources ayant la même aspiration sont impossible d'utilisation si l'on ne rajoute pas une balise spéciale dans le code source Java.

Le projet ne se lance pas. S'agissant d'une librairie, il doit être importé dans le projet d'un utilisateur, via Gradle ou Maven, qui pourra alors utiliser les méthodes far de GSON, à savoir **toJson()** et **fromJson()** qui permettent de créer une description JSON d'un objet JAVA et inversement.

Le projet ne sort rien en tant que tel.


### 1.2 Description du projet

- Il existe un Readme dans le dépôt. Ce readme explique l'utilité de ce projet, il explique ausi comment utiliser d'autres documents open-source liés à ce projet. Il y a des warning qui indiquent l'état actuel du projet : il est ici actuellement en maintenance. Il montre aussi les limites de ce projet, son utilisation est par exemple non recommandée pour transformer du java en json sur android. Il explique aussi dans le projet comment rajouter cette dépendance a son projet personnel, qu'il soit Gradle ou Maven, avec les lignes nécessaires. Les prérequis sont énoncés, les dépendances le sont aussi. Il y a 5 liens différents vers toute sorte de documentation du projet. Pour build le projet, la commande "mvn clean verify" est donnée. Pour finir, quelques contributeurs, licences et disclaimer.

- Il existe une grande documentation du projet. Il déclare comment utiliser ce projet en tant que dépendance dans ses projets personnels. Il expliques aussi tous les packages que l'on peut importer séparément et comment les importer comme tel. De plus, il y a un ÉNORME "UserGuide.md" qui explique en outre comment fonctionne le projet et comment l'utiliser, avec des examples. Il y a aussi un "TroubleShootingGuide".

- La pertinence des informations données est bonne, tout dans ce projet est bien défini et expliqué. Il y a même des exemples et des démarches à suivre pour faire fonctionner ce projet dans multiples circonstances. La documentation quant à elle est complète et fonctionnelle. Tout dans le projet est définie et fonctionnel. Toutes les informations sont à jour, et quand une maintenance est faite, ou une refonte, l'utilisateur en est prévenu de par le readme.


## 2 Historique du logiciel

### 2.1 Analyse du git

- Le projet recense 158 contributeurs différents. Ils ne sont pas tous équitables quant à leur quantité de commits, ou leur quantités de lignes de code modifiées. Il n'y a que 6 contributeurs avec +100 commits, le 7eme étant à 23 commits. Le projet à été le plus modifié entre 2009 et 2012, puis entre 2022 et 2025.

- Le projet est toujours actif, il y a eu 6 commits depuis début janvier 2026, et il est encore actuellement en maintenance.

- Il y a 11 branches dans le projet, créés en majeur partie il y a +10 ans pour implémenter des design patterns comme strategies, state, refactoring, etc... Actuellement, que 2 branches utilisées : main et une branche dependantbot pour le bot des dépendances.

- L'utilisation des pull request est bel et bien utilisée. Il existe encore 100 pull request, ouverts, et 1107 fermés par le passé.



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


### 3.4

**Insérer image**
L'analyse de la profondeur de l'arbre d'héritage nous montre que la majorité des classes du projet a un DIT (Depth of Inheritance Tree) compris entre 1 et 4 avec une moyenne à 1,24. Cette valeur nous indique deux choses :
* La plupart des classes du projet hérite directement de la classe **Object**
* L'arbre d'héritage global du projet est très peu profond et la majorité des classes du projet sont indépendantes.

On obtient également à travers ces données des informations sur le NOC (Number of Children) de chaque classe. Les classes **TypeToken** et **TypeAdapter** sont les classes avec le plus de descendant, respectivement 194 et 129, ce qui en fait des classes centrales du projet sachant que toutes les autres ont un nombre d'enfant inférieur à 10.

En se concentrant sur le module **Gson** et en en observant le couplage au sein de ses classes, on remarque que 41% de celles ci ont un COP (Coupling Between Object) dît *extreme*, ce qui en fait des classes instables et complexes. On voit aussi que 20 autres pourcents ont un COP modéré. 
Cela dit, cette instabilité se trouvant majoritairement dans le package *functional* qui utilise de nombreux composants du systèmes pour assurer les tests de fonctionnement se trouve expliquée.

**Insérer image**
**Expliquer les données**  

## 4 Analyse approfondie

### 4.1 Tests

- Il y a 1943 tests unitaires dans le projet. Il existe 803 classes de tests. Au sein de ces tests, 2529 assertions sont utilisées. 
Conclusion : La structuration des tests de Gson reflète une philosophie de haute précision et de faible couplage. Le grand nombre de classes (745) par rapport au nombre de tests (1943) indique une organisation granulaire où chaque classe de test se concentre sur un périmètre réduit. De plus, la faible densité d'assertions par test (1,3) démontre une volonté de créer des tests atomiques, facilitant ainsi la localisation des régressions lors de l'évolution de la bibliothèque.

- L'étude de la couverture de code révèle les points suivants :
Pourcentage global : Le projet affiche une couverture de lignes d'environ 88%. C'est un score excellent pour une bibliothèque de production, garantissant que la quasi-totalité des fonctionnalités de sérialisation/désérialisation est vérifiée.
Zones non couvertes : Malgré ce score élevé, certaines parties du code restent "dans l'ombre" :
Gestion des erreurs critiques : Dans le package com.google.gson.internal, certains blocs catch gérant des exceptions d'entrée/sortie (IOException) ne sont pas couverts car ils traitent des cas théoriques quasi-impossibles à reproduire en mémoire vive.
Algorithmes de structures de données : Des classes complexes comme LinkedTreeMap possèdent des branches spécifiques de rééquilibrage d'arbre qui ne sont sollicitées que par des jeux de données très particuliers, non présents dans la suite de tests actuelle.

- Les tests sont des tests unitaires. Tout passe et le projet s'execute correctement.

### 4.2 Commentaires

- Le projet possède 7380 Lignes de commentaires, et 5409 lignes de javadoc. 

### 4.3 Dépréciation

- Identification des classes dépréciées : * La classe com.google.gson.DefaultDateTypeAdapter a souvent été marquée comme dépréciée ou modifiée en interne au profit de solutions plus flexibles dans les versions récentes.

    Certaines classes internes dans le package com.google.gson.internal sont annotées @Deprecated pour décourager les développeurs de s'appuyer sur des détails d'implémentation qui pourraient changer.

- Identification des méthodes dépréciées : * GsonBuilder.setFieldNamingStrategy(FieldNamingStrategy) : Certaines surcharges ont été remplacées par des versions plus modernes.

    JsonParser.parse(String) : C'est l'exemple le plus célèbre. Dans les versions récentes (2.8.6+), les méthodes d'instance de JsonParser sont dépréciées au profit des méthodes statiques comme JsonParser.parseString(String). Cela évite l'instanciation inutile d'un objet JsonParser.

    JsonElement.getAsJsonObject() (et variantes) : Bien que toujours très utilisées, certaines manières d'accéder aux éléments ont été revues pour forcer une meilleure gestion des types.

- Identification des appels à du code déprécié : * Gson contient de nombreux appels internes à son propre code déprécié. Par exemple, les anciennes méthodes de JsonParser appellent souvent les nouvelles méthodes statiques en interne pour assurer la transition.

    Dans les tests unitaires du projet GitHub (src/test/java), on trouve volontairement des appels à du code déprécié pour vérifier que la compatibilité n'est pas rompue (tests de non-régression).

- Appels du code non déprécié vers le déprécié : * Oui, cela arrive : Certaines méthodes publiques modernes appellent des constructeurs ou des méthodes utilitaires internes marquées @Deprecated.

    Conséquences : * Maintenance : Cela crée une "dette technique contrôlée". Le code reste fonctionnel, mais le compilateur génère des avertissements (warnings) lors de la compilation du projet Gson lui-même.

        Performance : Généralement nulle, car la méthode dépréciée n'est qu'une redirection vers la nouvelle logique.

        Risque : Le risque principal est qu'un développeur tiers utilise ces méthodes en pensant qu'elles sont stables, alors que Google se réserve le droit de les supprimer dans une version majeure future (ex: passage de la version 2.x à 3.x).

### 4.4 Duplication de code

- On observe une duplication structurelle dans les TypeAdapters de base (ex: IntegerTypeAdapter, FloatTypeAdapter). La logique de vérification du type de token (peek) avant la lecture est répétée de manière quasi identique. Les classes JsonReader et JsonWriter présentent des structures de contrôle (switch/case) similaires pour la gestion des états du document JSON (début d'objet, début de tableau, etc.).

### 4.5 God Classes

- Nombre de méthodes par classe :

    Min : 1 (certaines interfaces ou adaptateurs simples).

    Max : ~60 (Gson.java).

    Moyenne/Médiane : ~10. La majorité des classes dans internal sont petites et spécialisées.

- Nombre de variables d'instance :

    Min : 0 (classes utilitaires).

    Max : ~20 (Gson.java).

    Moyenne/Médiane : ~3.

    Comparaison : Les classes avec beaucoup de variables (comme Gson) sont celles qui portent toute la configuration globale, ce qui explique leur rôle centralisé.

- Nombre de lignes de code (LOC) :

    Min : ~30 LOC.

    Max : ~1600 LOC (JsonReader.java).

    Moyenne/Médiane : ~150 LOC.

    Comparaison : Les classes les plus longues ne sont pas forcément celles avec le plus de méthodes, mais celles contenant des automates à états complexes (parsing de caractères).

- Identification des God Classes : * Gson.java est la God Class de "coordination" : elle référence presque tout le projet et sert de point d'entrée unique.

    JsonReader.java est une God Class "opérationnelle" : elle est massive car elle gère l'intégralité de la grammaire JSON en un seul endroit pour maximiser les performances de lecture.

### 4.6 Analyse des méthodes

- Complexité cyclomatique :

    Min : 1 (getters/setters).

    Max : > 30 (méthodes comme doPeek() dans JsonReader).

    Moyenne/Médiane : ~4. La logique est généralement linéaire, sauf dans le cœur du parseur.

- Analyse des commentaires :

    Les commentaires sont d'excellente qualité, principalement sous forme de Javadoc.

    Corrélation : Il existe une corrélation directe entre complexité et commentaires. Les sections "hacky" (comme la gestion de l'accès aux champs privés via Unsafe) sont très documentées pour justifier les choix techniques.

- Nombre de lignes de code des méthodes :

    Min : 1 ligne.

    Max : ~150 lignes (read dans ReflectiveTypeAdapterFactory).

    Moyenne/Médiane : ~12 lignes.

- Méthodes avec beaucoup d'arguments : * C'est rare dans Gson grâce au GsonBuilder. Cependant, certains constructeurs internes de ReflectiveTypeAdapterFactory.Adapter peuvent prendre 5 à 6 arguments pour injecter toutes les dépendances de mapping.

- Modification d'état et retour d'information :

    C'est le modèle standard de JsonReader (ex: nextString()). La méthode modifie l'index de lecture du buffer (état) et retourne la valeur (information). Cela suit le principe du curseur.

- Méthodes retournant un code d'erreur :

    Gson n'utilise pratiquement jamais de codes d'erreur numériques (style C). Il utilise des Exceptions dédiées (JsonSyntaxException, JsonIOException) ou des enums internes (comme JsonToken) pour signaler l'état du flux.


## 5 Nettoyage de Code et Code smells

On va ici s'intéresser plus particulièrement au module **Gson**, le module principal du projet.

### 5.1 Règles de nommage

L'analyse des règles de nommage montre le respect des conventions Java. En effet les classes sont nommés avec du CamelCase, certaines d'entres elles ont, dans leur nom, le design pattern qu'elles utilisent (ExclusionStrategy, GsonBuilder, ect) et chacunes d'entres elle ont un nom spécifique qui explique correctement leur fonction. Chaque nom de classe est prononçable tout comme les noms de paquetages qui représentent fidèlement leur utilité.

### 5.2 Nombre magique

La présence de nombres magiques est extremement faible sachant que la plupart d'entres eux son des 0, des 1 ou des -1 qui sont des valeurs magiques plus ou moins acceptables en Java. Les quelques nombres magiques non acceptables sont présents dans le module **Gson Metrics** dans la déclaration de buffers mais restent trés peu nombreux. Malgrès tout, il serait intéressant de corriger ces implémentations incorrecte en transformant ces nombres magiques en variables statiques ou en constantes.

### 5.3 Structure du code
La structure interne des classes est faite de manière à ce que les variables d'instance soient déclarées en début de classe, avant les méthodes. De plus les méthodes publiques précèdent toujours les quelques méthodes privées ce qui améliorer la compréhension de l'API.

### 5.4 Code mort
**Isérer image**
On peut observer que dans le module **Gson**, il y a un nombre impréssionnant de 1972 warnings dû à des déclaration unitilisées. Bien que ce chiffre doit être revu à la baisse dans notre cas car la plus part de ces warning viennent de classes de tests qui logiquement ne doivent etre utilisées nulle part, ce n'est pas le cas de toutes les classes et cela pourrait etre amélioré.
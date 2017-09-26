# Software Development Industrialization

    Master 1 ICE
    ECc - DevOps - Labs
    Introduction to Maven, Git and Continuous Integration

Importez le projet disponible [ici]().


### Objectifs du TP

- Comprendre le fonctionnement de maven.
- Utiliser les artifacts
- Configurer un projet eclipse avec maven.
- Créer son propre MOJO
- Générer des rapports maven
- Utiliser Git pour sauvegarder le code source de votre projet
- Utiliser un système d’Intégration Continue

### Liens utiles

- Site de Maven : http://maven.apache.org/
- Plugin Checkstyle : http://maven.apache.org/plugins/maven-checkstyle-plugin/
- FAQ MAVEN developpez.com : http://java.developpez.com/faq/maven/

### Environnement 

Selon le 3ième lien donnée ci-dessus, Maven est essentiellement un outil de gestion et de compréhension de projet. Maven offre des fonctionnalités de : construction, compilation ; documentation ; rapport ; gestion des dépendances ; gestion des sources ; mise à jour de projet ; déploiement.

Utiliser Maven consiste à définir dans chaque projet à gérer un script Maven appelés POM : *pom.xml*. Nous allons voir dans ce TP qu'un POM permet de définir des dépendances, des configurations pour notamment construire, tester, mettre en paquet des artefacts logiciels (exécutables, tests, documentations, archives, etc.). Pour cela, Maven récupère sur des dépôts maven les outils dont il a besoin pour exécuter le POM. Utiliser Maven requière donc : une (bonne) connexion à Internet car il télécharge beaucoup de choses ; de l'espace disque pour la même raison. Les artefacts qu'il télécharge sont originellement stockés dans le dossier *.m2* dans votre home-dir. Ce dossier contient également le fichier de configuration Maven : settings.xml.

Pour configurer Maven de manière à pour changer l'endroit où les artefacts téléchargés seront stockés (e.g., afin d'éviter des problèmes d'espace disque), vous pouvez modifier le fichier settings.xml de la manière suivante :

    <?xml version="1.0" encoding="UTF-8"?>
    <settings>
        <localRepository>/tmp/mavenrepository</localRepository>
        <offline>false</offline>
    </settings>

## Partie 1 : Utilisation de Maven

### Création d'un projet Maven

Création d’une application basique : pour initialiser un projet Java, vous pouvez utiliser l’archetype maven *maven-archetype-quickstart*. Vous avez juste à fournir un *groupId* et un *artefactId*.

Dans Eclipse:

    new -> other -> maven -> maven project. Vous devrez sélectionner l’archetype,  l’artifactId et le groupId

En ligne de commande (non nécessaire si vous l’avez fait depuis Eclipse):

    mvn archetype:create \
        -DgroupId=[your project's group id] \
        -DartifactId=[your project's artifact id] \
        -DarchetypeArtifactId=maven-archetype-quickstart

Ou simplement :

    mvn archetype:create \
        -DgroupId=[your project's group id] \
        -DartifactId=[your project's artifact id]

Vous obtenez la structure de projet jointe

    |-- src
    |   |-- main
    |   |   `-- java
    |   |       `-- [your project's package]   
    |   |           `-- App.java
    |   `-- test
    |       `-- java
    |           `-- [your project's package]   
    |               `-- AppTest.java
    `-- pom.xml

Par exemple si vous exécutez la commande
    
    mvn archetype:generate \
        -DgroupId=fr.ut2j.m1ice.ci \
        -DartifactId=tpmaven

Vous obtiendrez l’architecture suivante.
 
    |-- src
    |   |-- main
    |   |   `-- java
    |   |       `-- fr
    |   |           `-- ut2j
    |   |               `-- m1ice
    |   |                   `-- ci
    |   |		           `--tpmaven
    |   |	                       `-- App.java
    |   `-- test
    |       `-- java
    |           `-- fr
    |               `-- ut2j
    | 	              `-- m1ice
    |		          `--ci
    |			      `--tpmaven
    |                                   `-- AppTest.java
    `-- pom.xml

Le fichier pom.xml est le fichier de configuration maven du projet. Il décrit les caractéristiques du projet (son nom, sa famille, sa version, etc.), ainsi que les processus (les « builds ») à exécuter (la compilation, l'exécution des tests, la création d'archive, etc.).

### Configuration d'un projet Maven dans Eclipse

Dépuis Eclipse 4.X, le support de maven s’est amélioré. Pour importer votre projet : 

    File -> import -> maven -> existing maven project.

Votre projet est configuré.

## Gestion des dépendances

Intégrer à votre code source le fichier [ici](https://raw.githubusercontent.com/barais/swingx/master/JXBusyLabelTest). 
Vous verrez que le code ne compile pas car il manque une dépendances. Intégrez maintenant la dépendance à swingx. 

    <!-- https://mvnrepository.com/artifact/org.swinglabs/swingx -->
    <dependency>
        <groupId>org.swinglabs</groupId>
        <artifactId>swingx</artifactId>
        <version>1.6.1</version>
    </dependency>

Votre IDE va downloader la dépendance et la mettre automatiquement dans votre classpath. Dans ce sens, cela permet de ne mettre dans votre gestionnaire de source que le code source et le descripteur de projet (pom.xml). 


## Spécialisation du processus de build

Imaginons que vous souhaitiez ajouter une tâche dans le processus de build. Par exemple, compilez votre code source avec la version Java 1.7. Ajoutez la section suivant à votre fichier pom.xml

    <build>
        <plugins>
          <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
              <!-- or whatever version you use -->
              <source>1.7</source>
              <target>1.7</target>
            </configuration>
          </plugin>
        </plugins>
    </build>

Vous pouvez ajouter de nombreux plugin dans cette section. 
https://maven.apache.org/plugins/

## Génération de rapports

### Générer la Javadoc

Ajoutez des commentaires à votre code, puis ajoutez le code suivant dans le pom.xml de votre projet.

    <reporting>
      <plugins>
       <plugin>
    	<groupId>org.apache.maven.plugins</groupId>
    	<artifactId>maven-javadoc-plugin</artifactId>
    	<version>2.10.4</version>
       </plugin>
     </plugins>
    </reporting>

Puis lancez en ligne de commande (au même niveau que le fichier pom.xml) : mvn site. Cette tâche crée un site Web pour votre projet. Par défaut, les goals maven générant des fichiers travaillent dans le dossier target se trouvant au même niveau que le fichier pom.xml. Allez dans le dossier target/site et ouvrez le fichier *index.html*. Vous pouvez regarder la Javadoc générée en cliquant sur Project reports.

Eclipse permet de lancer cette commande maven sans passer par la ligne de commande (voir menu run d'Eclipse). 

### Valider la qualité du code avec le plugin checkstyle

Ajoutez à la section <plugins> dans <reporting> le plugin checkstyle :

    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-checkstyle-plugin</artifactId>
	    <version>2.17</version>
    </plugin>

Lancez *mvn clean site* (le goal clean vide le dossier target). Sur la page Web générée par « mvn site », une nouvelle section Checkstyle a été ajouté dans Project reports.

- Site de l'outil CheckStyle : http://checkstyle.sourceforge.net/
- Site du plugin Maven : http://maven.apache.org/plugins/maven-checkstyle-plugin/

### Couverture des tests

A quel point les développeurs ont réalisé des tests unitaires ? Quelles parties de l'application n'ont pas été testées ?

    <reporting>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>cobertura-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </reporting>

Lien utile : http://mojo.codehaus.org/cobertura-maven-plugin/usage.html

## Intégration Continue

Vous allez étudier les outils d'intégration continue Sonar et Jenkins. La différence entre ces deux outils est simple : Sonar est un outil d'assurance qualité tandis que Jenkins est un outil de « release engineering ». Les deux sont évidemment complémentaires.

### Intégration avec l'outil Sonar

Téléchargez [Sonar](https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-5.6.1.zip), de-compressez-le dans */tmp*, puis lancez Sonar : 
    
    sh /tmp/sonarqube-5.6.1/bin/linux-x86-64/sonar.sh start

Au niveau du pom de votre projet, lancez la commande *mvn sonar:sonar* puis allez à l'adresse http://localhost:9000/. Loguez-vous avec le login admin et le mot-de-passe admin. Allez dans Quality Profiles et changer les règles de qualités utilisées puis relancer mvn sonar:sonar. Baladez-vous dans Sonar pour explorer ces différentes fonctionnalités. 

Vous pourrez ensuite arrêter sonar avec
    
    sh /tmp/sonarqube-5.6.1/bin/linux-x86-64/sonar.sh stop

### Intégration avec Jenkins

Sur http://jenkins-ci.org/, prenez la version LTS Java Web Archive (.war) pour la mettre dans /tmp. Il faut déplacer l'endroit où la configuration Jenkins sera stockée :

    export JENKINS_HOME=/tmp/.jenkins

Démarrez jenkins : 

    java -jar jenkins.war. 
    
Allez dans votre navigateur : http://localhost:8080/.

Configurez Jenkins :
- Commencez par cliquer sur « enable auto refresh »
- Allez dans le menu « Jenkins → Manage Jenkins → Global Tool Configuration »
Cliquez sur « Add JDK ». Saisissez un nom quelconque permettant d'identifier la JDK. Cochez « I agree... ». Ignorez le message d'erreur « requires Oracle account »
- Cliquez sur « Add Maven ». Saisissez un nom quelconque permettant d'identifier cette version de Maven.
- Cliquez sur « Save ». Le but de ces configurations est de pouvoir installer, si on le souhaite, plusieurs Maven ou plusieurs JDK (par exemple, certains projets peuvent nécessiter Java 6 et d'autres Java 8).
- Installez le module git pour Jenkins : « Jenkins → Manage Plugins → Available → « GIT plugin » et « Maven integration plugin » → Download now and install after restart → Restart Jenkins ». Ces plugins peuvent être déjà installés.

Mettez votre code sur github:

- créez un nouveau repository via l'interface github
- liez votre dépôt local au distant : 
    
    git remote add origin https://github.com/login/nomRepo.git

- mettez votre code sur ce dépôt : git push origin master (en cas de non-fast-forward : git pull origin master)

Par défaut, jenkins ne contient pas le plugin pour gérer des repository Git, Il vous faut installer le plugin “Git Plugin”. De plus, vous devez configurer Maven (voir Configure System).

Ensuite créez un « job » en cliquant sur « create new job -> Maven Project ». Donnez un nom à votre projet. Définissez les sources en indiquant l'url du repository git que vous avez préalablement créer sur github (i.e. https://github.com/login/nomRepo.git) et enfin définissez les goals maven pour le build (« Add build step » → « Invoke top-level Maven... ») : pour commencer clean package. Si le pom n’est pas à la racine de votre projet, cliquez sur « Advanced... » → remplissez le champ POM. Lancer un build.

Dans l'historique des builds, une icône bleu doit apparaître à la fin de la construction pour désigner la construction correcte de l'artefact (bleu car le développeur de Jenkins est Japonais et au Japon le bleu équivaut au vert chez nous, d'ailleurs un plugin Jenkins existe pour afficher des icônes vertes et non bleues...). Cliquez ensuite sur le lien sous « Module builds », les artefacts créés par jenkins en utilisant le POM du projet sont visibles dont un jar. Ouvrez ce dernier, vous verrez que le manifest est vide. Dans les étapes suivantes vous allez compléter le POM pour obtenir un vrai jar exécutable.

### Packager des artefacts logiciels avec maven

Comme expliqué précédemment, ces artefacts logiciels peuvent être produits soit en utilisant directement maven en ligne de commande, soit en utilisant Jenkins. Nous allons dans cette dernière partie étudier différents plugins maven permet de réaliser de nombreuses actions de liées à la construction d'artefacts logiciels. 

#### Création d'un jar exécutable via maven

Pour construire des artefacts vous allez ajouter un bloc \<build> dans le bloc \<project> de votre POM. Dans ce nouveau bloc vont être ajouté les plugins utilisés dans cette partie comme le suivant :

    <build>
    <plugins>
        <plugin> 
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId> 
            <version>3.5.1</version> 
            <configuration> 
                <source>1.8</source> 
                <target>1.8</target> 
            </configuration>
        </plugin>
    </plugins>
    </build>

Ce plugin permet de compiler du code Java. Vous pouvez spécifier la version de Java à utiliser. 

Cf. : http://maven.apache.org/plugins/maven-compiler-plugin/

Générez un jar exécutable grâce au plugin maven-jar-plugin qui vous permettra de définir un manifest :
http://maven.apache.org/plugins/maven-jar-plugin/ (regardez les exemples « creating an executable JAR file »).

Lancez mvn clean install et exécutez le nouveau jar généré se trouvant dans le dossier target. Commitez et pushez vos changements, relancez le build Jenkins, allez dans le « last build » et cliquez sur le « Module Builds » listé : la liste des éléments produits doit être visible et téléchargeable.

#### Exécution de test via maven
Utilisez le plugin maven-surefire-plugin pour exécuter les tests du projet lors de la commande mvn clean install, cf.: http://maven.apache.org/surefire/maven-surefire-plugin/
Commitez le POM sur github et relancez un build sur Jenkins afin d'observer les évolutions apportées.

#### Création d'archives des sources et des exécutables
Le plugin maven-assembly-plugin permet de créer des archives. Ce plugin est notamment très utile pour créer des archives des sources ou des fichiers exécutables, cf : http://maven.apache.org/plugins/maven-assembly-plugin/

Étudiez et adaptez l'utilisation de ce plugin dans le projet suivant :
https://github.com/arnobl/latexdraw/blob/master/latexdraw-core/net.sf.latexdraw.bundle/pom.xml
pour l'utiliser dans votre projet afin de créer un zip des sources et un autre contenant le jar exécutable.

Commitez les modifications sur github et relancez un build sur Jenkins afin d'observer les évolutions apportées. 

#### Daily/Nightly build avec Jenkins

Configurer vos builds Jenkins pour qu'ils se construisent automatiquement à 1h du matin tous les jours.
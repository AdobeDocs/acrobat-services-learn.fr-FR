---
title: Prise en main de l’API Adobe PDF Services et de Java
description: Les développeurs peuvent démarrer en quelques minutes grâce aux fichiers d’exemple prêts à l’emploi fournis pour accéder à tous les services web disponibles
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6676
thumbnail: KT-6676.jpg
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '457'
ht-degree: 0%

---

# Prise en main de l’API Adobe PDF Services et de Java

![Créer une image PDF principale](assets/GettingStartedJava_hero.jpg)

Les développeurs peuvent démarrer en quelques minutes grâce aux fichiers d’exemple prêts à l’emploi fournis pour accéder à tous les services web disponibles. Ce tutoriel vous guide à travers toutes les étapes pour commencer à exécuter les exemples à l’aide du SDK Java des services PDF :

## Étape 1 : obtention des informations d’identification et téléchargement des fichiers d’exemple

La première étape consiste à obtenir un identifiant (clé API) pour déverrouiller l’utilisation. [Inscrivez-vous ici](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) à l’essai gratuit et cliquez sur « Commencer » pour créer vos nouvelles informations d’identification.

![Etape 1 :](assets/GettingStartedJava_step1.png)

Il est important de choisir un « compte personnel » pour s’inscrire à l’essai gratuit :

![Personnel](assets/GettingStartedJava_personal.png)

À l’étape suivante, vous choisirez le service d’API des services de PDF, puis vous ajouterez un nom et une description pour vos informations d’identification.

Il existe une case à cocher pour « Créer un exemple de code personnalisé ». Choisissez cette option pour que vos nouvelles informations d’identification soient automatiquement ajoutées à vos fichiers d’exemple. Cela vous permettra d’enregistrer l’étape manuelle consistant à les ajouter à votre projet.

Ensuite, choisissez Java comme langue pour recevoir les exemples spécifiques à Java, puis cliquez sur le bouton Créer des informations d’identification.

![Informations d&#39;identification](assets/GettingStartedJava_credentials.png)

Vous recevrez un fichier .zip à télécharger appelé PDFToolsSDK-JavaSamples.zip qui peut être enregistré sur votre système de fichiers local.

## Étape 2 : Configuration de votre environnement Java

1. Installez [Java 8 ou version ultérieure](https://www.oracle.com/java/technologies/javase-downloads.html) si ce n&#39;est pas déjà fait.
1. Exécutez `javac -version` pour vérifier votre installation.
1. Vérifiez que le dossier bin JDK est inclus dans la variable PATH (la méthode varie en fonction du système d’exploitation).
1. Installez [Maven](https://maven.apache.org/install.html) à l&#39;aide de votre outil préféré si ce n&#39;est pas déjà fait.

Les exemples personnalisés fournissent de nombreux éléments, du code d’exemple prêt à l’emploi à un fichier json d’informations d’identification incorporé, en passant par les connexions préconfigurées et les dépendances.

1. Téléchargez [l&#39;exemple de projet](https://github.com/adobe/pdftools-java-sdk-samples).
1. Créez l’exemple de projet avec Maven : mvn clean install.
1. Testez l&#39;exemple de code sur la ligne de commande ou dans l&#39;IDE de votre choix.

## Dernières réflexions

L’API PDF Services peut vous aider à éliminer les processus manuels en automatisant les workflows courants et en transférant la charge de traitement vers le cloud. Dans un monde où chaque navigateur traite le PDF différemment, en tirant parti de l&#39;API Adobe PDF Embed avec l&#39;API PDF Services, vous pouvez créer des processus rationalisés, fiables et prévisibles qui s&#39;exécutent et s&#39;affichent correctement **à chaque fois**, quelle que soit la plate-forme ou le périphérique.

## Ressources et étapes suivantes

* Pour obtenir une aide et une assistance supplémentaires, consultez le forum de la communauté des [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) d&#39;Adobe

* API des services de PDF [Documentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197) pour les questions API des services PDF

* [Contactez-nous](https://www.adobe.com/go/pdftoolsapi_requestform) pour toute question sur les licences et les prix

* Articles connexes

  [La nouvelle API PDF Services offre encore plus de fonctionnalités pour les workflows de documents](https://community.adobe.com/t5/acrobat-services-api-discussions/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [Version de juillet de [!DNL Adobe Acrobat Services] : services PDF Embed et PDF](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

---
title: Prise en main de l’API Adobe PDF Services et de .Net
description: Les développeurs peuvent démarrer en quelques minutes grâce aux fichiers d’exemple prêts à l’emploi fournis pour accéder à tous les services web disponibles
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6675
thumbnail: KT-6675.jpg
keywords: En vedette
exl-id: 22c59c75-fd99-4467-a6f6-917fb246469a
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '483'
ht-degree: 0%

---

# Prise en main de l’API Adobe PDF Services et de .Net

![Créer une image PDF principale](assets/GettingStartedJava_hero.jpg)

Les développeurs peuvent démarrer en quelques minutes grâce aux fichiers d’exemple prêts à l’emploi fournis pour accéder à tous les services web disponibles. Ce tutoriel vous guide à travers toutes les étapes pour commencer à exécuter les exemples à l&#39;aide du SDK .Net des services de PDF :

## Étape 1 : obtention des informations d’identification et téléchargement des fichiers d’exemple

La première étape consiste à obtenir un identifiant (clé API) pour déverrouiller l’utilisation. [Inscrivez-vous ici](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) à l’essai gratuit et cliquez sur « Commencer » pour créer vos nouvelles informations d’identification.

![Etape 1 :](assets/GettingStartedJava_step1.png)

Il est important de choisir un « compte personnel » pour s’inscrire à l’essai gratuit :

![Personnel](assets/GettingStartedJava_personal.png)

À l’étape suivante, vous choisirez le service d’API des services de PDF, puis vous ajouterez un nom et une description pour vos informations d’identification.

Il existe une case à cocher pour « Créer un exemple de code personnalisé ». Choisissez cette option pour que vos nouvelles informations d’identification soient automatiquement ajoutées à vos fichiers d’exemple. Cela vous permettra d’enregistrer l’étape manuelle consistant à les ajouter à votre projet.

Ensuite, choisissez Node.js comme langue pour recevoir les échantillons spécifiques à Node.js et cliquez sur le bouton « Créer des informations d’identification ».

![Informations d&#39;identification](assets/GettingStartedJava_credentials.png)

Vous recevrez un fichier .zip à télécharger appelé PDFToolsSDK-.NetSamples.zip qui peut être enregistré sur votre système de fichiers local.

## Étape 2 : Configurez votre environnement .Net et exécutez l&#39;exemple de code

1. Télécharger et installer le [.Net SDK](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
1. Extrayez le **[!UICONTROL PDFToolsSDK-.NetSamples.zip]** téléchargé et décompressez son contenu
1. cd vers le répertoire racine des échantillons **[!UICONTROL adobe-DC.PDFTools.SDK.NET.Samples]**
1. À partir du répertoire racine des échantillons, exécutez `dotnet build`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>dotnet build

   Vous êtes maintenant prêt à exécuter les fichiers d’exemple !

   Les étapes finales suivantes vous montrent comment exécuter votre premier échantillon avec l’opération Créer un PDF à partir de Word :

1. Dans le répertoire racine des exemples, modifiez le répertoire en créant le dossier CreatePDFFromDocx, cd CreatePDFFromDocx/

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>cd CreatePDFFromDocx/

1. exécuter `dotnet run CreatePDFFromDocx.csproj`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples\CreatePDFFromDocx>dotnet exécutez CreatePDFFromDocx.csproj

Votre PDF sera créé à l’emplacement indiqué dans la sortie, qui est par défaut le même dossier.

## Dernières réflexions

L’API PDF Services peut vous aider à éliminer les processus manuels en automatisant les workflows courants et en transférant la charge de traitement vers le cloud. Dans un monde où chaque navigateur traite le PDF différemment, en tirant parti de l&#39;API Adobe PDF Embed avec l&#39;API PDF Services, vous pouvez créer des processus rationalisés, fiables et prévisibles qui s&#39;exécutent et s&#39;affichent correctement **à chaque fois**, quelle que soit la plate-forme ou le périphérique.

## Ressources et étapes suivantes

* Pour obtenir de l&#39;aide et une assistance supplémentaires, consultez le forum de la communauté des [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all)

* API des services de PDF [Documentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197) pour les questions API des services PDF

* [Contactez-nous](https://www.adobe.com/go/pdftoolsapi_requestform) pour toute question sur les licences et les prix

* Articles connexes

  [La nouvelle API PDF Services offre encore plus de fonctionnalités pour les workflows de documents](https://community.adobe.com/t5/acrobat-services-api-discussions/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [Version de juillet de [!DNL Adobe Acrobat Services] : services PDF Embed et PDF](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

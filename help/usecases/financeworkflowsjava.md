---
title: Gestion des workflows de documents financiers en Java
description: '[!DNL Adobe Acrobat Services] fournit tous les outils, services et fonctionnalités nécessaires au traitement et à l''extraction de données à partir de documents financiers PDF'
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-7482
thumbnail: KT-7482.jpg
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1204'
ht-degree: 0%

---

# Gestion des workflows de documents financiers en Java

![Bannière principale de cas d&#39;utilisation](assets/UseCaseFinancialHero.jpg)

Le secteur financier utilise beaucoup les fichiers de PDF pour l’exchange des données, car ils permettent de conserver le format, la conception et la structure des documents. Ce format robuste permet aux analystes financiers et aux conseillers financiers d&#39;aider leurs clients à prendre des décisions éclairées.

Le format PDF, cependant, peut être difficile à traiter et à automatiser, en particulier lorsqu’il associe plusieurs sources de données, un cas d’utilisation courant dans le secteur financier. La création d’une solution personnalisée pour traiter les documents PDF est une option, mais il n’est pas nécessaire d’investir trop de temps et d’argent dans les logiciels et les infrastructures. [!DNL Adobe Acrobat Services] fournit tous les outils, services et fonctionnalités nécessaires au traitement et à l&#39;extraction de données à partir des documents du PDF.

## Ce que vous pouvez apprendre

Dans ce tutoriel pratique, découvrez comment utiliser les API [!DNL Adobe Acrobat Services] pour les applications [!DNL Java Spring Boot]. Vous créez une application Model-view-controller (MVC) qui extrait le contenu des documents de mot de PDF, le convertit dans d’autres formats de données tels qu’Excel, associe plusieurs mots de PDF et protège les ressources par mot de passe. Ce tutoriel explique comment traiter les documents de PDF et les afficher sur vos sites Web à l&#39;aide de l&#39;[API PDF Embed](https://developer.adobe.com/document-services/apis/pdf-embed) Adobe.

## Ressources et API pertinentes

* [API PDF Services](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [API PDF Embed](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Exemples de projet](https://github.com/adobe/pdftools-java-sdk-samples)

## Configuration

[!DNL Adobe Acrobat Services] utilise un système d&#39;authentification pour contrôler l&#39;accès aux ressources. Pour accéder aux services, vous devez demander une clé API à Adobe pour votre organisation ou application. Si vous disposez d’une clé API, passez à la section suivante. Pour créer une nouvelle clé API, consultez la page [Prise en main](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) sur le site [!DNL Acrobat Services]. Vous pouvez créer une clé à l’aide de son essai gratuit, qui fournit 1 000 transactions de document pouvant être utilisées pendant six mois maximum.

Pour suivre ce tutoriel, vous avez besoin de deux ensembles de clés API :

* Services Adobe PDF : utilisés pour traiter le document du PDF

* API Adobe PDF Embed

Après avoir créé les informations d&#39;identification, copiez les informations d&#39;identification de l&#39;API des services de PDF et la clé privée dans l&#39;application [!DNL Spring Boot] dans la section des ressources. En savoir plus sur les [bibliothèques et dépendances Maven et Gradle](https://developer.adobe.com/document-services/docs/overview/pdf-services-api) sur le site web [!DNL Adobe Acrobat Services]. Assurez-vous de configurer tous les packages et bibliothèques nécessaires avant de continuer.

![Capture d&#39;écran de l&#39;emplacement du répertoire pour les identifiants d&#39;API des services de PDF](assets/FAWJ_1.png)

Pour configurer les services de journalisation, consultez la [documentation de l&#39;Adobe](https://developer.adobe.com/document-services/docs/overview/pdf-services-api) et faites défiler jusqu&#39;à la section Journalisation.

>[!NOTE]
>
> Dans votre environnement de production, n’enregistrez pas les clés privées dans le contrôle de version. Utilisez toujours un coffre secret ou un service d&#39;injection de clés pour empêcher l&#39;utilisation non autorisée des informations d&#39;identification.

Maintenant que votre application [!DNL Spring Boot] est configurée, vous pouvez continuer à traiter les mots de PDF et à générer des rapports pour les clients.

## Envoi des données du rapport

Pour utiliser l&#39;API Adobe PDF Services, commencez par configurer un `ExecutionContext` qui utilise les informations d&#39;identification que vous fournissez. Puisque vous disposez des informations d’identification dans votre application, vous pouvez les lire à partir du fichier et créer le contexte comme suit :

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

Ensuite, obtenez le contexte pour traiter les documents du PDF. Voici les actions que vous pouvez effectuer :

* Convertissez les documents du PDF (au format Excel, Word ou graphique)

* Création des documents PDF (depuis HTML, Excel, Word, etc.)

* Combinaison de plusieurs documents PDF

* Protect et annulation de la protection des documents par mot de PDF (vous devez avoir le mot de passe)

* Optimisation des documents du PDF pour leur diffusion sur les réseaux

Tous ces exemples sont disponibles dans le référentiel [GitHub samples](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples).

Ensuite, dans [!DNL Spring Boot], vous pouvez obtenir un fichier à l&#39;aide du chemin d&#39;accès Chaîne ou du flux dans lequel le fichier est téléchargé. Chaque opération que vous effectuez doit être initialisée et un chemin d&#39;accès au fichier d&#39;entrée doit être défini. Pour ce tutoriel, vous utilisez les rapports de PDF accessibles au public de [Blackrock](https://www.blackrock.com/us/individual/products/investment-funds). Vous pouvez utiliser toute autre source, y compris vos propres rapports.

Commencez par capturer l’objet FileRef à partir du fichier. Pour plus de simplicité, concentrez-vous sur les fichiers par chemin de chaîne. Vous trouverez ci-dessous une opération permettant de convertir un fichier dans votre chemin d’accès de PDF vers Excel :

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

Après cette étape, votre programme est prêt à exécuter la première opération sur le PDF. Ensuite, vous exécutez l&#39;opération et obtenez le résultat dans la feuille Excel :

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

Ce scénario ne gère qu’un seul fichier de PDF. Vous pouvez également commencer par plusieurs fichiers de PDF et les regrouper en un seul fichier. L&#39;utilisation de plusieurs fichiers est courante dans les rapports de données financières, car vous devez traiter des fonds provenant de plusieurs sources pour fournir un rapport complet.

## Génération du rapport

[!DNL Adobe Acrobat Services] ne prend pas en charge le traitement des documents Excel prêts à l&#39;emploi, mais vous pouvez toujours utiliser les infrastructures de la communauté et les bibliothèques pour traiter le contenu.

Par exemple, vous pouvez utiliser le [API Apache](https://poi.apache.org/) pour traiter Excel (ou d&#39;autres documents Microsoft) dans votre application [!DNL Java Spring Boot], ou vous pouvez effectuer d&#39;autres tâches manuelles ou automatisées sur le fichier Excel.

Dans cet exemple, en commençant par les documents de votre PDF, vous extrayez la valeur nette de vos trois fonds et les affichez dans un tableau. Vous pouvez également extraire d’autres informations, telles que des graphiques et des tableaux, en fonction de vos besoins et des données disponibles. Vous pouvez même importer des données provenant d’autres sources.

Une fois votre rapport généré (dans cet exemple, au format Excel), vous pouvez utiliser les opérations Adobe PDF Services pour reconvertir le rapport en document de PDF et le protéger.

Pour convertir le rapport au format Excel en document PDF, procédez comme suit :

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
CreatePDFOperation exportOperation = CreatePDFOperation.createNew();

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_EXCEL);
exportOperation.setInput(inputPdf);

try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_PDF);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

>[!TIP]
>
> Pour éviter d&#39;avoir à recréer l&#39;objet chaque fois qu&#39;une demande est envoyée, utilisez l&#39;injection de dépendance de Spring pour injecter l&#39;objet `ExecutionContext`.

Ce code génère un document de PDF à partir du rapport au format Excel.

Avant de fournir ce mot de PDF à vos clients, vous pouvez le protéger avec un mot de passe. Créez une autre opération qui gère cette protection pour vous, ProtectPDFOperation, puis utilisez ProtectPDFOptions pour ajouter le mot de passe au document.

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

Ensuite, spécifiez l’entrée et exécutez l’opération. Le fichier obtenu doit comporter un mot de passe pour empêcher tout accès non autorisé.

## Affichage du rapport

Une fois le rapport de votre PDF généré, vous pouvez l’afficher sur le site web à l’aide de l’API Adobe PDF Embed. Cette API JavaScript permet aux développeurs web de charger et de rendre les documents du PDF en mode natif dans le navigateur web.

>[!NOTE]
>
> À ce stade, vous avez besoin du deuxième jeton d’identification, l’ID client.

Dans votre application [!DNL Spring Boot], ajoutez l&#39;extrait de HTML suivant à l&#39;endroit où vous souhaitez afficher le rapport du PDF :

```
<div id="pdf-viewer"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
    document.addEventListener("adobe_dc_view_sdk.ready", function()
    {
        var adobeDCView = new AdobeDC.View({ clientId: "<your-client-id-here>", divId: "pdf-viewer" });
        adobeDCView.previewFile(
        {
            content: {
                location: {
                    url: "<your-document.pdf>"
                }
            },
            metaData: {
                fileName: "<document-name.pdf>"
            }
        });
    });
</script>
```

Ce script charge le document du PDF et permet aux utilisateurs d’annoter et de commenter les documents. Voici la vue de cette API Embed comme indiqué dans Firefox :

![Capture d&#39;écran d&#39;un document de PDF dans Firefox](assets/FAWJ_2.png)

L’API PDF Embed fournit tous les outils nécessaires pour prévisualiser le PDF et annoter le rapport.

## Marche à suivre

Ce tutoriel pratique a exploré les API [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/homepage/) et expliqué comment utiliser ces services pour traiter les données des PDF et générer des rapports pour les décisions financières. Il a montré comment vous pouvez intégrer les API dans vos systèmes, en utilisant [!DNL Java Spring Boot] comme exemple de framework, pour montrer à quel point il est facile de traiter rapidement les documents de PDF.

Explorez [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/homepage/) et découvrez ce que les services Adobe PDF peuvent apporter à votre entreprise. Pour en savoir plus sur les fonctionnalités disponibles dans le SDK, consultez le [référentiel GitHub](https://github.com/adobe/pdftools-java-sdk-samples) pour obtenir des exemples et découvrez comment l&#39;[API PDF Embed](https://developer.adobe.com/document-services/apis/pdf-embed) peut vous aider à afficher rapidement les PDF dans vos applications.

Pour combiner et manipuler facilement des documents et créer des rapports de PDF utiles pour vos clients financiers, commencez par créer votre [compte développeur Adobe](https://developer.adobe.com/document-services/homepage/) gratuit dès aujourd&#39;hui.

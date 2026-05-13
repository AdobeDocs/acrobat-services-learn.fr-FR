---
title: Utilisation de l’API des services de PDF pour exporter du PDF vers Word, PowerPoint, etc.
description: Découvrez comment exécuter l’opération d’exportation de l’API PDF Services à l’aide de fichiers d’exemple pour les langages Node.js, Java et .Net
feature: PDF Services API
role: Developer
level: Intermediate
type: Tutorial
jira: KT-6674
thumbnail: KT-6674.jpg
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8
TQID: https://experienceleague.adobe.com/CV-KH0fg1Tjnr7eCmNaFMtttWO1QUCyjfyIfwg1Vqm0
product_v2: id: acdc2bde-2937-4877-90d9-031dd66278c9
feature_v2: id: b1809bd0-a86b-4991-8083-2e3b517fc3b8id: c4d07275-6387-4756-8bf7-681e581ffd27
subfeature_v2: id: c6f72a9c-54c4-4933-93c9-d7c656ff1f14
role_v2: id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
level_v2: id: b5a62a22-46f7-4f0d-b151-3fc640bef588
topic_v2: id: bce87dde-a4ab-44c9-8a18-ad66e4ddb377
source-git-commit: 0110d2606056220c4236fe2f0e3afbfc112746e7
workflow-type: tm+mt
source-wordcount: 524
ht-degree: 2%

---

# Utilisation de l’API des services de PDF pour exporter du PDF vers Word, PowerPoint, etc.

![Créer une image PDF principale](assets/ExportPDF_hero.jpg)

L’API Adobe PDF Services convertit les fichiers du PDF en fichiers MS Office, texte et images à l’aide des API. Il existe de nombreux cas d’utilisation courants pour déverrouiller des PDF existants à des fins d’édition et d’analyse de contenu. Les développeurs d’API de services de PDF peuvent facilement intégrer cette fonctionnalité dans les systèmes et applications existants. Convertissez les fichiers de PDF en MS Word pour modifier le contenu, les approbations et l’envoi ultérieur pour signature afin de créer des workflows de contrat personnalisés. Ou exportez le contenu du PDF au format MS Excel pour les calculs de factures et financiers ou l’analyse des données.

L’opération d’exportation prend en charge les conversions de fichiers PDF suivantes :

* PDF à Microsoft Word (DOC, DOCX)
* PDF à Microsoft PowerPoint (PPTX)
* PDF à Microsoft Excel (XLSX)
* PDF au texte (RTF)
* PDF à l’image (JPEG, PNG)

Dans ce tutoriel, découvrez les principes de base de l’exécution de votre première opération d’exportation d’API PDF Services à l’aide de fichiers d’exemple pour les langages Node.js, Java et .Net.

## Étape 1 : Créez vos informations d’identification et configurez votre environnement :

Utilisez les tutoriels de prise en main ci-dessous pour créer vos identifiants d’API, télécharger des fichiers d’exemple et configurer votre environnement.

[Prise en main de l’API PDF Services et de Java](gettingstartedjava.md)

[Prise en main de l’API des services de PDF et de .Net](gettingstartednet.md)

[Prise en main de l’API PDF Services et de Node.js](createpdffromhtml.md)

## Étape 2 : exécutez l’opération d’exportation au format PDF à l’aide des fichiers d’exemple

**Java**

1. Ouvrez une invite de commande.

1. Transformez les répertoires en répertoire d’exemple de code.

   Par exemple, C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples

1. Exécutez la commande suivante :

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX`

Votre PDF est créé dans le répertoire src/main/resources.

**.Net**

1. Ouvrez une invite de commande.

1. Transformez les répertoires en répertoire d’exemple de code.

   Par exemple, C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. Remplacez à nouveau les répertoires dans le répertoire ExportPDFtoDocx.

1. Exécutez la commande suivante :

   `dotnet run ExportPDFToDocx.csproj`

Votre PDF est créé dans le même répertoire.

**Node.js**

1. Ouvrez une invite de commande.

1. Transformez les répertoires en répertoire d’exemple de code.

   Par exemple, C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. Exécutez la commande suivante :

   `node src/ocr/ocr-pdf.js`

Votre PDF est créé à l’emplacement indiqué dans la sortie, qui est par défaut le répertoire pdfServicesSdkResult.

## Dernières réflexions

Vous devez maintenant avoir un exemple fonctionnel qui peut être importé dans vos applications existantes pour démarrer une preuve de concept. Dans chacun des répertoires d’exemple, vous pouvez voir un autre exemple pour exporter des fichiers de PDF au format image. Les étapes ci-dessus vous permettent également d’exécuter cet exemple. Pour passer à un autre format, vous pouvez mettre à jour le code vers le nouveau format souhaité :

SupportedTargetFormats.PPTX

Et le résultat de destination :

output/exportPdfOutput.PPTX

Vers un autre format.

## Ressources et étapes suivantes

* Pour obtenir de l&#39;aide et une assistance supplémentaires, consultez le forum de la communauté des [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all)

* API des services de PDF [Documentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197) pour les questions API des services PDF

* [Contactez-nous](https://www.adobe.com/go/pdftoolsapi_requestform) pour toute question sur les licences et les prix

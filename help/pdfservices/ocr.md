---
title: Utilisation de l’API Adobe PDF Services pour la reconnaissance optique des caractères des fichiers du PDF
description: La reconnaissance optique de caractères vous permet de déverrouiller des mots de PDF numérisés pour extraire du texte et créer des fichiers pouvant faire l’objet de recherches
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6677
thumbnail: KT-6677.jpg
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '567'
ht-degree: 0%

---

# Utilisation de l’API Adobe PDF Services pour la reconnaissance optique des caractères des fichiers du PDF

![Créer une image PDF principale](assets/OCR_hero.jpg)

Avec la reconnaissance optique de caractères (ROC), vous pouvez déverrouiller les PDF numérisés pour extraire du texte et créer des fichiers pouvant faire l’objet de recherches. À l’aide de nos puissantes API basées sur le cloud, intégrez la ROC dans n’importe quel workflow documentaire pour une solution parfaite d’archivage, de copie de texte et de création d’index de documents consultables. Créez des archives consultables à partir de référentiels de PDF numérisés pour déverrouiller des informations importantes et gagner du temps avec une recherche rapide. Vous pouvez également appliquer la ROC à vos PDF à partir de numérisations chargées afin de pouvoir les modifier pour les utiliser dans les workflows d’intégration.

Les développeurs peuvent démarrer en quelques minutes grâce aux fichiers d’exemple prêts à l’emploi fournis pour la ROC.

Dans ce tutoriel, vous apprendrez à exécuter votre première opération de ROC d’API PDF Services à l’aide de fichiers d’exemple pour les langages Node.js, Java et .Net.

## Étape 1 : Créez vos informations d’identification et configurez votre environnement

Utilisez les tutoriels de prise en main ci-dessous pour créer vos identifiants d’API, télécharger des fichiers d’exemple et configurer votre environnement.

[Prise en main de l’API PDF Services et de Java](gettingstartedjava.md)

[Prise en main de l’API des services de PDF et de .Net](gettingstartednet.md)

[Prise en main de l’API PDF Services et de Node.js](createpdffromhtml.md)

## Exécutez l’exemple de ROC fourni dans les fichiers d’exemple

Notre opération de ROC autorise les paramètres régionaux anglais par défaut, mais prend également en charge l&#39;allemand, le français, le danois et [d&#39;autres langues](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language). La valeur par défaut est locale en-us.

Lorsque vous transmettez des options avec une opération de ROC incluant des paramètres régionaux spécifiques, la méthode accepte également le paramètre &#39;type&#39; qui a deux options :

* SEARCHABLE_IMAGE : modifie l’image d’origine pendant le processus de nettoyage (par exemple, en la désinclinant) avant de placer un calque de texte invisible dessus. Ce type de fichier supprime les artefacts indésirables et peut se traduire par un document plus lisible dans certains scénarios.

* SEARCHABLE_IMAGE_EXACT : garantit que le texte peut être recherché et sélectionné. Cette option conserve l’image d’origine et place un calque de texte invisible dessus. Recommandé pour les cas nécessitant une fidélité maximale à l’image originale.

**Java**

1. Ouvrez une invite de commande.

1. Transformez les répertoires en répertoire d’exemple de code.

   Par exemple, C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>.

1. Exécutez la commande suivante :

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

Votre PDF sera créé dans le répertoire src/main/resources.

**.Net**

1. Ouvrez une invite de commande.

1. Transformez les répertoires en répertoire d’exemple de code.

   Par exemple, C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. Remplacez à nouveau les répertoires par le répertoire OcrPDF.

1. Exécutez la commande suivante :

   `dotnet run OcrPDF.csproj`

Votre PDF sera créé dans le même répertoire.

**Node.js**

1. Ouvrez une invite de commande.

1. Transformez les répertoires en répertoire d’exemple de code.

   Par exemple, C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. Exécutez la commande suivante :

   `node src/ocr/ocr-pdf.js`

Votre PDF sera créé à l’emplacement indiqué dans la sortie, qui est par défaut le répertoire de sortie.

## Dernières réflexions

En suivant ces étapes simples à l’aide des fichiers d’exemple, vous devriez avoir un exemple fonctionnel sur lequel vous pouvez vous appuyer. Outre l’exemple de reconnaissance optique des caractères que nous avons utilisé dans ce tutoriel, il existe un autre exemple de reconnaissance optique des caractères à l’aide des options de type et de paramètres régionaux prises en charge décrites précédemment.

À partir de là, vous pouvez simplement remplacer vos fichiers d&#39;entrée et de sortie situés dans l&#39;exemple pour utiliser votre propre PDF afin de finaliser votre preuve de concept pour votre propre cas d&#39;utilisation.

![Preuve de concept](assets/OCR_poc.png)

## Ressources et étapes suivantes

* Pour obtenir une aide et une assistance supplémentaires, consultez le forum de la communauté des [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all) d&#39;Adobe

* API des services de PDF [Documentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197) pour les questions API des services PDF

* [Contactez-nous](https://www.adobe.com/go/pdftoolsapi_requestform) pour toute question sur les licences et les prix

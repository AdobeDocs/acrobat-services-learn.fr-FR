---
title: Recherche et indexation
description: Découvrez comment créer des fichiers de PDF pouvant faire l’objet de recherches à partir de documents numérisés
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8095
thumbnail: KT-8095.jpg
exl-id: a22230b5-1ff2-4870-84da-f06a904c99e1
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1298'
ht-degree: 0%

---

# Recherche et indexation

![Bannière principale de cas d&#39;utilisation](assets/UseCaseSearchingHero.jpg)

Les entreprises doivent souvent numériser leurs documents papier et leurs fichiers numérisés. Considérez ce [scénario](https://docs.google.com/document/d/11jZdVQAw-3fyE3Y-sIqFFTlZ4m02LsCC/edit). Un cabinet d&#39;avocats a des milliers de contrats juridiques qu&#39;il a scannés pour créer des fichiers numériques. Ils veulent déterminer si certains de ces contrats juridiques comportent une clause ou un supplément particulier qu&#39;ils doivent réviser. L’exactitude est nécessaire à des fins de conformité. La solution consiste à faire l’inventaire des documents numériques, à rendre le texte consultable et à créer un index pour trouver ces informations.

La création d’archives numériques permettant de récupérer des informations à des fins de montage ou d’opérations en aval représente un véritable cauchemar pour la plupart des entreprises.

## Ce que vous pouvez apprendre

Ce tutoriel pratique explique comment utiliser les fonctionnalités des API [!DNL Adobe Acrobat Services] pour archiver et numériser facilement des documents. Vous explorez ces fonctionnalités en créant une application Express NodeJS, puis en intégrant [!DNL Acrobat Services] API pour l’archivage, la numérisation et la transformation des documents.

Pour suivre, vous devez avoir installé [Node.js](https://nodejs.org/) et une compréhension de base de Node.js et de la syntaxe [ES6](https://www.w3schools.com/js/js_es6.asp).

## Ressources et API pertinentes

* [API PDF Services](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Code de projet](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)

## Configuration du projet

Tout d’abord, configurez la structure de dossiers pour l’application. Vous pouvez récupérer le code source [ici](https://github.com/agavitalis/AdobeDocumentAPI.git).

## Structure de répertoire

Créez un dossier appelé AdobeDocumentServicesAPIs et ouvrez-le dans l’éditeur de votre choix. Créez une application NodeJS de base avec la commande `npm init` à l&#39;aide de cette structure de dossiers :

```
AdobeDocumentServicesAPIs
config
default.json
controllers
createPDFController.js
makeOCRController.js
searchController.js
models
document.js
output
.gitkeep
routes
web.js
services
upload.js
views
index.hbs
ocr.hbs
search.hbs
index.js
```

Vous utilisez MongoDB comme base de données pour cette application. Par conséquent, pour configurer, placez vos configurations de base de données par défaut dans le dossier config/, en collant le fragment de code ci-dessous dans le fichier default.json de ce dossier, puis ajoutez l&#39;URL de votre base de données.

```
### config/default.json and config/dev.json
{ "DBHost": "YOUR_DB_URI" }
```

## Installation du pack

Maintenant, installez certains packs à l’aide de la commande npm install comme indiqué dans l’extrait de code ci-dessous :

```
{
    "name": "adobedocumentservicesapis",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "directories": {
    "test": "test"
    },
    "dependencies": {
    "body-parser": "^1.19.0",
    "config": "^3.3.6",
    "express": "^4.17.1",
    "hbs": "^4.1.1",
    "mongoose": "^5.12.1",
    "morgan": "^1.10.0",
    "multer": "^1.4.2",
    "path": "^0.12.7"
    },
    "devDependencies": {},
    "scripts": {
    "start": "set NODE_ENV=dev && node index.js"
    },
    "repository": {
    "type": "git",
    "url": "git+https://github.com/agavitalis/AdobeDocumentServicesAPIs.git"
    },
    "author": "Ogbonna Vitalis",
    "license": "ISC",
    "bugs": {
    "url": "https://github.com/agavitalis/AdobeDocumentServicesAPIs/issues"
    },
    "homepage": "https://github.com/agavitalis/AdobeDocumentServicesAPIs#readme"
}
```

```
###bash
npm install express mongoose config body-parser morgan multer hbs path pdf-parse
Ensure that the content of your package.json file is similar to this code snippet:
###package.json
{
```

Ces fragments de code installent les dépendances de l’application, y compris le moteur de modèle Handlebars pour la vue. Dans la balise scripts, vous configurez les paramètres d’exécution de l’application.

## Intégration de [!DNL Acrobat Services] API

[!DNL Acrobat Services] comprend trois API :

* API Adobe PDF Services

* API Adobe PDF Embed

* API Adobe Document Generation

Ces API automatisent la génération, la manipulation et la transformation du contenu des PDF par le biais d’un ensemble de services web basés sur le cloud.

Pour obtenir les informations d&#39;identification, vous devez [vous inscrire](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK) et terminer le workflow. L’API PDF Embed est gratuite. Les API PDF Services et Document Generation sont gratuites pendant six mois. À la fin de votre période d&#39;essai, vous pouvez [payer à l&#39;utilisation](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) à seulement 0,05 $ par transaction de document. Vous ne payez qu&#39;au fur et à mesure que votre entreprise se développe et traite davantage de contrats.

![Capture d&#39;écran de la création des informations d&#39;identification](assets/searching_1.png)

Une fois l’inscription terminée, un exemple de code est téléchargé sur votre ordinateur et contient vos identifiants d’API. Extrayez cet exemple de code et placez les fichiers private.key et pdftools-api-credentials.json dans le répertoire racine de votre application.

Maintenant, installez le [SDK des services PDF Node.js](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) en exécutant la commande ` npm install --save @adobe/documentservices-pdftools-node-sdk ` à l&#39;aide du terminal dans le répertoire racine de l&#39;application.

## Création d’un PDF

[!DNL Acrobat Services] prend en charge la création de mots de PDF à partir de documents Microsoft Office (Word, Excel et PowerPoint) et d&#39;autres [formats de fichiers pris en charge](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) tels que .txt, .rtf, .bmp, .jpg, .gif, .tiff et .png.

Pour créer des documents de PDF à partir des formats de fichiers pris en charge, utilisez ce formulaire pour charger les documents. Vous pouvez accéder aux fichiers HTML et CSS du formulaire sur [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git).

![Capture d’écran du formulaire web](assets/searching_2.png)

Ajoutez maintenant les fragments de code suivants au fichier controllers/createPDFController.js. Ce code récupère le document et le transforme en PDF.

Les fichiers d’origine et le fichier transformé sont enregistrés dans un dossier de votre application.

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
/*
* GET / route to show the createPDF form.
*/
function createPDF(req, res) {
//catch any response on the url
let response = req.query.response
res.render('index', { response })
}
/*
* POST /createPDF to create a new PDF File.
*/
function createPDFPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then((result) => {
result.saveAsFile('output/createPDFFromDOCX.pdf')
//download the file
res.redirect('/?response=PDF Successfully created')
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

Ce fragment de code nécessite le [SDK Node.js des services PDF](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk). Utilisez les fonctions suivantes :

* createPDF, qui affiche le formulaire de téléchargement de document

* createPDFPost, qui transforme le document chargé en PDF

Les documents transformés du PDF sont enregistrés dans le répertoire de sortie, tandis que le fichier d’origine est enregistré dans le répertoire de téléchargement.

## Utilisation de la reconnaissance de texte

La reconnaissance optique des caractères convertit les images et les documents numérisés en fichiers pouvant faire l’objet de recherches. Vous pouvez convertir [!DNL Acrobat Services] API, images et documents numérisés en PDF interrogeables. Après avoir effectué une opération de ROC, le fichier devient modifiable et indexable. Vous pouvez stocker le contenu du fichier dans un magasin de données à des fins d’indexation et autres.

Rappelez-vous que la recherche et l’indexation des documents numérisés sont essentielles pour de nombreuses entreprises où la gestion des fichiers et le traitement de l’information sont essentiels. La fonctionnalité ROC permet d’éliminer ces problèmes.

Pour mettre en œuvre cette fonctionnalité, vous devez concevoir un formulaire de téléchargement similaire à celui ci-dessus. Cette fois, limitez le formulaire aux fichiers du PDF, car vous ne pouvez utiliser la fonction ROC que sur les documents du PDF.

Voici le formulaire de téléchargement pour cet exemple :

![Capture d&#39;écran du formulaire pour charger des fichiers](assets/searching_3.png)

Maintenant, pour manipuler le PDF chargé et effectuer certaines opérations de ROC, ajoutez l’extrait de code ci-dessous au fichier controllers/makeOCRController.js. Ce code implémente le processus de reconnaissance optique des caractères sur un fichier chargé, puis enregistre le fichier dans le système de fichiers de votre application.

```
const fs = require('fs')
const pdf = require('pdf-parse');
const mongoose = require('mongoose');
const Document = require('../models/document');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
/*
* GET /makeOCR route to show the makeOCR form.
*/
function makeOCR(req, res) {
//catch any response on the url
let response = req.query.response
res.render('ocr', { response })
}
/*
* POST /makeOCRPost to create a new PDF File.
*/
function makeOCRPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
ocrOperation = PDFToolsSdk.OCR.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
ocrOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
ocrOperation.execute(executionContext)
.then(async (result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`;
await result.saveAsFile(`output/${newFileName}`);
let documentContent = fs.readFileSync(
require("path").resolve("./") + `\\output\\${newFileName}`
);
pdf(documentContent)
.then(function (data) {
//Creates a new document
var newDocument = new Document({
documentName: fileName,
documentDescription: description,
documentContent: data.text,
url: require("path").resolve("./") + `\\output\\${newFileName}`
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
} else {
//If no errors, send it back to the client
res.redirect(
"/makeOCR?response=OCR Operation Successfully performed on
the PDF File"
);
}
});
})
.catch(function (error) {
// handle exceptions
console.log(error);
});
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { makeOCR, makeOCRPost };
```

Vous avez besoin du SDK de nœud [!DNL Acrobat Services] et des modules mongoose, pdf-parse et fs, ainsi que de votre schéma de modèle de document. Ces modules sont nécessaires pour enregistrer le contenu du fichier transformé dans une base de données MongoDB.

Créez maintenant deux fonctions : makeOCR pour afficher le formulaire chargé, puis makeOCRPost pour traiter le document chargé. Enregistrez le formulaire d’origine dans une base de données, puis enregistrez le formulaire transformé dans le dossier de sortie de votre application.

Les informations d’identification fournies par l’Adobe du fichier pdftools-api-credentials.json sont chargées dans chaque cas avant la transformation du fichier.

>[!NOTE]
>
>La fonction ROC ne prend en charge que les documents PDF.

Ajoutez également le fragment de code ci-dessous au fichier Modes/Document.js de votre application.

Dans le fragment de code, définissez un modèle de mangouste, puis décrivez les propriétés du document à enregistrer dans la base de données. En outre, indexez le champ DocumentContent pour faciliter et optimiser la recherche de texte.

```
const mongoose = require("mongoose");
const Schema = mongoose.Schema;
//Document schema definition
var DocumentSchema = new Schema(
{
documentName: { type: String, required: false },
documentDescription: { type: String, required: false },
documentContent: { type: String, required: false },
url: { type: String, required: false },
status: {
type: String,
enum : ["active","inactive"],
default: "active"
}
},
{ timestamps: true }
);
//for text search
DocumentSchema.index({
documentContent: "text",
});
//Exports the DocumentSchema for use elsewhere.
module.exports = mongoose.model("document", DocumentSchema);
```

## Recherche de texte

Vous implémentez maintenant une fonction de recherche simple pour permettre aux utilisateurs d’effectuer des recherches de texte simples. Vous ajoutez également une fonctionnalité de téléchargement pour permettre le téléchargement de fichiers de PDF.

Cette fonctionnalité nécessite un formulaire et des cartes simples pour afficher les résultats de la recherche. Vous pouvez trouver les designs pour le formulaire et les cartes sur [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git).

La capture d’écran ci-dessous illustre la fonction de recherche et les résultats de la recherche. Vous pouvez télécharger n’importe lequel des résultats de recherche.

![Capture d&#39;écran des fonctionnalités de recherche](assets/searching_4.png)

Pour implémenter la fonction de recherche, créez un fichier searchController.js dans le dossier controller de votre application et collez le fragment de code ci-dessous :

```
const fs = require('fs')
const mongoose = require('mongoose');
const Document = require('../models/document');
/*
* GET / route to show the search form.
*/
function search(req, res) {
//catch any response on the url
let response = req.query.response
res.render('search', { response })
}
/*
* POST /searchPost to search the contents of your saved file.
*/
function searchPost(req, res) {
let searchString = req.body.searchString;
Document.aggregate([
{ $match: { $text: { $search: searchString } } },
{ $sort: { score: { $meta: "textScore" } } },
])
.then(function (documents) {
res.render('search', { documents })
})
.catch(function (error) {
let response = error
res.render('search', { response })
});
}
//export all the functions
module.exports = { search, searchPost, downloadPDF };
```

Mettez désormais en œuvre une fonction de téléchargement pour permettre le téléchargement des documents retournés par la recherche d’un utilisateur.

## Téléchargement de documents

La mise en œuvre d’une fonctionnalité de téléchargement est similaire à ce que vous avez déjà fait. Ajoutez l’extrait de code suivant après la fonction searchPost dans le fichier controllers/earchController.js :

```
/*
* POST /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
console.log("here")
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(download.link);
}
```

## Marche à suivre

Dans ce tutoriel pratique, vous avez intégré des API [!DNL Acrobat Services] dans une application Node.js et utilisé l&#39;API pour mettre en œuvre une transformation de document qui convertit les fichiers en PDF. Vous avez ajouté une fonction de ROC qui rend les images et les fichiers numérisés consultables. Vous avez ensuite enregistré les fichiers dans un dossier afin de pouvoir les télécharger.

Ensuite, vous avez ajouté une fonction de recherche pour rechercher les documents convertis en texte par ROC. Enfin, vous avez implémenté une fonction de téléchargement pour faciliter le téléchargement de ces fichiers. Une fois votre demande complétée, il sera beaucoup plus facile pour une société juridique de localiser et de traiter un texte spécifique.

L&#39;utilisation de [!DNL Acrobat Services] pour la transformation de documents est fortement recommandée en raison de sa robustesse et de sa facilité d&#39;utilisation par rapport à d&#39;autres services. Vous pouvez rapidement créer un compte pour commencer à profiter des fonctionnalités des API [!DNL Acrobat Services] pour la transformation et la gestion des documents.

Maintenant que vous avez une bonne compréhension de l&#39;utilisation des API [!DNL Acrobat Services], vous pouvez perfectionner vos compétences en vous entraînant. Vous pouvez cloner le référentiel utilisé dans ce tutoriel et mettre à l’essai certaines des compétences que vous venez d’acquérir. Mieux encore, vous pouvez tenter de reconstruire cette application tout en explorant les possibilités illimitées des API [!DNL Acrobat Services].

Prêt à activer le partage et la révision de documents dans votre propre application ? S&#39;abonner à [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
compte développeur. Profitez d&#39;un essai gratuit de six mois, puis d&#39;[un paiement à l&#39;utilisation](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) pour seulement \$0,05 par transaction de document à mesure que votre entreprise se développe.

---
title: Gestion des contrats juridiques
description: Découvrez comment générer et protéger automatiquement des documents juridiques avec la saisie de données personnalisées
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8097
thumbnail: KT-8097.jpg
exl-id: e0c32082-4f8f-4d8b-ab12-55d95b5974c5
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1890'
ht-degree: 0%

---

# Gestion des contrats juridiques

![Bannière principale de cas d&#39;utilisation](assets/UseCaseLegalHero.jpg)

La numérisation s&#39;accompagne de défis. Aujourd&#39;hui, la plupart des organisations ont de nombreux types de [contrats légaux](https://www.adobe.io/apis/documentcloud/dcsdk/legal-contracts.html) qu&#39;elles doivent créer, modifier, approuver et faire signer par différentes parties. Ces contrats juridiques nécessitent souvent une personnalisation et une image de marque uniques. Les organisations peuvent également avoir besoin de les enregistrer dans un format protégé une fois signées pour les protéger. Pour ce faire, ils ont besoin d’une solution robuste de génération et de gestion des documents.

De nombreuses solutions offrent une certaine génération de documents, mais ne peuvent pas personnaliser les entrées de données ni la logique conditionnelle, telles que les clauses qui s’appliquent uniquement à des scénarios spécifiques. La mise à jour manuelle des modèles légaux d’une entreprise est difficile et source d’erreurs, car ces documents sont de plus en plus volumineux. La nécessité d&#39;automatiser ces processus est considérable.

## Ce que vous pouvez apprendre

Dans ce tutoriel pratique, explorez les fonctionnalités des [[!DNL Adobe Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) dans la génération de champs de saisie personnalisés dans les documents. Découvrez également comment convertir facilement ces documents générés en un format de document portable protégé (PDF) pour éviter la manipulation de données.

Ce tutoriel implique une certaine programmation pour explorer la conversion des contrats en PDF. Pour suivre efficacement, [Microsoft Word](https://www.microsoft.com/en-us/download/office.aspx) et [Node.js](https://nodejs.org/) doivent être installés sur votre PC. Une compréhension de base de Node.js et de la syntaxe [ES6](https://www.w3schools.com/js/js_es6.asp) est également recommandée.

## Ressources et API pertinentes

* [API Document Generation Adobe](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [API PDF Embed](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [API Adobe Sign](https://www.adobe.io/apis/documentcloud/sign.html)

* [Code de projet](https://github.com/agavitalis/adobe_legal_contracts.git)

## Création d’un modèle de document

Vous pouvez créer des documents juridiques à l&#39;aide de l&#39;application Microsoft Word ou en téléchargeant les [exemples de modèles Word](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) d&#39;Adobe. Néanmoins, il n&#39;est pas facile de personnaliser les entrées et de signer numériquement ces documents sans utiliser certains outils d&#39;aide tels que le [complément Adobe Document Generation Tagger](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin) pour Microsoft Word.

Document Generation Tagger est un complément Microsoft Word, conçu pour rendre la personnalisation des documents transparente à l’aide de balises. Cela permet de créer des champs dynamiques dans les modèles de document qui se remplissent dynamiquement à l’aide de données JSON.

![Capture d’écran expliquant comment ajouter le balisage Adobe Document Generation dans Word](assets/legal_1.png)

Pour illustrer l’utilisation du balisage Document Generation, installez ce complément, puis créez un modèle de données JSON, qui est utilisé dans le balisage d’un simple document contractuel légal.

Installez Document Generation Tagger dans Word en cliquant sur l&#39;onglet **Insérer**, puis dans le groupe Compléments, cliquez sur **Mes compléments**. Dans le menu Compléments Office, recherchez « Adobe de la génération des documents », puis cliquez sur **Ajouter** et suivez le processus. Vous pouvez voir ces étapes dans la capture d’écran ci-dessus.

Après avoir installé le complément Document Generation Tagger for Word, créez un modèle de données JSON simple pour baliser le document légal.

Pour continuer, ouvrez l’éditeur de votre choix, créez un fichier appelé Agreement.json, puis collez le fragment de code ci-dessous dans le fichier JSON que vous avez créé.

```
{
"Agreement": {
"Date": "1/24/2021",
"Prime Contractor Name": "Ogbonna Vitalis Corp",
"Prime State": "Lagos",
"Address": "Maryland Ave, Lagos State, Ng",
"Sub Contractor Name": "Vivvaa Soln",
"Sub Contractor State": "California",
"Sub Contractor Address": "Molusi Avenue, Dallas Texas, CA",
"Agreement Date": "1/24/2021",
"Length": 5
}
}
```

Après avoir enregistré ce document JSON, importez-le dans le complément Baliseur de génération de documents. Importez le document en cliquant sur **Génération du document** dans le groupe Adobe en haut à droite de votre écran Word, comme indiqué dans la capture d&#39;écran ci-dessous.

![Capture d&#39;écran du complément Adobe Document Generation Tagger dans Word](assets/legal_2.png)

Une vidéo s’affiche pour vous guider. Vous pouvez le regarder ou accéder directement au champ de balisage en cliquant sur **Commencer**. Après avoir cliqué sur **Commencer**, un formulaire de téléchargement apparaît. Cliquez sur **Télécharger le fichier JSON** et sélectionnez le fichier JSON que vous venez de créer. Une fois l&#39;importation terminée, cliquez sur **Générer la balise** pour générer les balises.

Après avoir importé et généré des balises, vous pouvez les ajouter à votre document. Pour les ajouter, placez votre curseur à l’endroit exact où vous souhaitez que la balise apparaisse. Sélectionnez ensuite une balise dans l&#39;API Document Generation et cliquez sur **Insérer du texte**. La capture d’écran ci-dessous décrit cette procédure.

![Capture d&#39;écran de l&#39;ajout de balises au document](assets/legal_3.png)

Outre les balises de base créées à l’aide du modèle de données JSON importé, vous pouvez également utiliser des fonctionnalités avancées pour d’autres options, telles que les images, la logique conditionnelle, les calculs, les éléments répétés et les expressions conditionnelles. Vous pouvez accéder à ces fonctionnalités en cliquant sur **Avancé** dans le panneau Balisage de génération de document. Vous pouvez le voir dans la capture d’écran ci-dessous.

![Capture d&#39;écran de l&#39;onglet Avancé du Baliseur de génération de document d&#39;Adobe](assets/legal_4.png)

Ces fonctionnalités avancées ne sont pas différentes des balises de base. Pour inclure une logique conditionnelle, sélectionnez la partie du document à remplir. Ensuite, configurez la règle qui détermine l’insertion de la balise.

Pour illustrer davantage, disons que dans l&#39;accord, il y a une section que vous voulez inclure, mais seulement sous condition. Dans le champ Sélectionner le type de contenu, sélectionnez **Section.** Dans le champ Sélectionner des enregistrements, sélectionnez l&#39;option qui détermine si la section conditionnelle s&#39;affiche. Sélectionnez l’opérateur conditionnel souhaité et définissez la valeur que vous testez dans le champ Valeur. Cliquez ensuite sur **Insérer une condition.** La capture d&#39;écran ci-dessous illustre ce processus.

![Capture d&#39;écran de l&#39;insertion de contenu conditionnel](assets/legal_5.png)

Pour les calculs, sélectionnez Arithmétique ou Agrégation, puis incluez le premier enregistrement, l&#39;opérateur et le second enregistrement à utiliser en fonction des balises de modèle disponibles. Cliquez ensuite sur **Insérer un calcul**.

En outre, les contrats juridiques nécessitent souvent la signature des parties concernées. Vous pouvez insérer une signature électronique à l’aide des balises de texte Adobe Sign situées juste en dessous de la section « Calculs numériques ». Pour inclure la signature électronique, vous devez spécifier le nombre de destinataires, sélectionner **Signataire** et le type de champ dans les listes déroulantes, en conséquence. Une fois terminé, cliquez sur **Insérer une balise de texte Adobe Sign** pour terminer le processus.

Pour garantir l’intégrité des données, enregistrez les documents juridiques dans un format protégé. Avec les API [!DNL Acrobat Services], vous pouvez rapidement transformer des documents au format PDF. Vous pouvez créer une application simple express Node.js, y intégrer une API Document Generation et utiliser cette application simple pour convertir votre document balisé du format Word au format PDF.

## Configuration du projet

Tout d’abord, vous configurez la structure de dossiers pour l’application Node.js. Dans cet exemple, appelez cette application simple AdobeLegalContractAPI. Vous pouvez récupérer le code source [ici](https://github.com/agavitalis/adobe_legal_contracts.git).

### Structure de répertoire

Créez un dossier appelé AdobeLegalContractAPI et ouvrez-le dans l’éditeur de votre choix. Créez une application Node.js de base avec la commande ```npm init``` en utilisant la structure de dossiers ci-dessous :

```
###Directory Structure
AdobeLegalContractAPI
-----config
----------default.json
-----controllers
----------createPDFController.js
----------previewController.js
-----models
----------document.js
-----routes
----------web.js
-----services
-----------upload.js
-----uploads
-----views
-----index.js
```

Vous trouverez ci-dessus une structure d’application Node.js simple pour votre application. Procédez maintenant à l’installation des packages npm nécessaires.

### Installation du pack

Installez les packs requis à l’aide de la commande npm install comme indiqué dans l’extrait de code ci-dessous :

```
npm install express body-parser morgan multer hbs path config mongoose
```

Après avoir installé les packs, assurez-vous que le contenu de votre fichier package.json est similaire à l’extrait de code ci-dessous :

```
###package.json
{
"name": "adobelegalcontractapi",
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
"start": "node index.js"
},
"repository": {
"type": "git",
"url": "https://github.com/agavitalis/adobe_legal_contracts.git"
},
"author": "Ogbonna Vitalis",
"license": "ISC",
"bugs": {
"url": "https://github.com/agavitalis/adobe_legal_contracts/issues"
},
"homepage": "https://github.com/agavitalis/adobe_legal_contracts#readme"
}
```

Dans ces extraits de code, vous avez installé les dépendances de l’application, y compris le moteur de modèle Handlebars pour la vue.

Ce tutoriel porte principalement sur l&#39;utilisation des [[!DNL Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/) pour convertir des documents en PDF. Par conséquent, il n&#39;y a pas de processus étape par étape pour construire cette application Node.js. Cependant, vous pouvez récupérer le code d&#39;application Node.js de travail complet sur [GitHub](https://github.com/agavitalis/adobe_legal_contracts.git).

## Intégration de [!DNL Adobe Acrobat Services] API dans une application Node.js

Les API [!DNL Adobe Acrobat Services] sont des services fiables basés sur le cloud, conçus pour la manipulation transparente de documents. Il propose trois API :

* API Adobe PDF Services

* API Adobe PDF Embed

* API Adobe Document Generation

Vous avez besoin d’informations d’identification pour utiliser les API [!DNL Acrobat Services] (différentes de vos informations d’identification d’API PDF Embed). Si vous ne disposez pas d&#39;informations d&#39;identification valides, [enregistrez-vous](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK) et terminez le workflow comme illustré dans la capture d&#39;écran ci-dessous. Profitez d&#39;un essai gratuit de [six mois, puis d&#39;un paiement à l&#39;utilisation](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html), à seulement 0,05 $ par transaction de document.

![Capture d&#39;écran de la création de nouvelles informations d&#39;identification](assets/legal_6.png)

Une fois le processus d’inscription terminé, un exemple de code se télécharge automatiquement sur votre PC pour vous aider à démarrer. Vous pouvez extraire cet exemple de code et le suivre. N’oubliez pas de copier les fichiers pdftools-api-credentials.json et private.key de l’exemple de code extrait dans le répertoire racine du projet Node.js. Les informations d&#39;identification sont requises avant de pouvoir accéder aux points de terminaison de l&#39;API [!DNL Acrobat Services]. Vous pouvez également télécharger des exemples de SDK avec vos informations d’identification personnalisées afin de ne pas avoir à mettre à jour la clé dans l’exemple de code.

Installez maintenant le SDK de nœud Adobe PDF Services en exécutant la commande ```npm install \--save @adobe/documentservices-pdftools-node-sdk``` à l’aide du terminal situé dans le répertoire racine de votre application. Une fois l&#39;installation réussie, vous pouvez utiliser les API [!DNL Acrobat Services] pour manipuler des documents dans votre application.

## Création d’un document de PDF

Les API [!DNL Acrobat Services] prennent en charge la création de mots de PDF à partir de documents Office Microsoft (Word, Excel et PowerPoint) et d&#39;autres [formats de fichiers pris en charge](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) tels que .txt, .rtf, .bmp, .jpeg, gif, .tiff et .png. Vous pouvez facilement convertir des contrats juridiques de n’importe quel autre format de fichier en PDF à l’aide des API de service Acrobat.

L’API Adobe Document Generation permet la conversion en fichier ou en PDF Word. Par exemple, vous pouvez utiliser un modèle Word pour générer un contrat, y compris une ligne rouge pour marquer le texte modifié. Convertissez-le ensuite en PDF et utilisez l’API des services de PDF pour protéger le document avec un mot de passe, l’envoyer pour signature, etc.

Pour mettre en œuvre la création de documents de PDF à partir des formats de fichiers pris en charge disponibles, il existe un formulaire permettant de télécharger un document pour transformation à l&#39;aide de [!DNL Acrobat Services].

Le formulaire de téléchargement conçu apparaît dans la capture d&#39;écran ci-dessous et vous pouvez accéder aux fichiers HTML et CSS sur [GitHub](https://github.com/agavitalis/adobe_legal_contracts.git).

![Capture d&#39;écran du téléchargement de formulaire](assets/legal_7.png)

Maintenant, ajoutez les fragments de code suivants au fichier /createPDFController.js des contrôleurs. Ce code récupère le document chargé et le transforme en PDF. [!DNL Acrobat Services] enregistre le fichier d&#39;origine téléchargé et le fichier transformé dans différents dossiers.

```
###controllers/createPDFController.js
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
const Document = require('../models/document');
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
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then(async(result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`
let newFilePath = require('path').resolve('./') + `\\output\\${newFileName}`
await result.saveAsFile(`views/output/${newFileName}`)
//Creates a new document
let newDocument = new Document({
documentName: newFileName,
url: newFilePath
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
}
else {
res.redirect('/?response=PDF Successfully created')
}
});
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation', err);
} else {
console.log('Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

L&#39;extrait de code ci-dessus nécessitait le modèle de document et le SDK de nœud [!DNL Acrobat Services] que vous avez installés précédemment. Il existe deux fonctions :

* createPDF affiche le formulaire de téléchargement de document.

* createPDFPost transforme le document chargé en PDF.

Les fonctions enregistrent les documents transformés du PDF dans le répertoire views/output, où vous pouvez les télécharger sur votre PC.

Vous pouvez également prévisualiser le fichier de PDF transformé à l’aide de l’API gratuite PDF Embed. À l&#39;aide de l&#39;API PDF Embed, vous pouvez générer des informations d&#39;identification d&#39;Adobe [ici](https://www.adobe.com/go/dcsdks_credentials) (différentes de vos informations d&#39;identification [!DNL Acrobat Services]) et enregistrer des domaines autorisés pour accéder à l&#39;API. Suivez le processus et générez des identifiants d’API PDF Embed pour votre application. Vous pouvez également consulter la démonstration [ici](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf), à partir de laquelle vous pouvez facilement générer des codes pour vous aider à démarrer rapidement.

Revenez à l’application, créez les fichiers list.hbs et preview.hbs dans le dossier d’affichage de votre application et collez l’extrait de code ci-dessous dans les fichiers list.hbs et preview.hbs, respectivement.

```
###views/list.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>Adobe Legal Contract</title>
<!-- Meta tags -->
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,
initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<!-- //Meta tags -->
<link
href=".min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/
bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous">
<link rel="stylesheet" href="css/style.css" type="text/css"
media="all" /><!-- Style-CSS -->
<link href="css/font-awesome.css" rel="stylesheet" /><!--
font-awesome-icons -->
</head>
<body>
<section>
<div class="form-36-mian section-gap">
<div class="wrapper">
<div class="container">
<div class="row">
{{#each documents}}
<div class="col-md-4 mb-2">
<div class="card" style="width:
18rem;">
<img class="card-img-top"
src="./images/pdf.png"
alt="Card image cap">
<div class="card-body">
<h5
class="card-title">{{documentName}}</h5>
<a
href="/downloadPDF/{{_id}}" class="btn btn-primary"><i class="fa
fa-download" aria-hidden="true"></i> Download</a>
<a
href="/previewPDF/{{_id}}" class="btn btn-info"><i class="fa fa-eye"
aria-hidden="true"></i> Preview</a>
</div>
</div>
</div>
{{/each}}
</div>
</div>
<!-- copyright -->
<div class="copy-right">
<p>(c) 2021 Vitalis</p>
</div>
<!-- //copyright -->
</div>
</div>
</section>
</body>
</html>
###views/preview.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>[!DNL Adobe Acrobat Services] PDF Embed API</title>
<meta charset="utf-8" />
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
<meta id="viewport" name="viewport" content="width=device-width,
initial-scale=1" />
</head>
<body style="margin: 0px">
<input type="hidden" id="pdfDocumentName"
value={{document.documentName}} />
<input type="hidden" id="pdfDocumentUrl" value={{document.url}} />
<div id="adobe-dc-view"></div>
<script
src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
let pdfDocumentName =
document.getElementById("pdfDocumentName").value;
let pdfDocumentUrl =
document.getElementById("pdfDocumentUrl").value;
document.addEventListener("adobe_dc_view_sdk.ready", function
() {
var adobeDCView = new AdobeDC.View({ clientId:
"XXXXXXXXXXXXXXXX", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url:
`http://localhost:5000/output/${pdfDocumentName}` } },
metaData: { fileName: pdfDocumentName }
}, {});
});
</script>
</body>
</html>
```

Créez également un fichier controller/previewController.js et collez-y les fragments de code ci-dessous.

```
const Document = require('../models/document');
/*
* GET /listFiles route to show PDF file lists.
*/
async function listFiles(req, res) {
let documents = await Document.find({});
res.render('lists', { documents })
}
/*
* GET /previewPDF route to show PDF file in AdobeEmbedAPI.
*/
async function previewPDF(req, res) {
//catch any response on the url
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.render('preview', { document })
}
/*
* GET /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(document.url);
}
//export all the functions
module.exports = {listFiles, previewPDF, downloadPDF };
```

Dans le fichier de contrôleur ci-dessus, il y a trois fonctions, listFiles, previewPDF et downloadPDF. La fonction listFiles répertorie tous les fichiers de PDF générés jusqu&#39;à présent à l&#39;aide des API [!DNL Acrobat Services]. La fonction previewPDF vous permet de prévisualiser les fichiers du PDF à l&#39;aide de l&#39;API PDF Embed, tandis que la fonction downloadPDF vous permet de télécharger le fichier du PDF généré sur votre PC. La capture d’écran ci-dessous montre un exemple de l’aperçu du PDF à l’aide de l’API PDF Embed.

![Capture d&#39;écran de l&#39;aperçu du PDF](assets/legal_8.png)

## Résumé

Dans ce tutoriel pratique, vous avez balisé un document à l’aide du complément Microsoft Word Baliseur de génération de document. Puis, intégration des API [!DNL Acrobat Services] dans une application Node.js et
conversion d’un document balisé en un format de PDF téléchargeable, bien que vous ayez pu également créer le contrat juridique directement dans PDF. Enfin, vous avez utilisé l’API Adobe PDF Embed pour prévisualiser le PDF généré à des fins de vérification et de signature.

L&#39;application complétée facilite considérablement le balisage des [modèles de contrat légal](https://www.adobe.io/apis/documentcloud/dcsdk/legal-contracts.html) avec des champs dynamiques, leur conversion en PDF, leur prévisualisation et leur signature à l&#39;aide des API [!DNL Acrobat Services]. Au lieu de passer du temps à créer un contrat unique, votre équipe peut automatiquement envoyer le bon contrat à chaque client, puis passer plus de temps à faire croître votre entreprise.

Les organisations utilisent les API [!DNL Adobe Acrobat Services] pour leur exhaustivité et leur facilité d&#39;utilisation. Mieux encore, vous pouvez bénéficier d&#39;un essai gratuit de [six mois, puis d&#39;une formule prépayée](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html). Vous ne payez que pour ce que vous utilisez. De plus, l’API PDF Embed est toujours gratuite.

Prêt à stimuler la productivité en améliorant votre flux de documents ? [Commencez](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) dès aujourd&#39;hui.

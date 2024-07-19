---
title: Gestion des lettres d'offre d'un employé
description: Découvrez comment générer une lettre d’offre qui peut être remise à un nouvel employé pour signature.
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8096
thumbnail: KT-8096.jpg
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1714'
ht-degree: 0%

---

# Gestion des lettres d’offre des employés

![Bannière principale de cas d&#39;utilisation](assets/UseCaseOfferHero.jpg)

Les lettres d’offre aux employés sont l’une des premières expériences que les employés ont avec votre organisation. Par conséquent, vous voulez vous assurer que vos lettres d’offre sont conformes à la marque, mais vous ne voulez pas avoir à créer une lettre dans votre logiciel de traitement de texte à partir de zéro à chaque fois. Les API [!DNL Adobe Acrobat Services] offrent un moyen rapide, facile et efficace de gérer les éléments clés de la [génération et de la distribution de lettres d&#39;offre aux nouveaux employés](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html).

## Ce que vous pouvez apprendre

Ce tutoriel pratique explique la configuration d’un projet Node Express qui affiche un formulaire web à remplir par un utilisateur avec des détails sur les employés. Ces détails utilisent [!DNL Acrobat Services] sur le Web pour générer une lettre d&#39;offre en tant que PDF qui peut être remise à un client pour sa signature à l&#39;aide de l&#39;API Adobe Sign.

## Ressources et API pertinentes

* [API PDF Services](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [API Document Generation Adobe](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [API Adobe Sign](https://www.adobe.io/apis/documentcloud/sign.html)

* [Complément Word Document Generation Tagger](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)

* [Exemple de projet](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)

## Prise en main

[Node.js](https://nodejs.org/) est la plateforme de programmation. Il est fourni avec un ensemble énorme de bibliothèques, telles que le serveur web Express. [Téléchargez Node.js](https://nodejs.org/en/download/) et suivez les étapes pour installer cet environnement de développement open source exceptionnel.

Pour utiliser l&#39;API Document Generation Adobe dans Node.js, accédez au site [API Document Generation](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) pour accéder à votre compte ou créez-en un nouveau. Votre compte est [gratuit pendant six mois, puis payé à l&#39;utilisation](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) pour seulement 0,05 $ par transaction de document, de sorte que vous pouvez l&#39;essayer sans risque, puis ne payer qu&#39;au fur et à mesure que votre entreprise se développe.

Après vous être connecté à [Adobe Developer Console](https://console.adobe.io/), cliquez sur **[!UICONTROL Créer un projet]**. Le projet est nommé « Projet 1 » par défaut. Cliquez sur le bouton **[!UICONTROL Modifier le projet]** et remplacez le nom par « Générateur de lettres d&#39;offre ». Au centre de l&#39;écran se trouve une section **[!UICONTROL Prise en main de votre nouveau projet]**. Pour activer la sécurité sur votre projet, procédez comme suit :

Cliquez sur **Ajouter une API**. Vous voyez un certain nombre d’API parmi lesquelles choisir. Dans la section **[!UICONTROL Filtrer par produit]**, sélectionnez **[!UICONTROL Document Cloud]**, puis cliquez sur **[!UICONTROL Suivant]**.

À présent, générez des informations d’identification pour accéder à l’API. Les informations d&#39;identification se présentent sous la forme d&#39;un jeton Web JSON ([JWT](https://jwt.io/)) : une norme ouverte pour une communication sécurisée. Si vous êtes familier avec JWT et que vous avez déjà généré des clés, vous pouvez télécharger votre clé publique ici. Vous pouvez également sélectionner **Option 1** pour que l&#39;Adobe génère les clés pour vous.

![Capture d&#39;écran de la génération des informations d&#39;identification](assets/offer_1.png)

Cliquez sur le bouton **[!UICONTROL Générer une paire de clés]**. Vous obtenez un fichier config.zip à télécharger. Décompressez le fichier d&#39;archive. Il contient deux fichiers : certificate_pub.crt et private.key. Assurez-vous que ce dernier est sécurisé, car il contient vos informations d’identification privées et peut être utilisé pour générer des documents factices s’il est hors de votre contrôle.

Cliquez sur **[!UICONTROL Suivant]**. Non, active l’accès à l’API PDF Generation. Dans l&#39;écran **[!UICONTROL Sélectionner des profils de produit]**, cochez la case **[!UICONTROL Développeur des services Enterprise PDF]**, puis cliquez sur le bouton **[!UICONTROL Enregistrer l&#39;API configurée]**. Vous êtes maintenant prêt à commencer à utiliser l’API.

## Configuration du projet

Configurez un projet Node pour exécuter votre code. Cet exemple utilise [Visual Studio Code](https://code.visualstudio.com/) (code VS) comme éditeur. Créez un dossier appelé « Letter-generator » et ouvrez-le dans VS Code. Dans le menu **[!UICONTROL Fichier]**, sélectionnez **[!UICONTROL Terminal]** \> **[!UICONTROL Nouveau Terminal]** pour ouvrir un shell dans ce dossier. Vérifiez que le nœud est installé et sur votre chemin en entrant les informations suivantes :

```
node -v
```

Vous devriez voir la version du nœud que vous avez installée.

Maintenant que votre environnement de développement est installé, vous pouvez continuer et créer votre projet.

Tout d’abord, initialisez le projet à l’aide de Node Package Manager (npm). Tapez ce qui suit :

```
npm init
```

Des questions vous sont posées au sujet de votre projet Node. Vous pouvez ignorer la plupart de ces questions, mais assurez-vous que le nom du projet est « letter-generator » et que le point d’entrée est **index.js**. Sélectionnez **Oui** pour terminer l&#39;initialisation du projet.

Vous avez maintenant un fichier package.json. Node utilise ce fichier pour organiser votre projet. Avant de créer index.js, vous devez ajouter des bibliothèques d’Adobe avec les éléments suivants :
commande :

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

Un nouveau dossier nommé node_modules doit être ajouté à votre projet. C&#39;est dans ce dossier que toutes les bibliothèques (appelées dépendances dans Node) sont téléchargées. Le fichier package.json est également mis à jour avec une référence aux services Adobe PDF.

Vous souhaitez maintenant installer Express comme infrastructure web légère. Entrez la commande suivante :

```
npm install express –save
```

Comme précédemment, la section des dépendances de package.json est mise à jour en conséquence.

## Création d’un modèle de lettre d’offre

Maintenant, dans la racine du projet, créez un fichier appelé « app.js ». Mettons le code de démarrage suivant à l’intérieur :

```
const express = require('express');
const bodyParser = require('body-parser');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk')
const path = require('path');
const app = express();
const port = 8000;
app.use(bodyParser.urlencoded({ extended: true }));
app.get('/', (req, res) => {
res.sendFile(path.join(__dirname + '/index.html'));
});
app.post('/', (req, res) => {
console.log('Got body:', req.body);
res.sendStatus(200);
});
app.listen(port, () => {
console.log(`Candidate offer letter app listening on port ${port}!`)
});
```

Notez que l&#39;itinéraire get renvoie un fichier **index.html**. Nous allons créer un fichier de HTML portant ce nom et le formulaire simple suivant. Vous pouvez ajouter des styles CSS et d’autres éléments de conception ultérieurement, à votre convenance. Ce formulaire prend les informations de base du candidat pour générer une lettre de bienvenue :

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Offer Letter Generator</title>
</head>
<body>
<h1>Enter Candidate Details</h1>
<form action="" method="post">
<div>
<label for="firstname">First name: </label>
<input type="text" name="firstname" id="firstname" required>
</div>
<div>
<label for="lastname">Last name: </label>
<input type="text" name="lastname" id="lastname" required>
</div>
<div>
<label for="salary">Salary ($): </label>
<input type="number" name="salary" id="salary" required>
</div>
<div>
<label for="startdate">Start Date: </label>
<input type="date" name="startdate" id="startdate" required>
</div>
<div>
<input type="submit" value="Generate Letter">
</div>
</form>
</body>
</html>
```

Exécutez le serveur web avec la commande suivante :

```
node app.js
```

Vous devriez voir le message « Application de lettre d&#39;offre de candidat à l&#39;écoute sur le port 8000 ». Si vous ouvrez votre navigateur sur <http://localhost:8000/>, le formulaire doit se présenter comme suit :

![Capture d’écran du formulaire web](assets/offer_2.png)

Notez que le formulaire est publié sur lui-même. Si vous renseignez les données et cliquez sur **Générer la lettre**, les informations suivantes devraient s&#39;afficher sur la console :

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

Vous remplacez cette journalisation de la console par un appel de service Web à [!DNL Acrobat Services]. Tout d’abord, vous devez créer un modèle JSON des informations. Le format de ce modèle se présente comme suit :

```
{
    "offer_letter": {
    "firstname": "John",
    "lastname": "Doe",
    "salary": "887888",
    "startdate": "2021-04-01"
    }
}
```

Vous pouvez rendre ce modèle plus élaboré si vous le souhaitez, mais pour ce tutoriel, tenez-vous-en à cet exemple simple. Il n’y a pas de validation sur ce formulaire car cela sort du cadre de cet article. Pour convertir le corps de votre formulaire au modèle de données décrit ci-dessus, modifiez la méthode du gestionnaire app.post pour qu’elle contienne le code suivant :

```
app.post('/', (req, res) => {
const docModel = {'offer_letter': req.body};
generateLetter(docModel);
res.sendStatus(200);
});
```

La première ligne place vos données JSON au format souhaité. Vous allez maintenant transmettre ces données à une fonction generateLetter. Arrêtez votre serveur et collez le code suivant à la fin de app.js. Ce code prend un document Word comme modèle et remplit les balises d’emplacement avec les informations d’un document JSON.

```
// Letter generation function
function generateLetter(jsonDataForMerge) {
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge,
documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(
'resources/OfferLetter-Template.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/OfferLetter.pdf'))
.catch(err => {
if(err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log(
'Exception encountered while executing operation', err);
} else {
console.log(
'Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

Il y a beaucoup de code à déballer. Commençons par la partie principale : le `documentMergeOperation`. Dans cette section, vous pouvez fusionner vos données JSON avec un modèle de document Word. Vous pouvez utiliser l&#39;[exemple sur le site Adobe](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) comme référence, mais créons votre propre exemple simple. Ouvrez Word et créez un document vierge. Vous pouvez le personnaliser autant que vous le souhaitez, mais vous devez au moins disposer de quelque chose comme ceci :

Cher X,

Nous sommes ravis de vous offrir un poste de X $ par année. Votre date de début sera X.

Bienvenue

Enregistrez le document sous le nom « OfferLetter-Template.docx » dans un dossier appelé « resources » à la racine de votre projet. Notez les trois X dans le document. Ces X sont des espaces réservés temporaires pour vos informations JSON. Bien que vous puissiez utiliser une syntaxe spéciale pour remplacer ces espaces réservés, Adobe fournit un complément Word qui simplifie cette tâche. Pour installer le complément, accédez à l&#39;Adobe [Complément Word Document Generation Tagger](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin).

Dans votre modèle de lettre d&#39;offre, cliquez sur le nouveau bouton **Génération de document**. Un panneau latéral s’ouvre. Cliquez sur **Commencer**. Une zone de texte vous est fournie pour coller les exemples de données JSON. Copiez l’extrait de code « offer-data » du fichier JSON du haut dans la zone de texte. Elle doit se présenter comme suit :

![Capture d&#39;écran de la lettre et du code](assets/offer_3.png)

Cliquez sur le bouton **Générer des balises**. Vous obtenez un menu déroulant de balises à insérer aux points appropriés dans le document. Mettez en surbrillance le premier X du document et sélectionnez **[!UICONTROL prénom]**. Cliquez sur **[!UICONTROL Insérer du texte]** et « Cher X » est remplacé par « Cher ```{{`offer_letter`.firstname}}``` ». Ce format de balise est correct pour `documentMergeOperation`. Continuez et ajoutez les trois balises restantes à l’emplacement Xs approprié. N’oubliez pas d’enregistrer OfferLetter-template.docx. Il devrait ressembler à ceci :

Bonjour ```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}```,

Nous sommes ravis de vous proposer un poste de ```{{`offer_letter`.salary}}``` $ par an. Votre date de début sera le ```{{`offer_letter`.startdate}}```.

Bienvenue

Le modèle Word comporte désormais un balisage correspondant au format JSON. Par exemple, ```{{`offer_letter`.`firstname`}}``` au début du document Word est remplacé par la valeur dans la section « firstname » des données JSON.

Revenez à votre fonction `generateLetter`. Pour sécuriser votre appel REST, créez un nouveau fichier intitulé pdftools-api-credentials.json dans la racine du projet. Collez les données JSON suivantes et ajustez-les avec les détails de la section Compte de service (JWT) de votre [console de développement](https://console.adobe.io/).

```
{
"client_credentials": {
"client_id": "<YOUR_CLIENT_ID>",
"client_secret": "<YOUR_CLIENT_SECRET>"
},
"service_account_credentials": {
"organization_id": "<YOUR_ORGANIZATION_ID>",
"account_id": "<YOUR_TECHNICAL_ACCOUNT_ID>",
"private_key_file": "<PRIVATE_KEY_FILE_PATH>"
}
}
```

* L&#39;ID client, le secret client et l&#39;ID d&#39;organisation peuvent être copiés directement à partir de la section **[!UICONTROL Informations d&#39;identification]** de la console.

* L&#39;ID de compte est l&#39;**ID de compte technique**.

* Copiez le fichier private.key que vous avez généré précédemment dans le projet et entrez son nom dans la section private_key_file du
fichier pdftools-api-credentials.json. Si vous le souhaitez, vous pouvez placer un chemin d’accès au fichier de clé privée ici. N&#39;oubliez pas de le garder en sécurité car il peut être mal utilisé une fois hors de votre contrôle.

Pour générer un PDF avec les données JSON renseignées, revenez à votre formulaire web **[!UICONTROL Saisir les détails du candidat]** et publiez des données. Cela prend un peu de temps car le document doit être téléchargé depuis l’Adobe, mais vous devriez avoir un fichier intitulé OfferLetter.pdf dans un nouveau dossier intitulé output.

## Marche à suivre

C&#39;est tout ! Ce n&#39;est que le début. Si vous étudiez la section Avancé de l’onglet Génération du document du complément Word, vous remarquez que tous les marqueurs d’espace réservé ne proviennent pas des données JSON associées. Vous pouvez également ajouter des balises de signature. Ces balises vous permettent de prendre le document obtenu et de le charger sur [Adobe Sign](https://acrobat.adobe.com/ca/en/sign.html) pour le remettre et le signer au nouvel employé. Lisez la section Prise en main de l’API Adobe Sign pour savoir comment procéder. Ce processus est similaire car vous utilisez des appels REST sécurisés avec un jeton JWT.

L&#39;exemple de document unique fourni ci-dessus peut être utilisé comme base pour une application lorsqu&#39;une organisation doit [accélérer l&#39;embauche saisonnière](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html) d&#39;employés sur plusieurs sites. Comme nous l&#39;avons démontré, le principal flux consiste à prendre les données des candidats par le biais d&#39;une application en ligne. Les données sont utilisées pour remplir les champs d’une lettre d’offre et l’envoyer pour signature électronique.

[!DNL Adobe Acrobat Services] est gratuit pendant six mois, puis [paiement à l&#39;utilisation](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) à seulement 0,05 $ par transaction de document, afin que vous puissiez l&#39;essayer et adapter votre workflow de lettre d&#39;offre au fur et à mesure que votre entreprise se développe. Pour [commencer](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
pour créer vos propres modèles, [inscrivez votre compte développeur](https://www.adobe.io/).

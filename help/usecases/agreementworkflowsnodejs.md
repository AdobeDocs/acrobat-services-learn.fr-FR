---
title: Workflows d’accord dans Node.js
description: Les API [!DNL Adobe Acrobat Services] intègrent facilement des fonctionnalités de PDF dans vos applications web
feature: Use Cases
role: Developer
level: Beginner
type: Tutorial
jira: KT-7473
thumbnail: KT-7473.jpg
keywords: En vedette
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '2094'
ht-degree: 0%

---

# Workflows d’accord dans Node.js

![Bannière principale de cas d&#39;utilisation](assets/UseCaseAgreementHero.jpg)

De nombreuses applications et processus métier nécessitent une documentation de type propositions et accords. Les documents de mot de PDF garantissent des fichiers plus sécurisés et moins modifiables. Ils prennent également en charge la signature numérique afin que vos clients puissent remplir rapidement et facilement leurs documents. Les API [!DNL Adobe Acrobat Services] intègrent facilement des fonctionnalités de PDF dans vos applications web.

## Ce que vous pouvez apprendre

Dans ce tutoriel pratique, découvrez comment ajouter des services de PDF à une application Node.js pour numériser un processus d’accord.

## Ressources et API pertinentes

* [API PDF Services](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [API PDF Embed](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [API Adobe Sign](https://developer.adobe.com/adobesign-api/)

* [Code de projet](https://github.com/adobe/pdftools-node-sdk-samples)

## Configuration de [!DNL Adobe Acrobat Services]

Pour commencer, configurez les informations d&#39;identification pour utiliser [!DNL Adobe Acrobat Services]. Enregistrez un compte et utilisez le Quickstart [Node.js](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js) pour vérifier que vos informations d&#39;identification fonctionnent avant d&#39;intégrer la fonctionnalité dans une application plus vaste.

Tout d’abord, obtenez un compte Développeur Adobe. Ensuite, sur la page [Prise en main](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK), sélectionnez l&#39;option *Prise en main* sous Créer de nouvelles informations d&#39;identification. Vous pouvez vous inscrire à leur essai gratuit qui fournit 1 000 transactions de document pouvant être utilisées sur six mois.

![Image de la création d’informations d’identification](assets/AWNjs_1.png)

Sur la page suivante Créer de nouvelles informations d’identification, vous êtes invité à choisir entre l’API PDF Embed et l’API PDF Services.

Sélectionnez *API PDF Services*.

Saisissez un nom pour l&#39;application et cochez la case *Créer un exemple de code personnalisé*. Cochez cette case pour incorporer automatiquement vos informations d’identification dans l’exemple de code. Si vous ne cochez pas cette case, vous devez ajouter manuellement vos informations d’identification à l’application.

Sélectionnez *Node.js* pour le type d&#39;application et cliquez sur *Créer des informations d&#39;identification*.

Quelques instants plus tard, un fichier .zip commence à être téléchargé avec un exemple de projet incluant vos informations d’identification. Le package Node.js pour [!DNL Acrobat Services] est déjà inclus dans l&#39;exemple de code de projet.

![Image de la sélection des identifiants d’API des services de PDF](assets/AWNjs_2.png)

## Configuration manuelle du projet d’exemple

Si vous choisissez de ne pas télécharger un projet d’exemple à partir de la page Créer de nouvelles informations d’identification, vous pouvez également configurer le projet manuellement.

Téléchargez le code (sans vos informations d&#39;identification intégrées) depuis [GitHub](https://github.com/adobe/pdftools-node-sdk-samples). Si vous utilisez cette version du code, vous devez ajouter vos informations d’identification au fichier pdftools-api-credentials.json avant de l’utiliser :

```
{
  "client_credentials": {
    "client_id": "<client_id>",
    "client_secret": "<client_secret>"
  },
  "service_account_credentials": {
    "organization_id": "<organization_id>",
    "account_id": "<technical_account_id>",
    "private_key_file": "<private_key_file_path>"
  }
}
```

Pour votre propre application, vous devez copier le fichier de clé privée et les fichiers d’informations d’identification dans la source de votre application.

Vous devez installer le package Node.js pour [!DNL Acrobat Services]. Pour installer le pack, utilisez la commande suivante :

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## Configuration de la journalisation

Les exemples ici utilisent Express pour le framework d’application. Ils utilisent également log4js pour la journalisation des applications. Avec log4js, vous pouvez facilement diriger la journalisation vers la console ou vers un fichier :

```
const log4js = require('log4js');
const logger = log4js.getLogger();
log4js.configure( {
    appenders: { fileAppender: { type:'file', filename: './logs/applicationlog.txt'}},
    categories: { default: {appenders: ['fileAppender'], level:'info'}}
});
 
logger.level = 'info';
logger.info('Application started')
```

Le code ci-dessus écrit les données enregistrées dans un fichier dans ./logs/applicationlog.txt. Si vous souhaitez qu’il écrive sur la console à la place, vous pouvez commenter l’appel à log4js.configure.

## Conversion de fichiers Word en PDF

Les accords et les propositions sont souvent rédigés dans une application de productivité, comme Microsoft Word. Pour accepter des documents dans ce format et convertir le document en PDF, vous pouvez ajouter des fonctionnalités à votre application. Voyons comment télécharger et enregistrer un document dans une application Express et l’enregistrer dans le système de fichiers.

Dans le HTML de l’application, ajoutez un élément fichier et un bouton pour lancer le téléchargement :

```
<input type="file" name="source" id="source" />
<button onclick="upload()" >Upload</button>
```

Dans le JavaScript de la page, téléchargez le fichier de manière asynchrone à l’aide de la fonction fetch :

```
function upload()
{
  let formData = new FormData();
  var selectedFile = document.getElementById('source').files[0];
  formData.append("source", selectedFile);
  fetch('documentUpload', {method:"POST", body:formData});
}
```

Choisissez un dossier pour accepter vos fichiers chargés. L’application a besoin d’un chemin d’accès à ce dossier. Recherchez le chemin absolu en utilisant un chemin relatif joint à \_\_dirname :

```
const uploadFolder = path.join(__dirname, "../uploads");
```

Comme le fichier est envoyé par courrier, vous devez répondre à un message de courrier côté serveur :

```
router.post('/', (req, res, next) => {
  console.log('uploading')
  if(!req.files || Object.keys(req.files).length === 0) {
  return res.status(400).send('No files were uploaded');
  }
    
  const uploadPath = path.join(uploadFolder, req.files.source.name);
  var buffer = req.files.source.data;
  var result = {"success":true};
  fs.writeFile(uploadPath, buffer, 'binary', (err)=> {
    if(err) {
      result.success = false;
    }
    res.json(result);
  });       
});
```

Une fois cette fonction exécutée, le fichier est enregistré dans le dossier de téléchargement des applications et peut être traité ultérieurement.

Ensuite, convertissez le fichier de son format natif en PDF. L&#39;exemple de code téléchargé précédemment contient un script nommé `create-pdf-from-docx.js` pour convertir un document en PDF. La fonction suivante, `convertDocumentToPDF`, prend un document téléchargé et le convertit en PDF dans un autre dossier :

```
function convertDocumentToPDF(sourcePath, destinationPath)
{    
  try {   
    const credentials = PDFToolsSDK.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdftools-api-credentials.json")
    .build();
 
    const executionContext = 
      PDFToolsSDK.ExecutionContext.create(credentials),
    createPdfOperation = PDFToolsSDK.CreatePDF.Operation.createNew();
 
    const docxReadableStream = fs.createReadStream(sourcePath);
    const input = PDFToolsSDK.FileRef.createFromStream(
      docxReadableStream, 
      PDFToolsSDK.CreatePDF.SupportedSourceFormat.docx);
    createPdfOperation.setInput(input);
 
    createPdfOperation.execute(executionContext)
    .then(result => result.saveAsFile(destinationPath))
    .catch(err => {        
      logger.erorr('Exception encountered while executing operation');        
    })
  }
  catch(err) {        
    logger.error(err);
  }
}
```

Vous remarquerez peut-être une tendance générale avec le code :

Le code crée un objet d&#39;informations d&#39;identification et un contexte d&#39;exécution, initialise une opération, puis exécute l&#39;opération avec le contexte d&#39;exécution. Vous pouvez voir ce motif dans l’ensemble du code d’exemple.

En ajoutant quelques éléments à la fonction de téléchargement pour qu’elle appelle cette fonction, les documents Word chargés par les utilisateurs sont désormais automatiquement convertis en PDF.

Le code suivant crée le chemin de destination du PDF converti et lance la conversion :

```
const documentFolder = path.join(__dirname, "../docs");
var extPosition = req.files.source.name.lastIndexOf('.') - 1;
if(extPosition < 0 ) {
  extPosition = req.files.source.name.length
}
const destinationName = path.join(documentFolder,  
  req.files.source.name.substring(0, extPosition) + '.pdf');
console.log(destinationName);
 
logger.info('converting to ${destinationName}')
  convertDocumentToPDF(uploadPath, destinationName);
```

## Conversion d’autres types de fichiers en PDF

L’Utilitaire de document convertit d’autres formats en PDF, comme HTML statique, un autre type de document courant. L’Utilitaire accepte les documents HTML compressés sous forme de fichier .zip avec toutes les ressources référencées par le document (fichiers CSS, images et autres fichiers) dans le même fichier .zip. Le document de HTML lui-même doit être nommé index.html et placé à la racine du fichier .zip.

Pour convertir un fichier .zip contenant du HTML, utilisez le code suivant :

```
//Create an HTML to PDF operation and provide the source file to it
htmlToPDFOperation = PDFToolsSdk.CreatePDF.Operation.createNew();     
const input = PDFToolsSdk.FileRef.createFromLocalFile(sourceZipFile);
htmlToPDFOperation.setInput(input);
 
// custom function for setting options
setCustomOptions(htmlToPDFOperation);
 
// Execute the operation and Save the result to the specified location.
htmlToPDFOperation.execute(executionContext)
  .then(result => result.saveAsFile(destinationPdfFile))
  .catch(err => {
    logger.error('Exception encountered while executing operation');
});
```

La fonction `setCustomOptions` spécifie d&#39;autres paramètres du PDF, tels que le format de la page. Ici, la fonction définit le format de la page sur 11,5 par 11 pouces :

```
const setCustomOptions = (htmlToPDFOperation) => {    
  const pageLayout = new PDFToolsSdk.CreatePDF.options.PageLayout();
  pageLayout.setPageSize(11.5, 8);

  const htmlToPdfOptions = 
    new PDFToolsSdk.CreatePDF.options.html.CreatePDFFromHtmlOptions.Builder()
    .includesHeaderFooter(true)
    .withPageLayout(pageLayout)
    .build();
  htmlToPDFOperation.setOptions(htmlToPdfOptions);
};
```

En ouvrant un document HTML contenant certains termes, vous obtenez ce qui suit dans le navigateur :

![Image des termes de l’ordinateur](assets/AWNjs_3.png)

La source de ce document est composée d’un fichier CSS et d’un fichier de HTML :

![Image du fichier CSS et de HTML](assets/AWNjs_4.png)

Après avoir traité le fichier de HTML, vous obtenez le même texte au format PDF :

![Fichier PDF des conditions d&#39;utilisation de l&#39;ordinateur](assets/AWNjs_5.png)

## Ajout de pages

Une autre opération courante avec les fichiers de PDF consiste à ajouter des pages à la fin qui peuvent contenir du texte standard, comme une liste de termes. L’Utilitaire de document peut regrouper plusieurs documents PDF en un seul. Si vous disposez d&#39;une liste de chemins d&#39;accès aux documents (ici dans `sourceFileList`), vous pouvez ajouter les références de fichier de chaque fichier à un objet pour une opération de combinaison.

Lorsque l’opération de combinaison s’exécute, elle fournit un fichier unique contenant la partie du contenu source. Vous pouvez utiliser `saveAsFile` sur l&#39;objet pour conserver le fichier dans le stockage.

```
const executionContext = PDFToolsSDK.ExecutionContext.create(credentials);
var combineOperation = PDFToolsSDK.CombineFiles.Operation.createNew();
 
sourceFileList.forEach(f => {
  var combinedSource = PDFToolsSDK.FileRef.createFromLocalFile(f);
  console.log(f);
  combineOperation.addInput(combinedSource);
});
    
 
combineOperation.execute(executionContext)
  .then(result=>result.saveAsFile(destinationFile))
  .catch(err => {
    logger.error(err.message);
});    
```

## Affichage des documents du PDF

Vous avez effectué plusieurs opérations sur les fichiers du PDF, mais au final, votre utilisateur doit afficher les documents. Vous pouvez incorporer le document dans une page Web à l’aide de l’API d’incorporation de PDF d’Adobe.

Sur la page qui affiche le PDF, ajoutez un élément `<div />` pour contenir le document et lui donner un ID. Vous utiliserez bientôt cet ID. Dans la page web, incluez une référence `<script />` à la bibliothèque JavaScript Adobe :

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

Le dernier morceau de code dont vous avez besoin est une fonction qui affiche le document une fois que l’API Adobe PDF Embed JavaScript est chargée. Lorsque vous recevez une notification indiquant que le script est chargé via un événement adobe_dc_view\_sdk.ready, créez un nouvel objet AdobeDC.View. Cet objet a besoin de votre ID client et de l’ID de l’élément créé précédemment. Recherchez votre ID client dans [Adobe Developer Console](https://developer.adobe.com/console/). Lorsque vous affichez les paramètres de l’application que vous avez créée lors de la génération des informations d’identification, l’ID client s’affiche.

![Image de la clé du client API](assets/AWNjs_6.png)

## Autres options du PDF

La [démonstration de l&#39;API Adobe PDF Embed](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) vous permet de prévisualiser les différentes autres options d&#39;incorporation de documents de PDF.

![Image des options d&#39;incorporation du PDF ](assets/AWNjs_7.png)

Vous pouvez activer et désactiver diverses options et voir immédiatement comment elles s’affichent. Lorsque vous avez trouvé une combinaison qui vous plaît, cliquez sur le bouton *\&lt;/\> Générer le code* pour générer le code de HTML réel à l&#39;aide de ces options.

![Image de l&#39;aperçu du code](assets/AWNjs_8.png)

## Ajout de signatures numériques et de fonctions de sécurité

Lorsqu’un document est prêt, vous pouvez ajouter des signatures numériques pour approbation à l’aide d’Adobe Sign. Cette fonctionnalité fonctionne un peu différemment de celle que vous avez utilisée jusqu’à présent. Pour les signatures numériques, une application doit être configurée pour utiliser OAuth pour l’authentification des utilisateurs.

La première étape de la configuration de votre application consiste à [enregistrer votre application](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/gstarted/create_app.md) pour utiliser OAuth pour Adobe Sign. Une fois connecté, accédez à l&#39;écran pour créer des applications en cliquant sur *Compte*, puis ouvrez la section *API Adobe Sign* et cliquez sur *Applications API* pour ouvrir la liste des applications enregistrées.

![Image de la première étape d&#39;enregistrement de votre application](assets/AWNjs_9.png)

Pour créer une nouvelle entrée d’application, cliquez sur l’icône Plus dans le coin supérieur droit.

![Image de l’icône Plus dans le coin supérieur droit de l’écran](assets/AWNjs_10.png)

Dans la fenêtre qui s’ouvre, saisissez un nom d’application et un nom d’affichage. Sélectionnez *Client* pour le domaine, puis cliquez sur *Enregistrer*.

![Image de l’emplacement où saisir le nom d’application et le nom d’affichage](assets/AWNjs_11.png)

Une fois l&#39;application créée, vous pouvez la sélectionner dans la liste et cliquer sur *Configurer OAuth pour l&#39;application*. Sélectionnez les options. Dans URL de redirection, entrez l’URL de votre application. Vous pouvez saisir plusieurs URL ici. Pour l’application que vous testez, la valeur est :

```
http://localhost:3000/signed-in 
```

Le processus d’utilisation d’OAuth pour obtenir un jeton est standard. Votre application dirige un utilisateur vers une URL de connexion. Une fois que l’utilisateur s’est connecté,
ils sont redirigés vers l&#39;application avec des informations supplémentaires dans les paramètres de requête de la page.

Pour l’URL de connexion, votre application doit transmettre votre ID client, l’URL de redirection et une liste des portées nécessaires.

Le modèle de l’URL se présente comme suit :

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

L’utilisateur est invité à se connecter avec son ID pour Adobe Sign. Une fois connectés, ils sont invités à fournir des autorisations pour l’application.

![Image de l’écran de confirmation d’accès](assets/AWNjs_12.png)

Si l&#39;utilisateur clique sur *Autoriser l&#39;accès* sur l&#39;URL de redirection, un paramètre de requête nommé code transmet le code d&#39;autorisation :

https://YourServer.com/?code=**\&lt;code_autorisation\>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

La publication de ce code sur le serveur Adobe Sign avec votre ID client et votre secret client fournit un jeton d’accès pour accéder au service. Enregistrez les valeurs dans les paramètres `api_access_point` et `web_access_point`. Ces valeurs sont utilisées pour les demandes ultérieures.

```
var requestURL = ' ${api_access_point}oauth/token?code=${code}'
  +'&client_id=${client_id}'
  +'&client_secret=${client_secret}&'
  +'redirect_uri=${redirect_url}&'
  +'grant_type=authorization_code';
request.post(requestURL, {form: { }
}, (err,response,body)=>{                
    var token_response = JSON.parse(body)
    var access_token = token_response.access_token;
    console.log(access_token);
});
```

Lorsqu’un document nécessite une signature, le document doit d’abord être téléchargé. Votre application peut télécharger le document vers la valeur `api_access_point` qui a été reçue lors de la demande du jeton OAUTH. Le point de terminaison est `/api/rest/v6/transientDocuments`. Les données de la demande se présentent comme suit :

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

Dans votre application, générez la demande avec le code suivant :

```
var uploadRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/transientDocuments',
  'headers': {
    'Authorization': 'Bearer  ${auth_token}'
  },
  formData: {
    'File': {
      'value': fs.createReadStream(documentPath),
      'options': {
        'filename': fileName,
        'contentType': null
      }
    }
  }
};
 
request(uploadRequest, (error, response) => {
  if (error) throw new Error(error);
  var jsonResponse = JSON.parse(response.body);
  var transientDocumentId = jsonResponse.transientDocumentId;
  logger.info('transientDocumentId:', transientDocumentId)
});
```

La demande renvoie une valeur `transientID`. Le document a été téléchargé, mais n’est pas encore envoyé. Pour envoyer le document, utilisez `transientID` pour demander l&#39;envoi du document.

Commencez par créer un objet JSON contenant les informations du document à signer. Dans ce qui suit, la variable `transientDocumentId` contient l’ID du code ci-dessus et `agreementDescription` contient du texte décrivant l’accord devant être signé. Les personnes qui doivent signer le document sont répertoriées dans `participantSetsInfo` en fonction de leur adresse e-mail et de leur rôle.

```
var requestBody = {
  "fileInfos":[
    {"transientDocumentId":transientDocumentId}],
    "name":agreementDescription,
    "participantSetsInfo":[
      {"memberInfos":[{"email":"user@domain.com"}],
       "order":1,"role":"SIGNER"}
    ],
    "signatureType":"ESIGN","state":"IN_PROCESS"
};
```

L’envoi de cette demande web crée la demande de signature et renvoie un objet JSON avec un ID pour la demande d’accord :

```
request(requestBody, function (error, response) {
  if (error) throw new Error(error);
  var JSONResponse = JSON.parse(response.body);
  var requestId = JSONResponse.id;
});
```

Si les signataires oublient de signer et ont besoin d’un autre e-mail de notification, renvoyez les notifications en utilisant l’ID reçu précédemment. La seule différence est que vous devez également ajouter les ID de participant des parties. Vous pouvez obtenir les ID des participants en envoyant une demande de GET à `/agreements/{agreementID}/members`.

Pour demander l’envoi du rappel, générez d’abord un objet JSON décrivant la demande. L’objet minimal a besoin d’une liste des ID de participant et d’un statut pour le rappel (« ACTIF », « TERMINÉ » ou « ANNULÉ »).

La demande peut éventuellement contenir des informations supplémentaires, telles qu&#39;une valeur pour « note » qui s&#39;affichera pour l&#39;utilisateur. Ou, un délai (en heures) pour attendre l’envoi du rappel (dans `firstReminderDelay`) et une fréquence de rappel (dans le champ « fréquence »), qui accepte des valeurs telles que DAILY_BEFORE_SIGNED, EVERYTHIRD_DAY_BEFORE_SIGNED ou WEEKLY_BEFORE_SIGNED.

```
var requestBody = {
  //participantList is an array of participant ID strings
  "recipientParticipantIds":participantList
  ,"status":"ACTIVE",
  "note":"This is a reminder to sign out important agreement."
}
 
var reminderRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/agreements/${agreementID}/reminders',
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
 
};

request(reminderRequest, function (error, response) {
});
```

Et c’est tout ce qu’il faut pour envoyer une demande de rappel.

![Image du formulaire web](assets/AWNjs_13.png)

## Création de formulaires web

Vous pouvez également utiliser l’API Adobe Sign pour créer des formulaires web. Les formulaires web vous permettent d’incorporer un formulaire dans une page web ou de créer un lien direct vers celui-ci. Une fois un formulaire web créé, il s’affiche également parmi les formulaires web dans votre console Adobe Sign. Vous pouvez créer des formulaires web avec l’état BROUILLON pour la création incrémentielle, l’état CRÉATION pour la modification des champs de formulaire web et l’état ACTIF pour héberger immédiatement le formulaire.

![Image du formulaire web dans l’écran Gérer d’Adobe Sign](assets/AWNjs_14.png)

Pour créer un formulaire web, utilisez le formulaire `transientDocumentId`. Choisissez un titre pour le formulaire et un statut pour l’initialiser.

```
var requestBody = {
  "fileInfos": [
    {
      "transientDocumentId": transientDocumentId
    }
  ],
  "name": webFormTitle,
  "state": status,
  "widgetParticipantSetInfo": {
    "memberInfos": [ { "email": "" } ],
    "role": "SIGNER"
  }
}
```

```
var createWebFormRequest = {
  'method': 'POST',
  'url': `${oauthParameters.signin_domain}/api/rest/v6/widgets`,
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
}
```

```
request(createWebFormRequest, function (error, response) {
  var jsonResp = JSON.parse(response.body);
  var webFormID = jsonResp.id;
});
```

Vous pouvez désormais incorporer ou lier à votre document.

## Marche à suivre

Comme vous pouvez le voir à partir des démarrages rapides et du code fourni, il est facile de mettre en œuvre des processus d&#39;approbation de documents PDF et numériques à l&#39;aide de Node avec les API [!DNL Adobe Acrobat Services]. Les API d’Adobe s’intègrent parfaitement dans vos applications clientes existantes.

Pour découvrir les portées requises pour un appel ou voir comment l&#39;appel est généré, vous pouvez créer des exemples d&#39;appels à partir de la [documentation de l&#39;API Rest](https://secure.na4.adobesign.com/public/docs/restapi/v6). Les [Démarrages rapides](https://github.com/adobe/pdftools-node-sdk-samples) illustrent également d&#39;autres fonctionnalités et formats de fichiers des processus des API [!DNL Adobe Acrobat Services].

Vous pouvez ajouter une multitude de fonctionnalités de PDF à vos applications, permettant ainsi à vos utilisateurs de consulter et de signer rapidement et facilement leurs documents et bien plus encore. Pour commencer, consultez [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/homepage/) dès aujourd&#39;hui.

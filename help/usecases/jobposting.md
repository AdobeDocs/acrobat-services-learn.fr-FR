---
title: Publication d’offre d’emploi
description: Apprenez à offrir une expérience Web fluide et uniforme aux postulants et aux employeurs
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8092
thumbnail: KT-8092.jpg
exl-id: 0e24c8fd-7fda-452c-96f9-1e7ab1e06922
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1447'
ht-degree: 0%

---

# Offre d’emploi

![Bannière principale de cas d&#39;utilisation](assets/UseCaseJobHero.jpg)

Lorsque vous exploitez un site Web avec plusieurs utilisateurs, il est essentiel de concevoir une expérience qui assure une expérience fluide pour tout le monde.

Imaginez le scénario suivant : vous disposez d&#39;un site Web qui permet aux employeurs de [télécharger des offres d&#39;emploi](https://developer.adobe.com/document-services/use-cases/content-publishing/job-posting). Pour les demandeurs d’emploi, il est pratique de visualiser facilement tous les documents liés à une offre d’emploi dans un format cohérent. Cependant, il est pratique pour les employeurs de joindre des renseignements dans le format de fichier qu&#39;ils possèdent. Pour plus de commodité pour les deux types d’utilisateurs, vous pouvez convertir automatiquement tous les documents chargés en PDF et les intégrer en ligne dans la publication.

## Ce que vous pouvez apprendre

Ce tutoriel pratique présente un exemple Node.js qui utilise [!DNL Adobe Acrobat Services] et son [SDK Node.js](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) pour ajouter ces fonctionnalités à un site de publication d&#39;offres d&#39;emploi. On crée ainsi un site Web plus facile à utiliser et plus attrayant pour les employeurs et les demandeurs d&#39;emploi. Voici le [code de projet[complet](https://github.com/contentlab-io/adobe_job_posting)](https://github.com/contentlab-io/adobe_job_posting), au cas où vous voudriez suivre pendant que vous lisez.

Pour commencer, configurez une application web Node.js simple basée sur Express. [Express](https://expressjs.com/) est un cadre d&#39;application web minimaliste offrant des fonctionnalités telles que le routage et le modèle. Le code de l&#39;application est disponible sur [GitHub](https://github.com/contentlab-io/adobe_job_posting). Installez également la [base de données PostgreSQL](https://www.postgresql.org/) et configurez-la pour stocker le PDF.

## API [!DNL Acrobat Services] pertinentes

* [API PDF Embed](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [API PDF Services](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

## Création d’identifiants d’API Adobe

Vous devez d’abord [créer des informations d’identification](https://www.adobe.com/go/dcsdks_credentials) pour l’API Adobe PDF Embed (gratuite) et l’API Adobe PDF Services (gratuite pendant six mois, puis [payer à l’utilisation](https://developer.adobe.com/document-services/pricing/main) pour seulement \$0,05 par transaction de document). Lors de la création des informations d’identification pour l’API PDF Services, sélectionnez l’option Créer un exemple de code personnalisé. Enregistrez le fichier ZIP et extrayez pdftools-api-credentials.json et private.key dans le répertoire racine de votre projet Node.js Express.

Vous avez également besoin d’une clé API pour l’API Embed disponible gratuitement. Dans [Projets](https://developer.adobe.com/console/projects), accédez au projet que vous avez créé. Cliquez ensuite sur **Ajouter au projet** et sélectionnez **API**. Enfin, cliquez sur **API PDF Embed**.

Spécifiez le domaine pour l’API PDF Embed. La clé API doit être publique (elle se trouve dans le code exécuté par le navigateur). En spécifiant un domaine, vous vous assurez qu’une autre personne appartenant à un autre domaine ne peut pas utiliser la clé API.

Vous ne pouvez pas utiliser « localhost » comme domaine. Spécifiez un domaine, tel que « testing.local », et modifiez le fichier hôte sur votre ordinateur pour rediriger ce domaine vers 127.0.0.1, qui est votre ordinateur. Ensuite, au lieu de tester votre application sur localhost:3000, vous pouvez la tester sur testing.local:3000. Lorsque vous avez terminé, recherchez la clé API pour l’API PDF Embed sur la page du projet.

## Ajout d’un formulaire et d’un gestionnaire de téléchargement

Avec une application Express qui fonctionne et des identifiants d’API, vous avez également besoin d’un formulaire permettant aux utilisateurs de télécharger leurs documents sur le site Web. Modifiez le modèle index.jade à cette fin.

Créez un champ de saisie pour le nom de l’offre d’emploi téléchargée et pour un document contenant plus d’informations.

Dans le bloc de contenu du modèle, ajoutez le formulaire suivant :

```
extends layout

block content
  h1= title

  form(action="/upload", enctype="multipart/form-data", method="POST")
    label Job posting name:&nbsp;
    input(type="text", name="name", required="required")
    br
    br
    label Describing document:&nbsp;
    input(type="file", name="attachment", required="required")
    br
    br
    input(type="submit", value="Submit job posting")
```

Ensuite, ajoutez un gestionnaire pour la demande du POST à l’action /upload. Ensuite, ajoutez un itinéraire pour /upload au fichier routes/index.js. Vous pouvez créer un nouveau fichier pour cet itinéraire, mais vous devrez mettre à jour le fichier app.js pour qu’il reflète le nouveau fichier. Dans ce gestionnaire d&#39;itinéraire, vous pouvez accéder au nom donné et au fichier chargé.

```
router.post('/upload', async function (req, res, next) {
    const name = req.body.name;
    const fileContents = req.files.attachment.data;

    // code to work with the uploaded document
  });
```

La fonction est asynchrone. Vous pouvez donc utiliser le mot-clé await dans la fonction, ce qui est pratique lors de l&#39;appel des méthodes qui effectuent des appels API.

![Capture d&#39;écran du site web de publication d&#39;offres d&#39;emploi](assets/jobs_1.png)

## Utilisation de l’API PDF Services

Avant d’utiliser l’API PDF Services, vous devez ajouter les importations suivantes au début du fichier d’itinéraires :

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
  const { Readable } = require('stream');
```

Juste en dessous des importations, vous pouvez charger les identifiants d&#39;API et créer un [contenu d&#39;exécution](https://www.javascripttutorial.net/javascript-execution-context/). Étant donné que vous pouvez réutiliser un contexte d&#39;exécution pour différentes opérations, il est logique de ne le faire qu&#39;une seule fois.

```
  const credentials = PDFToolsSdk.Credentials
  .serviceAccountCredentialsBuilder()
  .fromFile("pdftools-api-credentials.json")
  .build();

  const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
```

Maintenant, revenez à l&#39;écriture de code dans le gestionnaire de requêtes au niveau du commentaire dans le bloc `router.post`. Commencez par convertir le document en PDF.

```
  const createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();

  const input = PDFToolsSdk.FileRef.createFromStream(Readable.from(fileContents),
  req.files.attachment.mimetype);

  createPdfOperation.setInput(input);

  let result = await createPdfOperation.execute(executionContext);

  result.saveAsFile('output-pdf' + new Date().getTime() + '.pdf');
  return res.send('success!');
```

La plupart des opérations suivent les quatre mêmes étapes. Tout d&#39;abord, initialisez le type d&#39;opération à l&#39;aide de la méthode createNew de la classe appropriée. Créez ensuite l’entrée pour l’opération, FileRef. Les opérations suivantes peuvent ignorer cette étape car le résultat d&#39;une opération est également un FileRef. Pour cette opération initiale, créez une FileRef à partir des octets du fichier téléchargé. Troisièmement, vous devez affecter l’entrée à l’opération. Enfin, l&#39;opération s&#39;exécute, avec le contexte d&#39;exécution comme paramètre dans la méthode d&#39;exécution. Cette méthode renvoie une promesse afin que vous puissiez attendre le résultat.

Le code enregistre le PDF renvoyé dans un fichier et envoie une simple réponse de type « réussite » au navigateur. La partie « Date » du nom de fichier garantit un nom de fichier unique. La commande saveAsFile renvoie une erreur si le fichier de destination existe.

## Conversion d’images en texte et compression du PDF

Utilisez maintenant la reconnaissance optique des caractères (ROC) pour convertir des images en texte, puis compressez le résultat. Pour ce faire, utilisez les opérations OCR et CompressPDF semblables à l’opération CreatePDF. Ajoutez les éléments suivants au fichier routes, dans `router.post` :

```
  const name = req.body.name;
  const fileContents = req.files.attachment.data;

  const createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
  const input = PDFToolsSdk.FileRef.createFromStream(Readable.from(fileContents),
  req.files.attachment.mimetype);
  createPdfOperation.setInput(input);

  let result = await createPdfOperation.execute(executionContext);

  const ocrOperation = PDFToolsSdk.OCR.Operation.createNew();
  ocrOperation.setInput(result);
  result = await ocrOperation.execute(executionContext);

  const compressPdfOperation = PDFToolsSdk.CompressPDF.Operation.createNew();
  compressPdfOperation.setInput(result);
  result = await compressPdfOperation.execute(executionContext);

  result.saveAsFile('output-pdf' + new Date().getTime() + '.pdf');
  return res.send('success!');
```

Il n’est nécessaire d’effectuer cette opération qu’une seule fois, car le résultat est un FileRef, que le code peut transmettre à setInput.

Il existe une meilleure alternative que d’enregistrer le fichier sur un disque dur et de renvoyer une réponse HTTP simplifiée à l’excès. À la place, stockez le PDF dans une base de données et affichez une page Web qui intègre le PDF à l’aide de l’API gratuite d’intégration de PDF d’Adobe. De cette façon, l&#39;offre d&#39;emploi ou la brochure de l&#39;employeur est visible sur le site Web pour les demandeurs d&#39;emploi à trouver et à consulter, avec les logos de l&#39;entreprise et d&#39;autres éléments de conception.

## Stockage du PDF dans une base de données

Stockez les PDF dans une base de données PostgreSQL. Obtenez le package node-postgres pour vous connecter à Postgres dans Node.js. Installez le package stream-buffers car, à un certain point, vous devez stocker le contenu du PDF dans un tampon, et FileRef ne fonctionne qu&#39;avec les flux. Utilisez donc le package stream-buffers pour écrire le contenu dans un tampon.

```
npm install pg stream-buffers
```

Créez maintenant une table de base de données pour les offres d&#39;emploi. Il a besoin d’une colonne pour un identifiant unique, d’une colonne pour un nom et d’une colonne pour le PDF joint. Vous pouvez créer une table de base de données à partir de l&#39;interface de ligne de commande (CLI) Postgres :

```
CREATE TABLE job_postings (id TEXT PRIMARY KEY, name TEXT NOT NULL, attachment
BYTEA NOT NULL);
```

Revenez aux fichiers Node.js. Ajoutez des importations en haut du fichier :

```
  const { Client } = require('pg');
  const streamBuffers = require('stream-buffers');
```

Pour stocker le PDF dans la table de base de données, modifiez la fonction de téléchargement. Remplacez les deux dernières lignes (saveAsFile et send) par ce fragment de code :

```
  const pgClient = new Client();
  pgClient.connect();

  const id = Math.random().toString(36).substr(2, 6); // not securely random at all,
  but serves the purpose for this demo

  const writableStream = new streamBuffers.WritableStreamBuffer();
  writableStream.on("finish", async () => {    
    await pgClient.query("INSERT INTO job_postings VALUES ($1, $2, $3)", [
      id,
      name,
      writableStream.getContents()
    ]);
    res.redirect(`/job/${id}`);
  })
  result.writeToStream(writableStream);
```

Pour écrire le contenu, créez un WritableStreamBuffer. Avec l&#39;événement finish, il est temps d&#39;exécuter la requête SQL. Le package node-postgres convertit automatiquement le paramètre Buffer au format BYTEA. La requête redirige l&#39;utilisateur vers /job/{id}, un point de terminaison créé ultérieurement.

Pour l’API PDF Embed, vous avez également besoin d’un point de terminaison qui renvoie uniquement le contenu du PDF :

```
  router.get('/pdf/:id', async function (req, res, next) {
    const id = req.params.id;
 
    const pgClient = new Client();
    pgClient.connect();

  const pgResult = await pgClient.query("SELECT attachment FROM job_postings WHERE id
  = $1", [id]);
  const buffer = pgResult.rows[0].attachment;
  res.type('pdf');
    return res.send(buffer);
  });
```

## Incorporation du PDF

Maintenant, créez le point de terminaison /job/{id}, qui affiche un modèle contenant le nom de l&#39;offre d&#39;emploi demandée et un PDF intégré.

```
router.get('/job/:id', async function(req, res, next) {
    const id = req.params.id;

    const pgClient = new Client();
    pgClient.connect();

    const pgResult = await pgClient.query("SELECT name FROM job_postings WHERE id =
  $1", [id]);
    const name = pgResult.rows[0].name;

    res.render('job', { pdf_url: `/pdf/${id}`, name });
  });
```

Dans le répertoire views/, créez un fichier job.jade avec ce contenu :

```
  extends layout

  block content
    h1= name
    div(id='adobe-dc-view')
    script(src='https://documentcloud.adobe.com/view-sdk/main.js')
    script.
      window.embedUrl = "!{pdf_url}";
    script(src='/javascripts/embed-pdf.js')
```

Le premier script est le SDK View d’Adobe, qui facilite l’intégration du PDF. Le deuxième script est un texte d’une ligne qui définit la valeur de window.embedUrl sur l’URL du PDF fournie par le gestionnaire d’itinéraire Express. Créez le troisième script vous-même comme suit :

```
  document.addEventListener("adobe_dc_view_sdk.ready", function () {
    var adobeDCView = new AdobeDC.View({ clientId: "YOUR API KEY HERE", divId:
   "adobe-dc-view" });
    adobeDCView.previewFile({
      content: { location: { url: '//' + window.location.host + window.embedUrl }
         },
      metaData: { fileName: "Job posting" }
    });
  });
```

Vous pouvez maintenant tester l’ensemble du processus de chargement d’un document, de redirection vers la page /job/id et d’affichage du PDF intégré. Vos utilisateurs passent par les mêmes étapes pour ajouter une offre d’emploi ou un autre document à votre site Web.

![Capture d&#39;écran du test d&#39;un document de PDF chargé](assets/jobs_2.png)

Pour voir une intégration en ligne en action, consultez cette [démonstration en direct](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/IN_LINE/Bodea%20Brochure.pdf).

## Marche à suivre

Ce tutoriel pratique explique comment utiliser Node.js avec [!DNL Acrobat Services] pour convertir en PDF une [offre d&#39;emploi](https://developer.adobe.com/document-services/use-cases/content-publishing/job-posting) téléchargée dans différents formats. Le PDF obtenu a ensuite été incorporé dans une page Web. Vous pouvez désormais ajouter la même fonction à votre site Web, ce qui permet aux employeurs de télécharger plus facilement des descriptions de poste, des brochures et d&#39;autres documents pour les chercheurs d&#39;emploi. Ces fonctionnalités permettent à chacun d’obtenir les informations nécessaires pour trouver l’emploi de ses rêves.

[!DNL Acrobat Services] vous aide à ajouter des fonctions clés de traitement des documents à votre site web ou à votre application. Si vous souhaitez approfondir les possibilités offertes par ces API, reportez-vous à la documentation quickstart suivante :

* [API PDF Embed](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [API PDF Services](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

Pour commencer à ajouter des fonctionnalités conviviales de gestion des documents à votre site web, [inscrivez-vous à votre essai gratuit](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html). L’API Adobe PDF Embed est toujours libre d’utilisation et l’API Adobe PDF Services est gratuite pendant six mois, puis il ne s’agit que de \$0,05 par transaction de document afin que vous puissiez [payer à l’utilisation](https://developer.adobe.com/document-services/pricing/main) à mesure que votre entreprise se développe.

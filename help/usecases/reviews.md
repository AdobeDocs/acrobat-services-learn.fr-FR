---
title: Révisions et approbations
description: Découvrez comment créer un workflow de révision et d’approbation de documents pour la collaboration entre les équipes
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8094
thumbnail: KT-8094.jpg
exl-id: d704620f-d06a-4714-9d09-3624ac0fcd3a
source-git-commit: b7a20f30a2eb175053c7a25be0411f80dd88899f
workflow-type: tm+mt
source-wordcount: '1540'
ht-degree: 0%

---

# Révisions et approbations

![Bannière principale de cas d&#39;utilisation](assets/UseCaseReviewsHero.jpg)

La collaboration interéquipe à distance est devenue nécessaire pour de nombreuses entreprises pendant la pandémie de COVID-19. Le [partage et la révision de documents numériques](https://developer.adobe.com/document-services/use-cases/collaboration/review-and-approval) présentent une série de défis pour les équipes et les ressources interfonctionnelles.

Ces défis incluent le partage de documents dans différents formats de fichiers, la révision et les commentaires efficaces sur le contenu, ainsi que la synchronisation avec les modifications les plus récentes. Les API [!DNL Adobe Acrobat Services] sont conçues pour permettre aux développeurs d&#39;applications de résoudre ces problèmes pour leurs utilisateurs.

## Ce que vous pouvez apprendre

Ce tutoriel pratique montre comment créer un flux de révision et d’approbation de documents dans une application web Node.js et Express. Pour suivre ce tutoriel, vous devez maîtriser Node.js.

L’application présente les fonctionnalités suivantes :

* Conversion de différents types de fichiers en PDF

* Activer les chargements de fichiers

* Donner aux utilisateurs la possibilité d’ajouter des commentaires et des annotations

* Afficher les PDF avec ces commentaires

* Activation des profils utilisateur pour identifier les auteurs de commentaires

* Combiner des fichiers dans un PDF final que les utilisateurs peuvent télécharger

## Ressources et API pertinentes

* [API PDF Services](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [API PDF Embed](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Code de projet](https://github.com/contentlab-io/adobe_reviews_and_approvals)

## Création d’identifiants d’API Adobe

Avant de commencer le code, vous devez [créer des informations d&#39;identification](https://www.adobe.com/go/dcsdks_credentials) pour l&#39;API Adobe PDF Embed et l&#39;API Adobe PDF Services. L’API PDF Embed est gratuite. L&#39;API des services de PDF est gratuite pendant six mois, puis vous pouvez passer à une [formule prépayée](https://developer.adobe.com/document-services/pricing/main) à seulement \$0,05 par transaction de document.

Lors de la création des informations d&#39;identification pour l&#39;API des services de PDF, sélectionnez l&#39;option **Créer un exemple de code personnalisé** et sélectionnez Node.js pour la langue. Enregistrez le fichier ZIP et extrayez pdftools-api-credentials.json et private.key dans le répertoire racine de votre projet Node.js Express.

## Configuration d’un projet et de ses dépendances

Configurez votre projet Node.js et Express pour servir des fichiers statiques à partir d’un dossier nommé « public ». Vous pouvez configurer les méthodes de votre projet en fonction de vos préférences. Pour être rapidement opérationnel, vous pouvez utiliser le [générateur d&#39;applications Express](https://expressjs.com/en/starter/generator.html). Ou si vous souhaitez que les choses restent simples, vous pouvez [recommencer à zéro](https://expressjs.com/en/starter/hello-world.html) et conserver votre code dans un seul fichier JavaScript. Dans le projet d’exemple ci-dessus, vous utilisez l’approche d’un seul fichier et conservez tout votre code dans index.js.

Copiez les fichiers `pdftools-api-credentials.json` et `private.key` de l’exemple de code personnalisé dans le répertoire racine du projet. Ajoutez-les également au fichier .gitignore, si vous en avez un, afin que vos fichiers d’identification ne soient pas accidentellement envoyés à un référentiel.

Exécutez ensuite `npm install @adobe/documentservices-pdftools-node-sdk` pour installer le SDK Node.js pour les services de PDF. Importez ce module et créez l’objet d’identifiants d’API dans votre code (index.js dans votre exemple de projet), après avoir importé le reste de votre dépendance comme ceci :

```
  const PDFToolsSdk = require( "@adobe/documentservices-pdftools-node-sdk" );

  // Create Credentials
  const credentials =  PDFToolsSdk.Credentials
      .serviceAccountCredentialsBuilder()
      .fromFile( "pdftools-api-credentials.json" )
      .build();
```

Votre code de départ doit se présenter comme suit :

```
  
  const express = require( "express" );
  const PDFToolsSdk = require( "@adobe/documentservices-pdftools-node-sdk" );

  // Create Credentials
  const credentials =  PDFToolsSdk.Credentials
      .serviceAccountCredentialsBuilder()
      .fromFile( "pdftools-api-credentials.json" )
      .build();

  const app = express();

  app.use( express.static( "public" ) );

  app.listen( 8889, function() {
      console.log( "Server started on port", 8889 );
  } );
```

Vous êtes maintenant prêt à travailler avec les API [!DNL Acrobat Services].

## Conversion d’un fichier en PDF

Pour la première partie du flux de production de documents, l’utilisateur final doit charger des documents à partager. Pour ce faire, vous ajoutez une fonction de téléchargement et regroupez les différents formats de fichiers de document dans des PDF afin de les préparer au processus de révision.

Commencez par créer une fonction pour convertir des documents en PDF en fonction de l&#39;[exemple d&#39;extrait de code pour l&#39;API PDF Services](https://developer.adobe.com/document-services/apis/pdf-services). Cet exemple montre également des extraits de code pour de nombreuses autres fonctionnalités essentielles, notamment la reconnaissance optique de caractères (OCR), la protection et la suppression par mot de passe, ainsi que la compression.

```
function fileToPDF( filename, outputFilename, callback ) {
      // Create an ExecutionContext using credentials and create a new operation
  instance.
      const executionContext = PDFToolsSdk.ExecutionContext.create( credentials ),
          createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();

      // Set operation input from a source file.
      const input = PDFToolsSdk.FileRef.createFromLocalFile( filename );
      createPdfOperation.setInput( input );

      // Execute the operation and Save the result to the specified location.
      createPdfOperation.execute( executionContext )
          .then( result => {
              result.saveAsFile( outputFilename );
              callback( outputFilename );
          } );
  }
```

Vous pouvez désormais utiliser cette fonction pour créer des mots de PDF à partir de documents chargés.

## Gestion des chargements de fichiers

Ensuite, le serveur a besoin d’un point de terminaison de téléchargement de fichiers sur le serveur web pour recevoir et traiter les documents.

Tout d’abord, créez un dossier dans un dossier de téléchargement et nommez-le « brouillons ». Les fichiers chargés et les fichiers convertis dans le PDF sont stockés ici. Ensuite, exécutez `npm install express-fileupload` pour installer le module Express-FileUpload et ajouter l’intergiciel à Express dans votre code :

```
const fileUpload = require( "express-fileupload" );
app.use( fileUpload() );
```

Maintenant, ajoutez un point de terminaison `/upload` et enregistrez le fichier chargé dans le dossier Brouillons en utilisant le même nom de fichier. Ensuite, appelez la fonction que vous avez écrite précédemment pour créer un fichier PDF du même document s’il n’est pas déjà au format PDF. Vous pouvez générer un nom de fichier pour le nouveau fichier de PDF en fonction du nom du document chargé d’origine :

```
// Create a PDF file from an uploaded file
app.post( "/upload", ( req, res ) => {
    if( !req.files || Object.keys( req.files ).length === 0 ) {
        return res.status( 400 ).send( "No files were uploaded." );
    }
    
    // Create PDF from the uploaded file
    let file = req.files.myFile;
    file.mv( __dirname + "/uploads/drafts/" + file.name, ( err ) => {
        if( err ) {
            return res.status( 500 ).send( err );
        }
        if( file.name.endsWith( ".pdf" ) ) {
            res.redirect( "/" );
        }
        else {
            // Convert to PDF
            fileToPDF( __dirname + "/uploads/drafts/" + file.name, __dirname + "/uploads/drafts/" + file.name.replace( /\./g, "-" ) + ".pdf", ( file ) => {
                res.redirect( "/" );
            } );
        }
    });
} );
```

## Création d’une page de téléchargement

Maintenant, pour télécharger des fichiers à partir de l&#39;application web, créez une page web `index.html` dans le dossier des chargements. Sur la page, ajoutez un formulaire de téléchargement de fichier qui envoie le fichier au point d’entrée /upload :

```
<form ref="uploadForm" 
      action="/upload"
      method="post" 
      encType="multipart/form-data">
      <input type="file" name="myFile" accept=".doc,.docx,.ppt,.pptx,.xls,.xlsx,.txt,.rtf,.bmp,.jpg,.gif,.tiff,.png">
      <input type="submit" value="Upload File" />
  </form>
```

![Capture d&#39;écran de la fonctionnalité de téléchargement de fichiers sur la page web](assets/reviews_1.png)

Vous pouvez désormais charger des documents sur le serveur Node.js. Le serveur enregistre le fichier dans le dossier uploads/draft et crée une version au format PDF à côté.

Vous êtes maintenant prêt à incorporer les documents chargés. Utilisez donc l’API PDF Embed pour permettre aux utilisateurs d’ajouter facilement des commentaires et des annotations aux documents.

## Énumération des fichiers du PDF

Dans la mesure où un workflow de document standard peut impliquer plusieurs documents, vous devez afficher une liste de documents et lier chacun à une nouvelle page de révision de document dans votre application.

Tout d’abord, dans le code du serveur, ajoutez un point de terminaison /files qui obtient et renvoie une liste de tous les fichiers de PDF stockés dans le dossier uploads/draft :

```
const fs = require( "fs" );

app.get( "/files", ( req, res ) =\> {

fs.readdir( \_\_dirname + "/uploads/drafts/", ( err, files ) =\> {

if( err ) {

return res.status( 500 ).send( err );using

}

return res.json( files.filter( f =\> f.endsWith( ".pdf" ) ) );

} );

} );
```

Ajoutez un itinéraire `/download/:file` qui fournit l&#39;accès au fichier de PDF chargé pour l&#39;intégrer à la page Web.

>[!NOTE]
>
>Dans une application de production, vous devez ajouter une authentification et une autorisation pour vous assurer que la demande provient d’un utilisateur valide et que l’utilisateur est autorisé à accéder au document.

```
app.get( "/download/:file", function( req, res ){
    // Note: In production code, this should check authentication and user access permissions
    res.download( __dirname + "/uploads/drafts/" + req.params[ "file" ] );
});
```

Mettez à jour la page index.html avec un élément de liste de fichiers qui remplit au moment du chargement. Chaque élément peut être lié à une page web draft.html et vous passez le nom du fichier à la page à l&#39;aide de paramètres de chaîne de requête.

>[!NOTE]
>
>Pour ajouter chaque élément à l&#39;aide de jQuery, vous devez charger la bibliothèque jQuery sur votre page Web ou ajouter l&#39;élément selon une méthode différente.

```
  <ul id="filelist">
      <li>Loading documents...</li>
  </ul>

  ...

  <script>
      // Load current files
      fetch( "/files" )
      .then( r => r.json() )
      .then( files => {
          if( files && files.length > 0 ) {
              $( "#filelist" ).empty();
              files.forEach( file => {
                  $( "#filelist" ).append( `<li><a
  href="/draft.html?file=${file}">${file}</a></li>` );
              })
          } else {
                  $("#filelist").append("<div>No documents found.</div>");
                }
      });
  </script>
```

![Capture d&#39;écran de la sélection d&#39;un fichier pour révision](assets/reviews_2.png)

## Incorporation d’un PDF

Vous êtes prêt à incorporer et à afficher les fichiers de PDF dans votre application web.

Créez une page web appelée « draft.html » et ajoutez un élément div sur la page pour le PDF incorporé :

```
  <div id="adobe-dc-view"></div>
```

Inclure la bibliothèque [!DNL Acrobat Services] :

```
  <script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

Dans une balise de script personnalisé, analysez le nom du fichier à partir des paramètres de la chaîne de requête afin de savoir quel fichier incorporer sur la page :

```
  <script type="text/javascript">
          let params = new URLSearchParams( window.location.search );
          let filename = params.get( "file" );
  </script>
```

Ajoutez un écouteur d’événement de document pour l’événement adobe_dc_view_sdk.ready qui charge le fichier de PDF spécifié dans une vue incorporée à l’intérieur de l’élément div. Utilisez votre ID client à partir des informations d’identification de l’API PDF Embed. Pour activer les commentaires et les annotations, incorporez la vue en mode FULL_WINDOW et définissez l&#39;option showAnnotationsTools sur true.

```
  document.addEventListener( "adobe_dc_view_sdk.ready", () => { 
      var adobeDCView = new AdobeDC.View( { 
          clientId: "YOUR CLIENT ID HERE",
          divId: "adobe-dc-view",
          locale: "en-US",
      } );
      adobeDCView.previewFile( {
          content: { location: { url: "download/" + filename } },
          metaData: { fileName: "Draft Version.pdf" }
      }, {
          embedMode: "FULL_WINDOW",
          showAnnotationTools: true,
          showPageControls: true
      } );
  });
```

## Création d’un profil utilisateur

Par défaut, les commentaires et les annotations s’affichent comme « Invité » dans cette vue. Vous pouvez définir le nom du réviseur en cours pour les commentaires et les annotations en enregistrant un rappel de profil utilisateur dans le code de la page pour la vue PDF. Voici un exemple de profil. Dans une application complète qui inclut l’authentification des utilisateurs, les informations de profil de la session utilisateur connectée peuvent être définies de cette manière pour identifier chaque commentateur du document dans le flux de révision.

```
  adobeDCView.registerCallback(
      AdobeDC.View.Enum.CallbackType.GET_USER_PROFILE_API,
      () => {
          return new Promise( ( resolve, reject ) => {
              resolve({
                  code: AdobeDC.View.Enum.ApiResponseCode.SUCCESS,
                  data: {
                      userProfile: {
                          name: "YOUR NAME",
                          firstName: "FIRST",
                          lastName: "LAST",
                          email: "document.editor@adobe.com"
                      }
                  }
              });
          });
      }
  );
```

Votre profil vous identifie en tant qu’utilisateur spécifique lorsque vous voyez et annotez tout document chargé à l’aide de cette page web.

## Enregistrement des commentaires sur le document

Après qu&#39;un utilisateur a commenté un document, il clique sur **Enregistrer.** Par défaut, cliquer sur **Enregistrer** télécharge le fichier de PDF mis à jour. Modifiez cette action pour mettre à jour le fichier de PDF actuel sur le serveur.

Ajoutez un point de terminaison `/save` au code serveur qui écrase le fichier PDF dans le dossier uploads/draft :

```
  // Overwrite the PDF file with latest PDF changes and annotations
  app.post( "/save", ( req, res ) => {
      if( !req.files || Object.keys( req.files ).length === 0 ) {
          return res.status( 400 ).send( "No files were uploaded." );
      }

      let file = req.files.pdf;
      file.mv( __dirname + "/uploads/drafts/" + file.name, ( err ) => {
          if( err ) {
              return res.status( 500 ).send( err );
          }
          res.send( "File uploaded" );
      });
  } );
```

Enregistrez un rappel de vue de PDF pour l’API SAVE_API qui télécharge le contenu vers le point de terminaison /save. Vous pouvez modifier la valeur autoSaveFrequency pour permettre à votre application d’enregistrer automatiquement les modifications sur un minuteur et d’inclure des métadonnées supplémentaires dans le fichier actuellement incorporé une fois l’opération terminée, si vous le souhaitez.

```
  adobeDCView.registerCallback(
      AdobeDC.View.Enum.CallbackType.SAVE_API,
      ( metaData, content, options ) => {
          return new Promise( ( resolve, reject ) => {
              let formData = new FormData();
              formData.append( "pdf", new Blob( [ content ] ), "drafts/" + filename
  );
              fetch( "/save", {
                  method: "POST",
                  body: formData
              }).then( resp => {
                  resolve({
                      code: AdobeDC.View.Enum.ApiResponseCode.SUCCESS,
                      data: {
                          /* Updated file metadata after successful save operation */
                          metaData: Object.assign( metaData, {} )
                      }
                  });
              });
          });
      },
      {
          autoSaveFrequency: 0,
          enableFocusPolling: false,
          showSaveButton: true
      }
  );
```

Les commentaires et les annotations sur les brouillons de documents sont désormais enregistrés sur le serveur. Vous pouvez [en savoir plus sur la façon dont les rappels](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#callbacks-workflows) s&#39;intègrent dans votre workflow. Par exemple, les [rappels d&#39;état](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#status-callback) aident à gérer les conflits de fichiers si plusieurs personnes souhaitent réviser et commenter le même document simultanément.

À l’étape finale, vous pouvez regrouper tous les documents modifiés dans un seul fichier de PDF à l’aide de l’API des services de PDF.

## Combinaison de fichiers de PDF

Le code de combinaison de mots de PDF est similaire au code de création de mots de PDF, mais utilise l&#39;opération CombineFiles et ajoute chaque fichier comme entrée.

```
  function combineFilesToPDF( files, outputFilename, callback ) {
      // Create an ExecutionContext using credentials and create a new operation
  instance.
      const executionContext = PDFToolsSdk.ExecutionContext.create( credentials ),
          combineFilesOperation = PDFToolsSdk.CombineFiles.Operation.createNew();

      // Set operation inputs from source files.
      files.forEach( file => {
          const input = PDFToolsSdk.FileRef.createFromLocalFile( file );
          combineFilesOperation.addInput( input );
      } );

      // Execute the operation and Save the result to the specified location.
      combineFilesOperation.execute( executionContext )
          .then( result => {
              result.saveAsFile( outputFilename );
              callback( outputFilename );
          } );
 }
```

## Téléchargement du PDF final

Ajoutez un point de terminaison appelé /finalize qui appelle la fonction pour regrouper tous les fichiers de PDF du dossier `uploads/drafts` dans un fichier `Final.pdf`, puis téléchargez-le.

```
  app.get( "/finalize", ( req, res ) => {
      fs.readdir( __dirname + "/uploads/drafts/", ( err, files ) => {
          if( err ) {
              return res.status( 500 ).send( err );
          }
          combineFilesToPDF(
              files.filter( f => f.endsWith( ".pdf" ) ).map( f => __dirname + 
  "/uploads/drafts/" + f ),
              __dirname + "/uploads/Final.pdf", ( file ) => {
              res.download( file );
          } );
      } );
  } );
```

Enfin, ajoutez un lien dans la page web index.html principale à ce point d’entrée /finalize. Ce lien permet aux utilisateurs de télécharger le résultat du workflow de document.

```
<a href="/finalize">Download final PDF</a>
```

![Capture d&#39;écran du téléchargement du document final](assets/reviews_3.png)

## Marche à suivre

Ce tutoriel pratique a montré comment les API [!DNL Acrobat Services] intègrent un [workflow de partage et de révision de documents](https://developer.adobe.com/document-services/use-cases/collaboration/review-and-approval) dans une application web. L&#39;application permet aux travailleurs à distance de partager des fichiers et de collaborer avec leurs coéquipiers, ce qui est particulièrement utile pour les employés et les entrepreneurs qui travaillent à domicile.

Vous pouvez utiliser ces techniques pour activer la collaboration dans votre application ou explorer les [exemples de SDK de nœud PDF Services](https://github.com/adobe/pdftools-node-sdk-samples) et les [exemples d’API PDF Embed](https://github.com/adobe/pdf-embed-api-samples) sur GitHub pour trouver l’inspiration sur la façon d’utiliser les API d’Adobe autrement.

Prêt à activer le partage et la révision de documents dans votre propre application ? Inscrivez-vous à votre compte développeur [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html). Accédez gratuitement à Adobe PDF Embed et profitez d’un essai gratuit de six mois des autres API. Après votre évaluation, vous pouvez [payer à l&#39;utilisation](https://developer.adobe.com/document-services/pricing/main) pour seulement \$0,05 par transaction de document à mesure que votre entreprise se développe.

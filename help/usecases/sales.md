---
title: Gestion des devis et des contrats
description: Découvrez comment créer un workflow efficace pour automatiser et simplifier les propositions commerciales
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8099
thumbnail: KT-8099.jpg
exl-id: 219c70de-fec1-4946-b10e-8ab5812562ef
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1306'
ht-degree: 0%

---

# Gestion des propositions de vente et des contrats

![Bannière principale de cas d&#39;utilisation](assets/UseCaseSalesHero.jpg)

Les propositions de vente sont la première étape d&#39;une entreprise vers l&#39;acquisition de clients. Comme toujours, les premières impressions durent. Ainsi, votre première interaction avec les clients définit leurs attentes pour votre entreprise. Votre proposition doit être concise, précise et pratique.

Les contrats et les propositions contiennent différents types de données dans leur structure documentaire. Ils contiennent à la fois des données dynamiques (nom du client, montant du devis, etc.) et des données statiques (texte standard tel que les capacités de l’entreprise, les profils d’équipe et les conditions générales standard). La création de documents modèles, tels que des devis, implique souvent des tâches monotones, comme le remplacement manuel des détails du projet dans un modèle standard. Dans ce tutoriel, vous allez utiliser des données et des workflows dynamiques pour créer un processus efficace de [création de devis](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/sales-proposals-and-contracts).

## Ce que vous pouvez apprendre

Dans ce tutoriel pratique, découvrez comment implémenter des données et des workflows dynamiques à l&#39;aide de plusieurs outils, dont les plus importants sont les API [!DNL Adobe Acrobat Services]. Ces API sont utilisées pour rendre les propositions de vente et les contrats plus pratiques pour vous et votre entreprise. Ce tutoriel présente des techniques pratiques pour apprendre à créer, fusionner et afficher automatiquement des documents de PDF. L’exécution manuelle de ces tâches est longue et fastidieuse. En tirant parti des API [!DNL Acrobat Services], vous pouvez raccourcir le temps passé sur ces tâches.

## Ressources et API pertinentes

* [Microsoft Word](https://www.office.com/)

* [Node.js](https://nodejs.org/en/)

* [npm](https://www.npmjs.com/get-npm)

* [[!DNL Acrobat Services] API](https://developer.adobe.com/document-services/homepage/)

* [API Document Generation Adobe](https://developer.adobe.com/document-services/apis/doc-generation)

* [API Adobe Sign](https://developer.adobe.com/adobesign-api/)

* [Balisage de génération de document Adobe](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

## Résoudre le problème

Maintenant que les outils sont installés, vous pouvez commencer à résoudre le problème. Les propositions ont à la fois un contenu statique et un contenu dynamique unique à chaque client. Des goulots d&#39;étranglement se produisent parce que les deux types de données sont nécessaires chaque fois que vous faites une proposition. La saisie de texte statique prend beaucoup de temps. Vous allez donc l’automatiser et ne traiter que manuellement les données dynamiques de chaque client.

Tout d&#39;abord, créez un formulaire de capture de données dans [Microsoft Forms](https://www.office.com/launch/forms?auth=1) (ou l&#39;outil de création de formulaire de votre choix). Ce formulaire concerne les données dynamiques des clients qui sont ajoutées à une proposition de vente. Remplissez ce formulaire avec des questions pour obtenir les détails dont vous avez besoin des clients, par exemple, le nom de l&#39;entreprise, la date, l&#39;adresse, la portée du projet, le prix et des commentaires supplémentaires. Pour créer le vôtre, utilisez ce [formulaire]&#x200B;(https://forms.office.com/Pages/ShareFormPage.aspx id=DQSIkWdsW0yxEjajBLZtrQAAAAAAAAAAN__rtiGj5UNElTR0pCQ09ZNkJRUlowSjVQWDNYUEg2RC4u&amp;sharetoken=1AJeMavBAzzxuISRKmUy). L’objectif est que les clients potentiels remplissent le formulaire, puis exportent leurs réponses au format JSON, qui sont transmises à la partie suivante de votre workflow.

Certains créateurs de formulaires vous permettent uniquement d’exporter des données au format CSV. Il peut donc être utile de [convertir](http://csvjson.com/csv2json) le fichier CSV généré en fichier JSON.

Les données statiques sont réutilisées dans chaque proposition de vente. Vous pouvez donc utiliser un modèle de devis dans Microsoft Word pour fournir le texte statique. Vous pouvez utiliser ce [modèle](https://1drv.ms/w/s!AiqaN2pp7giKkmhVu2_2pId9MiPa?e=oeqoQ2), mais vous pouvez créer le vôtre ou utiliser un [modèle Adobe](https://developer.adobe.com/document-services/apis/doc-generation).

Désormais, vous avez besoin d’un élément qui prend à la fois les données dynamiques des clients au format JSON et le texte statique du modèle Microsoft Word pour créer une proposition de vente unique pour un client. Les API [!DNL Acrobat Services] sont utilisées pour fusionner les deux et générer un PDF pouvant être signé.

Pour que cela fonctionne, utilisez des balises. Les balises sont des chaînes faciles à utiliser qui peuvent représenter des nombres, des mots, des tableaux ou même des objets complexes. Les balises font office de marques de réservation pour les données dynamiques, qui sont dans ce cas des données client saisies dans le formulaire. Une fois les balises insérées dans le modèle, vous pouvez mapper les champs de formulaire du fichier JSON au modèle Word.

## Utilisation des balises

Ouvrez votre modèle de devis et sélectionnez l&#39;onglet **Insérer**. Dans le groupe **Compléments**, sélectionnez **Obtenir des compléments**. Ensuite, sélectionnez **Complément Adobe de la génération de documents** pour l&#39;ajouter. Une fois ajouté, le balisage Document Generation apparaît sur l&#39;onglet **Accueil** dans le groupe **Adobe**.

Dans l&#39;onglet **Accueil** du groupe **Adobe**, sélectionnez **Génération de document** pour commencer à baliser le document. Une vidéo de démonstration utile s’affiche dans un panneau à droite de la fenêtre.

![Capture d&#39;écran du complément Document Tagger dans Word](assets/sales_1.png)

Sélectionnez **Commencer**. Vous êtes ensuite invité à fournir des exemples de données. Collez ou chargez le fichier JSON de réponse au formulaire comme indiqué ci-dessous.

![Capture d&#39;écran du collage d&#39;un exemple de code](assets/sales_2.png)

Sélectionnez **Générer des balises** pour obtenir une liste des champs à partir du fichier JSON que vous avez collé ou téléchargé. Les balises sont affichées ci-dessous, sur la barre latérale droite.

![Capture d&#39;écran des balises disponibles](assets/sales_3.png)

Après avoir généré les balises, vous pouvez les insérer dans le document. Les balises sont ajoutées au document à l’emplacement du curseur. Comme indiqué ci-dessus, vous devez ajouter la balise **Étendue du projet** directement sous le sous-titre **Étendue du projet**. Ainsi, lorsqu&#39;un client entre la portée du projet dans le formulaire, sa réponse passe sous le sous-titre **Portée du projet**, en remplaçant la balise que vous venez d&#39;ajouter. Une fois les balises ajoutées, une partie du document doit ressembler à la capture d’écran ci-dessous.

![Capture d&#39;écran de l&#39;ajout de balises au document Word](assets/sales_4.png)

## Utilisation des API

Accédez à la [page d&#39;accueil](https://developer.adobe.com/document-services/apis/doc-generation) des API [!DNL Acrobat Services]. Pour commencer à utiliser les API [!DNL Acrobat Services], vous devez disposer des informations d&#39;identification pour votre application. Faites défiler l&#39;écran jusqu&#39;en bas et sélectionnez **Commencer l&#39;essai gratuit** pour créer des informations d&#39;identification. Vous pouvez utiliser ces services [gratuitement pendant six mois, puis payer au fur et à mesure](https://developer.adobe.com/document-services/pricing/main) pour seulement 0,05 $ par transaction de document, de sorte que vous ne payez que ce dont vous avez besoin.

Sélectionnez **API des services de PDF** comme service de votre choix et renseignez les autres détails comme indiqué ci-dessous.

![Capture d&#39;écran de l&#39;obtention des informations d&#39;identification](assets/sales_5.png)

Une fois vos informations d’identification créées, vous obtenez des exemples de code. Sélectionnez la langue de votre choix (ce tutoriel utilise Node.js). Vos identifiants d’API se trouvent dans un fichier zip. Extrayez les fichiers vers PDFToolsSDK-Node.jsSamples.

Pour commencer, créez un dossier vide appelé auto-doc\*\*.\*\* Dans le dossier, exécutez la commande suivante pour initialiser un projet Node.js : `npm init`. Nommez votre projet « auto-doc »*.*

Dans le dossier ./PDFToolsSDK-Node.jsSamples/adobe-dc-pdf-tools-sdk-node-samples, il existe un fichier appelé pdftools-api-credentials.json. Déplacez-le avec private.key dans le dossier auto-doc. Il contient vos identifiants d’API. En outre, dans le dossier auto-doc, créez un sous-dossier appelé « resources ». Il contient les données au format JSON reçues des clients chaque fois que vous générez une proposition de vente. Dans le même dossier, enregistrez le modèle de devis à partir de Microsoft Word.

Maintenant, vous êtes prêt à faire de la magie ! Étant donné que vous utilisez Node.js dans ce tutoriel, vous devez installer le SDK [!DNL Acrobat Services] Node.js. Pour ce faire, dans le dossier auto-doc, exécutez yarn add @adobe/documentservices-pdftools-node-sdk.

Créez maintenant un fichier appelé merge.js et collez-y le code suivant.

```
javascript
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk'),
fs = require('fs');
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Setup input data for the document merge process
const jsonString = fs.readFileSync('resources/Proposal.json'),
jsonDataForMerge = JSON.parse(jsonString);
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile('resources/Proposal.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/Proposal.pdf'))
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
```

Ce code obtient votre fichier JSON à partir du formulaire Microsoft à l’aide des balises que vous avez créées à l’aide de [!DNL Acrobat Services]. Il fusionne ensuite les données avec le modèle de devis que vous avez créé dans Microsoft Word pour générer un tout nouveau PDF. Le PDF est enregistré dans le fichier nouvellement créé.dossier /output.

En outre, le code utilise l&#39;[API Adobe Sign](https://developer.adobe.com/adobesign-api/) pour que les deux sociétés signent la proposition de vente générée. Consultez cet article de blog pour obtenir une explication détaillée de cette API.

## Marche à suivre

Vous avez commencé avec un processus inefficace et fastidieux qui nécessitait une automatisation. Vous êtes passé de la création manuelle de documents pour chaque client à la création d&#39;un workflow rationalisé pour automatiser et simplifier [le processus de devis](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/sales-proposals-and-contracts).

En utilisant Microsoft Forms, vous obteniez de vos clients des données critiques qui entraient dans leurs propositions uniques. Vous avez créé un modèle de devis dans Microsoft Word pour fournir le texte statique que vous ne vouliez pas recréer à chaque fois. Vous avez ensuite utilisé les API [!DNL Acrobat Services] pour fusionner les données du formulaire et du modèle afin de créer un PDF de devis pour vos clients de manière plus efficace.

Ce tutoriel pratique n’est qu’un aperçu de ce qui est possible avec ces API. Pour découvrir d&#39;autres solutions, consultez la page des API [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html). Utilisez tous ces outils gratuitement pendant six mois. Ensuite, ne payez que 0,05 $ par transaction documentaire sur la formule [paiement à l&#39;utilisation](https://developer.adobe.com/document-services/pricing/main), afin de ne payer que lorsque votre équipe ajoute des prospects à votre pipeline de ventes.

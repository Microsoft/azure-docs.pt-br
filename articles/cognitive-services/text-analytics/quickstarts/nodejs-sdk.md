---
title: 'Início Rápido: Usando Node.js para chamar a API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Análise de Texto.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697477"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Início Rápido: Usando Node.js para chamar o Serviço Cognitivo de Análise de Texto
<a name="HOLTop"></a>

Use este guia de início rápido para começar a analisar com o SDK de Análise de Texto para Node.js. Embora a API REST da [Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711) seja compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Consulte as [definições da API](//go.microsoft.com/fwlink/?LinkID=759346) para obter a documentação técnica das APIs.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/)
* O [SDK para Node.js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) de Análise de Texto. Você pode instalar o SDK com:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Também é necessário ter o [ponto de extremidade e a chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que foi gerada para você durante a inscrição.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Criar um aplicativo de Node.js e instalar o SDK

Depois de instalar o Node.js, crie um projeto de nó. Crie um novo diretório para seu aplicativo e navegue até seu diretório.

```mkdir myapp && cd myapp```

Execute ```npm init``` para criar um aplicativo de nó com um arquivo package.json. Instale os pacotes NPM `ms-rest-azure` e `azure-cognitiveservices-textanalytics`:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

O arquivo package.json do seu aplicativo será atualizado com as dependências.

## <a name="authenticate-your-credentials"></a>Autenticar suas credenciais

Crie um novo arquivo `index.js` na raiz do projeto e importe as bibliotecas instaladas

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Criar uma variável para a chave de assinatura de Análise de Texto.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Para a implantação segura de segredos em sistemas de produção, é recomendável usar o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Criar um cliente de Análise de Texto

Crie um novo objeto `TextAnalyticsClient` com `credentials` como parâmetro. Use a região correta do Azure para sua assinatura de Análise de Texto.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Análise de sentimento

Crie uma lista de objetos contendo os documentos que você deseja analisar. A carga para a API consiste em uma lista de `documents`, que contém os atributos `id`, `language` e `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Chame `client.sentiment` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e a pontuação de sentimento. Uma pontuação mais perto de 0 indica um sentimento negativo, enquanto uma pontuação mais perto de 1 indica um sentimento positivo.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Detecção de idioma

Crie uma lista de objetos contendo seus documentos. A carga para a API é composta por uma lista de `documents`, que contêm os atributos `id` e `text`. O atributo `text` armazena o texto a ser analisado, e `id` pode ser qualquer valor.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

Chame `client.detectLanguage()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e o primeiro idioma retornado.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidade

Crie uma lista de objetos contendo seus documentos. A carga para a API consiste em uma lista de `documents`, que contém os atributos `id`, `language` e `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Chame `client.entities()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento. Para cada entidade detectada, imprimi o nome da Wikipédia dela, o tipo e os subtipos (se existir), bem como os locais no texto original.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extração de frases-chave

Crie uma lista de objetos contendo seus documentos. A carga para a API consiste em uma lista de `documents`, que contém os atributos `id`, `language` e `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor.

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

Chame `client.keyPhrases()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e quaisquer frases-chave detectadas.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também

 [Visão geral da Análise de Texto](../overview.md) [Perguntas frequentes](../text-analytics-resource-faq.md)

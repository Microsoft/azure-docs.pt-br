---
title: Importar declarações usando node. js-LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como criar um aplicativo LUIS programaticamente com base em dados preexistentes em formato CSV usando a API de criação do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 79a372087e162fedc5b2e014a5cd4976df3cb2ce
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637815"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Criar um aplicativo LUIS programaticamente usando o Node.js

O LUIS fornece uma API programática que faz tudo o que o site do [LUIS](luis-reference-regions.md) faz. Isso poderá poupar tempo quando tiver dados preexistentes, e seria mais rápido criar um aplicativo LUIS programaticamente do que inserir as informações manualmente. 

## <a name="prerequisites"></a>Pré-requisitos

* Entre no site do [Luis](luis-reference-regions.md) e localize a [chave de criação](luis-concept-keys.md#authoring-key) nas configurações da conta. Use essa chave para chamar as APIs de criação.
* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Este tutorial começa com um CSV para arquivos de log de solicitações de usuário de uma empresa hipotética. Faça download dela [aqui](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Instale o Node.js mais recente com o NPM. Baixe-o [aqui](https://nodejs.org/en/download/).
* **[Recomendado]** O Visual Studio Code para IntelliSense e depuração, baixe-o [aqui](https://code.visualstudio.com/) gratuitamente.

Todo o código neste tutorial está disponível no [Repositório de Exemplos do Azure para Reconhecimento vocal no GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapear dados preexistentes para entidades e intenções
Mesmo se tiver um sistema que não foi criado considerando o LUIS, se ele contiver dados textuais que mapeiam diferentes coisas que os usuários desejam fazer, você poderá criar um mapeamento com base nas categorias existentes de entrada de usuário para as intenções no LUIS. Se for possível identificar palavras ou frases importantes no que os usuários disseram, essas palavras poderão ser mapeadas para entidades.

Abra o [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) arquivo. Ele contém um log de consultas de usuário para um serviço de automação residencial hipotético, incluindo como elas foram classificadas, o que o usuário disse e algumas colunas com informações úteis extraídas delas. 

![Arquivo CSV de dados pré-existentes](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Veja que a coluna **RequestType** poderia ser intenções, e a coluna **Request** mostra um exemplo de declaração. Os outros campos poderão ser entidades se elas ocorrerem na declaração. Como há intenções, entidades e declarações de exemplo, você tem os requisitos para um aplicativo de exemplo simples.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Etapas para gerar um aplicativo LUIS de dados não LUIS
Para gerar um novo aplicativo LUIS a partir do arquivo CSV:

* Analise os dados do arquivo CSV:
    * Converta em um formato que você possa carregar para o LUIS usando a API de criação. 
    * A partir dos dados analisados, reúna informações sobre intenções e entidades. 
* Crie chamadas à API de criação para:
    * Crie o aplicativo.
    * Adicione tentativas e entidades que foram coletadas dos dados analisados. 
    * Depois de criar o aplicativo LUIS, será possível adicionar os exemplos de declarações dos dados analisados. 

Você pode ver esse fluxo de programa na última parte do `index.js` arquivo. Copie ou [baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) esse código e salve-o em `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analisar o CSV

As entradas da coluna que contêm as declarações no CSV precisam ser analisadas para um formato JSON que o LUIS possa entender. Esse formato JSON deve conter um campo `intentName` que identifica a intenção da declaração. Ele também deve conter um campo `entityLabels`, que poderá estar vazio se não houver nenhuma entidade na declaração. 

Por exemplo, a entrada para "Ligue as luzes" mapeia este JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

Neste exemplo, o `intentName` é proveniente da solicitação do usuário no título de coluna **Solicitação** no arquivo CSV, e o `entityName` é proveniente de outras colunas com informações importantes. Por exemplo, se houver uma entrada para **Operação** ou **Dispositivo** e essa cadeia de caracteres também ocorrer na solicitação real, ela poderá ser rotulada como uma entidade. O código a seguir demonstra esse processo de análise. É possível copiar ou [baixá-lo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) e salvá-lo em `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Criar o aplicativo LUIS
Depois que os dados forem analisados no JSON, adicione-os a um aplicativo LUIS. O seguinte código cria o aplicativo LUIS. Copie ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) e salve-o em `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Adicionar tentativas
Depois de ter um aplicativo, você precisará de intenções nele. O seguinte código cria o aplicativo LUIS. Copie ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) e salve-o em `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Adicionar entidades
O código a seguir adiciona as entidades ao aplicativo LUIS. Copie ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) e salve-o em `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Adicionar declarações
Após a definição das entidades e intenções no aplicativo LUIS, será possível adicionar as declarações. O código a seguir usa a API [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09), que permite que você adicione até 100 declarações por vez.  Copie ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) e salve-o em `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Executar o código


### <a name="install-nodejs-dependencies"></a>Instalar dependências do Node.js
Instale as dependências do Node.js do NPM no terminal/linha de comando.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Alterar configurações
Para usar esse aplicativo, é necessário alterar os valores no arquivo index.js para sua própria chave de ponto de extremidade e fornecer o nome que deseja que o aplicativo tenha. Também é possível definir a cultura do aplicativo ou alterar o número de versão.

Abra o arquivo index.js e altere esses valores na parte superior do arquivo.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Execute o script
Execute o script em um terminal/linha de comando com Node.js.

```console
> node index.js
```

ou

```console
> npm start
```

### <a name="application-progress"></a>Andamento do aplicativo
Enquanto o aplicativo está em execução, a linha de comando mostra o andamento. A saída da linha de comando inclui o formato das respostas do LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Abrir o aplicativo LUIS
Quando o script for concluído, você poderá entrar no [Luis](luis-reference-regions.md) e ver o aplicativo Luis que você criou em **meus aplicativos**. Você deve ser capaz de ver as declarações adicionadas nas intenções **TurnOn**, **TurnOff** e **None**.

![Intenção TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Testar e treinar seu aplicativo no site do LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Recursos adicionais

Este aplicativo de exemplo usa as seguintes APIs do LUIS:
- [criar aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [adicionar intenções](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [adicionar entidades](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [adicionar declarações](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)

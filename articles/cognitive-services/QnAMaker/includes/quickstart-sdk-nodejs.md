---
title: 'Início Rápido: Biblioteca de clientes do QnA Maker para Node.js'
description: Este início rápido oferece uma introdução à biblioteca de clientes do QnA Maker para Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0d0522be53ec9b1008f18725308c91a19ee24156
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609522"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

Use a biblioteca de clientes do QnA Maker para Node.js para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime de previsão
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Obter uma resposta de uma base de dados de conhecimento
* Excluir base de dados de conhecimento

[Documentação de referência](/javascript/api/@azure/cognitiveservices-qnamaker/) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Exemplos de Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Use a biblioteca de clientes do QnA Maker para Node.js para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Obter uma resposta de uma base de dados de conhecimento
* Excluir base de dados de conhecimento

[Documentação de referência](/javascript/api/@azure/cognitiveservices-qnamaker/) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Exemplos de Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual do [Node.js](https://nodejs.org).
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o recurso. Após a implantação, selecione **Ir para recurso**.
    * Você precisará do nome do recurso e da chave do recurso que você criar para conectar o aplicativo à API do QnA Maker. Cole a chave e o nome do recurso no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual do [Node.js](https://nodejs.org).
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade.
    * OBSERVAÇÃO: marque a caixa de seleção **Gerenciado**.
    * Após a implantação do recurso QnA Maker, selecione **Ir para o recurso**. Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Execute o comando `npm init -y` para criar um aplicativo do Node com um arquivo `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

Instale os seguintes pacotes NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Instale os seguintes pacotes NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

O arquivo `package.json` do seu aplicativo é atualizado com as dependências.

Crie um arquivo chamado index.js e importe as seguintes bibliotecas:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

Crie uma variável para o nome de recurso e a chave do Azure do seu recurso.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)

- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.azurewebsites.net`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Exportar Modelo**, em **Automação**, para localizar o ponto de extremidade do runtime.

 ![Ponto de extremidade do runtime do QnA Maker](../media/runtime-endpoint.png)
   
- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)
 
- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>Modelos de objeto

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O [QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) usa dois modelos de objeto diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar e baixar a base de dados de conhecimento.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto usado para consultar a base de dados de conhecimento com a API GenerateAnswer e para enviar novas perguntas sugeridas usando a API de Treinamento (como parte do [aprendizado ativo](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O [QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) usa o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar, baixar e consultar a base de dados de conhecimento.

---

### <a name="qnamakerclient-object-model"></a>Modelo de objeto do QnAMakerClient

O cliente de criação do QnA Maker é um objeto [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) que se autentica no Azure usando suas credenciais, que contém sua chave.

Após o cliente ser criado, use a [base de dados de conhecimento](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient#knowledgebase) para criar, gerenciar e publicar sua base de dados de conhecimento.

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chame o método [client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations#getdetails-string--msrest-requestoptionsbase-) com a ID da operação para determinar o [status da solicitação](/javascript/api/@azure/cognitiveservices-qnamaker/operation).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto do QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O cliente de previsão do QnA Maker é um objeto QnAMakerRuntimeClient que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave do runtime de previsão, retornada pela chamada do cliente de criação, [client.EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys#getkeys-msrest-requestoptionsbase-), após a publicação da base de dados de conhecimento.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Um recurso gerenciado do QnA Maker não exige o uso do objeto QnAMakerRuntimeClient. Em vez disso, você chama [generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) diretamente no objeto [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient).

---

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes do QnA Maker para .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

* [Autenticar o cliente de criação](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Baixar uma base de dados de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter a chave do runtime de consulta](#get-query-runtime-key)
* [Obter o status de uma operação](#get-status-of-an-operation)
* [Autenticar o cliente do runtime de consulta](#authenticate-the-runtime-for-generating-an-answer)
* [Gerar uma resposta da base de dados de conhecimento](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

* [Autenticar o cliente de criação](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Baixar uma base de dados de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter o status de uma operação](#get-status-of-an-operation)
* [Gerar uma resposta da base de dados de conhecimento](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para criar a base de dados de conhecimento

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto ServiceClientCredentials com a sua chave e use-o com o ponto de extremidade para criar um objeto [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto).
    * Para usar os metadados e as solicitações de acompanhamento, use o contexto editorial, pois esses dados são adicionados no nível do par individual de P e R.
* Para **arquivos**, use o objeto [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto). O FileDTO inclui o nome de arquivo, bem como a URL pública para acessar o arquivo.
* Em **URLs**, use uma lista de cadeias de caracteres para representar URLs disponíveis publicamente.

A etapa de criação também inclui propriedades para a base de conhecimento:
* `defaultAnswerUsedForExtraction` – o que é retornado quando nenhuma resposta é encontrada
* `enableHierarchicalExtraction` – criar automaticamente relações de prompt entre pares extraídos do QnA
* `language` – ao criar a primeira base de dados de conhecimento de um recurso, defina o idioma a ser usado no índice do Azure Search.

Chame o método [create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#create-createkbdto--servicecallback-operation--) com as informações da base de dados de conhecimento. As informações da base de dados de conhecimento são, basicamente um objeto JSON.

Quando o método create for retornado, passe a ID da operação retornada para o método [wait_for_operation](#get-status-of-an-operation) para sondar o status. O método wait_for_operation é retornado quando a operação é concluída. Analise o valor do cabeçalho de `resourceLocation` da operação retornada para obter a nova ID da base de dados de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

Inclua a função [`wait_for_operation`](#get-status-of-an-operation), referenciada no código acima, para criar uma base de dados de conhecimento com êxito.

## <a name="update-a-knowledge-base"></a>Como atualizar uma base de dados de conhecimento

É possível atualizar uma base de dados de conhecimento passando sua ID e um [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto) contendo os objetos [add](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#add), [update](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#update) e [delete](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#deleteproperty) DTO para o método [update](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#update-string--updatekboperationdto--msrest-requestoptionsbase-). Os DTOs também são, basicamente, objetos JSON. Use o método [wait_for_operation](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

Inclua a função [`wait_for_operation`](#get-status-of-an-operation), referenciada no código acima, para atualizar uma base de dados de conhecimento com êxito.

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [download](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#download-string--models-environmenttype--msrest-requestoptionsbase-) para baixar o banco de dados como uma lista de [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto). Isso _não_ é equivalente à exportação do portal do QnA Maker da página **Configurações** do portal, porque o resultado deste método não é um arquivo TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#publish-string--msrest-requestoptionsbase-). Ele usa o modelo salvo e treinado atual, referenciado pela ID da base de dados de conhecimento, e o publica em um ponto de extremidade. Verifique o código de resposta HTTP para validar que a publicação foi bem-sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Consultar uma base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Obter a chave do runtime de consulta

Após uma base de dados de conhecimento ser publicada, você precisa da chave do runtime de consulta para consultar o runtime. Ela não é a mesma chave usada para criar o objeto cliente original.

Use o método [EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys) para obter a classe [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto).

Use qualquer uma das propriedades da chave retornadas no objeto para consultar a base de dados de conhecimento.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o runtime para gerar uma resposta

Crie um QnAMakerRuntimeClient para consultar a base de dados de conhecimento para gerar uma resposta ou treinar com base em um aprendizado ativo.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Use o QnAMakerRuntimeClient para obter uma resposta de conhecimento ou para enviar novas perguntas sugeridas para a base de dados de conhecimento, para o [aprendizado ativo](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método RuntimeClient.runtime.generateAnswer. Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Acesse as propriedades adicionais do QueryDTO, como Superior e Contexto, para usar em seu chatbot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método QnAMakerClient.knowledgebase.generateAnswer. Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Acesse as propriedades adicionais do QueryDTO, como Superior e Contexto, para usar em seu chatbot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

Este é um exemplo simples de consulta à base de dados de conhecimento. Para entender cenários de consulta avançada, confira [outros exemplos de consulta](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#deletemethod-string--msrest-requestoptionsbase-) com um parâmetro da ID da base de dados de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de aguardar a conclusão do processo, uma [operação](/javascript/api/@azure/cognitiveservices-qnamaker/operations) seja retornada. Use a [ID da operação](/javascript/api/@azure/cognitiveservices-qnamaker/operation#operationid) para fazer uma sondagem (com lógica de repetição) para determinar o status do método original.

A chamada _delayTimer_ no bloco de código a seguir é usada para simular a lógica de repetição. Substitua isso por sua pela lógica de repetição.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node index.js` do seu próprio diretório de aplicativo.

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js).

---

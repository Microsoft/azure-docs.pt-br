---
title: 'Início Rápido: Biblioteca de clientes do QnA Maker para Python'
description: Este início rápido oferece uma introdução à biblioteca de clientes do QnA Maker para Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 7c897b524f7038f4ff7b18e273202bd02886c0e8
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609424"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

Use a biblioteca de clientes do QnA Maker para python para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime de previsão
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Obter uma resposta de uma base de dados de conhecimento
* Excluir base de dados de conhecimento

[Documentação de referência](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/) | [Exemplos do Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Use a biblioteca de clientes do QnA Maker para python para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Obter uma resposta de uma base de dados de conhecimento
* Excluir base de dados de conhecimento

[Documentação de referência](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Exemplos do Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade. Após a implantação, selecione **Ir para recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade.
    * OBSERVAÇÃO: marque a caixa de seleção **Gerenciado**.
    * Após a implantação do recurso QnA Maker, selecione **Ir para o recurso**. Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um arquivo do Python chamado `quickstart-file.py` e importe as bibliotecas a seguir.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)

- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.azurewebsites.net`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Exportar Modelo**, em **Automação**, para localizar o ponto de extremidade do runtime.

 ![Ponto de extremidade do runtime do QnA Maker](../media/runtime-endpoint.png)
   
- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)
 
- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>Modelos de objeto

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O [QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) usa dois modelos de objeto diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar e baixar a base de dados de conhecimento.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto usado para consultar a base de dados de conhecimento com a API GenerateAnswer e para enviar novas perguntas sugeridas usando a API de Treinamento (como parte do [aprendizado ativo](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O [QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) usa o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar, baixar e consultar a base de dados de conhecimento.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto do QnAMakerClient

O cliente de criação do QnA Maker é um objeto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Após a criação do cliente, use a propriedade da [Base de dados de conhecimento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations) para criar, gerenciar e publicar sua base de dados de conhecimento.

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chame o método [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#get-details-kb-id--custom-headers-none--raw-false----operation-config-) com a ID da operação para determinar o [status da solicitação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto do QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O cliente de previsão do QnA Maker é um objeto `QnAMakerRuntimeClient` que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave do runtime de previsão, retornada pela chamada do cliente de criação, [client.EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-), após a publicação da base de dados de conhecimento.

Use o método `generate_answer` para obter uma resposta do runtime da consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Um recurso gerenciado do QnA Maker não exige o uso do objeto QnAMakerRuntimeClient. Em vez disso, você chama [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) diretamente no objeto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient).

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para criar a base de dados de conhecimento

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto CognitiveServicesCredentials com a sua chave e use-o com o ponto de extremidade para criar um objeto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

Use o objeto de cliente para obter um objeto de [operações da base de dados de conhecimento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations).

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto).
    * Para usar os metadados e as solicitações de acompanhamento, use o contexto editorial, pois esses dados são adicionados no nível do par individual de P e R.
* Para **arquivos**, use o objeto [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto). O FileDTO inclui o nome de arquivo, bem como a URL pública para acessar o arquivo.
* Em **URLs**, use uma lista de cadeias de caracteres para representar URLs disponíveis publicamente.

Chame o método [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) e, em seguida, passe a ID da operação retornada para o método [Operations.getDetails](#get-status-of-an-operation) para sondar o status.

A linha final do código a seguir retorna a ID da base de dados de conhecimento da resposta de MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

Inclua a função [`_monitor_operation`](#get-status-of-an-operation), referenciada no código acima, para criar uma base de dados de conhecimento com êxito.

## <a name="update-a-knowledge-base"></a>Como atualizar uma base de dados de conhecimento

É possível atualizar uma base de dados de conhecimento passando sua ID e um [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) contendo os objetos [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) e [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO para o método [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations). Use o método [Operation.getDetail](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

Inclua a função [`_monitor_operation`](#get-status-of-an-operation), referenciada no código acima, para atualizar uma base de dados de conhecimento com êxito.

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [download](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations) para baixar o banco de dados como uma lista de [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Isso _não_ é equivalente à exportação do portal do QnA Maker da página **Configurações** do portal, porque o resultado deste método não é um arquivo TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#publish-kb-id--custom-headers-none--raw-false----operation-config-). Ele usa o modelo salvo e treinado atual, referenciado pela ID da base de dados de conhecimento, e o publica em um ponto de extremidade.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Consultar uma base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Obter a chave do runtime de consulta

Após uma base de dados de conhecimento ser publicada, você precisa da chave do runtime de consulta para consultar o runtime. Ela não é a mesma chave usada para criar o objeto cliente original.

Use o método [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-) para obter a classe [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto).

Use qualquer uma das propriedades da chave retornadas no objeto para consultar a base de dados de conhecimento.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o runtime para gerar uma resposta

Crie um [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient) para consultar a base de dados de conhecimento para gerar uma resposta ou treinar com base em um aprendizado ativo.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Use o QnAMakerRuntimeClient para obter uma resposta de conhecimento ou para enviar novas perguntas sugeridas para a base de dados de conhecimento, para o [aprendizado ativo](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método QnAMakerRuntimeClient.runtime.generate_answer. Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Acesse as propriedades adicionais do QueryDTO, como Superior e Contexto, para usar em seu chatbot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-). Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Acesse as propriedades adicionais do QueryDTO, como Superior e Contexto, para usar em seu chatbot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

Este é um exemplo simples de consulta à base de dados de conhecimento. Para entender cenários de consulta avançada, confira [outros exemplos de consulta](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#delete-kb-id--custom-headers-none--raw-false----operation-config-) com um parâmetro da ID da base de dados de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de aguardar a conclusão do processo, uma [operação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)) seja retornada. Use a ID da operação para fazer uma sondagem (com lógica de repetição) para determinar o status do método original.

A chamada _setTimeout_ no bloco de código a seguir é usada para simular código assíncrono. Substitua isso pela lógica de repetição.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando python no seu arquivo de início rápido.

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).

---

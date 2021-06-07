---
title: 'Início Rápido: Biblioteca de clientes do QnA Maker para .NET'
description: Este início rápido oferece uma introdução à biblioteca de clientes do QnA Maker para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.  O QnA Maker permite ter um serviço avançado de perguntas e respostas a partir de um conteúdo semiestruturado, como documentos de perguntas frequentes ou URLs e manuais de produtos.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: aac57f4ca173a7ac94c64884fa1d2db4a478c3f3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609402"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

Use a biblioteca de clientes do QnA Maker para .NET para:

 * Criar uma base de dados de conhecimento
 * Atualizar uma base de dados de conhecimento
 * Publicar uma base de dados de conhecimento
 * Obter a chave de ponto de extremidade do runtime de previsão
 * Aguardar tarefa de execução longa
 * Baixar uma base de dados de conhecimento
 * Obter uma resposta de uma base de dados de conhecimento
 * Excluir uma base de dados de conhecimento

[Documentação de referência](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1) | [Exemplos de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Use a biblioteca de clientes do QnA Maker para .NET para:

 * Criar uma base de dados de conhecimento
 * Atualizar uma base de dados de conhecimento
 * Publicar uma base de dados de conhecimento
 * Aguardar tarefa de execução longa
 * Baixar uma base de dados de conhecimento
 * Obter uma resposta de uma base de dados de conhecimento
 * Excluir uma base de dados de conhecimento

[Documentação de referência](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [Exemplos de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/) ou a versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o nome do recurso. Após a implantação, selecione **Ir para recurso**.
    * Você precisará do nome do recurso e da chave do recurso que você criar para conectar o aplicativo à API do QnA Maker. Cole a chave e o nome do recurso no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/) ou a versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade.
    * OBSERVAÇÃO: marque a caixa de seleção **Gerenciado**.
    * Após a implantação do recurso QnA Maker, selecione **Ir para o recurso**. Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configurando

### <a name="cli"></a>CLI

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `qna-maker-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Dentro do diretório do aplicativo, instale a biblioteca de clientes do QnA Maker para .NET com o seguinte comando:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), que contém os exemplos de código neste início rápido.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs), que contém os exemplos de código neste início rápido.

---

### <a name="using-directives"></a>Uso de diretivas

No diretório do projeto, abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Chave de assinatura e pontos de extremidade de recurso

No método `Main` do aplicativo, adicione as variáveis e o código mostrados na seção a seguir para usar as tarefas comuns neste guia de início rápido.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)

- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.azurewebsites.net`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Exportar Modelo**, em **Automação**, para localizar o ponto de extremidade do runtime.

 ![Ponto de extremidade do runtime do QnA Maker](../media/runtime-endpoint.png)
      
- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)

- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---


## <a name="object-models"></a>Modelos de objeto

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O [QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) usa dois modelos de objeto diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar e baixar a base de dados de conhecimento.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto usado para consultar a base de dados de conhecimento com a API GenerateAnswer e para enviar novas perguntas sugeridas usando a API de Treinamento (como parte do [aprendizado ativo](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O [QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) usa o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar, baixar e consultar a base de dados de conhecimento.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto do QnAMakerClient

O cliente de criação do QnA Maker é um objeto [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Após a criação do cliente, use a propriedade da [Base de dados de conhecimento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) para criar, gerenciar e publicar sua base de dados de conhecimento.

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chame o método [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) com a ID da operação para determinar o [status da solicitação](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto do QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O cliente de previsão do QnA Maker é um objeto [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave do runtime de previsão, retornada pela chamada do cliente de criação, `client.EndpointKeys.GetKeys`, após a publicação da base de dados de conhecimento.

Use o método [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) para obter uma resposta do runtime da consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Um recurso gerenciado do QnA Maker não exige o uso do objeto **QnAMakerRuntimeClient**. Em vez disso, você chama o método [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync).

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

Crie uma instância de um objeto de cliente com sua chave e use esse objeto com seu recurso para construir o ponto de extremidade para criar um [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) com o ponto de extremidade e a chave. Crie um objeto [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto).
    * Para usar os metadados e as solicitações de acompanhamento, use o contexto editorial, pois esses dados são adicionados no nível do par individual de P e R.
* Para **arquivos**, use o objeto [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto). O FileDTO inclui o nome de arquivo, bem como a URL pública para acessar o arquivo.
* Em **URLs**, use uma lista de cadeias de caracteres para representar URLs disponíveis publicamente.

A etapa de criação também inclui propriedades para a base de conhecimento:
* `defaultAnswerUsedForExtraction` – o que é retornado quando nenhuma resposta é encontrada
* `enableHierarchicalExtraction` – criar automaticamente relações de prompt entre pares extraídos do QnA
* `language` – ao criar a primeira base de dados de conhecimento de um recurso, defina o idioma a ser usado no índice do Azure Search.

Chame o método [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) e, em seguida, passe a ID da operação retornada para o método [MonitorOperation](#get-status-of-an-operation) para sondar o status.

A linha final do código a seguir retorna a ID da base de dados de conhecimento da resposta de MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Inclua a função [`MonitorOperation`](#get-status-of-an-operation), referenciada no código acima, para criar uma base de dados de conhecimento com êxito.

## <a name="update-a-knowledge-base"></a>Como atualizar uma base de dados de conhecimento

É possível atualizar uma base de dados de conhecimento passando sua ID e um [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) contendo os objetos [add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) e [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO para o método [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync). Use o método [MonitorOperation](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Inclua a função [`MonitorOperation`](#get-status-of-an-operation), referenciada no código acima, para atualizar uma base de dados de conhecimento com êxito.

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) para baixar o banco de dados como uma lista de [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Isso _não_ é equivalente à exportação do portal do QnA Maker da página **Configurações** do portal, pois o resultado deste método não é um arquivo.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync). Ele usa o modelo salvo e treinado atual, referenciado pela ID da base de dados de conhecimento, e o publica em seu ponto de extremidade. Essa é uma etapa necessária para consultar sua base de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Obter a chave do runtime de consulta

Após uma base de dados de conhecimento ser publicada, você precisa da chave do runtime de consulta para consultar o runtime. Ela não é a mesma chave usada para criar o objeto de cliente original.

Use o método [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para obter a classe [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto).

Use qualquer uma das propriedades da chave retornadas no objeto para consultar a base de dados de conhecimento.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

Uma chave de runtime é necessária para consultar sua base de dados de conhecimento.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o runtime para gerar uma resposta

Crie um [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) para consultar a base de dados de conhecimento para gerar uma resposta ou treinar com base em um aprendizado ativo.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Use o QnAMakerRuntimeClient para:
* obter uma resposta da base de dados de conhecimento
* enviar novas perguntas sugeridas à base de dados de conhecimento para [aprendizado ativo](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync). Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Acesse as propriedades adicionais do QueryDTO, como [Superior](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Contexto](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context) para usar em seu chatbot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync). Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Acesse propriedades adicionais do QueryDTO, como [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) e [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest), para usar em seu chatbot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Este é um exemplo simples de consulta à base de dados de conhecimento. Para entender cenários de consulta avançada, confira [outros exemplos de consulta](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) com um parâmetro da ID da base de dados de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de aguardar a conclusão do processo, uma [operação](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation) seja retornada. Use a [ID da operação](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) para fazer uma sondagem (com lógica de repetição) para determinar o status do método original.

Os elementos _loop_ e _Task.Delay_ no bloco de código a seguir são usados para simular a lógica de repetição. Eles devem ser substituídos por sua própria lógica de repetição.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `dotnet run` do seu próprio diretório de aplicativo.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---

---
title: 'Início Rápido: Biblioteca de clientes do QnA Maker para Java'
description: Este guia de início rápido oferece uma introdução à biblioteca de clientes do QnA Maker para Java.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 755e4fcb9d5b9f1711bc615a29489037ea377d43
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609376"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

Use a biblioteca de clientes do QnA Maker para Java para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime de previsão
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Obter uma resposta de uma base de dados de conhecimento
* Excluir base de dados de conhecimento

[Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Pacote](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Use a biblioteca de clientes do QnA Maker para Java para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Obter uma resposta de uma base de dados de conhecimento
* Excluir base de dados de conhecimento

[Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Pacote](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade. Após a implantação, selecione **Ir para recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade.
    * OBSERVAÇÃO: marque a caixa de seleção **Gerenciado**.
    * Após a implantação do recurso QnA Maker, selecione **Ir para o recurso**. Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

---

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-libraries"></a>Instalar as bibliotecas cliente

Depois de instalar o Java, você pode instalar as bibliotecas de clientes usando [Maven](https://maven.apache.org/) no [Repositório MVN](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker).

### <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Crie um arquivo chamado `quickstart.java` e importe as bibliotecas a seguir.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)

- O valor de QNA_MAKER_RUNTIME_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.azurewebsites.net`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Exportar Modelo**, em **Automação**, para localizar o ponto de extremidade do runtime.

 ![Ponto de extremidade do runtime do QnA Maker](../media/runtime-endpoint.png)
   
- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

- Usamos a chave de assinatura e a chave de criação de modo intercambiável. Para obter mais detalhes sobre a chave de criação, siga [Chaves no QnA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- O valor de QNA_MAKER_ENDPOINT tem o formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Acesse o portal do Azure e localize o recurso do QnA Maker criado nos pré-requisitos. Clique na página **Chaves e Ponto de Extremidade**, em **gerenciamento de recursos**, para localizar a chave de Criação (Assinatura) e o ponto de extremidade do QnA Maker.

 ![Ponto de extremidade de criação do QnA Maker](../media/keys-endpoint.png)
 
- Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>Modelos de objeto

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O QnA Maker usa dois modelos de objeto diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar e baixar a base de dados de conhecimento.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto usado para consultar a base de dados de conhecimento com a API GenerateAnswer e para enviar novas perguntas sugeridas usando a API de Treinamento (como parte do [aprendizado ativo](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O QnA Maker usa o seguinte modelo de objeto:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar, baixar e consultar a base de dados de conhecimento.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto do QnAMakerClient

O cliente de criação do QnA Maker é um objeto [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) que é autenticado no Azure usando MsRest::ServiceClientCredentials, que contém a sua chave.

Após a criação do cliente, use os métodos da propriedade [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) do cliente para criar, gerenciar e publicar a sua base de dados de conhecimento.

Para operações imediatas, um método geralmente retornará o resultado, se houver. Para operações de execução prolongada, a resposta é um objeto [Operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java). Chame o método [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) com o valor `operation.operationId` para determinar o [status da solicitação](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto do QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O cliente de runtime do QnA Maker é um objeto [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java).

Depois de publicar a sua base de dados de conhecimento usando o cliente de criação, use o método [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) do cliente de runtime para obter uma resposta da base de dados de conhecimento.

Você cria um cliente de runtime chamando [QnAMakerRuntimeManager.authenticat](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) e passando uma chave de ponto de extremidade de runtime. Para obter a chave de ponto de extremidade de runtime, use o cliente de criação para chamar [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Um recurso gerenciado do QnA Maker não exige o uso do objeto QnAMakerRuntimeClient. Em vez disso, você chama [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) diretamente no objeto [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java).

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para criar a base de dados de conhecimento

Crie uma instância de um cliente com o ponto de extremidade de criação e a chave de assinatura.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java).
    * Para usar os metadados e as solicitações de acompanhamento, use o contexto editorial, pois esses dados são adicionados no nível do par individual de P e R.
* Para **arquivos**, use o objeto [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java). O FileDTO inclui o nome de arquivo, bem como a URL pública para acessar o arquivo.
* Em **URLs**, use uma lista de cadeias de caracteres para representar URLs disponíveis publicamente.

Chame o método [create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173) e, em seguida, passe a propriedade `operationId` da operação retornada para o método [getDetails](#get-status-of-an-operation) para sondar o status.

A linha final do código a seguir retorna a ID da base de dados de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>Como atualizar uma base de dados de conhecimento

É possível atualizar uma base de dados de conhecimento chamando [update](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) e passando a ID da base de dados de conhecimento e um objeto [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java). Esse objeto, por sua vez, pode conter:
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Passe a propriedade `operationId` da operação retornada para o método [getDetails](#get-status-of-an-operation) para sondar o status.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [download](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) para baixar o banco de dados como uma lista de [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java). Isso _não_ é equivalente à exportação do portal do QnA Maker da página **Configurações** do portal, porque o resultado deste método não é um arquivo TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196). Ele usa o modelo salvo e treinado atual, referenciado pela ID da base de dados de conhecimento, e o publica em um ponto de extremidade.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

Após uma base de dados de conhecimento ser publicada, você precisará da chave de ponto de extremidade de runtime para consultar a base de dados de conhecimento. Isso não é o mesmo que a chave de assinatura usada para criar o cliente de criação.

Use o método [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) para obter um objeto [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java).

Crie um cliente de runtime chamando [QnAMakerRuntimeManager.authenticat](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) e passando uma chave de ponto de extremidade de runtime do objeto EndpointKeysDTO.

Gere uma resposta de uma base de dados de conhecimento publicada usando o método [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36). Esse método aceita a ID da base de dados de conhecimento e o objeto [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java).

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

Gere uma resposta de uma base de dados de conhecimento publicada usando o método [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308). Esse método aceita a ID da base de dados de conhecimento e o objeto [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java).

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

Esse é um exemplo simples de consulta à base de dados de conhecimento. Para entender cenários de consulta avançada, confira [outros exemplos de consulta](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) com um parâmetro da ID da base de dados de conhecimento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de aguardar a conclusão do processo, uma [operação](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) seja retornada. Use a ID da operação para fazer uma sondagem (com lógica de repetição) para determinar o status do método original.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>Executar o aplicativo

Este é o método principal para o aplicativo.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

Execute o aplicativo da maneira a seguir. Isso pressupõe que o nome de classe é `Quickstart` e as suas dependências estão em uma subpasta chamada `lib` abaixo da pasta atual.

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/version-1)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/version-2)

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java).

---

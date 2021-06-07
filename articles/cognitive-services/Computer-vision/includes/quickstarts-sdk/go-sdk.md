---
title: 'Guia de início rápido: Biblioteca de clientes de reconhecimento óptico de caracteres para Go'
titleSuffix: Azure Cognitive Services
description: Comece a usar a biblioteca de clientes de reconhecimento óptico de caracteres para Go com este guia de início rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: b935b8a467097eb60c095c5d317f13693ef68b62
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284733"
---
<a name="HOLTop"></a>

Use a biblioteca de clientes de OCR para ler textos impressos e manuscritos de imagens.

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Pacote](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão mais recente do [Go](https://golang.org/dl/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-go-project-directory"></a>Criar um diretório do projeto Go

Em uma janela de console (cmd, PowerShell, Terminal, Bash), crie um novo workspace para seu projeto do Go, chamado `my-app`, e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Seu workspace conterá três pastas:

* **src** – Esse diretório terá o código-fonte e os pacotes. Todos os pacotes instalados com o comando `go get` residirão nesse diretório.
* **pkg** – Esse diretório terá os objetos de pacote Go compilados. Todos esses arquivos têm uma extensão `.a`.
* **bin** – Esse diretório terá os arquivos executáveis binários que são criados quando você executa o `go install`.

> [!TIP]
> Para saber mais sobre a estrutura de um workspace Go, consulte a [documentação da linguagem Go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para configurar `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Instalar a biblioteca de clientes para Go

Em seguida, instale a biblioteca de clientes para Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

Ou se você usar o DEP, no repositório, execute:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Criar um aplicativo Go 

Em seguida, crie um arquivo no diretório **src** chamado `sample-app.go`:

```bash
cd src
touch sample-app.go
```

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), que contém os exemplos de código neste início rápido.

Abra `sample-app.go` em seu IDE ou editor de texto preferido.

Declare um contexto na raiz do script. Você precisará desse objeto para executar a maioria das chamadas de função da Pesquisa Visual Computacional.

### <a name="find-the-subscription-key-and-endpoint"></a>Encontrar a chave de assinatura e o ponto de extremidade

Acesse o portal do Azure. Se o recurso da Pesquisa Visual Computacional criado na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave de assinatura e o ponto de extremidade na página **Chave e ponto de extremidade** do recurso em **Gerenciamento de recursos**. 

Crie variáveis para a chave de assinatura e o ponto de extremidade da Pesquisa Visual Computacional. Cole a chave de assinatura e o ponto de extremidade no código a seguir, quando indicado. O ponto de extremidade da Pesquisa Visual Computacional tem o formato `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Lembre-se de remover a chave de assinatura do código quando terminar e nunca a poste publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../../key-vault/general/overview.md).

Em seguida, você começará a adicionar um código para realizar diferentes operações de OCR.

> [!div class="nextstepaction"]
> [Configurei o cliente](?success=set-up-client#object-model) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK de OCR do Go.

|Nome|Descrição|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional, como análise de imagem e leitura de texto. Você cria uma instância delas com suas informações de assinatura e usa-a para fazer a maioria das operações de imagem.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Esse tipo contém os resultados de uma operação de leitura em lote. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes de OCR para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Esta etapa pressupõe que você já [criou variáveis de ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave e o ponto de extremidade da Pesquisa Visual Computacional, denominados `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

Crie uma função `main` e adicione o código a seguir a ela para criar uma instância de um cliente com o ponto de extremidade e a chave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Autentiquei o cliente](?success=authenticate-client#read-printed-and-handwritten-text) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço de OCR pode ler um texto visível em uma imagem e convertê-lo em um fluxo de caracteres. O código desta seção define uma função, `RecognizeTextReadAPIRemoteImage`, que usa o objeto de cliente para detectar e extrair um texto impresso ou manuscrito na imagem.

Adicione a referência de imagem de exemplo e a chamada de função em sua função `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Você também pode extrair um texto de uma imagem local. Confira os métodos de [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), como **BatchReadFileInStream**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para obter cenários que envolvam imagens locais.

### <a name="call-the-read-api"></a>Chamar a API de Leitura

Defina a nova função para leitura do texto, `RecognizeTextReadAPIRemoteImage`. Adicione o código abaixo, que chama o método **BatchReadFile** da imagem especificada. Esse método retorna uma ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Obter os resultados da leitura

Em seguida, obtenha a ID da operação retornada da chamada **BatchReadFile** e use-a com o método **GetReadOperationResult** para consultar o serviço a fim de obter os resultados da operação. O código a seguir verifica a operação em intervalos de um segundo até que os resultados sejam retornados. Em seguida, ele imprime os dados de texto extraídos no console.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Exibir os resultados da leitura

Adicione o código a seguir para analisar e exibir os dados de texto recuperados e concluir a definição de função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Li o texto](?success=read-printed-handwritten-text#run-the-application) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `go run`.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Executei o aplicativo](?success=run-the-application#clean-up-resources) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API de OCR (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [Visão geral de OCR](../../overview-ocr.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

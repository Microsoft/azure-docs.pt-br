---
title: 'Guia de início rápido: Biblioteca de clientes de reconhecimento óptico de caracteres para .NET'
description: Neste guia de início rápido, comece a usar a biblioteca de clientes de reconhecimento óptico de caracteres para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 58da3353f94a9caafdbd70ad56789ab138cfd6f4
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284734"
---
<a name="HOLTop"></a>

Use a biblioteca de clientes de OCR para ler textos impressos e manuscritos de uma imagem.

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/) ou a versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Usando o Visual Studio, crie um aplicativo .NET Core. 

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Depois de criar um projeto, instale a biblioteca de clientes clicando com o botão direito do mouse na solução do projeto no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**. No gerenciador de pacotes aberto, selecione **Procurar**, marque **Incluir pré-lançamento** e pesquise `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Selecione a versão `6.0.0-preview.1` e, em seguida, **Instalar**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `computer-vision-quickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
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

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

No diretório do aplicativo, instale a biblioteca de clientes da Pesquisa Visual Computacional para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), que contém os exemplos de código neste início rápido.

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência.

### <a name="find-the-subscription-key-and-endpoint"></a>Encontrar a chave de assinatura e o ponto de extremidade

Acesse o portal do Azure. Se o recurso da Pesquisa Visual Computacional criado na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave de assinatura e o ponto de extremidade na página **Chave e ponto de extremidade** do recurso em **Gerenciamento de recursos**. 

Na classe **Program** do aplicativo, crie variáveis para a chave de assinatura e o ponto de extremidade da Pesquisa Visual Computacional. Cole a chave de assinatura e o ponto de extremidade no código a seguir, quando indicado. O ponto de extremidade da Pesquisa Visual Computacional tem o formato `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Lembre-se de remover a chave de assinatura do código quando terminar e nunca a poste publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../../key-vault/general/overview.md).

No método `Main` do aplicativo, adicione chamadas para os métodos usados neste início rápido. Elas serão criadas posteriormente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Configurei o cliente](?success=set-up-client#object-model) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK de OCR do .NET.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Essa classe é necessária para toda a funcionalidade da Pesquisa Visual Computacional. Você cria uma instância delas com suas informações de assinatura e usa-a para fazer a maioria das operações de imagem.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Essa classe contém métodos adicionais para o **ComputerVisionClient**.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes de OCR para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método na classe **Program**, crie uma instância de um cliente com o ponto de extremidade e a chave de assinatura. Crie um objeto **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** com a chave de assinatura e use-o com o ponto de extremidade para criar um objeto **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Autentiquei o cliente](?success=authenticate-client#read-printed-and-handwritten-text) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço de OCR pode ler um texto visível em uma imagem e convertê-lo em um fluxo de caracteres. Para obter mais informações sobre o reconhecimento de texto, confira a [Visão geral do OCR (reconhecimento óptico de caracteres)](../../overview-ocr.md). O código desta seção usa a última versão do [SDK da Pesquisa Visual Computacional para Leitura 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) e define um método, `BatchReadFileUrl`, que usa o objeto de cliente para detectar e extrair um texto na imagem.

> [!TIP]
> Você também pode extrair um texto de uma imagem local. Confira os métodos de [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient), como **ReadInStreamAsync**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) para obter cenários que envolvam imagens locais.

### <a name="set-up-test-image"></a>Configurar imagem de teste

Na classe **Program**, salve uma referência à URL da imagem da qual deseja extrair o texto. Este snippet inclui imagens de exemplo de um texto impresso e manuscrito.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Chamar a API de Leitura

Defina o novo método para leitura do texto. Adicione o código abaixo, que chama o método **ReadAsync** da imagem especificada. Isso retorna uma ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Obter os resultados da leitura

Em seguida, obtenha a ID da operação retornada da chamada **ReadAsync** e use-a para consultar o serviço e obter os resultados da operação. O código a seguir verifica a operação até que os resultados sejam retornados. Em seguida, ele imprime os dados de texto extraídos no console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Exibir os resultados da leitura

Adicione o código a seguir para analisar e exibir os dados de texto recuperados e concluir a definição de método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Executar o aplicativo

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Execute o aplicativo clicando no botão **Depurar** na parte superior da janela do IDE.

#### <a name="cli"></a>[CLI](#tab/cli)

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Referência da API da Pesquisa Visual Computacional (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/computervision)

* [Visão geral de OCR](../../overview-ocr.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).

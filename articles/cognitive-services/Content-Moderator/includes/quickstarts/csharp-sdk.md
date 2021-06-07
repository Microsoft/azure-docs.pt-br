---
title: Início rápido da biblioteca de clientes .NET do Content Moderator
titleSuffix: Azure Cognitive Services
description: Neste início rápido, saiba como começar a usar a biblioteca de clientes do Content Moderator do Azure para .NET. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: bdf49f16b003c25da9fe8972a3fb7e3837d62819
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105186"
---
Introdução à biblioteca de clientes do Content Moderator do Azure para .NET. Siga essas etapas para instalar o pacote do NuGet e testar o código de exemplo para tarefas básicas. 

O Content Moderator é um serviço de IA que permite que você lide com conteúdos potencialmente ofensivos, arriscados ou, de alguma outra forma, indesejáveis. Use o serviço de moderação de conteúdo baseado em IA para digitalizar texto, imagem e vídeos e aplicar sinalizadores de conteúdo automaticamente. Em seguida, integre seu aplicativo com a ferramenta de Análise, um ambiente de moderador online para uma equipe de revisores humanos. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.

Use a biblioteca de clientes do Content Moderator para .NET para:

* Moderar texto
* Moderar imagens
* Criar uma revisão

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Exemplos](../../samples-dotnet.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/) ou a versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Depois de obter uma assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Criar um recurso do Content Moderator"  target="_blank">crie um recurso do Content Moderator </a> no portal do Azure para obter uma chave e um ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Será necessário obter uma chave e um ponto de extremidade do recurso criado para conectar o aplicativo ao Content Moderator. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Usando o Visual Studio, crie um aplicativo .NET Core. 

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Depois de criar um projeto, instale a biblioteca de clientes clicando com o botão direito do mouse na solução do projeto no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**. No gerenciador de pacotes aberto, selecione **Procurar**, marque **Incluir pré-lançamento** e pesquise `Microsoft.Azure.CognitiveServices.ContentModerator`. Selecione a versão `2.0.0` e, em seguida, **Instalar**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `content-moderator-quickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Content Moderator para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs), que contém os exemplos de código neste início rápido.

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Adicione as seguintes declarações de `using` :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Na classe **Program**, crie variáveis para a chave e o ponto de extremidade do recurso.

> [!IMPORTANT]
> Acesse o portal do Azure. Caso o recurso do Content Moderator criado na seção **Pré-requisitos** tenha sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


No método `main()` do aplicativo, adicione chamadas para os métodos usados neste início rápido. Elas serão criadas posteriormente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos da biblioteca de clientes do .NET do Content Moderator.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Essa classe é necessária para toda a funcionalidade do Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo para adulto, informações pessoais ou rostos humanos.|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
|[Análises](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Essa classe fornece a funcionalidade das APIs de Revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Content Moderator para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Moderar texto](#moderate-text)
* [Moderar imagens](#moderate-images)
* [Criar uma revisão](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método, crie uma instância de objetos do cliente com o ponto de extremidade e a chave.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Moderar texto

O código a seguir usa um cliente do Content Moderator para analisar um corpo de texto e imprimir os resultados no console. Na raiz de sua classe **Program**, defina os arquivos de entrada e saída:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Depois adicione um arquivo *TextFile.txt* na raiz do seu projeto. Adicione seu próprio texto a este arquivo ou use o seguinte texto de exemplo:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Em seguida, defina o método de moderação de texto em algum lugar de sua classe **Program**:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Moderar imagens

O seguinte código usa um cliente do Content Moderator, em conjunto com um objeto [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation), para analisar imagens remotas para detectar conteúdo adulto e obsceno.

> [!NOTE]
> Você também pode analisar o conteúdo de uma imagem local. Consulte a [documentação de referência](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para ver métodos e operações que funcionam com imagens locais.

### <a name="get-sample-images"></a>Obter imagens de exemplo

Defina os arquivos de entrada e saída na raiz da classe **Program**:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Em seguida, crie o arquivo de entrada, *ImageFiles.txt*, na raiz do projeto. Nesse arquivo, você adiciona as URLs de imagens para analisar&mdash;uma URL em cada linha. Use as seguintes imagens de exemplo:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definir a classe auxiliar

Adicione a seguinte definição de classe dentro da classe **Program**. Essa classe interna manipulará os resultados da moderação de imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definir o método de moderação de imagem

O método a seguir itera as URLs de imagem em um arquivo de texto, cria uma instância de **EvaluationData** e analisa a imagem para detectar conteúdo adulto/erótico, texto e rostos humanos. Em seguida, ele adiciona a instância final de **EvaluationData** a uma lista e grava a lista completa de dados retornados no console.

#### <a name="iterate-through-images"></a>Iterar as imagens

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analisar conteúdo

Para obter mais informações sobre os atributos de imagem que o Content Moderator avalia, consulte o guia [Conceitos de moderação de imagem](../../image-moderation-api.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Gravar os resultados da moderação em arquivo

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Criar uma revisão

Você pode usar a biblioteca de clientes do .NET do Content Moderator para alimentar conteúdo na [Ferramenta de análise](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam analisá-lo. Para saber mais sobre a ferramenta Revisão, confira o [Guia conceitual da ferramenta Revisão](../../review-tool-user-guide/human-in-the-loop.md).

O método nesta seção usa a classe [Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) para criar uma revisão, recuperar sua ID e verificar seus detalhes depois de receber a entrada humana por meio do portal da Web da Ferramenta de análise. Ele registra todas essas informações em um arquivo de texto de saída. 

### <a name="get-sample-images"></a>Obter imagens de exemplo

Declare a seguinte matriz na raiz de sua classe **Program**. Essa variável faz referência a uma imagem de exemplo a ser usada para criar a revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Obter credenciais de revisão

Entre na [Ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) e recupere o nome da equipe. Em seguida, atribua-o à variável apropriada na classe **Program**. Como alternativa, você pode configurar um ponto de extremidade de retorno de chamada para receber atualizações sobre a atividade da revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definir a classe auxiliar

Adicione a seguinte definição de classe dentro da classe **Program**. Essa classe será usada para representar uma única instância de revisão que é enviada para a Ferramenta de revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definir o método auxiliar

Adicione o seguinte método à classe **Programa**. Esse método gravará os resultados das consultas de revisão no arquivo de texto de saída.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definir o método de criação da revisão

Agora, você está pronto para definir o método que manipulará a criação e a consulta da revisão. Adicione um novo método, **CreateReviews**, e defina as variáveis locais a seguir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Postar revisões na Ferramenta de revisão

Em seguida, adicione o código a seguir para iterar pelas imagens de exemplo fornecidas, adicionar metadados e enviá-los para a Ferramenta de revisão em um único lote. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

O objeto retornado da chamada à API conterá valores de ID exclusivos para cada imagem carregada. O código a seguir analisa essas IDs e, em seguida, as usa para consultar o Content Moderator quanto ao status de cada imagem no lote.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Obter detalhes de revisão

O código a seguir faz com que o programa aguarde a entrada do usuário. Quando chega a esta etapa no runtime, você pode acessar a [Ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) por conta própria, verificar se a imagem de exemplo foi carregada e interagir com ela. Para saber mais sobre como interagir com uma revisão, confira o [Guia de instruções de revisões](../../review-tool-user-guide/review-moderated-images.md). Quando terminar, você poderá pressionar qualquer tecla para dar continuidade ao programa e recuperar os resultados do processo de revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Se você usou um ponto de extremidade de retorno de chamada neste cenário, ele deverá receber um evento neste formato:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

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

Neste início rápido, você aprendeu a usar a biblioteca .NET do Content Moderator para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
> [Conceitos de moderação de imagem](../../image-moderation-api.md)
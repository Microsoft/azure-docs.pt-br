---
title: 'Guia de início rápido: Biblioteca de clientes da Análise de Imagem para Go'
titleSuffix: Azure Cognitive Services
description: Comece a usar a biblioteca de clientes da Análise de Imagem para Go com este guia de início rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 780d0db40651255adbd2dd70336f5696ae99862e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287369"
---
<a name="HOLTop"></a>

Use a biblioteca de clientes da Análise de Imagem para analisar uma imagem em busca de marcas, descrição de texto, rostos, conteúdo para adulto, entre outros.

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

Abra `sample-app.go` em seu IDE ou editor de texto preferido. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Além disso, declare um contexto na raiz do seu script. Você precisará deste objeto para executar a maioria das chamadas de função da Análise de Imagem:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Em seguida, você começará a adicionar o código para realizar diferentes operações de Pesquisa Visual Computacional.

> [!div class="nextstepaction"]
> [Configurei o cliente](?success=set-up-client#object-model) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK da Análise de Imagem para Go.

|Nome|Descrição|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional, como análise de imagem e leitura de texto. Você cria uma instância delas com suas informações de assinatura e usa-a para fazer a maioria das operações de imagem.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Esse tipo contém os resultados de uma chamada de função **AnalyzeImage**. Há tipos semelhantes para cada uma das funções específicas da categoria.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Esse tipo define os diferentes tipos de análise de imagem que podem ser feitos em uma operação de análise padrão. Especifique um conjunto de valores VisualFeatureTypes dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Análise de Imagem para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Esta etapa pressupõe que você já [criou variáveis de ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave e o ponto de extremidade da Pesquisa Visual Computacional, denominados `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

Crie uma função `main` e adicione o código a seguir a ela para criar uma instância de um cliente com o ponto de extremidade e a chave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Autentiquei o cliente](?success=authenticate-client#analyze-an-image) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analisar uma imagem

O código a seguir usa o objeto de cliente para analisar uma imagem remota e imprimir os resultados para o console. Você pode obter uma descrição de texto, categorização, lista de marcas, objetos detectados, rostos detectados, sinalizadores de conteúdo para adulto, cores principais e tipo de imagem.

### <a name="set-up-test-image"></a>Configurar imagem de teste

Primeiro, salve uma referência à URL da imagem que você deseja analisar. Coloque isso dentro de sua função `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Você também pode analisar uma imagem local. Confira os métodos de [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), como **DescribeImageInStream**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para obter cenários que envolvam imagens locais.

### <a name="specify-visual-features"></a>Especificar recursos visuais

As chamadas de função a seguir extraem recursos visuais diferentes da imagem de exemplo. Você definirá essas funções nas seções a seguir.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Obter a descrição da imagem

A função a seguir obtém a lista de legendas geradas para a imagem. Para obter mais informações sobre a descrição da imagem, consulte [Descrever imagens](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Obter categoria da imagem

A função a seguir obtém a categoria detectada da imagem. Para obter mais informações, consulte [Categorizar imagens](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Obter marcas da imagem

A função a seguir obtém o conjunto de marcas detectadas na imagem. Para obter mais informações, consulte [Marcas de conteúdo](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Detectar objetos

A função a seguir detecta objetos comuns na imagem e imprime-os no console. Para obter mais informações, confira [Detecção de objetos](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta logotipos e marcas corporativas na imagem e imprime-os no console. Para obter mais informações, [Detecção de objetos](../../concept-brand-detection.md).

Primeiro, declare uma referência a uma nova imagem dentro de sua função `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

O código a seguir define a função de detecção de marca.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Detectar faces

A função a seguir retorna os rostos detectados na imagem com suas coordenadas de retângulo e alguns atributos de rosto. Para obter mais informações, consulte [Detecção facial](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo para adulto, erótico ou violento

A função a seguir imprime a presença detectada de conteúdo para adulto na imagem. Para obter mais informações, confira [Conteúdo para adulto, erótico e violento](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

A função a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Para obter mais informações, consulte [Esquemas de cores](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

A Análise de Imagem pode usar modelos especializados para realizar análise adicional em imagens. Para obter mais informações, confira [Conteúdo específico do domínio](../../concept-detecting-domain-content.md). 

O código a seguir analisa dados sobre celebridades detectados na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

O código a seguir analisa dados sobre pontos de referência detectados na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

A função a seguir imprime informações sobre o tipo de imagem &mdash;, seja clip-art ou desenho de uma linha.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Analisei uma imagem](?success=analyze-image#run-the-application) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)


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
> [Referência da API de Análise de Imagem (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [Visão geral da Análise de Imagem](../../overview-image-analysis.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

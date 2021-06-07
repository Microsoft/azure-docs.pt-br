---
title: 'Guia de início rápido: Biblioteca de clientes da Análise de Imagem para Node.js'
description: Comece a usar a biblioteca de clientes da Análise de Imagem para Node.js com este guia de início rápido
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 80c08eb20a734c16543f2767d18d86b260f17eee
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287363"
---
<a name="HOLTop"></a>

Use a biblioteca de clientes da Análise de Imagem para analisar uma imagem em busca de marcas, descrição de texto, rostos, conteúdo para adulto, entre outros.

[Documentação de referência](/javascript/api/@azure/cognitiveservices-computervision/) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [Node.js](https://nodejs.org/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo do Node com um arquivo `package.json`.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Instale os pacotes NPM `ms-rest-azure` e `@azure/cognitiveservices-computervision`:

```console
npm install @azure/cognitiveservices-computervision
```

Instale também o módulo assíncrono:

```console
npm install async
```

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.

Crie um novo arquivo, *index.js*, e abra-o em um editor de texto. Adicione as seguintes instruções de importação ao arquivo.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), que contém os exemplos de código neste início rápido.

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso da Pesquisa Visual Computacional criado na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

> [!div class="nextstepaction"]
> [Configurei o cliente](?success=set-up-client#object-model) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK da Análise de Imagem para Node.js.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Essa classe é necessária para toda a funcionalidade da Pesquisa Visual Computacional. Você cria uma instância delas com suas informações de assinatura e usa-a para fazer a maioria das operações de imagem.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitos em uma operação de análise padrão. Especifique um conjunto de valores **VisualFeatureTypes** dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Análise de Imagem para Node.js:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autenticar o cliente


Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) com sua chave e ponto de extremidade e use-o para criar um objeto [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Em seguida, defina uma função `computerVision` e declare uma série assíncrona com a função principal e a função de retorno de chamada. Você adicionará o código de início rápido à função principal e chamará `computerVision` na parte inferior do script. O restante do código neste guia de início rápido vai dentro da função `computerVision`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Autentiquei o cliente](?success=authenticate-client#analyze-an-image) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analisar uma imagem

O código nesta seção analisa as imagens remotas para extrair vários recursos visuais. Você pode executar essas operações como parte do método **analyzeImage** do objeto cliente ou chamá-las usando métodos individuais. Confira a [documentação de referência](/javascript/api/@azure/cognitiveservices-computervision/) para obter detalhes.

> [!NOTE]
> Você também pode analisar uma imagem local. Confira os métodos de [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient), como **analyzeImageInStream**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para obter cenários que envolvam imagens locais.

### <a name="get-image-description"></a>Obter a descrição da imagem

O código a seguir obtém a lista de legendas geradas para a imagem. Confira [Descrever imagens](../../concept-describing-images.md) para obter mais detalhes.

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Em seguida, adicione o código a seguir para obter a descrição da imagem e imprimi-la no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obter categoria da imagem

O código a seguir obtém a categoria detectada da imagem. Confira [Categorizar imagens](../../concept-categorizing-images.md) para obter detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Defina a função auxiliar `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obter marcas da imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Confira [Marcas de conteúdo](../../concept-tagging-images.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Defina a função auxiliar `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detectar objetos

O código a seguir detecta objetos comuns na imagem e imprime-os no console. Confira [Detecção de objetos](../../concept-object-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Defina a função auxiliar `formatRectObjects` para retornar as coordenadas superior, esquerda, inferior e direita, juntamente com a largura e a altura.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta logotipos e marcas corporativas na imagem e imprime-os no console. Confira [Detecção de marca](../../concept-brand-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Detectar faces

O código a seguir retorna os rostos detectados na imagem com suas coordenadas de retângulo e atributos de rosto selecionados. Confira [Detecção facial](../../concept-detecting-faces.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Defina a função auxiliar `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo para adulto, erótico ou violento

O código a seguir imprime a presença detectada de conteúdo para adulto na imagem. Confira [Conteúdo para adulto, erótico e violento](../../concept-detecting-adult-content.md) para obter mais detalhes.

Defina a URL da imagem que será usada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Depois, adicione o código a seguir para detectar conteúdo para adulto e imprimir os resultados no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Confira [Esquemas de cores](../../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Defina a função auxiliar `printColorScheme` para imprimir os detalhes do esquema de cores no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

A Análise de Imagem pode usar o modelo especializado para realizar a análise adicional em imagens. Confira [Conteúdo específico do domínio](../../concept-detecting-domain-content.md) para obter mais detalhes.

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

O código a seguir analisa dados sobre pontos de referência detectados na imagem.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Defina a função auxiliar `formatRectDomain` para analisar os dados de localização sobre os pontos de referência detectados.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código a seguir imprime informações sobre o tipo de imagem – seja clip-art ou desenho de linha.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Defina a função auxiliar `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Analisei uma imagem](?success=analyze-image#run-the-application) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image)



## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node` no seu arquivo de início rápido.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Executei o aplicativo](?success=run-the-application#clean-up-resources) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Referência da API de Análise de Imagem (Node.js)](/javascript/api/@azure/cognitiveservices-computervision/)


* [Visão geral da Análise de Imagens](../../overview-image-analysis.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).

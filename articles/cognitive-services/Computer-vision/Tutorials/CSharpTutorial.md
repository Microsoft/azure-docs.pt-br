---
title: 'Exemplo: Explorar um aplicativo de processamento de imagens no C#'
titleSuffix: Azure Cognitive Services
description: Explore um aplicativo Windows básico que usa a API da Pesquisa Visual Computacional nos Serviços Cognitivos do Azure. Execute o OCR, crie miniaturas e trabalhe com recursos visuais em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0a1878268beb135151a23b87d03839f3a51f4a96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102432580"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Exemplo: Explorar um aplicativo de processamento de imagens com o C#

Explore um aplicativo do Windows que usa a Pesquisa Visual Computacional para executar o OCR (reconhecimento óptico de caracteres), criar miniaturas com recorte inteligente e detectar, categorizar, marcar e descrever recursos visuais, incluindo rostos, em uma imagem. O exemplo abaixo permite que você envie uma URL de imagem ou um arquivo armazenado localmente. Você pode usar este exemplo de software livre como modelo para criar seu próprio aplicativo para Windows usando a API do Computer Vision e o Windows Presentation Foundation (WPF), uma parte do .NET Framework.

> [!div class="checklist"]
> * Obter o aplicativo de amostra do GitHub
> * Abra e construa o aplicativo de amostra no Visual Studio
> * Execute o aplicativo de amostra e interaja com ele para executar vários cenários
> * Explore os vários cenários incluídos no aplicativo de amostra

## <a name="prerequisites"></a>Pré-requisitos

Antes de explorar o aplicativo de amostra, verifique se você atendeu aos seguintes pré-requisitos:

* É necessário ter o [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou posterior.
* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="get-the-sample-app"></a>Obter o aplicativo de exemplo

O aplicativo de amostra do Computer Vision está disponível no GitHub do repositório `Microsoft/Cognitive-Vision-Windows`. Este repositório também inclui o repositório `Microsoft/Cognitive-Common-Windows` como um submódulo Git. Você pode clonar recursivamente este repositório, incluindo o submódulo, usando o comando `git clone --recurse-submodules` na linha de comando ou usando o GitHub Desktop.

Por exemplo, para clonar recursivamente o repositório do aplicativo de amostra do Computer Vision a partir de um prompt de comando, execute o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Não baixe este repositório como um ZIP. O Git não inclui submódulos ao baixar um repositório como um ZIP.

### <a name="get-optional-sample-images"></a>Obter imagens de exemplo opcional

Opcionalmente, você pode usar as imagens de amostra incluídas com o aplicativo de amostra [Face](../../Face/Overview.md), disponível no GitHub do repositório`Microsoft/Cognitive-Face-Windows`. Esse aplicativo de amostra inclui uma pasta, `/Data`, que contém várias imagens de pessoas. Você pode clonar recursivamente este repositório, também, pelos métodos descritos para o aplicativo de amostra do Computer Vision.

Por exemplo, para clonar recursivamente o repositório do aplicativo de amostra Face a partir de um prompt de comando, execute o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Abra e construa o aplicativo de amostra no Visual Studio

Você deve criar o aplicativo de amostra primeiro, para que o Visual Studio possa resolver dependências antes que você possa executar ou explorar o aplicativo de amostra. Para abrir e construir o aplicativo de amostra, execute as seguintes etapas:

1. Abra o arquivo de solução do Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln`, no Visual Studio.
1. Verifique se a solução do Visual Studio contém dois projetos:  

   * SampleUserControlLibrary
   * Exemplos de WPF VisionAPI  

   Se o projeto SampleUserControlLibrary não estiver disponível, confirme se você clonou recursivamente o repositório `Microsoft/Cognitive-Vision-Windows`.
1. No Visual Studio, pressione Ctrl + Shift + B ou escolha **Build** no menu da faixa de opções e escolha **Build Solution** para criar a solução.

## <a name="run-and-interact-with-the-sample-app"></a>Executar e interagir com o aplicativo de exemplo

Você pode executar o aplicativo de amostra para ver como ele interage com você e com a biblioteca cliente do Computer Vision ao executar várias tarefas, como gerar miniaturas ou marcar imagens. Para executar e interagir com o aplicativo de amostra, execute as seguintes etapas:

1. Após a compilação ser concluída, pressione **F5** ou escolha **Depurar** no menu da faixa de opções e escolha **Iniciar depuração** para executar o aplicativo de amostra.
1. Quando o aplicativo de amostra for exibido, escolha **Gerenciamento de Chave de Assinatura** no painel de navegação para exibir a página Gerenciamento de Chave de Assinatura.
   ![Página de gerenciamento de chaves de assinatura](../Images/Vision_UI_Subscription.PNG)  
1. Digite sua chave de assinatura na **Chave de assinatura**.
1. Insira a URL do ponto de extremidade no **Ponto de extremidade**.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Se você não quiser inserir sua chave de assinatura e o URL do endpoint na próxima vez que executar o aplicativo de amostra, escolha **Save Setting** para salvar a chave de assinatura e o URL do endpoint em seu computador. Se você deseja excluir sua chave de assinatura e URL de ponto de extremidade previamente salvas, escolha **Excluir configuração**.

   > [!NOTE]
   > O aplicativo de amostra usa armazenamento isolado e `System.IO.IsolatedStorage` para armazenar sua chave de assinatura e URL do terminal.

1. Em **Selecione um cenário** no painel de navegação, selecione um dos cenários atualmente incluídos no aplicativo de amostra:  

   | Cenário | Descrição |
   |----------|-------------|
   |Analisar a imagem | Usa a operação [Analisar imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para analisar uma imagem local ou remota. Você pode escolher os recursos visuais e a linguagem para a análise e ver a imagem e os resultados.  |
   |Analisar imagem com modelo de domínio | Usa a operação [Listar modelos específicos de domínio](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) para listar os modelos de domínio a partir dos quais você pode selecionar e a operação [Reconhecer conteúdo específico de domínio](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) para analisar uma imagem local ou remota usando o modelo de domínio selecionado. Você também pode escolher o idioma para a análise. |
   |Descrever a imagem | Usa a operação [Descrever imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) para criar uma descrição legível por humanos de uma imagem local ou remota. Você também pode escolher o idioma para a descrição. |
   |Gerar marcações | Usa a operação [Tag Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) para marcar os recursos visuais de uma imagem local ou remota. Você também pode escolher o idioma usado para as tags. |
   |Reconhecer texto (OCR) | Usa a operação [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para reconhecer e extrair texto impresso de uma imagem. Você pode escolher o idioma a ser usado ou deixe computacional detectar automaticamente o idioma. |
   |Reconhecer texto V2 (em inglês) | Usa o [reconhecer texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) e [obter resultado da operação de reconhecer texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) operações para reconhecer e extrair texto impresso ou escrito à mão de uma imagem de forma assíncrona. |
   |Obter a miniatura | Usa o [miniatura obter](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) operação para gerar uma miniatura de uma imagem local ou remota. |

   A captura de tela a seguir ilustra a página fornecida para o cenário Analyze Image, após analisar uma imagem de amostra.
   ![Captura de tela da página de imagem Analisar](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Explorar o aplicativo de exemplo

A solução Visual Studio para o aplicativo de amostra do Computer Vision contém dois projetos:

* SampleUserControlLibrary  
  O projeto SampleUserControlLibrary fornece funcionalidade compartilhada por vários exemplos de Serviços Cognitivos. O projeto contém o seguinte:
  * SampleScenarios  
    Um UserControl que fornece uma apresentação padronizada, como a barra de título, o painel de navegação e o painel de conteúdo, para amostras. O aplicativo de amostra do Computer Vision usa esse controle na janela MainWindow.xaml para exibir páginas de cenário e acessar informações compartilhadas entre cenários, como a chave de assinatura e o URL do terminal.
  * SubscriptionKeyPage  
    Uma Página que fornece um layout padronizado para inserir uma chave de assinatura e um URL de terminal para o aplicativo de amostra. O aplicativo de amostra do Computer Vision usa essa página para gerenciar a chave de assinatura e o URL do ponto de extremidade usados pelas páginas do cenário.
  * VideoResultControl  
    Um UserControl que fornece uma apresentação padronizada para informações de vídeo. O aplicativo de amostra do Computer Vision não usa esse controle.
* Exemplos de WPF VisionAPI  
  O principal projeto para o aplicativo de amostra do Computer Vision, este projeto contém todas as funcionalidades interessantes para o Computer Vision. O projeto contém o seguinte:
  * AnalyzeInDomainPage.xaml  
    A página do cenário para o cenário Analisar imagem com modelo de domínio.
  * AnalyzeImage.xaml  
    A página do cenário para o cenário Analyze Image.
  * DescribePage.xaml  
    A página do cenário para o cenário Descrever imagem.
  * ImageScenarioPage.cs  
    A classe ImageScenarioPage, da qual todas as páginas do cenário no aplicativo de amostra são derivadas. Essa classe gerencia a funcionalidade, como fornecer credenciais e saída de formatação, compartilhada por todas as páginas do cenário.
  * MainWindow.xaml  
    A janela principal do aplicativo de amostra usa o controle SampleScenarios para apresentar as páginas SubscriptionKeyPage e scenario.
  * OCRPage.xaml  
    A página do cenário para o cenário Reconhecer Texto (OCR).
  * RecognizeLanguage.cs  
    A classe RecognizeLanguage, que fornece informações sobre os idiomas suportados pelos vários métodos no aplicativo de amostra.
  * TagsPage.xaml  
    A página do cenário para o cenário Gerar Tags.
  * TextRecognitionPage.xaml  
    A página do cenário para o cenário Reconhecer texto V2 (inglês).
  * ThumbnailPage.xaml  
    A página do cenário para o cenário Get Thumbnail.

### <a name="explore-the-sample-code"></a>Explorar o código de exemplo

As partes principais do código de amostra são enquadradas com blocos de comentários que começam com `KEY SAMPLE CODE STARTS HERE` e terminam com `KEY SAMPLE CODE ENDS HERE`, para facilitar a exploração do aplicativo de amostra. Essas partes principais do código de amostra contêm o código mais relevante para aprender a usar a biblioteca cliente da API do Computer Vision para executar várias tarefas. Você pode pesquisar por `KEY SAMPLE CODE STARTS HERE` no Visual Studio para mover-se entre as seções mais relevantes do código no aplicativo de exemplo do Computer Vision. 

Por exemplo, o método `UploadAndAnalyzeImageAsync`, mostrado a seguir e incluído em AnalyzePage.xaml, demonstra como usar a biblioteca cliente para analisar uma imagem local chamando o método `ComputerVisionClient.AnalyzeImageInStreamAsync`.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Explore a biblioteca de cliente

Este aplicativo de amostra usa a biblioteca de cliente da API do Computer Vision, um wrapper de cliente C# fino para a API do Computer Vision no Azure Cognitive Services. A biblioteca do cliente está disponível no NuGet no pacote [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/). Quando você criou o aplicativo Visual Studio, recuperou a biblioteca cliente de seu pacote NuGet correspondente. Você também pode visualizar o código-fonte da biblioteca do cliente na pasta do `/ClientLibrary`repositório`Microsoft/Cognitive-Vision-Windows`.

Funcionalidade da biblioteca de cliente gira em torno de `ComputerVisionClient` classe, o `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` namespace, enquanto os modelos usados pelo `ComputerVisionClient` classe ao interagir com a visão do computador são encontrados no `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` namespace. Nas várias páginas de cenário XAML incluídas no aplicativo de exemplo, você encontrará as seguintes diretrizes `using` para esses namespaces:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Você aprenderá mais sobre os vários métodos incluídos na classe `ComputerVisionClient` ao explorar os cenários incluídos no aplicativo de amostra do Computer Vision.

## <a name="explore-the-analyze-image-scenario"></a>Explore o cenário de analisar imagem

Este cenário é gerenciado pela página AnalyzePage.xaml. Você pode escolher os recursos visuais e a linguagem para a análise e ver a imagem e os resultados. A página de cenário faz isso usando um dos seguintes métodos, dependendo da origem da imagem:

* UploadAndAnalyzeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como `Stream` e enviada para o Computer Vision chamando o método `ComputerVisionClient.AnalyzeImageInStreamAsync`.
* AnalyzeUrlAsync  
  Esse método é usado para imagens remotas, em que a URL da imagem é enviada para o Computer Vision chamando o método `ComputerVisionClient.AnalyzeImageAsync`.

O método `UploadAndAnalyzeImageAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Como o aplicativo de amostra está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para o Computer Vision. Ele abre o arquivo local especificado em `imageFilePath` para leitura como `Stream`, depois obtém os recursos visuais e o idioma selecionados na página do cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageInStreamAsync`, passando o `Stream` para o arquivo, os recursos visuais e o idioma e, em seguida, retorna o resultado como uma instância `ImageAnalysis`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

O método `AnalyzeUrlAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Obtém os recursos visuais e o idioma selecionados na página do cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageInStreamAsync`, passando o URL da imagem, os recursos visuais e o idioma e retorna o resultado como uma instância `ImageAnalysis`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Explore o cenário Analisar imagem com o modelo de domínio

Este cenário é gerenciado pela página AnalyzeInDomainPage.xaml. Você pode escolher um modelo de domínio, como `celebrities` ou `landmarks`, e idioma para realizar uma análise específica do domínio da imagem e ver a imagem e os resultados. A página do cenário usa os seguintes métodos, dependendo da origem da imagem:

* GetAvailableDomainModelsAsync  
  Esse método obtém a lista de modelos de domínio disponíveis do Computer Vision e preenche o controle `_domainModelComboBox` ComboBox na página, usando o método `ComputerVisionClient.ListModelsAsync`.
* UploadAndAnalyzeInDomainImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como `Stream` e enviada para o Computer Vision chamando o método `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`.
* AnalyzeInDomainUrlAsync  
  Esse método é usado para imagens remotas, em que a URL da imagem é enviada para o Computer Vision chamando o método `ComputerVisionClient.AnalyzeImageByDomainAsync`.

O método `UploadAndAnalyzeInDomainImageAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Como o aplicativo de amostra está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para o Computer Vision. Ele abre o arquivo local especificado em `imageFilePath` para leitura como `Stream`, depois obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`, passando o `Stream` para o arquivo, o nome do modelo de domínio e o idioma e retorna o resultado como uma instância `DomainModelResults`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

O método `AnalyzeInDomainUrlAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageByDomainAsync`, passando o URL da imagem, os recursos visuais e o idioma e retorna o resultado como uma instância `DomainModelResults`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

## <a name="explore-the-describe-image-scenario"></a>Explore o cenário Descrever Imagem

Este cenário é gerenciado pela página DescribePage.xaml. Você pode escolher um idioma para criar uma descrição legível da imagem e ver a imagem e os resultados. A página do cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndDescribeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como `Stream` e enviada para o Computer Vision chamando o método `ComputerVisionClient.DescribeImageInStreamAsync`.
* DescribeUrlAsync  
  Esse método é usado para imagens remotas, em que a URL da imagem é enviada para o Computer Vision chamando o método `ComputerVisionClient.DescribeImageAsync`.

O método `UploadAndDescribeImageAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Como o aplicativo de amostra está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para o Computer Vision. Ele abre o arquivo local especificado em `imageFilePath` para leitura como `Stream`, depois obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.DescribeImageInStreamAsync`, passando o `Stream` para o arquivo, o número máximo de candidatos (neste caso, 3) e o idioma e retorna o resultado como uma instância `ImageDescription`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

O método `DescribeUrlAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.DescribeImageAsync`, passando o URL da imagem, o número máximo de candidatos (neste caso, 3) e o idioma e retorna o resultado como uma instância `ImageDescription`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

## <a name="explore-the-generate-tags-scenario"></a>Explore o cenário Gerar tags

Este cenário é gerenciado pela página TagsPage.xaml. Você pode escolher um idioma para marcar os recursos visuais de uma imagem e ver a imagem e os resultados. A página do cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndGetTagsForImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como `Stream` e enviada para o Computer Vision chamando o método `ComputerVisionClient.TagImageInStreamAsync`.
* GenerateTagsForUrlAsync  
  Esse método é usado para imagens remotas, em que a URL da imagem é enviada para o Computer Vision chamando o método `ComputerVisionClient.TagImageAsync`.

O método `UploadAndGetTagsForImageAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Como o aplicativo de amostra está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para o Computer Vision. Ele abre o arquivo local especificado em `imageFilePath` para leitura como `Stream`, depois obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.TagImageInStreamAsync`, passando o `Stream` para o arquivo e o idioma e retorna o resultado como uma instância `TagResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

O método `GenerateTagsForUrlAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.TagImageAsync`, passando o URL da imagem e o idioma e retorna o resultado como uma instância `TagResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Explore o cenário Recognize Text (OCR)

Este cenário é gerenciado pela página OCRPage.xaml. Você pode escolher um idioma para reconhecer e extrair texto impresso de uma imagem e ver a imagem e os resultados. A página do cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como `Stream` e enviada para o Computer Vision chamando o método `ComputerVisionClient.RecognizePrintedTextInStreamAsync`.
* RecognizeUrlAsync  
  Esse método é usado para imagens remotas, em que a URL da imagem é enviada para o Computer Vision chamando o método `ComputerVisionClient.RecognizePrintedTextAsync`.

O método `UploadAndRecognizeImageAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Como o aplicativo de amostra está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para o Computer Vision. Ele abre o arquivo local especificado em `imageFilePath` para leitura como `Stream`, depois obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.RecognizePrintedTextInStreamAsync`, indicando que a orientação não é detectada e passando o `Stream` para o arquivo e o idioma e retorna o resultado como uma instância `OcrResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

O método `RecognizeUrlAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Obtém o idioma selecionado na página do cenário. Ele chama o método `ComputerVisionClient.RecognizePrintedTextAsync`, indicando que a orientação não é detectada e passando o URL da imagem e o idioma e retorna o resultado como uma instância `OcrResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Explore o cenário Recognize Text V2 (inglês)

Este cenário é gerenciado pela página TextRecognitionPage.xaml. Você pode escolher o modo de reconhecimento e um idioma para reconhecer e extrair de forma assíncrona texto impresso ou manuscrito de uma imagem e ver a imagem e os resultados. A página do cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como `Stream` e enviada para o Computer Vision chamando o método `RecognizeAsync` e passando um delegado parametrizado para o método `ComputerVisionClient.RecognizeTextInStreamAsync`.
* RecognizeUrlAsync  
  Esse método é usado para imagens remotas, em que a URL da imagem é enviada para o Computer Vision chamando o método `RecognizeAsync` e passando um delegado parametrizado para o método `ComputerVisionClient.RecognizeTextAsync`.
* RecognizeAsync Esse método manipula a chamada assíncrona para os métodos `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync`, além de pesquisar resultados chamando o método `ComputerVisionClient.GetTextOperationResultAsync`.

Ao contrário dos outros cenários incluídos no aplicativo de amostra do Computer Vision, esse cenário é assíncrono, em que um método é chamado para iniciar o processo, mas um método diferente é chamado para verificar o status e retornar os resultados desse processo. O fluxo lógico neste cenário é um pouco diferente dos outros cenários.

O método `UploadAndRecognizeImageAsync` abre o arquivo local especificado em `imageFilePath` para leitura como `Stream` e, em seguida, chama o método `RecognizeAsync`, passando:

* Uma expressão lambda para um delegado assíncrono parametrizado do método `ComputerVisionClient.RecognizeTextInStreamAsync`, com `Stream` para o arquivo e o modo de reconhecimento como parâmetros, em `GetHeadersAsyncFunc`.
* Uma expressão lambda para um delegado para obter o valor do cabeçalho de resposta `Operation-Location`, em `GetOperationUrlFunc`.

O método `RecognizeUrlAsync` chama o método `RecognizeAsync`, passando:

* Uma expressão lambda para um delegado assíncrono parametrizado do método `ComputerVisionClient.RecognizeTextAsync`, com o URL da imagem remota e o modo de reconhecimento como parâmetros, em `GetHeadersAsyncFunc`.
* Uma expressão lambda para um delegado para obter o valor do cabeçalho de resposta `Operation-Location`, em `GetOperationUrlFunc`.

Quando o método `RecognizeAsync` é concluído, os métodos `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync` retornam o resultado como uma instância `TextOperationResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

O método `RecognizeAsync` chama o delegado parametrizado para o método `ComputerVisionClient.RecognizeTextInStreamAsync` ou `ComputerVisionClient.RecognizeTextAsync` transmitido em `GetHeadersAsyncFunc` e aguarda a resposta. O método chama o delegado passado em `GetOperationUrlFunc` para obter o valor do cabeçalho de resposta `Operation-Location` da resposta. Esse valor é o URL usado para recuperar os resultados do método transmitido em `GetHeadersAsyncFunc` do Computer Vision.

O método `RecognizeAsync` chama o método `ComputerVisionClient.GetTextOperationResultAsync`, passando o URL recuperado do cabeçalho de resposta `Operation-Location`, para obter o status e o resultado do método passado em `GetHeadersAsyncFunc`. Se o status não indicar que o método foi concluído, com êxito ou sem êxito, o método `RecognizeAsync` chama `ComputerVisionClient.GetTextOperationResultAsync` mais 3 vezes, aguardando 3 segundos entre as chamadas. O método `RecognizeAsync` retorna os resultados para o método que o chamou.

## <a name="explore-the-get-thumbnail-scenario"></a>Explore o cenário Get Thumbnail

Este cenário é gerenciado pela página ThumbnailPage.xaml. Você pode indicar se deseja usar o recorte inteligente e especificar a altura e a largura desejadas para gerar uma miniatura a partir de uma imagem e ver a imagem e os resultados. A página do cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndThumbnailImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como `Stream` e enviada para o Computer Vision chamando o método `ComputerVisionClient.GenerateThumbnailInStreamAsync`.
* ThumbnailUrlAsync  
  Esse método é usado para imagens remotas, em que a URL da imagem é enviada para o Computer Vision chamando o método `ComputerVisionClient.GenerateThumbnailAsync`.

O método `UploadAndThumbnailImageAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Como o aplicativo de amostra está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para o Computer Vision. Ele abre o arquivo local especificado em `imageFilePath` para leitura como `Stream`. Ele chama o método `ComputerVisionClient.GenerateThumbnailInStreamAsync`, passando a largura, a altura, o `Stream` para o arquivo e se deseja usar o corte inteligente e retorna o resultado como um `Stream`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

O método `RecognizeUrlAsync` cria uma nova instância `ComputerVisionClient`, usando a chave de assinatura e o URL do terminal especificados. Ele chama o método `ComputerVisionClient.GenerateThumbnailAsync`, passando a largura, a altura, a URL da imagem e se deseja usar o recorte inteligente e retorna o resultado como `Stream`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página do cenário.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua a pasta na qual você clonou o repositório `Microsoft/Cognitive-Vision-Windows`. Se você optou por usar as imagens de amostra, exclua também a pasta na qual você clonou o repositório `Microsoft/Cognitive-Face-Windows`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução à serviço de Detecção Facial](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)

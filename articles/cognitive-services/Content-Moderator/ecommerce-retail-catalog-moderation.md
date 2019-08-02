---
title: 'Tutorial: Moderar imagens de produto de comércio eletrônico – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Configure um aplicativo para analisar e classificar imagens de produtos com rótulos especificados (usando a Pesquisa Visual Computacional e a Visão Personalizada do Azure). Marque imagens indesejáveis para serem examinadas mais detalhadamente (usando o Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: b118a509f72af2146abf854b881fa34d8de302a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564929"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Tutorial: Moderar imagens de produto de comércio eletrônico com o Azure Content Moderator

Neste tutorial, você aprenderá a usar os Serviços Cognitivos do Azure, incluindo o Content Moderator, para classificar e moderar imagens de produtos em um cenário de comércio eletrônico. Você usará a Pesquisa Visual Computacional e a Visão Personalizada para aplicar marcas (rótulos) a imagens e, em seguida, criará uma análise de equipe, que combina as tecnologias baseadas em aprendizado de máquina do Content Moderator com as equipes de análise humana para fornecer um sistema de moderação inteligente.

Este tutorial mostra como:

> [!div class="checklist"]
> * Inscrever-se no Content Moderator e criar uma equipe de análise.
> * Use o API de imagem do Content Moderator para verificar possível conteúdo adulto e obsceno.
> * Usar o serviço de Pesquisa Visual Computacional para examinar se há algum conteúdo com celebridades (ou outras marcas detectáveis pela Pesquisa Visual Computacional).
> * Usar o Serviço de Visão Personalizada para examinar a presença de bandeiras, brinquedos e canetas (ou outras marcas personalizadas).
> * Apresentar os resultados do exame combinados para revisão humana e tomada de decisão final.

O código de exemplo completo está disponível no repositório [Amostras de Moderação de Catálogo de Comércio Eletrônico](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) no GitHub.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura do Content Moderator. Siga as instruções descritas em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço Content Moderator e obter sua chave.
- Uma chave de assinatura da Pesquisa Visual Computacional (mesmas instruções acima).
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- Um conjunto de imagens para cada rótulo que será usado pelo classificador da Visão Personalizada (nesse caso, brinquedos, canetas e bandeiras dos EUA).

## <a name="create-a-review-team"></a>Criar uma equipe de análise

Veja o início rápido [Familiarize-se com o Content Moderator](quick-start.md) para obter instruções sobre como se inscrever na [ferramenta de Análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipe de análise. Anote o valor da **ID da Equipe** na página **Credenciais**.

## <a name="create-custom-moderation-tags"></a>Criar marcas de moderação personalizadas

Em seguida, crie marcas personalizadas na ferramenta de Análise (confira o artigo [Marcas](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) caso precise obter ajuda com esse processo). Nesse caso, adicionaremos as seguintes marcas: **celebridade**, **EUA**, **bandeira**, **brinquedo** e **caneta**. Nem todas as marcas precisam ser categorias detectáveis na Pesquisa Visual Computacional (como **celebridade**); você pode adicionar suas próprias marcas personalizadas, desde que treine o classificador da Visão Personalizada para detectá-las mais tarde.

![Configurar marcas personalizadas](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Criar um projeto do Visual Studio

1. No Visual Studio, abra o diálogo Novo Projeto. Expanda **Instalado**, **Visual C#** e, em seguida, selecione **Aplicativo de console (.NET Framework)** .
1. Nomeie o aplicativo **EcommerceModeration** e, em seguida, clique em **OK**.
1. Se estiver adicionando esse projeto a uma solução existente, selecione esse projeto como o único projeto de inicialização.

Este tutorial realça o código fundamental para o projeto, mas não abordará todas as linhas de código. Copie o conteúdo completo de _Program.cs_ no projeto de exemplo ([Amostras de Moderação de Catálogo de Comércio Eletrônico](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) para o arquivo _Program.cs_ do novo projeto. Em seguida, siga as próximas seções para saber mais sobre como funciona o projeto e como usá-lo por conta própria.

## <a name="define-api-keys-and-endpoints"></a>Definir chaves de API e pontos de extremidade

Este tutorial usa três serviços cognitivos; portanto, exige três chaves e pontos de extremidade de API correspondentes. Confira os seguintes campos na classe **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Você precisará atualizar os campos `___Key` com os valores de suas chaves de assinatura (você obterá a `CustomVisionKey` posteriormente) e talvez precise alterar os campos `___Uri`, de modo que eles contenham os identificadores de região corretos. Preencha a parte `YOURTEAMID` do campo `ReviewUri` com a ID da equipe de análise criada anteriormente. Você preencherá a parte final do campo `CustomVisionUri` mais tarde.

## <a name="primary-method-calls"></a>Principais chamadas de método

Confira o código a seguir no método **Main**, que executa um loop por uma lista de URLs de imagem. Ele analisa cada imagem com os três serviços diferentes, registra as marcas aplicadas na matriz **ReviewTags** e, em seguida, cria uma análise para moderadores humanos enviando as imagens para a Ferramenta de Análise do Content Moderator. Você explorará esses métodos nas seções a seguir. Se desejar, você poderá controlar quais imagens são enviadas para análise, usando a matriz **ReviewTags** em uma instrução condicional para verificar quais marcas foram aplicadas.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Método EvaluateAdultRacy

Confira o método **EvaluateAdultRacy** na classe **Program**. Esse método usa uma URL de imagem e uma matriz de pares chave-valor como parâmetros. Ele chama uma API de Imagem do Content Moderator (usando a REST) para obter as pontuações de conteúdo para Adulto e conteúdo Sexual da imagem. Se a pontuação for maior que 0,4 (o intervalo é entre 0 e 1), ela definirá o valor correspondente na matriz **ReviewTags** como **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Método EvaluateComputerVisionTags

O próximo método usa uma URL de imagem e suas informações de assinatura da Pesquisa Visual Computacional e analisa na imagem a presença de celebridades. Se uma ou mais celebridades forem encontradas, ele definirá o valor correspondente na matriz **ReviewTags** como **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Método EvaluateCustomVisionTags

Em seguida, confira o método **EvaluateCustomVisionTags**, que classifica os produtos reais – nesse caso, bandeiras, brinquedos e canetas. Siga as instruções do guia [Como criar um classificador](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) para criar seu próprio classificador de imagens personalizado e detectar bandeiras, brinquedos e canetas (ou qualquer coisa que você escolha como marcas personalizadas) nas imagens. Você pode usar as imagens na pasta **sample-images** do [repositório GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) para treinar rapidamente algumas das categorias neste exemplo.

![Página da Web da Visão Personalizada com imagens de treinamento de canetas, brinquedos e bandeiras](images/tutorial-ecommerce-custom-vision.PNG)

Depois de treinar o classificador, obtenha a chave de previsão e a URL de ponto de extremidade de previsão (confira [Obter a URL e a chave de previsão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) caso precise de ajuda para recuperá-las) e atribua esses valores aos campos `CustomVisionKey` e `CustomVisionUri`, respectivamente. O método usa esses valores para consultar o classificador. Se o classificador encontrar uma ou mais das marcas personalizadas na imagem, esse método definirá os valores correspondentes na matriz **ReviewTags** como **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Criar análises para a ferramenta de Análise

Nas seções anteriores, você explorou como o aplicativo examina as imagens recebidas para encontrar conteúdo erótico ou para adulto (Content Moderator), celebridades (Pesquisa Visual Computacional) e vários outros objetos (Visão Personalizada). Em seguida, confira o método **CreateReview**, que carrega as imagens com todas as suas tags aplicadas (passadas como _Metadata_) na Ferramenta de Análise do Content Moderator.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

As imagens serão exibidas na guia Análise da [ferramenta de Análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Captura de tela da ferramenta de Análise do Content Moderator com várias imagens e suas marcas realçadas](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Enviar uma lista de imagens de teste

Como você pode ver no método **Main**, esse programa procura um diretório "C:Test" com um arquivo _Urls.txt_ que contém uma lista de URLs de imagem. Crie esse arquivo e esse diretório ou altere o caminho para que ele aponte para o arquivo de texto. Em seguida, popule esse arquivo com as URLs de imagens que deseja testar.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Execute o programa

Se você seguir todas as etapas acima, o programa deverá processar cada imagem (consultando as marcas relevantes nos três serviços) e, em seguida, fazer upload das imagens com as informações de tag na Ferramenta de Análise do Content Moderator.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurará um programa para analisar imagens de produtos, marcará essas imagens por tipo de produto e permitirá que uma equipe de análise tome decisões informadas sobre a moderação de conteúdo. Em seguida, saiba mais sobre os detalhes da moderação de imagem.

> [!div class="nextstepaction"]
> [Examinar imagens moderadas](./review-tool-user-guide/review-moderated-images.md)

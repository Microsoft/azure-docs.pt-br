---
title: Usar um modelo ONNX com Windows ML – Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como criar um aplicativo UWP do Windows que usa um modelo ONNX exportado dos Serviços Cognitivos do Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616069"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Usar um modelo ONNX do Serviço de Visão Personalizada com Windows ML (versão prévia)

Saiba como usar o modelo exportado ONNX do Serviço de Visão Personalizada com Windows ML (versão prévia).

Neste guia, você aprenderá a usar um arquivo ONNX exportado do Serviço de Visão Personalizada com o Windows ML. Você usará o aplicativo de exemplo UWP com seu próprio classificador de imagem treinado.

## <a name="prerequisites"></a>Pré-requisitos

* Windows 10 versão 1809 ou superior
* SDK do Windows build 17763 ou superior
* Visual Studio 2017 versão 15.7 ou posterior com a carga de trabalho __Desenvolvimento com a Plataforma Universal do Windows__ habilitada.
* Modo de desenvolvedor habilitado em seu PC. Para obter mais informações, consulte [habilitar seu dispositivo para desenvolvimento](/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Sobre o aplicativo de exemplo

O aplicativo incluído é um aplicativo UWP genérico do Windows. Ele permite que você selecione uma imagem do seu computador, que é processada por um modelo de classificação armazenado localmente. As marcações e as pontuações retornadas pelo modelo são exibidas ao lado da imagem.

## <a name="get-the-example-code"></a>Obter o código de exemplo

O aplicativo de exemplo está disponível no [ONNX de serviços cognitivas visão personalizada](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) repositório de exemplo no github. Clone-o no computador local e abra *SampleOnnxEvaluationApp. sln* no Visual Studio.

## <a name="test-the-application"></a>Testar o aplicativo

1. Use a tecla `F5` para iniciar o aplicativo do Visual Studio. É possível que seja solicitado que você habilite o Modo de Desenvolvedor.
1. Quando o aplicativo for iniciado, use o botão para selecionar uma imagem para pontuação. O modelo ONNX padrão é treinado para classificar diferentes tipos de Plankton.

## <a name="use-your-own-model"></a>Usar seu próprio modelo

Para usar seu próprio modelo classificador de imagem, siga estas etapas:

1. Crie e treine um classificador com o Serviço de Visão Personalizada. Para obter instruções sobre como fazer isso, consulte [criar e treinar um classificador](./getting-started-build-a-classifier.md). Use um dos domínios do **Compact** , como **geral (compacto)**. 
   * Se você tiver um classificador existente que usa um domínio diferente, poderá convertê-lo para **compactar** as configurações do projeto. Em seguida, treine novamente o seu projeto antes de continuar.
1. Exporte seu modelo. Alterne para a guia desempenho e selecione uma iteração que foi treinada com um domínio **compacto** . Selecione o botão **Exportar** que aparece. Em seguida, selecione **ONNX** e **Exportar**. Depois que o arquivo estiver pronto, selecione o botão **Baixar**. Para obter mais informações sobre opções de exportação, consulte [exportar seu modelo](./export-your-model.md).
1. Abra o arquivo *. zip* baixado e extraia o arquivo *Model. onnx* dele. Este arquivo contém o modelo de classificador.
1. Na Gerenciador de Soluções no Visual Studio, clique com o botão direito do mouse na pasta **ativos** e selecione __Adicionar item existente__. Selecione o arquivo ONNX.
1. Em Gerenciador de Soluções, clique com o botão direito do mouse no arquivo ONNX e selecione **Propriedades**. Altere as seguintes propriedades para o arquivo:
   * __Ação__  ->  de Build __Conteúdo__ do
   * __Copiar para diretório__  ->  de saída __Copiar se mais recente__
1. Em seguida, abra _MainPage. XAML. cs_ e altere o valor de `_ourOnnxFileName` para o nome do arquivo ONNX.
1. Use o `F5` para compilar e executar o projeto.
1. Clique no botão para selecionar a imagem a ser avaliada.

## <a name="next-steps"></a>Próximas etapas

Para descobrir outras maneiras de exportar e usar um modelo do Serviço de Visão Personalizada, confira os seguintes documentos:

* [Exportar seu modelo](./export-your-model.md)
* [Usar o modelo Tensorflow exportado em um aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Usar o modelo CoreML exportado em um aplicativo iOS Swift](https://go.microsoft.com/fwlink/?linkid=857726)
* [Usar o modelo CoreML exportado em um aplicativo iOS com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Para obter mais informações sobre como usar modelos ONNX com o Windows ML, consulte [integrar um modelo em seu aplicativo com o Windows ml](/windows/ai/windows-ml/integrate-model).
---
title: 'Tutorial: Usar um modelo ONNX com Windows ML – Serviço de Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Saiba como criar um aplicativo UWP do Windows que usa um modelo ONNX exportado dos Serviços Cognitivos do Azure.
services: cognitive-services
author: larryfr
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: larryfr
ms.openlocfilehash: 65956ab9fa182794b4cb9ac1fc1800198f2dcdaa
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561116"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Tutorial: Usar um modelo ONNX do Serviço de Visão Personalizada com Windows ML (versão prévia)

Saiba como usar o modelo exportado ONNX do Serviço de Visão Personalizada com Windows ML (versão prévia).

As informações neste documento demonstram como usar um arquivo ONNX exportado do Serviço de Visão Personalizada com Windows ML. Um aplicativo UWP do Windows de exemplo é fornecido. Um modelo treinado capaz de reconhecimento é incluído com o exemplo. Também são fornecidas etapas sobre como você pode usar seu próprio modelo com esse exemplo.

> [!div class="checklist"]
> * Sobre o aplicativo de exemplo
> * Obter o código de exemplo
> * Executar o exemplo
> * Usar seu próprio modelo

## <a name="prerequisites"></a>Pré-requisitos

* Windows 10 build 17738 ou superior

* SDK do Windows 10 build 17738 ou superior

* Visual Studio 2017 versão 15.7 ou posterior com a carga de trabalho __Desenvolvimento com a Plataforma Universal do Windows__ habilitada.

* Modo de desenvolvedor habilitado. Para obter mais informações, confira o documento [Habilitar seu dispositivo para desenvolvimento](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Sobre o aplicativo de exemplo

O aplicativo é um aplicativo UWP do Windows genérico. Ele permite que você selecione uma imagem do seu computador, que é enviada para o modelo. As marcações e as pontuações retornadas pelo modelo são exibidas ao lado da imagem.

## <a name="get-the-example-code"></a>Obter o código de exemplo

O aplicativo de exemplo fica disponível em [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>Executar o exemplo

1. Use a tecla `F5` para iniciar o aplicativo do Visual Studio. É possível que seja solicitado que você habilite o Modo de Desenvolvedor. Para obter mais informações, confira o documento [Habilitar seu dispositivo para desenvolvimento](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

1. Quando o aplicativo for iniciado, use o botão para selecionar uma imagem para pontuação.

## <a name="use-your-own-model"></a>Usar seu próprio modelo

Para usar seu próprio modelo, use as seguintes etapas:

1. [Crie e treine](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) um classificador com o Serviço de Visão Personalizada. Para exportar o modelo, selecione um domínio __compacto__, como **Geral (compacto)** . Para exportar um classificador existente, converta o domínio a ser compactado, selecionando o ícone de engrenagem no canto superior direito. Em __Configurações__, selecione um modelo compacto, salve e treine seu projeto.  

1. [Exporte o seu modelo](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) indo até a guia Desempenho. Selecione uma iteração treinada com um domínio compacto, um botão “Export” será exibido. Selecione *Exportar*, *ONNX*, *ONNX1.2* e, em seguida, *Exportar*. Depois que o arquivo estiver pronto, selecione o botão *Baixar*.

1. Solte o arquivo ONNX na pasta __Ativos__ do projeto. 

1. No Gerenciador de Soluções, clique com o botão direito na pasta Ativos e selecione __Adicionar item existente__. Selecione o arquivo ONNX.

1. No Gerenciador de Soluções, selecione o arquivo ONNX da pasta Ativos. Altere as seguintes propriedades para o arquivo:

    * __Ação de Compilação__ -> __Conteúdo__
    * __Copiar para Diretório de Saída__ -> __Copiar se mais recente__

1. Altere a variável `_onnxFileNames` para o nome do arquivo ONNX. Modifique também `ClassLabel` para o número de rótulos que o modelo contém.

1. Compilar e executar.

1. Clique no botão para selecionar a imagem a ser avaliada.

## <a name="next-steps"></a>Próximas etapas

Para descobrir outras maneiras de exportar e usar um modelo do Serviço de Visão Personalizada, confira os seguintes documentos:

* [Exportar seu modelo](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Usar o modelo Tensorflow exportado em um aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Usar o modelo CoreML exportado em um aplicativo iOS Swift](https://go.microsoft.com/fwlink/?linkid=857726)
* [Usar o modelo CoreML exportado em um aplicativo iOS com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Para obter mais informações sobre como usar modelos ONNX com Windows ML, confira o documento [Integrar um modelo ao aplicativo no Windows ML](/windows/ai/windows-ml/integrate-model).

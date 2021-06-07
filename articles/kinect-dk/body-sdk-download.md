---
title: Download do SDK de acompanhamento de corpo do Azure Kinect
description: Entenda como baixar cada versão do SDK do sensor de Kinect do Azure no Windows ou Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, Kinect, SDK, baixar atualização, mais recente, disponível, instalar, corpo, acompanhamento
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654485"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Baixar o SDK de acompanhamento de corpo do Azure Kinect

Este documento fornece links para instalar cada versão do SDK do controle de corpo do Azure Kinect.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Conteúdo do SDK de acompanhamento de corpo do Azure Kinect

- Cabeçalhos e bibliotecas para criar um aplicativo de acompanhamento de corpo usando o Azure Kinect DK.
- DLLs redistribuíveis necessárias para aplicativos de acompanhamento de corpo usando o Azure Kinect DK.
- Aplicativos de acompanhamento de corpo de exemplo.

## <a name="windows-download-links"></a>Links de download do Windows

Versão       | Baixar
--------------|----------
1.1.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1) do MSI
1.0.0 | [](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0) do MSI

## <a name="linux-installation-instructions"></a>Instruções de instalação para Linux

Atualmente, a única distribuição com suporte é o Ubuntu 18, 4 e 20, 4. Para solicitar compatibilidade com outras distribuições, confira [esta página](https://aka.ms/azurekinectfeedback).

Primeiro você vai precisar configurar o [Repositório de Pacotes da Microsoft](https://packages.microsoft.com/), seguindo as instruções fornecidas [aqui](/windows-server/administration/linux-package-repository-for-microsoft-software).

O pacote `libk4abt<major>.<minor>-dev` contém os cabeçalhos e os arquivos CMake que devem ser criados em relação a `libk4abt`.
O `libk4abt<major>.<minor>` pacote contém os objetos compartilhados necessários para executar executáveis que dependem `libk4abt` , bem como o Visualizador de exemplo.

Os tutoriais básicos exigem o pacote `libk4abt<major>.<minor>-dev`. Para instalar, execute

`sudo apt install libk4abt<major>.<minor>-dev`

Se o comando for executado com sucesso, o SDK estará pronto para uso.

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho de instalação. Por exemplo, "C:\Arquivos de Programas\azure Kinect corpo Tracking SDK 1.0.0". Você encontrará os exemplos referenciados nos artigos neste caminho.
> Os exemplos de acompanhamento de corpo estão localizados na pasta [Body-Tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) no repositório Azure-Kinect-Samples. Você encontrará os exemplos referenciados nos artigos aqui.

## <a name="change-log"></a>Log de alterações

### <a name="v110"></a>v 1.1.0
* Recurso Adicionar suporte para DirectML (somente Windows) e execução de TensorRT do modelo de estimativa de pose. Consulte as perguntas frequentes sobre novos ambientes de execução.
* Recurso Adicionar `model_path` ao `k4abt_tracker_configuration_t` struct. Permite que os usuários especifiquem o caminho para o modelo de estimativa de pose. O padrão é o `dnn_model_2_0_op11.onnx` modelo de estimativa de pose padrão localizado no diretório atual.
* Recurso Incluir `dnn_model_2_0_lite_op11.onnx` modelo de estimativa de pose Lite. Esse modelo compensa aproximadamente 2x aumento de desempenho para uma diminuição de precisão de aproximadamente 5%.
* Recurso Exemplos de compilação verificados com o Visual Studio 2019 e atualizações de exemplos para usar esta versão.
* [Alteração significativa] Atualize para o ONNX Runtime 1,6 com suporte para os ambientes de execução CPU, CUDA 11,1, DirectML (somente Windows) e TensorRT 7.2.1. Requer a atualização do driver NVIDIA para R455 ou melhor.
* [Alteração significativa] Nenhuma instalação do NuGet.
* [Correção de Bug] Adicionar suporte para o [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) de GPUS NVIDIA RTX 30xx Series
* [Correção de Bug] Adicionar suporte para AMD e o [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) de GPUs integradas Intel (somente Windows)
* [Correção de Bug] Atualizar para o [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125) do CUDA 11,1
* [Correção de Bug] Atualizar para o SDK do sensor 1.4.1 [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v 1.0.1
* [Correção de Bug] Correção do problema que o SDK falhará se estiver carregando onnxruntime.dll do caminho no Windows Build 19025 ou posterior: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Recurso Adicione o wrapper C# ao instalador MSI.
* [Correção de Bug] Correção do problema que a rotação de cabeçalho não pode ser detectada corretamente: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Correção de Bug] Correção do problema que o uso da CPU passa até 100% no computador Linux: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Amostras Adicione dois exemplos ao repositório de exemplo. O exemplo 1 demonstra como transformar os resultados do controle do corpo do espaço de profundidade para o [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)de espaço de cores; a amostra 2 demonstra como detectar o [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) do plano de chão

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurar o acompanhamento de corpo do Azure Kinect](body-sdk-setup.md)
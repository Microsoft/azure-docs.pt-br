---
title: Download do SDK de acompanhamento de corpo do Azure Kinect
description: Entenda como baixar cada versão do SDK do sensor de Kinect do Azure no Windows ou Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, baixar atualização, mais recente, disponível, instalar, corpo, acompanhamento
ms.openlocfilehash: 0ac0598d893617f341b9e1fd4d45c0c3e3f3c619
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359588"
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
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1) do MSI
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0) do MSI
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5) do MSI
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4) do MSI
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3) do MSI
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2) do MSI
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1) do MSI
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0) do MSI

## <a name="linux-installation-instructions"></a>Instruções de instalação para Linux

Atualmente, a única distribuição compatível é o Ubuntu 18.04. Para solicitar compatibilidade com outras distribuições, confira [esta página](https://aka.ms/azurekinectfeedback).

Primeiro você vai precisar configurar o [Repositório de Pacotes da Microsoft](https://packages.microsoft.com/), seguindo as instruções fornecidas [aqui](/windows-server/administration/linux-package-repository-for-microsoft-software).

O pacote `libk4abt<major>.<minor>-dev` contém os cabeçalhos e os arquivos CMake que devem ser criados em relação a `libk4abt`.
O `libk4abt<major>.<minor>` pacote contém os objetos compartilhados necessários para executar executáveis que dependem `libk4abt` , bem como o Visualizador de exemplo.

Os tutoriais básicos exigem o pacote `libk4abt<major>.<minor>-dev`. Para instalar, execute

`sudo apt install libk4abt1.0-dev`

Se o comando for executado com sucesso, o SDK estará pronto para uso.

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho de instalação. Por exemplo, "C:\Arquivos de Programas\azure Kinect corpo Tracking SDK 1.0.0". Você encontrará os exemplos referenciados nos artigos neste caminho.
> Os exemplos de acompanhamento de corpo estão localizados na pasta [Body-Tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) no repositório Azure-Kinect-Samples. Você encontrará os exemplos referenciados nos artigos aqui.

## <a name="change-log"></a>Log de alterações

### <a name="v101"></a>v 1.0.1
* [Correção de Bug] Correção do problema que o SDK falhará se estiver carregando onnxruntime.dll do caminho no Windows Build 19025 ou posterior: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Recurso Adicione o wrapper C# ao instalador MSI.
* [Correção de Bug] Correção do problema que a rotação de cabeçalho não pode ser detectada corretamente: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Correção de Bug] Correção do problema que o uso da CPU passa até 100% no computador Linux: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Amostras Adicione dois exemplos ao repositório de exemplo. O exemplo 1 demonstra como transformar os resultados do controle do corpo do espaço de profundidade para o [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)de espaço de cores; a amostra 2 demonstra como detectar o [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) do plano de chão

### <a name="v095"></a>v 0.9.5
* Recurso Suporte a C#. O wrapper do C# é fornecido no pacote NuGet.
* Recurso Suporte a vários controladores. A criação de vários rastreadores é permitida. Agora, o usuário pode criar vários rastreadores para controlar os corpos de diferentes dispositivos Kinect do Azure.
* Recurso Suporte ao processamento de vários threads para o modo de CPU. Durante a execução no modo de CPU, todos os núcleos serão usados para maximizar a velocidade.
* Recurso Adicionar `gpu_device_id` ao `k4abt_tracker_configuration_t` struct. Permitir que os usuários especifiquem um dispositivo de GPU diferente do padrão para executar o algoritmo de acompanhamento de corpo.
* [Correção de bug/alteração significativa] Corrija a digitação em um nome de conjunto. Altere o nome de conjunto de `K4ABT_JOINT_SPINE_NAVAL` para `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v 0.9.4
* Recurso Adicionar suporte a junções de mão. O SDK fornecerá informações para três junções adicionais para cada mão: mão, HANDTIP, polegar.
* Recurso Adicione o nível de confiança de previsão para cada uma das junções detectadas.
* Recurso Adicione suporte ao modo de CPU. Ao alterar o `cpu_only_mode` valor em `k4abt_tracker_configuration_t` , agora o SDK pode ser executado no modo de CPU, o que não exige que o usuário tenha uma placa gráfica poderosa.

### <a name="v093"></a>v 0.9.3
* Recurso Publique um novo modelo de DNN dnn_model_2_0. onnx, que melhora em grande parte a robustez do acompanhamento do corpo.
* Recurso Desabilite a suavização temporal por padrão. As junções rastreadas serão mais responsivas.
* Recurso Melhorar a precisão do mapa do índice de corpo.
* [Correção de Bug] Corrija o bug de que a configuração de orientação do sensor não é eficaz.
* [Correção de Bug] Altere o tipo de body_index_map de K4A_IMAGE_FORMAT_CUSTOM para K4A_IMAGE_FORMAT_CUSTOM8.
* [Problema conhecido] Dois corpos próximos podem ser mesclados a um segmento de instância única.

### <a name="v092"></a>v 0.9.2
* [Alteração significativa] Atualize para depender do mais recente 1.2.0 do SDK do sensor do Azure Kinect.
* [Alteração de API] `k4abt_tracker_create` a função começará a receber uma `k4abt_tracker_configuration_t` entrada. 
* [Alteração de API] Altere a `k4abt_frame_get_timestamp_usec` API para `k4abt_frame_get_device_timestamp_usec` que seja mais específica e consistente com o 1.2.0 do SDK do sensor.
* Recurso Permita que os usuários especifiquem a orientação de montagem do sensor ao criar o rastreador para obter resultados de controle de corpo mais precisos ao montar em diferentes ângulos.
* Recurso Forneça uma nova API `k4abt_tracker_set_temporal_smoothing` para alterar a quantidade de suavização temporal que o usuário deseja aplicar.
* Recurso Adicione o invólucro C++ K4ABT. HPP.
* Recurso Adicionar cabeçalho de definição de versão k4abtversion. h.
* [Correção de Bug] Corrija o bug que causou um uso extremamente alto da CPU.
* [Correção de Bug] Corrigir bug de falha do agente de log.

### <a name="v091"></a>v 0.9.1
* [Correção de Bug] Corrigir o vazamento de memória ao destruir o controlador
* [Correção de Bug] Melhores mensagens de erro para dependências ausentes
* [Correção de Bug] Falha sem falha ao criar uma segunda instância do rastreador
* [Correção de Bug] As variáveis de ambiente do agente agora funcionam corretamente
* Suporte para Linux

### <a name="v090"></a>v 0.9.0

* [Alteração significativa] Downgrade da dependência do SDK para CUDA 10,0 (de CUDA 10,1). O tempo de execução ONNX oficialmente suporta apenas até CUDA 10,0.
* [Alteração significativa] Alternado para o tempo de execução ONNX em vez de Tensorflow Runtime. Reduz o tempo de inicialização do primeiro quadro e o uso de memória. Ele também reduz o tamanho binário do SDK.
* [Alteração de API] Renomeado `k4abt_tracker_queue_capture()` para `k4abt_tracker_enqueue_capture()`
* [Alteração de API] `k4abt_frame_get_body()` Dividido em duas funções separadas: `k4abt_frame_get_body_skeleton()` e `k4abt_frame_get_body_id()` . Agora você pode consultar a ID do corpo sem sempre copiar a estrutura de esqueleto inteira.
* [Alteração de API]  `k4abt_frame_get_timestamp_usec()` Função adicionada para simplificar as etapas para que os usuários consultem o carimbo de data/hora do quadro do corpo.
* Melhorou a precisão de controle e a confiabilidade do algoritmo de acompanhamento do corpo

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurar o acompanhamento de corpo do Azure Kinect](body-sdk-setup.md)
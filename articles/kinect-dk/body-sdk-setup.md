---
title: Início Rápido – Configurar o acompanhamento do corpo do Azure Kinect
description: Neste guia de início rápido, você vai configurar o SDK de acompanhamento do corpo para o Azure Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, sensor, access, depth, sdk, body, tracking, joint, setup, cuda, nvidia
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277761"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Início Rápido: Configurar o acompanhamento do corpo do Azure Kinect

Este guia de início rápido explicará o processo de como obter acompanhamento do corpo em execução no Azure Kinect DK.

## <a name="system-requirements"></a>Requisitos do sistema

O SDK de Acompanhamento do Corpo requer uma GPU NVIDIA instalada no computador host. O requisito do computador host de acompanhamento do corpo recomendado é descrito na página [requisitos do sistema](system-requirements.md).

## <a name="install-software"></a>Instalar o software

### <a name="install-the-latest-nvidia-driver"></a>[Instalar o driver NVIDIA mais recente](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Baixe e instale o driver NVIDIA mais recente para sua placa gráfica. Drivers mais antigos podem não ser compatíveis com os binários CUDA redistribuídos com o SDK de acompanhamento do corpo.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Pacotes Redistribuíveis do Visual C++ para Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Baixar e instalar os Pacotes Redistribuíveis do Visual C++ para Visual Studio 2015. 

## <a name="set-up-hardware"></a>Configurar o hardware

### <a name="set-up-azure-kinect-dk"></a>[Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

Inicie o [Azure Kinect Viewer](azure-kinect-viewer.md) para verificar se o Azure Kinect DK está configurado corretamente.

## <a name="download-the-body-tracking-sdk"></a>Baixar o SDK de rastreamento de corpo
 
1. Selecione o link para [Baixar o SDK de acompanhamento do Corpo](body-sdk-download.md)
2. Instale o SDK de Acompanhamento do Corpo em seu computador.

## <a name="verify-body-tracking"></a>Verificar o acompanhamento do corpo

Inicie o **Visualizador de Acompanhamento do Corpo do Azure Kinect** para verificar se o SDK do Acompanhamento do Corpo está configurado corretamente. O visualizador é instalado com o instalador MSI do SDK. Você pode encontrá-lo no menu iniciar ou em `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe`.

Se você não tiver uma GPU suficientemente avançada e ainda quiser testar o resultado, poderá iniciar o **Visualizador de Acompanhamento do Corpo do Azure Kinect** na linha de comando por meio do seguinte comando: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Se tudo estiver configurado corretamente, será exibida uma janela com uma nuvem de ponto 3D e corpos rastreados.


![Visualizador 3D do Acompanhamento do Corpo](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Exemplos

É possível encontrar os exemplos sobre como usar o SDK de acompanhamento do corpo [aqui](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Criar seu primeiro aplicativo de acompanhamento do corpo](build-first-body-app.md)


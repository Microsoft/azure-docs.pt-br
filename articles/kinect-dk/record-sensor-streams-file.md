---
title: Início Rápido – Registrar fluxos do sensor do Azure Kinect em um arquivo
description: Neste guia de início rápido, você aprenderá a registrar fluxos de dados do SDK do Sensor em um arquivo.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azure, kinect, record, play back, reader, matroska, mkv, streams, depth, rgb, camera, color, imu, audio, sensor
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277756"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Início Rápido: Registrar fluxos do sensor do Azure Kinect em um arquivo

Este guia de início rápido fornece informações sobre como a ferramenta [gravador do Azure Kinect](azure-kinect-recorder.md) pode ser usada para registrar fluxos de dados do SDK do sensor em um arquivo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido pressupõe que:

- Você tenha o Azure Kinect DK conectado ao seu computador host e ativado corretamente.
- Você tenha concluído a [configuração](set-up-azure-kinect-dk.md) do hardware.

## <a name="create-recording"></a>Criar gravação

1. Abra um prompt de comando e forneça o caminho para o [gravador do Azure Kinect](azure-kinect-recorder.md), localizado no diretório de ferramentas instalado como `k4arecorder.exe`. Por exemplo: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Registre 5 segundos.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. Por padrão, o gravador usa o modo de profundidade NFOV Unbinned e gera a 1080p RGB a 30 fps, incluindo dados IMU. Para obter uma visão geral completa de opções e dicas de gravação, veja o [gravador do Azure Kinect](azure-kinect-recorder.md).

## <a name="play-back-recording"></a>Reproduzir gravação

Você pode usar o [Azure Kinect Viewer](azure-kinect-viewer.md) para reproduzir uma gravação.

1. Iniciar [`k4aviewer.exe`](azure-kinect-viewer.md)
2. Exiba a guia **Abrir a Gravação** e abra sua gravação.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a registrar fluxos de sensor em um arquivo, é hora de

> [!div class="nextstepaction"]
> [Criar seu primeiro aplicativo do Azure Kinect](build-first-app.md)

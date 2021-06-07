---
title: Gravador do Azure Kinect DK
description: Entenda como registrar fluxos de dados do SDK do sensor em um arquivo usando o gravador de Kinect do Azure.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, registro, reprodução, leitor, Matroska, MKV, fluxos, profundidade, RGB, câmera, cor, IMU, áudio
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85276349"
---
# <a name="azure-kinect-dk-recorder"></a>Gravador do Azure Kinect DK

Este artigo aborda como você pode usar o `k4arecorder` Utilitário de linha de comando para registrar fluxos de dados do SDK do sensor em um arquivo.

>[!NOTE]
>O gravador Kinect do Azure não grava áudio.

## <a name="recorder-options"></a>Opções do gravador

O `k4arecorder` tem vários argumentos de linha de comando para especificar o arquivo de saída e os modos de gravação.

As gravações são armazenadas no [formato Matroska. mkv](record-file-format.md). A gravação usa várias faixas de vídeo para cor e profundidade e também informações adicionais, como a calibragem da câmera e os metadados.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Arquivos de registro

Exemplo 1. Gravar o modo 640x576 (profundidade NFOV unbinned), RGB 1080p a 30 fps com IMU.
Pressione as teclas **Ctrl-C** para parar a gravação.

```
k4arecorder.exe output.mkv
```

Exemplo 2. Registre WFOV não compartimentalizados (1MP), RGB 3072p a 15 fps sem IMU, por 10 segundos.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Exemplo 3. Grave WFOV 2x2 compartimentalizados a 30 fps por 5 segundos e salve em Output. mkv.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>Você pode usar o [Azure Kinect Viewer](azure-kinect-viewer.md) para configurar controles de câmera RGB antes da gravação (por exemplo, para definir o equilíbrio de branco manual).

## <a name="verify-recording"></a>Verificar gravação

Você pode abrir o arquivo output. mkv com o [Visualizador de Kinect do Azure](azure-kinect-viewer.md).

Para extrair faixas ou exibir informações do arquivo, ferramentas como `mkvinfo` o estão disponíveis como parte do kit de ferramentas do [MKVToolNix](https://mkvtoolnix.download/) .

## <a name="next-steps"></a>Próximas etapas

[Usando o gravador com unidades sincronizadas externas](record-external-synchronized-units.md)
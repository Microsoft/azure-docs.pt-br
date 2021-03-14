---
title: Capturar a sincronização do dispositivo Kinect do Azure
description: Saiba como sincronizar dispositivos do Azure Kinect Capture usando o SDK do sensor Kinect do Azure.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, profundidade, RGB, interno, externo, sincronização, Cadeia de Margarida, deslocamento de fase
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034295"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Capturar a sincronização do dispositivo Kinect do Azure

O hardware Kinect do Azure pode alinhar o tempo de captura das imagens de cores e de profundidade. O alinhamento entre as câmeras no mesmo dispositivo é a **sincronização interna**. O alinhamento de tempo de captura em vários dispositivos conectados é a **sincronização externa**. A matriz de microfone funciona independentemente das câmeras de cor e profundidade.

## <a name="device-internal-synchronization"></a>Sincronização interna do dispositivo

A captura de imagem entre as câmeras individuais é sincronizada no hardware. Em cada [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) que contém imagens do sensor de cor e de profundidade, os carimbos de data/hora das imagens são alinhados com base no modo de operação do hardware. Por padrão, as imagens de uma captura são alinhadas ao centro de exposição. A temporização relativa de profundidade e capturas de cor pode ser ajustada usando o `depth_delay_off_color_usec` campo de [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

## <a name="device-external-synchronization"></a>Sincronização externa do dispositivo

Consulte [Configurar sincronização externa](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) para configuração de hardware.

O software para cada dispositivo conectado deve ser configurado para operar em modo **mestre** ou **subordinado** . Essa configuração é definida no [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

Ao usar a sincronização externa, as câmeras subordinadas sempre devem ser iniciadas antes do mestre para que os carimbos de data/hora sejam alinhados corretamente.

### <a name="subordinate-mode"></a>Modo subordinado

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Modo mestre

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Recuperando o estado da tomada de sincronização

Para recuperar programaticamente o estado atual dos conectores de entrada e de saída de sincronização, use a função [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) .

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como habilitar e capturar a sincronização do dispositivo. Você também pode examinar como usar o 

>[!div class="nextstepaction"]
>[API de registro e reprodução do SDK do sensor do Azure Kinect](record-playback-api.md)

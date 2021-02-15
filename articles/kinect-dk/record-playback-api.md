---
title: API de reprodução do Azure Kinect
description: Saiba como usar o SDK do sensor de Kinect do Azure para abrir um arquivo de gravação usando a API de reprodução.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, profundidade, RGB, registro, reprodução, Matroska, MKV
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276333"
---
# <a name="the-azure-kinect-playback-api"></a>A API de reprodução do Azure Kinect

O SDK do sensor fornece uma API para gravar dados do dispositivo em um arquivo Matroska (. mkv). O formato de contêiner Matroska armazena faixas de vídeo, exemplos de IMU e calibragem de dispositivo. As gravações podem ser geradas usando o utilitário de linha de comando [k4arecorder](record-sensor-streams-file.md) fornecido. As gravações também podem ser personalizadas e registradas diretamente usando a API de registro.

Para obter mais informações sobre a API de gravação, consulte [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Para obter mais informações sobre as especificações de formato de arquivo Matroska, consulte a página [gravando formato de arquivo](record-file-format.md) .

## <a name="use-the-playback-api"></a>Usar a API de reprodução

A gravação de arquivos pode ser aberta usando a API de reprodução. A API de reprodução fornece acesso aos dados do sensor no mesmo formato que o restante do SDK do sensor.

### <a name="open-a-record-file"></a>Abrir um arquivo de registro

No exemplo a seguir, abrimos uma gravação usando [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) , imprime o tamanho da gravação e, em seguida, fechamos o arquivo com [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Ler capturas

Quando o arquivo estiver aberto, podemos começar a ler as capturas da gravação. Este exemplo a seguir lerá cada uma das capturas no arquivo.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Buscar em uma gravação

Depois de atingirmos o final do arquivo, talvez queiramos voltar e ler novamente. Esse processo pode ser feito lendo para trás com [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , mas pode ser muito lento dependendo do comprimento da gravação.
Em vez disso, podemos usar a [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) função para ir para um ponto específico no arquivo.

Neste exemplo, especificamos carimbos de data/hora em microssegundos para buscar vários pontos no arquivo.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Ler informações de marca

As gravações também podem conter vários metadados, como o número de série do dispositivo e as versões do firmware. Esses metadados são armazenados em marcas de gravação, que podem ser acessadas usando a [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) função.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Lista de marcas de registro

Abaixo está uma lista de todas as marcas padrão que podem ser incluídas em um arquivo de gravação. Muitos desses valores estão disponíveis como parte da [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struct e podem ser lidos com a [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) função.

Se uma marca não existir, supõe-se que tenha o valor padrão.

| Nome da marca                     | Valor padrão      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Campo | Observações     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | DESCONTO              | `color_format` / `color_resolution`  | Valores possíveis: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" e assim por diante                                      |
| `K4A_DEPTH_MODE`             | DESCONTO              | `depth_mode` / `depth_track_enabled` | Valores possíveis: "OFF", "NFOV_UNBINNED", "PASSIVE_IR" e assim por diante                                                |
| `K4A_IR_MODE`                | DESCONTO              | `depth_mode` / `ir_track_enabled`    | Valores possíveis: "OFF", "ativo", "passivo"                                                                    |
| `K4A_IMU_MODE`               | DESCONTO              | `imu_track_enabled`                  | Valores possíveis: "ON", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.js" | N/D                                  | Confira [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | Valor armazenado em nanossegundos, a API fornece microssegundos.                                                        |
| `K4A_WIRED_SYNC_MODE`        | AUTÔNOMO       | `wired_sync_mode`                    | Valores possíveis: "autônomo", "mestre", "SUBORDINAdo"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | Valor armazenado em nanossegundos, a API fornece microssegundos.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | N/D                                  | Versão do firmware de cores do dispositivo, por exemplo, "1. x. XX"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | N/D                                  | Versão do firmware de profundidade do dispositivo, por exemplo, "1. x. XX"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | N/D                                  | Gravando número de série do dispositivo                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | Confira [sincronização de carimbo de data/hora](record-playback-api.md#timestamp-synchronization) abaixo.                       |
| `K4A_COLOR_TRACK`            | Nenhum               | N/D                                  | Consulte [formato de arquivo de gravação-identificando faixas](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | Nenhum               | N/D                                  | Consulte [formato de arquivo de gravação-identificando faixas](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | Nenhum               | N/D                                  | Consulte [formato de arquivo de gravação-identificando faixas](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | Nenhum               | N/D                                  | Consulte [formato de arquivo de gravação-identificando faixas](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Sincronização de timestamp

O formato Matroska requer que as gravações comecem com um carimbo de data/hora igual a zero. Quando as [câmeras são sincronizadas externamente](record-external-synchronized-units.md), o primeiro carimbo de data/hora de cada dispositivo pode ser diferente de zero.

Para preservar os carimbos de data/hora originais dos dispositivos entre a gravação e a reprodução, o arquivo armazena um deslocamento para aplicar aos carimbos de data/hora.

A `K4A_START_OFFSET_NS` marca é usada para especificar um deslocamento de carimbo de data/hora para que os arquivos possam ser ressincronizados após a gravação. Esse deslocamento de carimbo de data/hora pode ser adicionado a cada carimbo de data/hora no arquivo para reconstruir os carimbos de data/hora do dispositivo original.

O deslocamento de início também está disponível na [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) estrutura.

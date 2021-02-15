---
title: Usar o SDK do sensor de Kinect do Azure para registrar o formato de arquivo
description: Entenda como usar o formato de arquivo gravado do SDK do sensor de Kinect do Azure.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: Kinect, Azure, sensor, SDK, profundidade, RGB, registro, reprodução, Matroska, MKV
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654584"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Usar o SDK do sensor de Kinect do Azure para registrar o formato de arquivo

Para registrar dados de sensor, é usado o formato de contêiner Matroska (. mkv), que permite que várias faixas sejam armazenadas usando uma ampla gama de codecs. O arquivo de gravação contém faixas para armazenar cores, profundidade, imagens IR e IMU.

Detalhes de baixo nível do formato de contêiner. mkv podem ser encontrados no [site do Matroska](https://www.matroska.org/index.html).

| Nome da faixa | Formato do codec                          |
|------------|---------------------------------------|
| Cor      | Mode-Dependent (MJPEG, NV12 ou YUY2) |
| DEPTH      | b16g (escala de cinza de 16 bits, big-endian)   |
| IR         | b16g (escala de cinza de 16 bits, big-endian)   |
| IMU        | Estrutura personalizada, consulte a [estrutura de exemplo do IMU](record-file-format.md#imu-sample-structure) abaixo. |

## <a name="using-third-party-tools"></a>Usando ferramentas de terceiros

Ferramentas como `ffmpeg` ou o `mkvinfo` comando do kit de ferramentas [MKVToolNix](https://mkvtoolnix.download/) podem ser usadas para exibir e extrair informações de arquivos de gravação.

Por exemplo, o comando a seguir irá extrair a faixa de profundidade como uma sequência de PNGs de 16 bits para a mesma pasta:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

O `-map 0:1` parâmetro extrairá o controle do índice 1, que para a maioria das gravações será profundidade. Se a gravação não contiver uma faixa de cores, ela `-map 0:0` será usada.

O `-vsync 0` parâmetro força o ffmpeg a extrair os quadros como estão, em vez de tentar corresponder a uma taxa de quadros de 30 fps, 15 fps ou 5 fps.

## <a name="imu-sample-structure"></a>Estrutura de exemplo IMU

Se os dados do IMU forem extraídos do arquivo sem usar a API de reprodução, os dados estarão em formato binário.
A estrutura dos dados de IMU está abaixo. Todos os campos são little-endian.

| Campo                        | Tipo     |
|------------------------------|----------|
| Carimbo de data/hora do acelerômetro (μs) | uint64   |
| Dados do acelerômetro (x, y, z) | float [3] |
| Giroscópio Timestamp (μs)     | uint64   |
| Dados de giroscópio (x, y, z)     | float [3] |

## <a name="identifying-tracks"></a>Identificando faixas

Pode ser necessário identificar qual faixa contém cor, profundidade, IR e assim por diante. É necessário identificar as faixas ao trabalhar com ferramentas de terceiros para ler um arquivo Matroska.
Os números de faixa variam com base no modo de câmera e no conjunto de faixas habilitadas. As marcas são usadas para identificar o significado de cada faixa.

A lista de marcas abaixo é cada anexada a um elemento Matroska específico e pode ser usada para pesquisar a faixa ou o anexo correspondente.

Essas marcas são visíveis com ferramentas como `ffmpeg` e `mkvinfo` .
A lista completa de marcas é listada na página [gravar e reproduzir](record-playback-api.md) .

| Nome da marca             | Destino da marca             | Valor da marca             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Faixa de cores            | UID da faixa de Matroska    |
| K4A_DEPTH_TRACK      | Faixa de profundidade            | UID da faixa de Matroska    |
| K4A_IR_TRACK         | Faixa IR               | UID da faixa de Matroska    |
| K4A_IMU_TRACK        | Faixa de IMU              | UID da faixa de Matroska    |
| K4A_CALIBRATION_FILE | Anexo de calibragem | Nome de arquivo do anexo   |

## <a name="next-steps"></a>Próximas etapas

[Gravar e reproduzir](record-playback-api.md)

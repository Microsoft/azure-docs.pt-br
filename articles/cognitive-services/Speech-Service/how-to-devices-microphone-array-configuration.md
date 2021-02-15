---
title: Como configurar uma matriz de microfone-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como configurar uma matriz de microfone para que o SDK dos dispositivos de fala possa usá-la.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025054"
---
# <a name="how-to-configure-a-microphone-array"></a>Como configurar uma Matriz de Microfone

Neste artigo, você aprenderá a configurar uma [matriz de microfone](./speech-devices-sdk-microphone.md). Ele inclui a definição do ângulo de trabalho e como selecionar qual microfone é usado para o SDK de dispositivos de fala.

O SDK dos dispositivos de fala funciona melhor com uma matriz de microfone que foi projetada de acordo com [nossas diretrizes](./speech-devices-sdk-microphone.md). A configuração da matriz de microfone pode ser fornecida pelo sistema operacional ou fornecida por meio de um dos métodos a seguir.

Inicialmente, o SDK de dispositivos de fala suportava matrizes de microfone selecionando um conjunto fixo de configurações.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

No Windows, a configuração da matriz de microfone é fornecida pelo driver de áudio.

Do v 1.11.0, o SDK dos dispositivos de fala também dá suporte à configuração de um [arquivo JSON](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
No Windows, as informações de geometria da matriz do microfone são obtidas automaticamente do driver de áudio. Portanto, as propriedades `DeviceGeometry` ,  `SelectedGeometry` e `MicArrayGeometryConfigFile` são opcionais. Usamos o [arquivo JSON](https://aka.ms/sdsdk-micarray-json) fornecido usando `MicArrayGeometryConfigFile` para obter apenas o intervalo beamforming.

Se uma matriz de microfone for especificada usando `AudioConfig::FromMicrophoneInput` , então usaremos o microfone especificado. Se um microfone não for especificado ou `AudioConfig::FromDefaultMicrophoneInput` for chamado, então usaremos o microfone padrão, que é especificado nas configurações de som no Windows.
A pilha de áudio da Microsoft no SDK dos dispositivos de fala só dá suporte à amostragem vertical para taxas de exemplo que são múltiplos inteiros de 16 KHz.

## <a name="linux"></a>Linux
No Linux, as informações de geometria do microfone devem ser fornecidas. O uso de `DeviceGeometry` e `SelectedGeometry` permanece com suporte. Ele também pode ser fornecido por meio do arquivo JSON usando a `MicArrayGeometryConfigFile` propriedade. Semelhante ao Windows, o intervalo beamforming pode ser fornecido pelo arquivo JSON.

Se uma matriz de microfone for especificada usando `AudioConfig::FromMicrophoneInput` , então usaremos o microfone especificado. Se um microfone não for especificado ou `AudioConfig::FromDefaultMicrophoneInput` for chamado, então registramos a partir do dispositivo alsa chamado *padrão*. Por padrão, o *padrão* sempre aponta para o dispositivo 0 para o cartão 0, mas os usuários podem alterá-lo no `asound.conf` arquivo. 

A pilha de áudio da Microsoft no SDK de dispositivos de fala só dá suporte à diminuição de resolução para taxas de amostra que são integralmente múltiplos de 16 KHz. Além disso, há suporte para os seguintes formatos: IEEE de 32 bits little endian float, 32 de little endian de bits assinado int, little endian de 24 bits assinados int, 16 bits little endian int assinado e int com sinal de 8 bits.

## <a name="android"></a>Android
Atualmente, somente o [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) é suportado pelo SDK dos dispositivos de fala. O comportamento é o mesmo que as versões anteriores, exceto que `MicArrayGeometryConfigFile` a propriedade Now pode ser usada para especificar o arquivo JSON que contém o intervalo beamforming.

## <a name="microphone-array-configuration-json"></a>JSON de configuração de matriz de microfone

O arquivo JSON para a configuração de geometria da matriz de microfone seguirá o [esquema JSON](https://aka.ms/sdsdk-micarray-json). Veja a seguir alguns exemplos que seguem o esquema.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Ou


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Ou

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha seu dispositivo de fala](get-speech-devices-sdk.md)
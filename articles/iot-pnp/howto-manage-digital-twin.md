---
title: Como gerenciar gêmeos digitais de IoT Plug and Play
description: Como gerenciar o dispositivo de Plug and Play de IoT usando APIs de entrelaçamento digital
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ddb8027c145f6a38bfcd953be66dae2943a20c3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654601"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Gerenciar gêmeos digital de IoT Plug and Play

O IoT Plug and Play dá suporte a **obter** operações digitais de atualização de e/ou **Atualizar** para gerenciar o digital gêmeos. Você pode usar as [APIs REST](/rest/api/iothub/service/digitaltwin) ou um dos SDKs de [serviço](libraries-sdks.md).

No momento da gravação, a versão da API do digital up é `2020-09-30` .

## <a name="update-a-digital-twin"></a>Atualizar um gêmeo digital

Um dispositivo de Plug and Play IoT implementa um modelo descrito pelo [DTDL (digital gêmeos Definition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl). Os desenvolvedores de soluções podem usar a **API de atualização de atualizações digitais** para atualizar o estado do componente e as propriedades do digital.

O dispositivo IoT Plug and Play usado como exemplo neste artigo implementa o modelo do [controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) com componentes [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .

O trecho de código a seguir mostra a resposta a uma solicitação de " **obter digital** " formatada como um objeto JSON. Para saber mais sobre o formato de informações digitais, consulte [entender IoT plug and Play Digital gêmeos](./concepts-digital-twin.md#digital-twin-example):

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

O digital gêmeos permite que você atualize um componente ou propriedade inteira usando um [patch JSON](http://jsonpatch.com/).

Por exemplo, você pode atualizar a `targetTemperature` propriedade da seguinte maneira:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

A atualização anterior define o valor desejado de uma propriedade no nível de componente correspondente `$metadata` , conforme mostrado no trecho a seguir. O Hub IoT atualiza a versão desejada da propriedade:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Adicionar, substituir ou remover um componente

Operações de nível de componente exigem um marcador de objeto vazio `$metadata` dentro do valor.

Uma operação Adicionar ou substituir componente define os valores desejados de todas as propriedades fornecidas. Ele também limpa os valores desejados para as propriedades graváveis não fornecidas com a atualização.

A remoção de um componente limpa os valores desejados de todas as propriedades graváveis presentes. Um dispositivo eventualmente sincroniza essa remoção e para de relatar as propriedades individuais. O componente é, então, removido da digital.

O exemplo de patch JSON a seguir mostra como adicionar, substituir ou remover um componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Adicionar, substituir ou remover uma propriedade

Uma operação de adicionar ou substituir define o valor desejado de uma propriedade. O dispositivo pode sincronizar o estado e relatar uma atualização do valor junto com um `ack` código, uma versão e uma descrição.

A remoção de uma propriedade desmarcará o valor desejado da propriedade se ela estiver definida. O dispositivo pode então parar de relatar essa propriedade e ela é removida do componente. Se essa propriedade for a última no componente, o componente também será removido.

O exemplo de patch JSON a seguir mostra como adicionar, substituir ou remover uma propriedade dentro de um componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regras para definir o valor desejado de uma propriedade de propriedades digitais

**Nome**

O nome de um componente ou propriedade deve ser um nome de DTDL v2 válido.

Os caracteres permitidos são a-z, A-Z, 0-9 (não como o primeiro caractere) e sublinhado (não como o primeiro ou último caractere).

Um nome pode ter de 1-64 A caracteres de comprimento.

**Valor da propriedade**

O valor deve ser uma [Propriedade DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)válida.

Há suporte para todos os tipos primitivos. Em tipos complexos, enums, mapas e objetos têm suporte. Para saber mais, confira [esquemas do DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

As propriedades não dão suporte à matriz ou a qualquer esquema complexo com uma matriz.

Há suporte para uma profundidade máxima de cinco níveis para um objeto complexo.

Todos os nomes de campo no objeto complexo devem ser nomes DTDL v2 válidos.

Todas as chaves de mapa devem ser nomes DTDL v2 válidos.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Solucionar problemas de erros de API de atualização digital

A API de teledigital de digitais gera a seguinte mensagem de erro genérica:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Se você vir esse erro, verifique se o patch de atualização segue as [regras para definir o valor desejado de uma propriedade de propriedades digitais](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

Ao atualizar um componente, verifique se o [objeto vazio $Metadata marcador](#add-replace-or-remove-a-component) está definido.

As atualizações podem falhar se os valores relatados de um dispositivo não estiverem em conformidade com as [convenções plug and Play de IOT](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre o digital gêmeos, aqui estão alguns recursos adicionais:

- [Interagir com um dispositivo da sua solução](quickstart-service.md)
- [API REST de monodigital de IoT](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)

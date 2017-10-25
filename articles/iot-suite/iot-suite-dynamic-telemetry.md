---
title: "Usar a telemetria dinâmica | Microsoft Docs"
description: "Siga este tutorial para saber como usar a telemetria dinâmica com a solução pré-configurada de monitoramento remoto Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 0114f27f9b8ae76e1170d04ddf66e2c4bf20686a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Usar telemetria dinâmica com a solução pré-configurada de monitoramento remoto

A telemetria dinâmica permite que você visualize qualquer telemetria enviada à solução pré-configurada de monitoramento remoto. Os dispositivos simulados que implantam a solução pré-configurada enviam telemetria de temperatura e umidade, que pode ser visualizada no painel. Se você personalizar os dispositivos simulados existentes, criar novos dispositivos simulados ou conectar dispositivos físicos à solução pré-configurada, será possível enviar outros valores de telemetria, como a temperatura externa, RPM ou velocidade do vento. Em seguida, você pode visualizar essa telemetria adicional no painel.

Este tutorial usa um dispositivo Node.js simples simulado que você pode facilmente modificar a fim de experimentar a telemetria dinâmica.

Para concluir este tutorial, você precisará de:

* Uma assinatura ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].
* [Node.js][lnk-node] versão 0.12.x ou posterior.

Você pode concluir este tutorial em qualquer sistema operacional, como Windows ou Linux, onde é possível instalar o Node.js.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Adicionar um tipo de telemetria

A próxima etapa é substituir a telemetria gerada pelo dispositivo simulado Node.js por um novo conjunto de valores:

1. Interrompa o dispositivo simulado Node.js digitando **Ctrl + C** no prompt de comando ou no shell.
2. No arquivo remote_monitoring.js, você pode ver os valores de dados base para a telemetria existente de temperatura, umidade e temperatura externa. Adicione um valor de dados base para **rpm** da seguinte maneira:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. O dispositivo simulado Node.js usa a função **generateRandomIncrement** no arquivo remote_monitoring.js para adicionar um incremento aleatório aos valores de dados base. Torne o valor de **rpm** aleatório adicionando uma linha de código após as aleatorizações existentes da seguinte maneira:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Adicione o novo valor de rpm à carga JSON que o dispositivo envia ao Hub IoT:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Execute o dispositivo Node.js simulado usando o seguinte comando:

    `node remote_monitoring.js`

6. Observe o novo tipo de telemetria RPM exibido no gráfico no painel:

![Adicionar RPM ao painel][image3]

> [!NOTE]
> Talvez seja necessário desabilitar e, em seguida, habilitar o dispositivo Node.js na página **Dispositivos** no painel, para ver a alteração imediatamente.

## <a name="customize-the-dashboard-display"></a>Personalizar a exibição do painel

A mensagem **Informações do Dispositivo** pode incluir metadados sobre a telemetria que o dispositivo pode enviar para o Hub IoT. Esses metadados podem especificar os tipos de telemetria enviados pelo dispositivo. Modifique o valor de **deviceMetaData** no arquivo remote_monitoring.js a fim de incluir uma definição de **Telemetria** logo após a definição de **Comandos**. O seguinte trecho de código mostra a definição **Comandos** (não se esqueça de adicionar um `,` após a definição de **Comandos**):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> A solução de monitoramento remoto usa uma correspondência que diferencia maiúsculas e minúsculas a fim de comparar a definição de metadados com os dados no fluxo de telemetria.


A adição de uma definição de **Telemetria** , como mostra o trecho de código acima, não altera o comportamento do painel. No entanto, os metadados também podem incluir um atributo **DisplayName** para personalizar a exibição no painel. Atualize a definição de metadados de **Telemetria** conforme mostra o trecho a seguir:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

A captura de tela a seguir mostra como essa alteração modifica a legenda do gráfico no painel:

![Personalizar a legenda do gráfico][image4]

> [!NOTE]
> Talvez seja necessário desabilitar e, em seguida, habilitar o dispositivo Node.js na página **Dispositivos** no painel, para ver a alteração imediatamente.

## <a name="filter-the-telemetry-types"></a>Filtrar os tipos de telemetria

Por padrão, o gráfico no painel mostra cada série de dados no fluxo de telemetria. Você pode usar os metadados **Informações do Dispositivo** para suprimir a exibição de tipos de telemetria específicos no gráfico. 

Para fazer com que o gráfico mostre somente a telemetria de Temperatura e Umidade, omita **ExternalTemperature** dos metadados **Informações do Dispositivo** de **Telemetria** da seguinte maneira:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

A **Temperatura Externa** não aparece mais no gráfico:

![Filtrar a telemetria no painel][image5]

Essa alteração afeta somente a exibição do gráfico. Os valores de **ExternalTemperature** ainda são armazenados e disponibilizados para o processamento de qualquer back-end.

> [!NOTE]
> Talvez seja necessário desabilitar e, em seguida, habilitar o dispositivo Node.js na página **Dispositivos** no painel, para ver a alteração imediatamente.

## <a name="handle-errors"></a>Tratar erros

Para que um fluxo de dados seja exibido no gráfico, seu **Tipo** nos metadados **Informações do Dispositivo** deve corresponder ao tipo de dados dos valores de telemetria. Por exemplo, se os metadados especificarem que o **Tipo** de dados de unidade é **int** e um **double** for encontrado no fluxo de telemetria, a telemetria de umidade não será exibida no gráfico. No entanto, os valores de **Umidade** ainda são armazenados e disponibilizados para qualquer processamento de back-end.

## <a name="next-steps"></a>Próximas etapas

Agora que você já viu como usar a telemetria dinâmica, saiba mais sobre como as soluções pré-configuradas usam das informações do dispositivo: [Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org

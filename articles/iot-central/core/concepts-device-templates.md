---
title: O que são modelos de dispositivo no Azure IoT Central | Microsoft Docs
description: Os modelos de dispositivo IoT Central do Azure permitem especificar o comportamento dos dispositivos conectados ao seu aplicativo. Um modelo de dispositivo especifica a telemetria, as propriedades e os comandos que o dispositivo deve implementar. Um modelo de dispositivo também define a interface do usuário para o dispositivo em IoT Central, como os formulários e painéis usados por um operador.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97795421"
---
# <a name="what-are-device-templates"></a>O que são modelos de dispositivo?

_Este artigo se aplica a desenvolvedores de dispositivos e construtores de solução._

Um modelo de dispositivo no Azure IoT Central é um plano gráfico que define as características e os comportamentos de um tipo de dispositivo que se conecta ao seu aplicativo. Por exemplo, o modelo de dispositivo define a telemetria que um dispositivo envia para que IoT Central possa criar visualizações que usam as unidades e os tipos de dados corretos.

Um Solution Builder adiciona modelos de dispositivo a um aplicativo IoT Central. Um desenvolvedor de dispositivos grava o código de dispositivo que implementa os comportamentos definidos no modelo de dispositivo.

Um modelo de dispositivo inclui as seguintes seções:

- _Um modelo de dispositivo_. Esta parte do modelo de dispositivo define como o dispositivo interage com seu aplicativo. Um desenvolvedor de dispositivo implementa os comportamentos definidos no modelo.
    - _Componente padrão_. Cada modelo de dispositivo tem um componente padrão. A interface do componente padrão descreve recursos que são específicos para o modelo do dispositivo.
    - _Componentes_ do. Um modelo de dispositivo pode incluir componentes além do componente padrão para descrever os recursos do dispositivo. Cada componente tem uma interface que descreve os recursos do componente. As interfaces de componente podem ser reutilizadas em outros modelos de dispositivo. Por exemplo, vários modelos de dispositivo de telefone podem usar a mesma interface de câmera.
    - _Interfaces herdadas_. Um modelo de dispositivo contém uma ou mais interfaces que estendem os recursos do componente padrão.
- _Propriedades de nuvem_. Essa parte do modelo de dispositivo permite que o desenvolvedor da solução especifique qualquer metadado de dispositivo a ser armazenado. As propriedades de nuvem nunca são sincronizadas com dispositivos e só existem no aplicativo. As propriedades de nuvem não afetam o código que um desenvolvedor de dispositivo grava para implementar o modelo de dispositivo.
- _Personalizações_. Essa parte do modelo de dispositivo permite que o desenvolvedor da solução substitua algumas das definições no modelo do dispositivo. As personalizações serão úteis se o desenvolvedor da solução quiser refinar como o aplicativo lida com um valor, como alterar o nome de exibição de uma propriedade ou a cor usada para exibir um valor de telemetria. As personalizações não afetam o código que um desenvolvedor de dispositivo grava para implementar o modelo de dispositivo.
- _Exibições_. Essa parte do modelo de dispositivo permite que o desenvolvedor da solução defina visualizações para exibir dados do dispositivo e formulários para gerenciar e controlar um dispositivo. As exibições usam o modelo de dispositivo, as propriedades de nuvem e as personalizações. As exibições não afetam o código que um desenvolvedor de dispositivo grava para implementar o modelo de dispositivo.

## <a name="device-models"></a>Modelos de dispositivo

Um modelo de dispositivo define como um dispositivo interage com seu aplicativo IoT Central. O desenvolvedor do dispositivo deve garantir que o dispositivo implemente os comportamentos definidos no modelo do dispositivo para que IoT Central possa monitorar e gerenciar o dispositivo. Um modelo de dispositivo é composto de uma ou mais _interfaces_, e cada interface pode definir uma coleção de tipos de _telemetria_ , _Propriedades de dispositivo_ e _comandos_. Um desenvolvedor de solução pode importar um arquivo JSON que define o modelo de dispositivo em um modelo de dispositivo ou usar a interface do usuário da Web no IoT Central para criar ou editar um modelo de dispositivo. As alterações em um modelo de dispositivo feito usando a interface do usuário da Web exigem [que o modelo de dispositivo tenha controle de versão](./howto-version-device-template.md).

Um desenvolvedor de soluções também pode exportar um arquivo JSON que contém o modelo de dispositivo. Um desenvolvedor de dispositivos pode usar este documento JSON para entender como o dispositivo deve se comunicar com o aplicativo IoT Central.

O arquivo JSON que define o modelo de dispositivo usa o [DTDL (digital Mydefinition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central espera que o arquivo JSON contenha o modelo de dispositivo com as interfaces definidas como embutidas, em vez de arquivos separados.

Um dispositivo IoT típico é composto de:

- Partes personalizadas, que são as coisas que tornam seu dispositivo exclusivo.
- As partes padrão, que são coisas comuns a todos os dispositivos.

Essas partes são chamadas de _interfaces_ em um modelo de dispositivo. As interfaces definem os detalhes de cada parte que seu dispositivo implementa. As interfaces são reutilizáveis em todos os modelos de dispositivo. No DTDL, um componente refere-se a uma interface definida em um arquivo DTDL separado.

O exemplo a seguir mostra a estrutura de tópicos do modelo de dispositivo para um dispositivo de controlador de temperatura. O componente padrão inclui definições para `workingSet` , `serialNumber` e `reboot` . O modelo de dispositivo também inclui as `thermostat` `deviceInformation` interfaces e:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Uma interface tem alguns campos obrigatórios:

- `@id`: uma ID exclusiva na forma de um simples nome de recurso uniforme.
- `@type`: declara que esse objeto é uma interface.
- `@context`: especifica a versão DTDL usada para a interface.
- `contents`: lista as propriedades, a telemetria e os comandos que compõem seu dispositivo. Os recursos podem ser definidos em várias interfaces.

Há alguns campos opcionais que você pode usar para adicionar mais detalhes ao modelo de funcionalidade, como nome de exibição e descrição.

Cada entrada na lista de interfaces na seção implementações tem um:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de funcionalidade implementa.

## <a name="interfaces"></a>Interfaces

O DTDL permite que você descreva os recursos do seu dispositivo. Os recursos relacionados são agrupados em interfaces. As interfaces descrevem as propriedades, a telemetria e os comandos que uma parte do seu dispositivo implementa:

- `Properties`. As propriedades são campos de dados que representam o estado do seu dispositivo. Use Propriedades para representar o estado durável do dispositivo, como o estado ligado de uma bomba refrigeração. As propriedades também podem representar as propriedades básicas do dispositivo, como a versão do firmware do dispositivo. É possível declarar propriedades como somente leitura ou graváveis. Somente os dispositivos podem atualizar o valor de uma propriedade somente leitura. Um operador pode definir o valor de uma propriedade gravável para enviar a um dispositivo.
- `Telemetry`. Campos de telemetria representam medições de sensores. Sempre que o dispositivo toma uma medida de sensor, ele deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os usuários do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de redefinição ou um comando para ativar ou desativar um ventilador.

O exemplo a seguir mostra a definição da interface termostato:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name" : "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name" : "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name" : "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name" : "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Este exemplo mostra duas propriedades (uma somente leitura e uma gravável), um tipo de telemetria e um comando. Uma descrição mínima do campo tem um:

- `@type` para especificar o tipo de recurso: `Telemetry` , `Property` ou `Command` .  Em alguns casos, o tipo inclui um tipo semântico para permitir que IoT Central faça algumas suposições sobre como lidar com o valor.
- `name` para o valor de telemetria.
- `schema` para especificar o tipo de dados para a telemetria ou a propriedade. Esse valor pode ser um tipo primitivo, como duplo, inteiro, booliano ou cadeia de caracteres. Também há suporte para mapas e tipos de objeto complexos.

Campos opcionais, como nome de exibição e descrição, permitem que você adicione mais detalhes à interface e aos recursos.

## <a name="properties"></a>Propriedades

Por padrão, as propriedades são somente leitura. Propriedades somente leitura significam que o valor da propriedade relatórios de dispositivo é atualizado para seu aplicativo IoT Central. O aplicativo IoT Central não pode definir o valor de uma propriedade somente leitura.

Você também pode marcar uma propriedade como gravável em uma interface. Um dispositivo pode receber uma atualização para uma propriedade gravável de seu aplicativo IoT Central, bem como relatar atualizações de valor de propriedade para seu aplicativo.

Os dispositivos não precisam ser conectados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo se conecta ao aplicativo. Esse comportamento se aplica a propriedades somente leitura e graváveis.

Não use Propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade ReadOnly, como `temperatureSetting=80` deveria significar que a temperatura do dispositivo foi definida como 80, e o dispositivo está tentando chegar ou permanecer nessa temperatura.

Para propriedades graváveis, o aplicativo do dispositivo retorna um código de status de estado desejado, versão e descrição para indicar se ele recebeu e aplicou o valor da propriedade.

## <a name="telemetry"></a>Telemetria

IoT Central permite exibir a telemetria em painéis e gráficos e usar regras para disparar ações quando os limites forem atingidos. IoT Central usa as informações no modelo de dispositivo, como tipos de dados, unidades e nomes de exibição, para determinar como exibir valores de telemetria.

Você pode usar o recurso de exportação de dados IoT Central para transmitir telemetria para outros destinos, como armazenamento ou hubs de eventos.

## <a name="commands"></a>Comandos

Um comando deve ser executado dentro de 30 segundos por padrão e o dispositivo deve ser conectado quando o comando chegar. Se o dispositivo responder no tempo ou o dispositivo não estiver conectado, o comando falhará.

Os comandos podem ter parâmetros de solicitação e retornar uma resposta.

### <a name="offline-commands"></a>Comandos offline

Você pode escolher os comandos de fila se um dispositivo estiver offline, habilitando a opção **fila se estiver offline** para um comando no modelo de dispositivo.

Os comandos offline são notificações unidirecionais para o dispositivo de sua solução. Comandos offline podem ter parâmetros de solicitação, mas não retornam uma resposta.

> [!NOTE]
> Essa opção só está disponível na interface do usuário do IoT Central Web. Essa configuração não será incluída se você exportar um modelo ou interface do modelo de dispositivo.

## <a name="cloud-properties"></a>Propriedades da Nuvem

As propriedades de nuvem fazem parte do modelo de dispositivo, mas não fazem parte do modelo de dispositivo. As propriedades de nuvem permitem que o desenvolvedor da solução especifique qualquer metadado de dispositivo a ser armazenado no aplicativo IoT Central. As propriedades de nuvem não afetam o código que um desenvolvedor de dispositivo grava para implementar o modelo de dispositivo.

Um desenvolvedor de soluções pode adicionar propriedades de nuvem a painéis e exibições junto com as propriedades do dispositivo para permitir que um operador gerencie os dispositivos conectados ao aplicativo. Um desenvolvedor de soluções também pode usar propriedades de nuvem como parte de uma definição de regra para tornar um valor de limite editável por um operador.

## <a name="customizations"></a>Personalizações

As personalizações fazem parte do modelo do dispositivo, mas não fazem parte do modelo do dispositivo. As personalizações permitem que o desenvolvedor da solução aprimore ou substitua algumas das definições no modelo do dispositivo. Por exemplo, um desenvolvedor de soluções pode alterar o nome de exibição de um tipo ou propriedade de telemetria. Um desenvolvedor de soluções também pode usar personalizações para adicionar validação, como um comprimento mínimo ou máximo para uma propriedade de dispositivo de cadeia de caracteres.

As personalizações podem afetar o código que um desenvolvedor de dispositivo grava para implementar o modelo de dispositivo. Por exemplo, uma personalização poderia definir comprimentos mínimos e máximos de cadeia de caracteres ou valores numéricos mínimos e máximos para telemetria.

## <a name="views"></a>Exibições

Um desenvolvedor de soluções cria exibições que permitem que os operadores monitorem e gerenciem dispositivos conectados. As exibições fazem parte do modelo de dispositivo, portanto, uma exibição é associada a um tipo de dispositivo específico. Uma exibição pode incluir:

- Gráficos para plotar a telemetria.
- Blocos para exibir propriedades de dispositivo somente leitura.
- Blocos para permitir que o operador edite as propriedades do dispositivo gravável.
- Blocos para permitir que o operador edite as propriedades de nuvem.
- Blocos para permitir que o operador chame comandos, incluindo comandos que esperam uma carga.
- Blocos para exibir rótulos, imagens ou texto de redução.

A telemetria, as propriedades e os comandos que você pode adicionar a uma exibição são determinados pelo modelo de dispositivo, propriedades de nuvem e personalizações no modelo de dispositivo.

## <a name="next-steps"></a>Próximas etapas

Como desenvolvedor de dispositivos, agora que você aprendeu sobre modelos de dispositivo, as próximas etapas sugeridas são ler as [cargas de telemetria, propriedade e comando](./concepts-telemetry-properties-commands.md) para saber mais sobre os dados que um dispositivo troca com IOT central.

Como desenvolvedor de soluções, uma próxima etapa sugerida é ler como [definir um novo tipo de dispositivo IOT em seu aplicativo de IOT central do Azure](./howto-set-up-template.md) para saber mais sobre como criar um modelo de dispositivo.

---
title: Entender o Registro da identidade do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ descrição do Registro de identidade do Hub IoT e como usá-lo para gerenciar seus dispositivos. Inclui informações sobre a importação e exportação de identidades de dispositivo em massa.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.openlocfilehash: 935635c474190413545d1a2731c367a691bfa56d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363137"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Entender o registro de identidade no Hub IoT

Cada Hub IoT tem um registro de identidade que armazena informações sobre os dispositivos e módulos com permissão para se conectar ao Hub IoT. Antes de um dispositivo ou módulo poder se conectar a um Hub IoT, deve existir uma entrada para esse dispositivo ou módulo no registro de identidade do Hub IoT. O dispositivo ou módulo também deve realizar a autenticação no Hub IoT com base em credenciais armazenadas no registro de identidade.

A ID do dispositivo ou módulo armazenada no registro de identidade diferencia maiúsculas de minúsculas.

Em um alto nível, o registro de identidade é uma coleção compatível com REST de recursos de identidade do dispositivo ou módulo. Quando você adiciona uma entrada ao registro de identidade, o Hub IoT cria um conjunto de recursos por dispositivo, tal como a fila que contém mensagens em andamento da nuvem para o dispositivo.

Use o registro de identidade quando você precisa:

* Provisionar dispositivos ou módulos que se conectam ao Hub IoT.
* Controlar o acesso por dispositivo/módulo aos pontos de extremidade do hub voltados para o dispositivo ou módulo.

> [!NOTE]
> * O registro de identidade não contém os metadados específicos do aplicativo.
> * A identidade do módulo e o módulo gêmeo estão em versão prévia pública. O recurso abaixo terá suporte na identidade do módulo quando estiver disponível para o público geral.
>

## <a name="identity-registry-operations"></a>Operações de registro de identidade

O registro de identidade do Hub IoT expõe as seguintes operações:

* Criar identidade de dispositivo ou módulo
* Atualizar identidade de dispositivo ou módulo
* Recuperar identidade de dispositivo ou módulo por ID
* Excluir identidade de dispositivo ou módulo
* Listar até 1000 identidades
* Exportar identidades de dispositivo para o armazenamento de blobs do Azure
* Importar identidades de dispositivo do armazenamento de blobs do Azure

Todas essas operações podem usar a simultaneidade otimista, conforme especificado no [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> A única maneira de recuperar todas as identidades no registro de identidade de um Hub IoT é usar a funcionalidade [Exportar](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

Um registro de identidade do Hub IoT:

* Não contém metadados de aplicativo.
* Pode ser acessado como um dicionário usando o **deviceId** ou **moduleId** como a chave.
* Não permite consultas expressivas.

Normalmente, uma solução de IoT tem um armazenamento específico da solução separado que contém metadados específicos do aplicativo. Por exemplo, o armazenamento específico da solução em uma solução de construção inteligente seria registrar a sala na qual um sensor de temperatura está implantado.

> [!IMPORTANT]
> Você deve usar o registro de identidade apenas para operações de provisionamento e gerenciamento de dispositivos. As operações de alta produtividade no tempo de execução não devem depender da execução de operações no registro de identidade. Por exemplo, verificar o estado da conexão de um dispositivo antes de enviar um comando não é um padrão permitido. Lembre-se de verificar as [taxas de limitação](iot-hub-devguide-quotas-throttling.md) do registro de identidade e o padrão de [pulsação do dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

## <a name="disable-devices"></a>Desabilitar dispositivos

Você pode desabilitar dispositivos atualizando a propriedade **status** de uma identidade no Registro. Normalmente, você deve usar essa propriedade em dois cenários:

* Durante um processo de orquestração de provisionamento. Para obter mais informações, confira [Provisionamento de dispositivos](iot-hub-devguide-identity-registry.md#device-provisioning).

* Se, por algum motivo, você acreditar que um dispositivo está comprometido ou que se tornou não autorizado.

Esse recurso não está disponível para módulos.

## <a name="import-and-export-device-identities"></a>Importar e exportar identidades de dispositivo

Use operações assíncronas no [ponto de extremidade de provedor de recursos do Hub IoT](iot-hub-devguide-endpoints.md) para exportar identidades de dispositivos em massa do registro de identidade de um Hub IoT. As exportações são trabalhos de execução longa que usam um contêiner de blobs fornecido pelo cliente para salvar dados de identidade do dispositivo lidos no registro de identidade.

Use operações assíncronas no [ponto de extremidade de provedor de recursos do Hub IoT](iot-hub-devguide-endpoints.md) para importar identidades de dispositivos em massa para o registro de identidade de um Hub IoT. As exportações são trabalhos de execução longa que usam dados em um contêiner de blobs fornecido pelo cliente para gravar dados de identidade do dispositivo no registro de identidade.

Para obter mais informações sobre as APIs de importação e exportação, confira [APIs REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource). Para saber mais sobre como executar trabalhos de importação e exportação, confira [Gerenciamento em massa de identidades de dispositivo do Hub IoT](iot-hub-bulk-identity-mgmt.md).

## <a name="device-provisioning"></a>Provisionamento de dispositivos

Os dados de dispositivo que uma determinada solução IoT armazena dependem dos requisitos específicos dessa solução. Porém, no mínimo, uma solução deve armazenar identidades e chaves de autenticação. O Hub IoT do Azure inclui um registro de identidades que pode armazenar valores para cada dispositivo, como IDs, chaves de autenticação e códigos de status. Uma solução pode usar outros serviços do Azure como armazenamento de tabelas, armazenamento de blobs ou Cosmos DB para armazenar outros dados de dispositivo.

*Provisionamento de dispositivos* é o processo de adição dos dados iniciais do dispositivo para as lojas em sua solução. Para permitir que um dispositivo se conecte ao hub, você deve adicionar uma ID e chaves de dispositivo ao registro de identidade do Hub IoT. Como parte do processo de provisionamento, talvez seja necessário inicializar dados específicos do dispositivo em outros repositórios da solução. Você também pode usar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para habilitar o provisionamento sem toque e Just-In-Time para um ou mais hubs IoT sem a necessidade de intervenção humana. Para obter mais informações, consulte a [documentação do serviço de provisionamento](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Pulsação do dispositivo

O registro de identidade do Hub IoT contém um campo chamado **connectionState**. Use somente o campo **connectionState** durante o desenvolvimento e a depuração. Soluções de IoT não devem consultar o campo em tempo de execução. Por exemplo, não consulte o campo **connectionState** para verificar se um dispositivo está conectado antes de enviar uma mensagem de nuvem para dispositivo ou um SMS. É recomendável inscrever-se para o [**evento** de dispositivo desconectado](iot-hub-event-grid.md#event-types) na Grade de Eventos para receber alertas e monitorar o estado de conexão do dispositivo. Use este [tutorial](iot-hub-how-to-order-connection-state-events.md) para saber como integrar os eventos Dispositivo Conectado e Dispositivo Desconectado do Hub IoT em sua solução de IoT.

Se sua solução de IoT precisa saber se um dispositivo está conectado, você pode implementar o *padrão de pulsação*.
No padrão de pulsação, o dispositivo envia mensagens do dispositivo para a nuvem pelo menos uma vez a cada período de tempo fixo (por exemplo, pelo menos uma vez a cada hora). Portanto, mesmo quando um dispositivo não tiver dados para enviar, ele enviará uma mensagem vazia do dispositivo para a nuvem (geralmente com uma propriedade que a identifique como uma pulsação). No lado do serviço, a solução mantém um mapa com a última pulsação recebida para cada dispositivo. Se a solução não recebe uma mensagem de pulsação de um dispositivo no tempo esperado, ela supõe que há um problema com ele.

Uma implementação mais complexa poderia incluir as informações do [Azure Monitor](../azure-monitor/index.yml) e do [Azure Resource Health](../service-health/resource-health-overview.md) para identificar os dispositivos que estão tentando se conectar ou se comunicar, mas falhando. Confira [Monitorar com o guia de diagnóstico](iot-hub-monitor-resource-health.md). Ao implementar o padrão de pulsação, verifique os [Limites e as cotas do Hub IoT](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Se uma solução IoT usa o estado de conexão apenas para determinar se deve enviar mensagens da nuvem para o dispositivo e as mensagens não forem difundidas para conjuntos grandes de dispositivos, considere usar o padrão mais simples de *tempo de expiração mais curto*. Esse padrão é o mesmo que manter um registro do estado da conexão do dispositivo usando o padrão de pulsação, embora seja mais eficiente. Se você solicita confirmações de mensagem, o Hub IoT pode notificar você sobre quais dispositivos têm capacidade de receber mensagens e quais não têm.

## <a name="device-and-module-lifecycle-notifications"></a>Notificações do ciclo de vida do dispositivo ou módulo

O Hub IoT pode notificar sua solução de IoT quando uma identidade é criada ou excluída, enviando notificações do ciclo de vida. Para fazer isso, sua solução de IoT precisa para criar uma rota e definir a Fonte de Dados como *DeviceLifecycleEvents* ou *ModuleLifecycleEvents*. Por padrão, nenhuma notificação de ciclo de vida é enviada, ou seja, nenhuma dessas rotas existe previamente. A mensagem de notificação inclui propriedades e o corpo.

Propriedades: As propriedades do sistema de mensagens são fixadas previamente com o símbolo `$`.

Mensagem de notificação para dispositivo:

| NOME | Value |
| --- | --- |
|$content-type | aplicativo/json |
|$iothub-enqueuedtime |  Hora em que a notificação foi enviada |
|$iothub-message-source | deviceLifecycleEvents |
|$content-encoding | utf-8 |
|opType | **createDeviceIdentity** ou **deleteDeviceIdentity** |
|hubName | Nome do Hub IoT |
|deviceId | ID do dispositivo |
|operationTimestamp | Carimbo de data/hora ISO8601 da operação |
|iothub-message-schema | deviceLifecycleNotification |

Corpo: Esta seção está no formato JSON e representa o gêmeo da identidade de dispositivo criada. Por exemplo,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Mensagem de notificação para módulo:

| NOME | Value |
| --- | --- |
$content-type | aplicativo/json |
$iothub-enqueuedtime |  Hora em que a notificação foi enviada |
$iothub-message-source | moduleLifecycleEvents |
$content-encoding | utf-8 |
opType | **createModuleIdentity** ou **deleteModuleIdentity** |
hubName | Nome do Hub IoT |
moduleId | ID do módulo |
operationTimestamp | Carimbo de data/hora ISO8601 da operação |
iothub-message-schema | moduleLifecycleNotification |

Corpo: Esta seção está no formato JSON e representa o gêmeo da identidade do módulo criado. Por exemplo,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Propriedades de identidade do dispositivo

As identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | DESCRIÇÃO |
| --- | --- | --- |
| deviceId |obrigatória, somente leitura em atualizações |Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits, mais determinados caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |obrigatória, somente leitura |Uma cadeia de caracteres que diferencia maiúsculas de minúsculas com até 128 caracteres gerada pelo Hub IoT. Esse valor é usado para distinguir os dispositivos com a mesma **deviceId** quando são excluídos e recriados. |
| etag |obrigatória, somente leitura |Uma cadeia de caracteres que representa uma ETag fraca para a identidade do dispositivo, de acordo com o [RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |opcional |Um objeto composto que contém as informações de autenticação e os materiais de segurança. |
| auth.symkey |opcional |Um objeto composto que contém as chaves primária e secundária, armazenadas no formato base64. |
| status |obrigatório |Um indicador de acesso. Pode estar **Habilitado** ou **Desabilitado**. Se estiver **Habilitado**, o dispositivo terá permissão para se conectar. Se estiver **Desabilitado**, este dispositivo não poderá acessar qualquer ponto de extremidade voltado para o dispositivo. |
| statusReason |opcional |Uma cadeia de caracteres com 128 caracteres que armazena o motivo do status de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |somente leitura |Um indicador temporal, mostrando a data e hora da última atualização de status. |
| connectionState |somente leitura |Um campo indicando o status da conexão: **Conectado** ou **Desconectado**. Esse campo representa a exibição do Hub IoT do status de conexão do dispositivo. **Importante**: Este campo deve ser usado apenas para fins de desenvolvimento/depuração. O estado da conexão é atualizado somente nos dispositivos que usam MQTT ou AMQP. Além disso, ele se baseia nos pings do nível de protocolo (pings MQTT ou AMQP) e pode ter um atraso máximo de apenas cinco minutos. Por esses motivos, pode haver falsos positivos, como dispositivos relatados como conectados, mas que estão desconectados. |
| connectionStateUpdatedTime |somente leitura |Um indicador temporal, mostrando a data e a hora da última atualização do estado da conexão. |
| lastActivityTime |somente leitura |Um indicador temporal, mostrando a data e hora da última vez em que o dispositivo se conectou, recebeu ou enviou uma mensagem. |

> [!NOTE]
> O estado da conexão pode representar apenas a visão do Hub IoT do status da conexão. As atualizações para esse estado podem ser atrasadas, dependendo das configurações e das condições da rede.

> [!NOTE]
> No momento, os SDKs do dispositivo não dão suporte ao uso dos caracteres `+` e `#` no **deviceId**.

## <a name="module-identity-properties"></a>Propriedades de identidade do módulo

As identidades do módulo são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | DESCRIÇÃO |
| --- | --- | --- |
| deviceId |obrigatória, somente leitura em atualizações |Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits, mais determinados caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId |obrigatória, somente leitura em atualizações |Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits, mais determinados caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |obrigatória, somente leitura |Uma cadeia de caracteres que diferencia maiúsculas de minúsculas com até 128 caracteres gerada pelo Hub IoT. Esse valor é usado para distinguir os dispositivos com a mesma **deviceId** quando são excluídos e recriados. |
| etag |obrigatória, somente leitura |Uma cadeia de caracteres que representa uma ETag fraca para a identidade do dispositivo, de acordo com o [RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |opcional |Um objeto composto que contém as informações de autenticação e os materiais de segurança. |
| auth.symkey |opcional |Um objeto composto que contém as chaves primária e secundária, armazenadas no formato base64. |
| status |obrigatório |Um indicador de acesso. Pode estar **Habilitado** ou **Desabilitado**. Se estiver **Habilitado**, o dispositivo terá permissão para se conectar. Se estiver **Desabilitado**, este dispositivo não poderá acessar qualquer ponto de extremidade voltado para o dispositivo. |
| statusReason |opcional |Uma cadeia de caracteres com 128 caracteres que armazena o motivo do status de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |somente leitura |Um indicador temporal, mostrando a data e hora da última atualização de status. |
| connectionState |somente leitura |Um campo indicando o status da conexão: **Conectado** ou **Desconectado**. Esse campo representa a exibição do Hub IoT do status de conexão do dispositivo. **Importante**: Este campo deve ser usado apenas para fins de desenvolvimento/depuração. O estado da conexão é atualizado somente nos dispositivos que usam MQTT ou AMQP. Além disso, ele se baseia nos pings do nível de protocolo (pings MQTT ou AMQP) e pode ter um atraso máximo de apenas cinco minutos. Por esses motivos, pode haver falsos positivos, como dispositivos relatados como conectados, mas que estão desconectados. |
| connectionStateUpdatedTime |somente leitura |Um indicador temporal, mostrando a data e a hora da última atualização do estado da conexão. |
| lastActivityTime |somente leitura |Um indicador temporal, mostrando a data e hora da última vez em que o dispositivo se conectou, recebeu ou enviou uma mensagem. |

> [!NOTE]
> No momento, os SDKs do dispositivo não dão suporte ao uso dos caracteres `+` e `#` no **deviceId** e no **moduleId**.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md) descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.

* [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas e os comportamentos de limitação que se aplicam ao serviço do Hub IoT.

* [SDKs do dispositivo e do serviço IoT do Azure](iot-hub-devguide-sdks.md) lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.

* [Linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.

* O [suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IoT ao protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o Registro de identidade do Hub IoT, pode ser interessante ler os seguintes tópicos do Guia do desenvolvedor do Hub IoT:

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Usar dispositivos gêmeos para sincronizar o estado e as configurações](iot-hub-devguide-device-twins.md)

* [Invocar um método direto em um dispositivo](iot-hub-devguide-direct-methods.md)

* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o tutorial do Hub IoT a seguir:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-dotnet.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](https://azure.microsoft.com/documentation/services/iot-dps)

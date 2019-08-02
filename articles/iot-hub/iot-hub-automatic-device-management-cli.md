---
title: Gerenciamento automático de dispositivos em escala com o Azure IoT Hub (CLI) | Microsoft Docs
description: Use o gerenciamento automático de dispositivo de IoT Hub do Azure para atribuir uma configuração para vários dispositivos de IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: d57dbbdd7614d09d52fef0f613c43d4ca1d08136
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485859"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-cli"></a>Gerenciamento automático de dispositivo do IoT em escala usando a CLI do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Gerenciamento automático de dispositivos no IoT Hub do Azure automatiza muitas das tarefas complexas e repetitivas de gerenciamento de frotas de dispositivo grandes. Com o gerenciamento automático de dispositivo, um conjunto de dispositivos com base em suas propriedades de destino, definir uma configuração desejada e, em seguida, permitir que o IoT Hub atualizar dispositivos quando eles entrarem no escopo. Esta atualização é feita usando um _configuração de dispositivo automático_, que permite que você resumir conclusão e conformidade, mescla de identificador e conflitos e distribuir as configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Propriedades relatadas do dispositivo automático gerenciamento funciona atualizando um conjunto de dispositivos gêmeos com as propriedades desejadas e um resumo com base no dispositivo gêmeo de emissão de relatórios.  Ele introduz uma nova classe e um documento JSON chamado um *configuração* que tem três partes:

* A **condição de destino** define o escopo de dispositivos gêmeos a serem atualizados. A condição de destino é especificada como uma consulta em marcas de dispositivos gêmeos e/ou propriedades reportadas.

* O **conteúdo de destino** define as propriedades desejadas para serem adicionadas ou atualizadas nos dispositivos gêmeos de destino. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterado.

* As **métricas** definem as contagens de resumos de vários estados de configuração como **êxito**, **em andamento**, e **erro**. Métricas personalizadas são especificadas como consultas nas propriedades reportados do dispositivo gêmeo.  As métricas do sistema são as métricas padrão que medem o status de atualização de gêmeos, como o número de dispositivos gêmeos que são o destino e o número de gêmeos foram atualizados com êxito.

Configurações de dispositivo automático executam pela primeira vez logo após a configuração é criada e, em seguida, em intervalos de cinco minutos. Consultas de métricas executada cada vez que a configuração de dispositivo automático é executado.

## <a name="cli-prerequisites"></a>Pré-requisitos da CLI

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure. 
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.24 ou superior. Use `az –-version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. 
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementar dispositivos gêmeos para configurar dispositivos

As configurações automáticas de dispositivo requerem o uso de dispositivos gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Consulte [Compreender e usar gêmeos de dispositivos no Hub IoT](iot-hub-devguide-device-twins.md) para obter orientações sobre o uso de gêmeos de dispositivos.

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma configuração, você deve especificar quais dispositivos você deseja afetar. O Azure IoT Hub identifica os dispositivos usando marcas no dispositivo gêmeo. Cada dispositivo pode ter várias marcações e você pode defini-las que qualquer modo que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em locais diferentes, adicione as seguintes marcas para um dispositivo gêmeo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definir as métricas e o conteúdo de destino

O conteúdo de destino e a métrica de consultas são especificadas como propriedades relatadas para medir e propriedades desejadas ao conjunto de documentos JSON que descrevem o dispositivo gêmeos.  Para criar uma configuração de dispositivo automática usando a CLI do Azure, salve o conteúdo de destino e as métricas localmente como arquivos .txt. Você pode usar os caminhos de arquivo em uma seção posterior quando você executar o comando para aplicar a configuração para seu dispositivo.

A seguir é apresentando um exemplo de conteúdo de destino básico:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

A seguir estão alguns exemplos de consultas de métricas:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Criar uma configuração

Você configura dispositivos de destino criando uma configuração que consiste de métricas e conteúdo de destino. 

Use o comando a seguir para criar uma configuração:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - O nome da configuração que será criada no Hub IoT. Dê um nome exclusivo à sua configuração de até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

* --**labels** - Adicione rótulos para ajudar a rastrear a configuração. Rótulos são pares de Nome e Valor que descrevem a implantação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`

* --**content** - JSON em linha ou caminho de arquivo para o conteúdo de destino a ser configurado como propriedades desejadas do gêmeo. 

* --**hub-name** - Nome do Hub IoT no qual a configuração será criada. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

* --**target-condition** - Insira uma condição de destino para determinar quais dispositivos serão segmentados com essa configuração. A condição é baseada nas marcações do dispositivo gêmeo ou nas propriedades desejadas do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`. 

* --**priority** - Um inteiro positivo. No caso de duas ou mais configurações a serem direcionadas ao mesmo dispositivo, será aplicada a configuração com o maior valor numérico para Prioridade.

* --**metrics** - Caminho para as consultas de métricas. As métricas fornecem contagens resumidas de vários estados em que um dispositivo pode relatar novamente após a aplicação de conteúdo da configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas. 

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Use o comando a seguir para exibir o conteúdo de uma configuração:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a configuração na janela de comando. A propriedade **Metrics** lista uma contagem para cada métrica avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de dispositivos gêmeos no Hub IoT que correspondem à condição de direcionamento.

* **appliedCount** - Uma métrica de sistema especifica o número de dispositivos que tiveram o conteúdo de destino aplicado.

* **Sua métrica personalizada** -qualquer métricas que você definiu são métricas de usuário.

É possível mostrar uma lista de IDs de dispositivos ou objetos para cada uma das métricas usando o comando a seguir:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - O nome da implantação que existe no hub IoT.

* --**metric-id** - O nome da métrica para a qual você deseja ver a lista de códigos de dispositivos, por exemplo `appliedCount`.

* --**hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --**metric-type** - O tipo de métrica pode ser `system` ou `user`.  Métricas de sistema são `targetedCount` e `appliedCount`. Todas as outras métricas são métricas do usuário.

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um dispositivo gêmeo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa configuração tiver a prioridade mais alta para o dispositivo gêmeo, essa configuração será aplicada ao dispositivo gêmeo. 

* Se um dispositivo gêmeo não atende mais à condição de destino, as definições da configuração serão removidas e o dispositivo gêmeo será modificada pela próxima configuração de prioridade mais alta. 

* Se um dispositivo gêmeo que executa essa configuração no momento não atende à condição de destino e não atende a condição destino de quaisquer outras configurações, as definições da configuração serão removidas e nenhuma outra alteração será feita no dispositivo gêmeo. 

Use o comando a seguir para atualizar uma configuração:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --**set** - Atualize uma propriedade na configuração. Você pode atualizar as seguintes propriedades:

    * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`

    * rótulos 

    * prioridade

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeo assume sua próxima configuração de prioridade mais alta. Se os dispositivos gêmeos não atendem a condição destino de qualquer outra configuração, não serão aplicadas outras configurações. 

Use o comando a seguir para excluir uma configuração:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar e monitorar dispositivos de IoT em escala. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerencie suas identidades de dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)

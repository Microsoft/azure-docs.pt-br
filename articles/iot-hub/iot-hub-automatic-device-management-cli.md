---
title: Gerenciamento automático de dispositivos em escala com o Hub IoT do Azure (CLI) | Microsoft Docs
description: Usar configurações automáticas do Hub IoT do Azure para gerenciar vários dispositivos ou módulos de IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 0b8b499613f8234f449e6d72f6ed6ec1f2f21287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545405"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Gerenciamento automático de módulos e dispositivos IoT usando a CLI do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

O gerenciamento automático de dispositivo no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos. Com o gerenciamento automático de dispositivo, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que entrem no escopo. Essa atualização é feita usando uma _configuração automática de dispositivo_ ou _configuração automática de módulo_, que permite resumir a conclusão e a conformidade, tratar a mesclagem e os conflitos e implementar configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

O gerenciamento automático de dispositivo trabalha atualizando um conjunto de dispositivos gêmeos ou módulos gêmeos com as propriedades desejadas e informando um resumo com base em propriedades reportadas por gêmeos.  Ele apresenta uma nova classe e um documento JSON denominado *Configuração* que tem três partes:

* A **condição de destino** define o escopo de dispositivos gêmeos ou módulos gêmeos a serem atualizados. A condição de destino é especificada como uma consulta em marcas de dispositivos gêmeos e/ou propriedades reportadas.

* O **conteúdo de destino** define as propriedades desejadas para serem adicionadas ou atualizadas nos dispositivos gêmeos ou módulos gêmeos de destino. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterado.

* As **métricas** definem as contagens de resumos de vários estados de configuração como **êxito**, **em andamento**, e **erro**. Métricas personalizadas são especificadas como consultas nas propriedades reportadas do gêmeo.  As métricas do sistema são métricas padrão que medem o status de atualização do gêmeo, como o número de gêmeos definidos como destino e o número de gêmeos que foram atualizados com êxito.

As configurações automáticas são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas de métricas são executadas cada vez que a configuração automática é executada.

## <a name="cli-prerequisites"></a>Pré-requisitos da CLI

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure. 

* [CLI do Azure](/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.70 ou superior. Use `az –-version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. 

* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementar gêmeos

As configurações automáticas de dispositivo requerem o uso de dispositivos gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](iot-hub-devguide-device-twins.md).

As configurações automáticas de módulo requerem o uso de módulos gêmeos para sincronizar o estado entre a nuvem e os módulos. Para obter mais informações, confira [Noções básicas e uso de módulos gêmeos no Hub IoT](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Usar marcas para direcionar gêmeos

Antes de criar uma configuração, especifique quais dispositivos ou módulos você deseja afetar. O Hub IoT do Azure identifica dispositivos e por meio do uso de marcas no dispositivo gêmeo e identifica os módulos que usam marcas no módulo gêmeo. Cada dispositivo ou módulo pode ter várias marcas e você pode defini-las de qualquer modo que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em locais diferentes, adicione as seguintes marcas a um dispositivo gêmeo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definir as métricas e o conteúdo de destino

As consultas de conteúdo e métrica de destino são especificadas como documentos JSON que descrevem as propriedades desejadas do dispositivo ou do módulo "..." para definir e relatar Propriedades a serem medidas.  Para criar uma configuração automática usando CLI do Azure, salve o conteúdo e as métricas de destino localmente como arquivos. txt. Você usará os caminhos de arquivo em uma seção posterior quando executar o comando para aplicar a configuração ao seu dispositivo.

Aqui está um exemplo de conteúdo de destino básico para uma configuração de dispositivo automática:

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

As configurações automáticas de módulo se comportam de forma semelhante, mas você tem como destino `moduleContent` `deviceContent` .

```json
{
  "content": {
    "moduleContent": {
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
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

As consultas de métricas para módulos também são semelhantes às consultas de dispositivos, mas você seleciona para `moduleId` de `devices.modules` . Por exemplo: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Criar uma configuração

Você configura dispositivos de destino criando uma configuração que consiste de métricas e conteúdo de destino. 

Use o comando a seguir para criar uma configuração:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - O nome da configuração que será criada no Hub IoT. Dê um nome exclusivo à sua configuração de até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

* --**labels** - Adicione rótulos para ajudar a rastrear a configuração. Rótulos são pares de Nome e Valor que descrevem a implantação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`

* --**content** - JSON em linha ou caminho de arquivo para o conteúdo de destino a ser configurado como propriedades desejadas do gêmeo. 

* --**hub-name** - Nome do Hub IoT no qual a configuração será criada. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

* --**destino-condição** -Insira uma condição de destino para determinar quais dispositivos ou módulos serão direcionados a essa configuração. Para configuração automática de dispositivo, a condição é baseada nas marcas de dispositivo ou nas propriedades de dispositivo de dispositivos de entrelaçamento e deve corresponder ao formato de expressão. Por exemplo, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`. Para a configuração automática de módulo, a condição é baseada nas marcas de módulo ou propriedades de módulo de entrelaçamento. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**Priority** -um inteiro positivo. Caso duas ou mais configurações sejam destinadas ao mesmo dispositivo ou módulo, a configuração com o maior valor numérico para prioridade será aplicada.

* --**metrics** - Caminho para as consultas de métricas. As métricas fornecem contagens de resumo dos vários estados que um dispositivo ou módulo pode relatar após aplicar o conteúdo da configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas. 

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Use o comando a seguir para exibir o conteúdo de uma configuração:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a configuração na janela de comando.  A propriedade **Metrics** lista uma contagem para cada métrica avaliada por cada hub:

* **targetedCount** -uma métrica do sistema que especifica o número de dispositivos gêmeos ou do módulo gêmeos no Hub IOT que correspondem à condição de destino.

* **appliedCount** -uma métrica do sistema especifica o número de dispositivos ou módulos que tiveram o conteúdo de destino aplicado.

* **Sua métrica personalizada** -quaisquer métricas que você definiu são métricas de usuário.

Você pode mostrar uma lista de IDs de dispositivo, IDs de módulo ou objetos para cada uma das métricas usando o seguinte comando:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - O nome da implantação que existe no hub IoT.

* --**Metric-ID** -o nome da métrica para a qual você deseja ver a lista de IDs de dispositivo ou IDs de módulo, por exemplo `appliedCount` .

* --**Hub-nome** -nome do Hub IOT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --**metric-type** - O tipo de métrica pode ser `system` ou `user`.  Métricas de sistema são `targetedCount` e `appliedCount`. Todas as outras métricas são métricas do usuário.

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um gêmeo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa configuração tenha a prioridade mais alta para o gêmeo, essa configuração será aplicada. 

* Se um gêmeo que executa essa configuração no momento não atender mais à condição de destino, as definições da configuração serão removidas e o gêmeo será modificado pela próxima configuração de prioridade mais alta. 

* Se um gêmeo que executa essa configuração no momento não atende à condição de destino e não atende a condição destino de outras configurações, as definições da configuração serão removidas e nenhuma outra alteração será feita no gêmeo. 

Use o comando a seguir para atualizar uma configuração:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --**set** - Atualize uma propriedade na configuração. Você pode atualizar as seguintes propriedades:

    * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`

    * rótulos 

    * priority

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeos ou módulo gêmeos assume sua próxima configuração de prioridade mais alta. Se gêmeos não atender à condição de destino de nenhuma outra configuração, nenhuma outra definição será aplicada. 

Use o comando a seguir para excluir uma configuração:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar e monitorar dispositivos IoT em escala. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerencie suas identidades de dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Monitorar o Hub IoT](monitor-iot-hub.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/quickstart-linux.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/index.yml)
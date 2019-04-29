---
title: Criar implantações automáticas do portal da linha de comando - Azure IoT Edge | Microsoft Docs
description: Usar a extensão IoT para a CLI do Azure para criar implantações automáticas para grupos dos dispositivos de IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f93d9eaefe18dd012a639cd26636b56b9eb09249
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595166"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implantar e monitorar módulos do IoT Edge em escala usando a CLI do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

O Azure IoT Edge permite mover a análise para a borda e fornece uma interface de nuvem, de modo que você possa gerenciar e monitorar seus dispositivos IoT Edge remotamente. A capacidade de gerenciar dispositivos remotamente é cada vez mais importante, à medida que as soluções da Internet das Coisas ficam cada vez maiores e mais complexas. O Azure IoT Edge foi projetado para dar suporte a suas metas de negócios, independentemente de quantos dispositivos você adicionar.

Gerencie dispositivos individuais e implante módulos neles um de cada vez. No entanto, se você quiser fazer alterações em dispositivos em grande escala, você pode criar uma **implantação automática de IoT Edge**, que faz parte do gerenciamento de dispositivo automático no Hub IoT. Implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos ao mesmo tempo, acompanhar o status e a integridade dos módulos e fazer alterações, quando necessário. 

Neste artigo, você configura a CLI do Azure e a extensão IoT. Em seguida, você aprenderá como implantar módulos em um conjunto de dispositivos do IoT Edge e monitorar o progresso usando os comandos da CLI disponíveis.

## <a name="cli-prerequisites"></a>Pré-requisitos da CLI

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure. 
* [Dispositivos do IoT Edge](how-to-register-device-cli.md) com o tempo de execução do IoT Edge instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.24 ou superior. Use `az –-version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. 
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

Para implantar módulos usando a CLI do Azure, salve o manifesto de implantação localmente como um arquivo .txt. Você usará o caminho do arquivo na próxima seção quando executar o comando para aplicar a configuração ao dispositivo. 

A seguir, é apresentado um manifesto básico de implantação com um módulo como um exemplo:

   ```json
   {
        "content": {
         "modulesContent": {
           "$edgeAgent": {
             "properties.desired": {
               "schemaVersion": "1.0",
               "runtime": {
                 "type": "docker",
                 "settings": {
                   "minDockerVersion": "v1.25",
                   "loggingOptions": "",
                   "registryCredentials": {
                     "registryName": {
                       "username": "",
                       "password": "",
                       "address": ""
                     }
                   }
               },
               "systemModules": {
                 "edgeAgent": {
                   "type": "docker",
                   "settings": {
                     "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                     "createOptions": "{}"
                   }
                 },
                 "edgeHub": {
                   "type": "docker",
                   "status": "running",
                   "restartPolicy": "always",
                   "settings": {
                     "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                     "createOptions": "{}"
                   }
                 }
               },
               "modules": {
                 "tempSensor": {
                   "version": "1.0",
                   "type": "docker",
                   "status": "running",
                   "restartPolicy": "always",
                   "settings": {
                     "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                     "createOptions": "{}"
                   }
                 }
               }
             }
           },
           "$edgeHub": {
             "properties.desired": {
               "schemaVersion": "1.0",
               "routes": {
                   "route": "FROM /* INTO $upstream"
               },
               "storeAndForwardConfiguration": {
                 "timeToLiveSecs": 7200
               }
             }
           },
           "tempSensor": {
             "properties.desired": {}
           }
         }
       }
   }
   ```

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias marcações e você pode defini-las que qualquer modo que faça sentido para sua solução. Por exemplo, se você gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes marcações a um dispositivo:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Para obter mais informações sobre tags e gêmeos de dispositivos, consulte [Entender e usar gêmeos de dispositivos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

Você implanta módulos nos dispositivos de destino criando uma implantação que consiste no manifesto de implantação, bem como outros parâmetros. 

Use o comando a seguir para criar uma implantação:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
   ```

* **--deployment-id** - O nome da implantação que será criada no Hub IoT. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
* **--hub-name** - Nome do hub IoT no qual a implantação será criada. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.
* **--content** - Caminho do arquivo para o manifesto de implantação JSON. 
* **--labels** - Adicione rótulos para ajudar a acompanhar as implantações. Rótulos são pares de Nome e Valor que descrevem a implantação. Os rótulos usam a formatação JSON para os nomes e valores. Por exemplo, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - Insira uma condição de destino para determinar quais dispositivos serão segmentados com essa implantação. A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** - Um inteiro positivo. No caso de duas ou mais implantações serem direcionadas ao mesmo dispositivo, será aplicada a implantação com o maior valor numérico para Prioridade.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Use o comando a seguir para exibir o conteúdo de uma implantação:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a implantação na janela de comando. A propriedade **Metrics** lista uma contagem para cada métrica avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de dispositivos gêmeos no Hub IoT que correspondem à condição de direcionamento.
* **appliedCount** - Uma métrica de sistema especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos módulos gêmeos no Hub IoT.
* **reportedSuccessfulCount** - Uma métrica de dispositivo que especifica o número de dispositivos do Edge no êxito do relatório de implantação do tempo de execução do cliente do IoT Edge.
* **reportedFailedCount** - Uma métrica de dispositivo que especifica o número de dispositivos do Edge na falha do relatório de implantação do tempo de execução do cliente do IoT Edge.

É possível mostrar uma lista de IDs de dispositivos ou objetos para cada uma das métricas usando o comando a seguir:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--metric-id** - O nome da métrica para a qual você deseja ver a lista de códigos de dispositivos, por exemplo `reportedFailedCount`
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova. 

Use o comando a seguir para atualizar uma implantação:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`
* **--set** - Atualize uma propriedade na implantação. Você pode atualizar as seguintes propriedades:
  * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`
  * rótulos 
  * prioridade


## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos usam sua próxima implantação com a prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída. 

Use o comando a seguir para excluir uma implantação:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Implantando módulos em dispositivos Edge](module-deployment-monitoring.md).

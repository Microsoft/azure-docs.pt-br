---
title: Monitorar implantações do IoT Edge - Azure IoT Edge
description: Monitoramento de alto nível, incluindo propriedades relatadas de edgeHub e edgeAgent e métricas de implantação automática.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 45df700cc1772250e42a0e007fb4ea91b49471ba
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684196"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorar implantações do IoT Edge

O Azure IoT Edge fornece relatórios que permitem monitorar informações em tempo real sobre os módulos implantados nos dispositivos do IoT Edge. O serviço do Hub IoT recupera o status dos dispositivos e os disponibiliza para o operador. O monitoramento também é importante para [implantações feitas em escala](module-deployment-monitoring.md) que incluem implantações automáticas e implantações em camadas.

Os dispositivos e os módulos têm dados semelhantes, como conectividade, para que os valores sejam obtidos de acordo com a ID do dispositivo ou a ID do módulo.

O serviço de Hub IoT coleta dados relatados pelo dispositivo e pelo módulo gêmeo e fornece contagens dos vários estados que os dispositivos podem ter. O serviço do Hub IoT organiza esses dados em quatro grupos de métricas:

| Type | Descrição |
| --- | ---|
| Direcionado | Mostra os dispositivos do IoT Edge que correspondem à condição de direcionamento da implantação. |
| Aplicado | Mostra os dispositivos do IoT Edge direcionados que não foram direcionados por outra implantação de maior prioridade. |
| Relatório de sucesso | Mostra os dispositivos do IoT Edge que relataram que os módulos foram implantados com sucesso. |
| Relatório de falha | Mostra os dispositivos do IoT Edge que relataram que um ou mais módulos não foram implantados com sucesso. Para investigar o erro mais detalhadamente, conecte-se remotamente a esses dispositivos e exiba os arquivos de log. |

O serviço de Hub IoT disponibiliza esses dados para você monitorar no portal do Azure e na CLI do Azure.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorar uma implantação no portal do Azure

Para exibir os detalhes de uma implantação e monitorar os dispositivos que a executam, use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecione **IoT Edge** no menu do painel esquerdo.
1. Selecione a guia **Implantações do IoT Edge**.
1. Inspecione a lista de implantação.  Para cada implantação, você pode exibir os seguintes detalhes:

    | Coluna | Descrição |
    | --- | --- |
    | ID | O nome da implantação. |
    | Type | O tipo de implantação, **Implantação** ou **Implantação em camadas**. |
    | Condição de destino | A marcação usada para definir os dispositivos direcionados. |
    | Prioridade | O número de prioridade atribuído à implantação. |
    | Métricas do sistema | O número de dispositivos gêmeos no Hub IoT que correspondem à condição de direcionamento. **Aplicado** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado ao módulo gêmeo no Hub IoT. |
    | Métricas do dispositivo | O número de dispositivos do IoT Edge que relatam sucesso ou erros no runtime do cliente IoT Edge. |
    | Métricas personalizadas | O número de dispositivos do IoT Edge que relatam dados para as métricas que você definiu para a implantação. |
    | Hora de criação | O carimbo de data/hora de quando a implantação foi criada. Esse carimbo de data/hora é usado para desempate quando duas implantações têm a mesma prioridade. |

1. Selecione a implantação que deseja monitorar.  
1. Na página **Detalhes da implantação**, role para baixo até a seção inferior e selecione a guia **Condição de destino**. Selecione **Exibir** para listar os dispositivos que correspondem à condição de destino. Você pode alterar a condição e também a **Prioridade**. Selecione **Salvar** se você fez alterações.

   ![Exibir dispositivos direcionados para uma implantação](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selecione a guia **Métricas**. Se você escolher uma métrica na lista suspensa **Selecionar métrica**, um botão **Exibir** aparecerá para mostrar os resultados. Você também pode selecionar **Editar métricas** para ajustar os critérios para as métricas personalizadas que você definiu. Selecione **Salvar** se você fez alterações.

   ![Exibir métrica de uma implantação](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Para fazer alterações na implantação, confira [Modificar uma implantação](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorar a implantação com a CLI do Azure

Use o comando [az IoT Edge deployment show](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show) para exibir os detalhes de uma única implantação:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

O comando deployment show usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT. Parâmetro obrigatório.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a implantação na janela de comando.  A propriedade **Metrics** lista uma contagem para cada métrica avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de dispositivos gêmeos no Hub IoT que correspondem à condição de direcionamento.
* **appliedCount** - Uma métrica de sistema especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos módulos gêmeos no Hub IoT.
* **reportedSuccessfulCount** - Uma métrica de dispositivo que especifica o número de dispositivos do IoT Edge no relatório de sucesso da implantação do runtime do cliente do IoT Edge.
* **reportedFailedCount** - Uma métrica de dispositivo que especifica o número de dispositivos do IoT Edge no relatório de falha da implantação do runtime do cliente do IoT Edge.

Você pode mostrar uma lista de IDs de dispositivo ou objetos para cada uma das métricas com o comando [az IoT Edge deployment show-metric](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show-metric):

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

O comando deployment show-metric usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--metric-id** - O nome da métrica para a qual você deseja ver a lista de códigos de dispositivos, por exemplo `reportedFailedCount`.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

Para fazer alterações na implantação, confira [Modificar uma implantação](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Próximas etapas

Saiba como [monitorar o módulo gêmeos](how-to-monitor-module-twins.md), principalmente os módulos agente de IoT Edge e IOT Edge tempo de execução de Hub, para a conectividade e a integridade de suas implantações de IOT Edge.

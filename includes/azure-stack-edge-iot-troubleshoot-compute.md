---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831529"
---
Use as respostas de tempo de execução do agente de IoT Edge para solucionar erros relacionados a computação. Aqui está uma lista das possíveis respostas:

* 200 - OK
* 400 - A configuração de implantação está malformada ou inválida.
* 417-o dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão do esquema na configuração de implantação é inválida.
* 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
* 500 – ocorreu um erro no runtime do IoT Edge.

Para obter mais informações, consulte [IOT Edge Agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).

O erro a seguir está relacionado ao serviço de IoT Edge em seu Azure Stack Edge pro<!--/ Data Box Gateway--> dispositivo.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Os módulos de computação têm status desconhecido e não podem ser usados

#### <a name="error-description"></a>Descrição do erro

Todos os módulos no dispositivo mostram o status desconhecido e não podem ser usados. O status desconhecido persiste através de uma reinicialização.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solução sugerida

Exclua o serviço IoT Edge e reimplante os módulos. Para obter mais informações, consulte [remover IOT Edge Service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).

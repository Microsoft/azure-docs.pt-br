---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750888"
---
Use as respostas de tempo de execução do agente de IoT Edge para solucionar erros relacionados a computação. Aqui está uma lista das possíveis respostas:

* 200 - OK
* 400 - A configuração de implantação está malformada ou inválida.
* 417-o dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão do esquema na configuração de implantação é inválida.
* 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
* 500 – ocorreu um erro no runtime do IoT Edge.

Para obter mais informações, consulte [IOT Edge Agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

O erro a seguir está relacionado ao serviço de IoT Edge em seu Azure Stack Edge pro<!--/ Data Box Gateway--> dispositivo.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Os módulos de computação têm status desconhecido e não podem ser usados

#### <a name="error-description"></a>Descrição do erro

Todos os módulos no dispositivo mostram o status desconhecido e não podem ser usados. O status desconhecido persiste através de uma reinicialização.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solução sugerida

Exclua o serviço IoT Edge e reimplante os módulos. Para obter mais informações, consulte [remover IOT Edge Service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
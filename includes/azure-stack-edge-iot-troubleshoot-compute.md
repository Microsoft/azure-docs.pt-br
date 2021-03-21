---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/02/2021
ms.openlocfilehash: 57415ec76a3e8d9fc3c160b47668d3419ff6ea5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622027"
---
Use as respostas de tempo de execução do agente de IoT Edge para solucionar erros relacionados a computação. Aqui está uma lista das possíveis respostas:

* 200 - OK
* 400 - A configuração de implantação está malformada ou inválida.
* 417-o dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão do esquema na configuração de implantação é inválida.
* 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
* 500 – ocorreu um erro no runtime do IoT Edge.

Para obter mais informações, consulte [IOT Edge Agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

O erro a seguir está relacionado ao serviço de IoT Edge em seu dispositivo do Azure Stack Edge pro.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Os módulos de computação têm status desconhecido e não podem ser usados

#### <a name="error-description"></a>Descrição do erro

Todos os módulos no dispositivo mostram o status desconhecido e não podem ser usados. O status desconhecido persiste através de uma reinicialização.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solução sugerida

Exclua o serviço IoT Edge e reimplante os módulos. Para obter mais informações, consulte [remover IOT Edge Service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>Os módulos são mostrados como em execução, mas não estão funcionando

#### <a name="error-description"></a>Descrição do erro

O status de tempo de execução do módulo é mostrado como executando, mas os resultados esperados não são vistos. 

Essa condição pode ser devido a um problema com a configuração de rota de módulo que não está funcionando ou `edgehub` não está Roteando mensagens conforme o esperado. Você pode verificar os `edgehub` logs. Se você vir que há erros como a falha ao se conectar ao serviço do Hub IoT, o motivo mais comum são os problemas de conectividade. Os problemas de conectividade podem ocorrer porque a porta AMPQ usada como uma porta padrão pelo serviço de Hub IoT para comunicação está bloqueada ou o servidor proxy da Web está bloqueando essas mensagens.

#### <a name="suggested-solution"></a>Solução sugerida

Execute as seguintes etapas:
1. Para resolver o erro, vá para o recurso de Hub IoT para seu dispositivo e, em seguida, selecione o dispositivo de borda. 
1. Vá para **definir módulos > configurações de tempo de execução**. 
1. Adicione a `Upstream protocol` variável de ambiente e atribua a ela um valor de `AMQPWS` . As mensagens configuradas nesse caso são enviadas por WebSockets pela porta 443.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>Os módulos são mostrados como em execução, mas não têm um IP atribuído

#### <a name="error-description"></a>Descrição do erro

O status de tempo de execução do módulo é mostrado como executando, mas o aplicativo em contêiner não tem um IP atribuído. 

Essa condição é porque o intervalo de IPs que você forneceu para IPs de serviço externo kubernetes não é suficiente. Você precisa estender esse intervalo para garantir que cada contêiner ou VM implantado seja abordado.

#### <a name="suggested-solution"></a>Solução sugerida

Na interface do usuário da Web local do seu dispositivo, execute as seguintes etapas:
1. Vá para a página de **computação** . Selecione a porta para a qual você habilitou a rede de computação. 
1. Insira um intervalo estático e contíguo de IPs para **IPS de serviço externo kubernetes**. Você precisa de 1 IP para o `edgehub` serviço. Além disso, você precisa de um IP para cada módulo IoT Edge e para cada VM que será implantada. 
1. Escolha **Aplicar**. O intervalo de IP alterado deve entrar em vigor imediatamente.

Para obter mais informações, consulte [alterar IPS de serviço externo para contêineres](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#change-external-service-ips-for-containers).
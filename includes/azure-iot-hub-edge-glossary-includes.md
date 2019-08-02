---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172041"
---
## <a name="automatic-device-management"></a>Gerenciamento de Dispositivo Automático
O gerenciamento automático do dispositivo no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Com o gerenciamento automático do dispositivo, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que entrem no escopo.  Consiste em [configurações de dispositivo automáticas](../articles/iot-hub/iot-hub-auto-device-config.md) e [implantações automáticas do IoT Edge](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
O Azure IoT Edge permite a implantação orientada a nuvem do código específico da solução e de serviços do Azure para dispositivos locais. Os dispositivos IoT Edge podem agregar dados de outros dispositivos para realizar a computação e a análise antes de os dados serem enviados para a nuvem. Para obter mais informações, consulte[ Borda do IoT do Azure](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agente do IoT Edge
A parte do tempo de execução do IoT Edge responsável por implantar e monitorar módulos.

## <a name="iot-edge-device"></a>Dispositivo IoT Edge
Os dispositivos IoT Edge têm o runtime do IoT Edge instalado e são marcados como **dispositivo IoT Edge** nos detalhes do dispositivo. Saiba como [implantar o Azure IoT Edge em um dispositivo simulado no Linux – versão prévia](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Implantação automática do IoT Edge
Uma implantação automática do IoT Edge configura um conjunto de destino de dispositivos IoT Edge para executar um conjunto de módulos do IoT Edge. Cada implantação continuamente garante que todos os dispositivos que correspondem à sua condição de destino estejam executando o conjunto de módulos especificado, mesmo quando novos dispositivos são criados ou modificados para corresponder à condição de destino. Cada dispositivo IoT Edge recebe apenas a implantação de prioridade mais alta cuja condição de destino ele atende. Saiba mais sobre [implantação automática do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifesto de implantação do IoT Edge
Um documento Json contendo as informações a serem copiadas em um ou mais módulos de dispositivos da IoT Edge para implantar um conjunto de módulos, rotas e propriedades desejadas do módulo associado.

## <a name="iot-edge-gateway-device"></a>Dispositivo de gateway IoT Edge
Um dispositivo IoT Edge com dispositivo downstream. O dispositivo downstream pode ser um dispositivo IoT Edge ou não.

## <a name="iot-edge-hub"></a>Hub do IoT Edge
A parte do tempo de execução do IoT Edge responsável pelas comunicações de módulo para módulo e comunicações upstream (para o Hub IoT) e downstream (para fora do Hub IoT). 

## <a name="iot-edge-leaf-device"></a>Dispositivo de folha IoT Edge
Um dispositivo IoT Edge sem nenhum dispositivo downstream. 

## <a name="iot-edge-module"></a>Módulo do IoT Edge
Um módulo do IoT Edge é um contêiner do Docker que você pode implantar para dispositivos IoT Edge. Ele executa uma tarefa específica, como a ingestão de uma mensagem de um dispositivo, a transformação de uma mensagem ou o envio de uma mensagem para um Hub IoT. Ele se comunica com outros módulos e envia dados para o tempo de execução do IoT Edge. [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identidade do módulo do IoT Edge
Um registro no Registro de identidade do módulo do Hub IoT detalhando as credenciais de segurança e existência a serem usadas por um módulo para a autenticação com um hub de borda ou Hub IoT.

## <a name="iot-edge-module-image"></a>Imagem do módulo do IoT Edge
A imagem do docker que é usada pelo tempo de execução do IoT Edge para instanciar as instâncias do módulo.

## <a name="iot-edge-module-twin"></a>Módulo gêmeo do IoT Edge
Um documento Json persistido no Hub IoT que armazena as informações de estado para uma instância de módulo.

## <a name="iot-edge-priority"></a>Prioridade do IoT Edge
Quando duas implantações do IoT Edge visam o mesmo dispositivo, a implantação com a prioridade mais alta é aplicada. Se duas implantações tiverem a mesma prioridade, a implantação com a criação mais recente será aplicada. Saiba mais sobre a [prioridade](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Tempo de execução do IoT Edge
O tempo de execução do IoT Edge inclui tudo que a Microsoft distribui para ser instalado em um dispositivo IoT Edge. Inclui o agente Edge, o hub Edge e o daemon de segurança IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Módulos de definição do IoT Edge para um único dispositivo
Uma operação que copia o conteúdo de um manifesto do IoT Edge no módulo gêmeo de um dispositivo. A API subjacente é uma “aplicar configuração” genérica, que simplesmente utiliza o manifesto do IoT Edge como uma entrada.

## <a name="iot-edge-target-condition"></a>Condição de destino do IoT Edge
Em uma implantação do IoT Edge, a condição de destino é qualquer condição booleana nas tags de gêmeos do dispositivo para selecionar os dispositivos de destino da implantação, por exemplo, **tag.environment = prod**. A condição de destino é avaliada continuamente para incluir quaisquer novos dispositivos que atendem aos requisitos ou remover os dispositivos que não atendem mais. Saiba mais sobre a [condição de destino](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)
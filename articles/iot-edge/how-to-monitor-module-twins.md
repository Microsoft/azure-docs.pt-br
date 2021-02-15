---
title: Gêmeos de módulo de monitor-Azure IoT Edge
description: Como interpretar o dispositivo gêmeos e o módulo gêmeos para determinar a conectividade e a integridade.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a11d3a9a972188af4cf8f054349da98d69691a3
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876151"
---
# <a name="monitor-module-twins"></a>Monitorar módulos gêmeos

Módulo gêmeos no Hub IoT do Azure habilite o monitoramento da conectividade e da integridade de suas implantações de IoT Edge. O módulo gêmeos armazena informações úteis em seu hub IoT sobre o desempenho de seus módulos em execução. O [agente de IOT Edge](iot-edge-runtime.md#iot-edge-agent) e os módulos de tempo de execução de [IOT Edge Hub](iot-edge-runtime.md#iot-edge-hub) mantêm o módulo gêmeos e `$edgeAgent` `$edgeHub` , respectivamente:

* `$edgeAgent` contém dados de integridade e conectividade sobre os módulos de tempo de execução do IoT Edge Agent e IoT Edge Hub e seus módulos personalizados. O agente de IoT Edge é responsável por implantar os módulos, monitorá-los e relatar o status da conexão com o Hub IoT do Azure.
* `$edgeHub` contém dados sobre comunicações entre o Hub de IoT Edge em execução em um dispositivo e o Hub IoT do Azure. Isso inclui o processamento de mensagens de entrada de dispositivos downstream. IoT Edge Hub é responsável por processar as comunicações entre o Hub IoT do Azure e os dispositivos e módulos IoT Edge.

Os dados são organizados em metadados, marcas, juntamente com os conjuntos de propriedades desejados e relatados nas estruturas JSON do módulo gêmeos. As propriedades desejadas que você especificou em seu deployment.jsno arquivo são copiadas para o módulo gêmeos. O agente de IoT Edge e o Hub de IoT Edge atualizam as propriedades relatadas para seus módulos.

Da mesma forma, as propriedades desejadas especificadas para seus módulos personalizados na deployment.jsno arquivo são copiadas para seu módulo de cópia, mas sua solução é responsável por fornecer seus valores de propriedade relatados.

Este artigo descreve como examinar o módulo gêmeos no portal do Azure, CLI do Azure e no Visual Studio Code. Para obter informações sobre como monitorar os dispositivos que recebem as implantações, consulte [monitorar implantações de IOT Edge](how-to-monitor-iot-edge-deployments.md). Para obter uma visão geral do conceito de gêmeos de módulo, consulte [entender e usar o módulo gêmeos no Hub IOT](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> As propriedades relatadas de um módulo de tempo de execução podem estar obsoletas se um dispositivo IoT Edge for desconectado de seu hub IoT. Você pode [executar o ping](how-to-edgeagent-direct-method.md#ping) no `$edgeAgent` módulo para determinar se a conexão foi perdida.

## <a name="monitor-runtime-module-twins"></a>Monitorar gêmeos do módulo de tempo de execução

Para solucionar problemas de conectividade de implantação, examine o agente de IoT Edge e IoT Edge módulo de tempo de execução do Hub gêmeos e faça uma busca detalhada em outros módulos.

### <a name="monitor-iot-edge-agent-module-twin"></a>Monitorar o módulo IoT Edge do agente

O JSON a seguir mostra o `$edgeAgent` módulo em Visual Studio Code com a maioria das seções JSON recolhidas.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

O JSON pode ser descrito nas seções a seguir, começando na parte superior:

* Metadados-contém dados de conectividade. Curiosamente, o estado da conexão para o agente de IoT Edge está sempre em um estado desconectado: `"connectionState": "Disconnected"` . A razão pela qual o estado de conexão pertence às mensagens de D2C (dispositivo para nuvem) e o agente de IoT Edge não envia mensagens D2C.
* Propriedades – contém as `desired` `reported` subseções e.
* Propriedades. Desired-(mostrado recolhido) os valores de propriedade esperados definidos pelo operador no deployment.jsno arquivo.
* Properties. Reported-valores de propriedade mais recentes relatados pelo agente de IoT Edge.

As `properties.desired` seções e `properties.reported` têm uma estrutura semelhante e contêm metadados adicionais para o esquema, a versão e as informações de tempo de execução. Também está incluída a `modules` seção para todos os módulos personalizados (como `SimulatedTemperatureSensor` ) e a `systemModules` seção para `$edgeAgent` e os `$edgeHub` módulos de tempo de execução.

Ao comparar os valores de propriedade relatados com os valores desejados, você pode determinar discrepâncias e identificar desconexões que podem ajudá-lo a solucionar problemas. Ao fazer essas comparações, verifique o `$lastUpdated` valor relatado na `metadata` seção da propriedade que você está investigando.

As propriedades a seguir são importantes para examinar a solução de problemas:

* **ExitCode** -qualquer valor diferente de zero indica que o módulo parou com uma falha. No entanto, os códigos de erro 137 ou 143 serão usados se um módulo tiver sido intencionalmente definido como um status parado.

* **lastStartTimeUtc** -mostra a **data e hora** em que o contêiner foi iniciado pela última vez. Esse valor é 0001-01-01T00:00:00Z se o contêiner não tiver sido iniciado.

* **lastExitTimeUtc** -mostra a **data e hora** em que o contêiner foi concluído pela última vez. Esse valor é 0001-01-01T00:00:00Z se o contêiner estiver em execução e nunca tiver sido interrompido.

* **runtimeStatus** -pode ser um dos seguintes valores:

    | Valor | Descrição |
    | --- | --- |
    | unknown | Estado padrão até que a implantação seja criada. |
    | retirada | O módulo está agendado para iniciar, mas não está em execução no momento. Esse valor é útil para um módulo que está passando por alterações de estado na reinicialização. Quando um módulo com falha estiver aguardando a reinicialização durante o período de resfriamento, o módulo estará em um estado de retirada. |
    | executando | Indica que o módulo está em execução no momento. |
    | não íntegro | Indica que uma verificação de investigação de integridade falhou ou atingiu o tempo limite. |
    | interrompido | Indica que o módulo saiu com êxito (com um código de saída zero). |
    | falhou | Indica que o módulo foi encerrado com um código de saída de falha (diferente de zero). O módulo pode fazer a transição de volta para retirada desse Estado, dependendo da política de reinicialização em vigor. Esse Estado pode indicar que o módulo apresentou um erro irrecuperável. A falha ocorre quando o Microsoft Monitoring Agent (MMA) não pode mais Resuscitate o módulo, exigindo uma nova implantação. |

Consulte [Propriedades relatadas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-reported-properties) para obter detalhes.

### <a name="monitor-iot-edge-hub-module-twin"></a>Monitorar o módulo de Hub de IoT Edge e

O JSON a seguir mostra o `$edgeHub` módulo em Visual Studio Code com a maioria das seções JSON recolhidas.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

O JSON pode ser descrito nas seções a seguir, começando na parte superior:

* Metadados-contém dados de conectividade.

* Propriedades – contém as `desired` `reported` subseções e.
* Propriedades. Desired-(mostrado recolhido) os valores de propriedade esperados definidos pelo operador no deployment.jsno arquivo.
* Properties. Reported-valores de propriedade mais recentes relatados pelo Hub IoT Edge.

Se você estiver enfrentando problemas com seus dispositivos downstream, examinar esses dados seria um bom lugar para começar.

## <a name="monitor-custom-module-twins"></a>Monitorar gêmeos de módulo personalizado

As informações sobre a conectividade de seus módulos personalizados são mantidas no módulo do agente de IoT Edge. O módulo para o módulo personalizado é usado principalmente para manter os dados da solução. As propriedades desejadas que você definiu em seu deployment.jsno arquivo são refletidas no módulo e o módulo pode atualizar os valores de propriedade relatados conforme necessário.

Você pode usar sua linguagem de programação preferida com os [SDKs de dispositivo do Hub IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) para atualizar os valores de propriedade relatados no módulo "/", com base no código do aplicativo do seu módulo. O procedimento a seguir usa o SDK do Azure para .NET para fazer isso, usando o código do módulo [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) :

1. Crie uma instância do [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) com o método [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) .

1. Obtenha uma coleção das propriedades do módulo ' s ' com o método [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) .

1. Crie um ouvinte (passando um retorno de chamada) para detectar alterações nas propriedades desejadas com o método [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) .

1. No seu método de retorno de chamada, atualize as propriedades relatadas no módulo "atualizar" com o método [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) , passando um [entrelaçacollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) dos valores de propriedade que você deseja definir.

## <a name="access-the-module-twins"></a>Acessar o módulo gêmeos

Você pode examinar o JSON para o módulo gêmeos no Hub IoT do Azure, em Visual Studio Code e com CLI do Azure.

### <a name="monitor-in-azure-iot-hub"></a>Monitorar no Hub IoT do Azure

Para exibir o JSON para o módulo "...":

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecione **IoT Edge** no menu do painel esquerdo.
1. Na guia **dispositivos IOT Edge** , selecione a **ID do dispositivo** com os módulos que você deseja monitorar.
1. Selecione o nome do módulo na guia **módulos** e, em seguida, selecione **identidade do módulo** pressione a barra de menus superior.

  ![Selecione um módulo para exibir na portal do Azure](./media/how-to-monitor-module-twins/select-module-twin.png)

Se você vir a mensagem "não existe uma identidade de módulo para este módulo", esse erro indica que a solução de back-end não está mais disponível e que originalmente criou a identidade.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Monitorar gêmeos do módulo no Visual Studio Code

Para revisar e editar um módulo:

1. Se ainda não estiver instalado, instale a [extensão de ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code.
1. No **Gerenciador**, expanda o **Hub IOT do Azure**e expanda o dispositivo com o módulo que você deseja monitorar.
1. Clique com o botão direito do mouse no módulo e selecione **Editar módulo**. Um arquivo temporário do módulo "r" é baixado para o computador e exibido no Visual Studio Code.

  ![Obter um módulo para editar no Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Se você fizer alterações, selecione **Atualizar módulo** ... acima do código no editor para salvar as alterações no Hub IOT.

  ![Atualizar um módulo no Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Monitorar gêmeos do módulo no CLI do Azure

Para ver se IoT Edge está em execução, use o [método AZ IOT Hub Invoke-Module-Method](how-to-edgeagent-direct-method.md#ping) para executar ping no agente de IOT Edge.

A estrutura [AZ IOT Hub Module-bidimensional](/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin) fornece estes comandos:

* **módulo AZ IOT Hub-alto mostrar** -mostrar uma definição de módulo.
* **módulo AZ IOT Hub-atualização** de maiúsculas-atualização de uma definição de módulo.
* **módulo AZ IOT Hub-entrelaçar substituir** – substitua uma definição de módulo de alta por um JSON de destino.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [comunicar-se com o EdgeAgent usando os métodos diretos integrados](how-to-edgeagent-direct-method.md).

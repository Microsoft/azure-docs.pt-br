---
title: Ingerir telemetria do Hub IoT
titleSuffix: Azure Digital Twins
description: Consulte como ingerir mensagens de telemetria do dispositivo do Hub IoT.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9ecc14aa9591d6e62dccd9899a80de44411928a1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051081"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingerir telemetria do Hub IoT no gêmeos digital do Azure

O Azure digital gêmeos é orientado com dados de dispositivos IoT e outras fontes. Uma fonte comum de dados de dispositivo a ser usada no Azure digital gêmeos é o [Hub IOT](../iot-hub/about-iot-hub.md).

O processo para ingerir dados no Azure digital gêmeos é configurar um recurso de computação externo, como uma função feita usando [Azure Functions](../azure-functions/functions-overview.md). A função recebe os dados e usa as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins) para definir propriedades ou disparar eventos de telemetria em [gêmeos digital](concepts-twins-graph.md) adequadamente. 

Este documento de instruções explica o processo de gravação de uma função que pode ingerir a telemetria do Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar com este exemplo, você precisará configurar os seguintes recursos como pré-requisitos:
* **Um hub IOT**. Para obter instruções, consulte a seção *criar um hub IOT* deste [início rápido do Hub IOT](../iot-hub/quickstart-send-telemetry-cli.md).
* **Uma função** com as permissões corretas para chamar sua instância de cópia digital. Para obter instruções, consulte [*como: configurar uma função no Azure para processar dados*](how-to-create-azure-function.md). 
* **Uma instância do gêmeos digital do Azure** que receberá a telemetria do dispositivo. Para obter instruções, consulte [*como: configurar uma instância e autenticação do gêmeos digital do Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Cenário de telemetria de exemplo

Este "como" descreve como enviar mensagens do Hub IoT para o Azure digital gêmeos, usando uma função no Azure. Há muitas configurações possíveis e estratégias de correspondência que você pode usar para enviar mensagens, mas o exemplo deste artigo contém as seguintes partes:
* Um dispositivo de termômetro no Hub IoT, com uma ID de dispositivo conhecida
* Um teledigital para representar o dispositivo, com uma ID correspondente

> [!NOTE]
> Este exemplo usa uma correspondência de ID direta entre a ID do dispositivo e uma ID de mesa digital correspondente, mas é possível fornecer mapeamentos mais sofisticados do dispositivo para seu número de mesa (como com uma tabela de mapeamento).

Sempre que um evento de telemetria de temperatura é enviado pelo dispositivo termostato, uma função processa a telemetria e a propriedade de *temperatura* de uma folha digital é atualizada. Este cenário é descrito em um diagrama abaixo:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Um diagrama que mostra um gráfico de fluxo. No gráfico, um dispositivo de Hub IoT envia a telemetria de temperatura por meio do Hub IoT para uma função no Azure, que atualiza uma propriedade de temperatura em um gêmeos no Azure digital." border="false":::

## <a name="add-a-model-and-twin"></a>Adicionar um modelo e um "r"

Você pode adicionar/carregar um modelo usando o comando da CLI abaixo e, em seguida, criar um entrelaçamento usando esse modelo que será atualizado com informações do Hub IoT.

O modelo tem a seguinte aparência:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Para **carregar esse modelo em sua instância do gêmeos**, abra o CLI do Azure e execute o seguinte comando:

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Em seguida, você precisará **criar um pressione 1 usando esse modelo**. Use o comando a seguir para criar um conjunto de entrelaçar e definir 0,0 como um valor de temperatura inicial.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

A saída de um comando MyCreate com êxito deve ser assim:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Criar uma função

Esta seção usa as mesmas etapas de inicialização e esqueleto de função do Visual Studio de [*como: configurar uma função para processar dados*](how-to-create-azure-function.md). O esqueleto manipula a autenticação e cria um cliente de serviço, pronto para processar dados e chamar as APIs do Azure digital gêmeos em resposta. 

Nas etapas a seguir, você adicionará um código específico a ele para processar eventos de telemetria IoT do Hub IoT.  

### <a name="add-telemetry-processing"></a>Adicionar processamento de telemetria
    
Os eventos de telemetria vêm na forma de mensagens do dispositivo. A primeira etapa da adição do código de processamento de telemetria é a extração da parte relevante desta mensagem de dispositivo do evento de grade de eventos. 

Dispositivos diferentes podem estruturar suas mensagens de forma diferente, portanto, o código **dessa etapa depende do dispositivo conectado.** 

O código a seguir mostra um exemplo de um dispositivo simples que envia telemetria como JSON. Este exemplo é explorado completamente no [*tutorial: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md). O código a seguir localiza a ID do dispositivo que enviou a mensagem, bem como o valor de temperatura.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Find_device_ID_and_temperature":::

O exemplo de código a seguir usa a ID e o valor de temperatura e as usa para "patch" (fazer atualizações) que se ocupam.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Update_twin_with_device_temperature":::

### <a name="update-your-function-code"></a>Atualizar o código de função

Agora que você entendeu o código dos exemplos anteriores, abra sua função na seção [*pré-requisitos*](#prerequisites) no Visual Studio. (Se você não tiver uma função que foi criada no Azure, visite o link nos pré-requisitos para criar uma agora).

Substitua o código da função por este código de exemplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Salve o código de função e publique o aplicativo de funções no Azure. Para saber como, consulte [*publicar o aplicativo de funções*](./how-to-create-azure-function.md#publish-the-function-app-to-azure) em [*como configurar uma função no Azure para processar dados*](how-to-create-azure-function.md).

Após uma publicação bem-sucedida, você verá a saída na janela de comando do Visual Studio, conforme mostrado abaixo:

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
Você também pode verificar seu status do processo de publicação no [portal do Azure](https://portal.azure.com/). Pesquise seu _grupo de recursos_ e navegue até o _log de atividades_ e procure _obter perfil de publicação de aplicativo Web_ na lista e verifique se o status foi bem-sucedido.

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="Captura de tela da portal do Azure que mostra o status do processo de publicação.":::

## <a name="connect-your-function-to-iot-hub"></a>Conectar sua função ao Hub IoT

Configure um destino de evento para dados de Hub.
Na [portal do Azure](https://portal.azure.com/), navegue até a instância do Hub IOT que você criou na seção [*pré-requisitos*](#prerequisites) . Em **eventos**, crie uma assinatura para sua função.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Captura de tela da portal do Azure que mostra a adição de uma assinatura de evento.":::

Na página **criar assinatura de evento** , preencha os campos da seguinte maneira:
  1. Em **nome**, nomeie a assinatura como você deseja.
  2. Em **esquema de evento**, escolha _esquema de grade de eventos_.
  3. Em **tipos de evento**, escolha a caixa de seleção _telemetria do dispositivo_ e desmarque outros tipos de evento.
  4. Em **tipo de ponto de extremidade**, selecione _Azure function_.
  5. Em **ponto de extremidade**, escolha _selecionar um_ link de ponto de extremidade para criar um ponto de extremidade.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Captura de tela da portal do Azure para criar os detalhes da assinatura do evento":::

Na página _selecionar função do Azure_ que é aberta, verifique os detalhes abaixo.
 1. **Assinatura**: sua assinatura do Azure
 2. **Grupo de recursos**: seu grupo de recursos
 3. **Aplicativo de funções**: o nome do aplicativo de funções
 4. **Slot**: _produção_
 5. **Função**: selecione sua função na lista suspensa.

Salve seus detalhes selecionando o botão _confirmar seleção_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Captura de tela da portal do Azure para selecionar a função.":::

Selecione o botão _criar_ para criar a assinatura de evento.

## <a name="send-simulated-iot-data"></a>Enviar dados de IoT simulados

Para testar sua nova função de entrada, use o simulador de dispositivo do [*tutorial: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md). Esse tutorial é orientado por um projeto de exemplo escrito em C#. O código de exemplo está localizado aqui: [exemplos de ponta a ponta do Azure digital gêmeos](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Você usará o projeto **DeviceSimulator** nesse repositório.

No tutorial de ponta a ponta, conclua as seguintes etapas:
1. [*Registrar o dispositivo simulado no Hub IoT*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configurar e executar a simulação*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Validar seus resultados

Ao executar o simulador de dispositivo acima, o valor de temperatura de sua digital de los será alterado. No CLI do Azure, execute o comando a seguir para ver o valor de temperatura.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

A saída deve conter um valor de temperatura como este:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Para ver a alteração do valor, execute repetidamente o comando de consulta acima.

## <a name="next-steps"></a>Próximas etapas

Leia sobre entrada e saída de dados com o Azure digital gêmeos:
* [*Conceitos: integração com outros serviços*](concepts-integration.md)

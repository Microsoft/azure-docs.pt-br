---
title: Ingerir telemetria do Hub IoT
titleSuffix: Azure Digital Twins
description: Consulte como ingerir mensagens de telemetria do dispositivo do Hub IoT.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: de16932f1f77e569302b222fe2948de3046fabd6
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950579"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingerir telemetria do Hub IoT no gêmeos digital do Azure

O Azure digital gêmeos é orientado com dados de dispositivos IoT e outras fontes. Uma fonte comum de dados de dispositivo a ser usada no Azure digital gêmeos é o [Hub IOT](../iot-hub/about-iot-hub.md).

O processo para ingerir dados no Azure digital gêmeos é configurar um recurso de computação externo, como uma função feita usando [Azure Functions](../azure-functions/functions-overview.md). A função recebe os dados e usa as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins) para definir propriedades ou disparar eventos de telemetria em [gêmeos digital](concepts-twins-graph.md) adequadamente. 

Este documento de instruções explica o processo de gravação de uma função que pode ingerir a telemetria do Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar com este exemplo, você precisará configurar os seguintes recursos como pré-requisitos:
* **Um hub IOT**. Para obter instruções, consulte a seção *criar um hub IOT* deste [início rápido do Hub IOT](../iot-hub/quickstart-send-telemetry-cli.md).
* **Uma instância do gêmeos digital do Azure** que receberá a telemetria do dispositivo. Para obter instruções, consulte [*como: configurar uma instância e autenticação do gêmeos digital do Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Cenário de telemetria de exemplo

Este "como" descreve como enviar mensagens do Hub IoT para o Azure digital gêmeos, usando uma função no Azure. Há muitas configurações possíveis e estratégias de correspondência que você pode usar para enviar mensagens, mas o exemplo deste artigo contém as seguintes partes:
* Um dispositivo termostato no Hub IoT, com uma ID de dispositivo conhecida
* Um teledigital para representar o dispositivo, com uma ID correspondente

> [!NOTE]
> Este exemplo usa uma correspondência de ID direta entre a ID do dispositivo e uma ID de mesa digital correspondente, mas é possível fornecer mapeamentos mais sofisticados do dispositivo para seu número de mesa (como com uma tabela de mapeamento).

Sempre que um evento de telemetria de temperatura é enviado pelo dispositivo termostato, uma função processa a telemetria e a propriedade de *temperatura* de uma folha digital é atualizada. Este cenário é descrito em um diagrama abaixo:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagrama do dispositivo do Hub IoT enviando telemetria de temperatura por meio do Hub IoT para uma função no Azure, que atualiza uma propriedade de temperatura em um entrelaçamento no Azure digital gêmeos." border="false":::

## <a name="add-a-model-and-twin"></a>Adicionar um modelo e um gêmeo

Nesta seção, você configurará uma atualização [digital](concepts-twins-graph.md) no Azure digital gêmeos que representará o dispositivo termostato e será atualizado com informações do Hub IOT.

Para criar um termostato de texto, você primeiro precisará carregar o [modelo](concepts-models.md) termostato em sua instância, que descreve as propriedades de um termostato e será usado posteriormente para criar o cópia. 

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Em seguida, você precisará **criar um pressione 1 usando esse modelo**. Use o comando a seguir para criar um termostato nome do grupo chamado **thermostat67** e defina 0,0 como um valor de temperatura inicial.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

> [!Note]
> Se você estiver usando Cloud Shell no ambiente do PowerShell, talvez seja necessário escapar os caracteres de aspas nos campos JSON embutidos para que seus valores sejam analisados corretamente. Aqui está o comando para criar o entrelaçamento com esta modificação:
>
> Criar a:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

Quando o ' myQuery ' for criado com êxito, a saída da CLI do comando deverá ser semelhante a esta:
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

Nesta seção, você criará uma função do Azure para acessar o gêmeos digital do Azure e atualizar o gêmeos com base nos eventos de telemetria do IoT recebidos. Siga as etapas abaixo para criar e publicar a função.

#### <a name="step-1-create-a-function-app-project"></a>Etapa 1: criar um projeto de aplicativo de funções

Primeiro, crie um novo projeto de aplicativo de funções no Visual Studio. Para obter instruções sobre como fazer isso, consulte a seção [**criar um aplicativo de funções no Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) do artigo *como: configurar uma função para processamento de dados* .

#### <a name="step-2-fill-in-function-code"></a>Etapa 2: preencher o código de função

Adicione os seguintes pacotes ao seu projeto:
* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Renomeie a função de exemplo *function1. cs* que o Visual Studio gerou com o novo projeto para *IoTHubtoTwins. cs*. Substitua o código no arquivo pelo seguinte código:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Salve seu código de função.

#### <a name="step-3-publish-the-function-app-to-azure"></a>Etapa 3: publicar o aplicativo de funções no Azure

Publique o projeto com a função *IoTHubtoTwins. cs* em um aplicativo de funções no Azure.

Para obter instruções sobre como fazer isso, consulte a seção [**publicar o aplicativo de funções no Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) do artigo *como: configurar uma função para processamento de dados* .

#### <a name="step-4-configure-the-function-app"></a>Etapa 4: configurar o aplicativo de funções

Em seguida, **atribua uma função de acesso** para a função e **defina as configurações do aplicativo** para que ele possa acessar sua instância do gêmeos digital do Azure. Para obter instruções sobre como fazer isso, consulte a seção [**Configurar o acesso de segurança para o aplicativo de funções**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) do artigo *como: configurar uma função para processamento de dados* .

## <a name="connect-your-function-to-iot-hub"></a>Conectar sua função ao Hub IoT

Nesta seção, você configurará sua função como um destino de evento para os dados do dispositivo do Hub IoT. Isso garantirá que os dados do dispositivo termostato no Hub IoT serão enviados para a função do Azure para processamento.

Na [portal do Azure](https://portal.azure.com/), navegue até a instância do Hub IOT que você criou na seção [*pré-requisitos*](#prerequisites) . Em **eventos**, crie uma assinatura para sua função.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Captura de tela da portal do Azure que mostra a adição de uma assinatura de evento.":::

Na página **criar assinatura de evento** , preencha os campos da seguinte maneira:
  1. Para **nome**, escolha qualquer nome que você deseja para a assinatura de evento.
  2. Para **esquema de evento**, escolha _esquema de grade de eventos_.
  3. Para **nome do tópico do sistema**, escolha qualquer nome desejado.
  1. Para **filtrar os tipos de evento**, escolha a caixa de seleção _telemetria do dispositivo_ e desmarque outros tipos de evento.
  1. Para **tipo de ponto de extremidade**, selecione _Azure function_.
  1. Para **ponto de extremidade**, use o link _selecionar um ponto de extremidade_ para escolher qual função do Azure usar para o ponto de extremidade.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Captura de tela da portal do Azure para criar os detalhes da assinatura do evento":::

Na página _selecionar função do Azure_ que é aberta, verifique ou preencha os detalhes abaixo.
 1. **Assinatura**: Sua assinatura do Azure.
 2. **Grupo de recursos**: seu grupo de recursos.
 3. **Aplicativo de funções**: o nome do aplicativo de funções.
 4. **Slot**: _produção_.
 5. **Função**: selecione a função de anterior, *IoTHubtoTwins*, na lista suspensa.

Salve seus detalhes com o botão _confirmar seleção_ .            
      
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

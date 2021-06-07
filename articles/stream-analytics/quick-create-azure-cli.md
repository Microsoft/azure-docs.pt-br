---
title: Início rápido – Criar um trabalho do Azure Stream Analytics usando a CLI do Azure
description: Este guia de início rápido mostra como usar a CLI do Azure para criar um trabalho do Azure Stream Analytics.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: 58dccf56cd493782a422b0ddf0386e31d4d87daf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765981"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure

Neste guia de início rápido, você usa a CLI do Azure para definir um trabalho do Stream Analytics que filtra mensagens de sensor em tempo real com uma leitura de temperatura maior que 27. Seu trabalho do Stream Analytics lerá dados do Hub IoT, transformará os dados e fará write-back para um contêiner no armazenamento de blobs. Os dados de entrada usados neste início rápido são gerados por um simulador online do Raspberry Pi.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Crie um grupos de recursos. Todos os recursos do Azure devem ser implantados em um grupo de recursos. Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.

   Para este início rápido, crie um grupo de recursos chamado *streamanalyticsrg* na localização *eastus* com o seguinte comando [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho do Stream Analytics, prepare os dados que são usados para a entrada do trabalho.

O bloco de código da CLI do Azure a seguir inclui comandos que preparam os dados de entrada necessários para o trabalho. Examine as seções para entender o código.

1. Crie um Hub IoT usando o comando [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub). Este exemplo cria um Hub IoT chamado **MyASAIoTHub**. Como os nomes do Hub IoT são exclusivos, você precisa inventar seu próprio nome do Hub IoT. Defina o SKU como F1 para usar a camada gratuita se ela estiver disponível com sua assinatura. Caso contrário, escolha a próxima camada mais baixa.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Quando o hub IoT for criado, obtenha a cadeia de conexão do Hub IoT usando o comando [az iot hub show-connection-string](/cli/azure/iot/hub). Copie a cadeia de conexão inteira e salve-a para quando você adicionar o Hub IoT como entrada para o trabalho do Stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Adicione um dispositivo ao Hub IoT usando o comando [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Este exemplo cria um dispositivo chamado **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Obtenha a cadeia de conexão do dispositivo usando o comando [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string). Copie a cadeia de conexão inteira e salve-a para quando você criar o simulador do Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Exemplo de saída:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Criar uma conta de armazenamento de blobs

Os blocos de código de CLI do Azure a seguir criam uma conta de Armazenamento de Blobs que é usada para a saída do trabalho. Examine as seções para entender o código.

1. Crie uma conta de armazenamento de uso geral com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento de uso geral pode ser usada para todos os quatro serviços: blobs, arquivos, tabelas e filas.

   Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Obtenha a chave para sua conta de armazenamento executando o comando [az storage account keys list](/cli/azure/storage/account/keys). Salve essa chave para usar na próxima etapa.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Crie um contêiner para armazenar os blobs com o comando [az storage container create](/cli/azure/storage/container). Você usa a chave de conta de armazenamento para autorizar a operação para criar o contêiner. Para obter mais informações sobre como autorizar operações de dados com a CLI do Azure, confira [Autorizar o acesso a dados de blob ou de fila com a CLI do Azure](../storage/blobs/authorize-data-operations-cli.md).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Os blocos de código da CLI do Azure a seguir criam um trabalho do Stream Analytics. Examine as seções para entender o código

1. Crie um trabalho do Stream Analytics com o comando [az stream-analytics job create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create).

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Configurar a entrada para o trabalho

Adicione uma entrada ao seu trabalho usando o cmdlet [az stream-analytics input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create). Esse cmdlet usa o nome do trabalho, o nome de entrada do trabalho, o nome do grupo de recursos e a definição da entrada do trabalho como parâmetros. A definição de entrada do trabalho é um arquivo JSON que contém as propriedades necessárias para configurar a entrada do trabalho. Neste exemplo, você criará um Hub IoT como uma entrada.

Em seu computador local, crie um arquivo chamado `datasource.json` e adicione os seguintes dados JSON a ele. Substitua o valor de `sharedAccessPolicyKey` pela parte `SharedAccessKey` da cadeia de conexão do Hub IoT salva em uma seção anterior.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Em seu computador local, crie um arquivo chamado `serialization.json` e adicione os seguintes dados JSON a ele.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Em seguida, execute o cmdlet `az stream-analytics input create`. Substitua o valor da variável `datasource` pelo caminho em que você armazenou o arquivo JSON de definição de entrada do trabalho e o valor da variável `serialization` pelo caminho em que você armazenou o arquivo JSON de serialização.

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Configurar a saída para o trabalho

Adicione uma saída ao seu trabalho usando o cmdlet [az stream-analytics output create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create). Esse cmdlet usa o nome do trabalho, o nome de saída do trabalho, o nome do grupo de recursos e a definição da saída do trabalho como parâmetros. A definição de saída do trabalho é um arquivo JSON que contém as propriedades necessárias para configurar a saída do trabalho. Este exemplo usa o armazenamento de blobs como saída.

Em seu computador local, crie um arquivo chamado `datasink.json` e adicione os seguintes dados JSON a ele. Substitua o valor de `accountKey` pela chave de acesso da sua conta de armazenamento, que é o valor armazenado no valor $storageAccountKey.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Em seguida, execute o cmdlet `az stream-analytics output`. Substitua o valor da variável `datasource` pelo caminho em que você armazenou o arquivo JSON de definição de saída do trabalho e o valor da variável `serialization` pelo caminho em que você armazenou o arquivo JSON de serialização.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Adicione uma transformação ao seu trabalho usando o cmdlet [az stream-analytics transformation create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create). Esse cmdlet usa o nome do trabalho, o nome da transformação do trabalho, o nome do grupo de recursos e a definição da transformação do trabalho como parâmetros. 

Execute o cmdlet `az stream-analytics transformation create`.

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [Simulador Online de IoT do Azure do Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o espaço reservado na Linha 15 pela cadeia de conexão inteira do Dispositivo Hub IoT do Azure salva em uma seção anterior.

3. Clique em **Executar**. A saída deve exibir os dados de sensor e as mensagens que estão sendo enviadas ao Hub IoT.

    ![Simulador online de IoT do Azure do Raspberry Pi](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar o trabalho do Stream Analytics e verificar a saída

Inicie o trabalho usando o cmdlet [az stream-analytics job start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start). Esse cmdlet usa o nome do trabalho, o nome do grupo de recursos, o modo inicial de saída e a hora de início como parâmetros. `OutputStartMode` aceita valores de `JobStartTime`, `CustomTime` ou `LastOutputEventTime`.

Depois de executar o cmdlet a seguir, ele retornará `True` como saída se o trabalho for iniciado. No contêiner de armazenamento, uma pasta de saída é criada com os dados transformados.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você estiver planejando usar o trabalho no futuro, ignore a exclusão e pare o trabalho agora. Se você não pretende continuar usando esse trabalho, exclua todos os recursos criados por este início rápido executando o seguinte cmdlet:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou um trabalho simples do Stream Analytics usando a CLI do Azure. Você também pode implantar trabalhos do Stream Analytics usando o [portal do Azure](stream-analytics-quick-create-portal.md) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre como configurar outras fontes de entrada e executar detecção em tempo real, avance para o artigo a seguir:

> [!div class="nextstepaction"]
> [Detecção de fraude em tempo real usando o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
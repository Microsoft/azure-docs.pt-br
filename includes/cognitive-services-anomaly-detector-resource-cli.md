---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503630"
---
Os comandos da CLI do Azure a seguir provisionarão um recurso do Detector de Anomalias no tipo de preço gratuito. Clique no botão **Experimentar**, cole o código e pressione Enter para executá-lo no Azure Cloud Shell. Ele imprimirá suas chaves para autenticar seu aplicativo. Depois que ele for concluído, [crie uma variável de ambiente](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para qualquer uma de suas chaves, chamada `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * Você pode, opcionalmente:
>    * criar um recurso usando o [portal do Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) ou a [CLI do Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) em seu computador local;
>    * obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por 7 dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/);
>    * exibir esse recurso no [portal do Azure](https://portal.azure.com/). 

Os Serviços Cognitivos são representados por recursos do Azure que você provisiona. Toda conta dos Serviços Cognitivos (e seus recursos do Azure associados) precisam pertencer a um grupo de recursos do Azure.

1. Criar um grupo de recursos do Azure

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Criar um recurso de Detector de Anomalias na camada gratuita

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Listar as chaves do recurso

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```
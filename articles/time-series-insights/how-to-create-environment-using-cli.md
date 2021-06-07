---
title: Criar um ambiente de Azure Time Series Insights Gen2 usando o CLI do Azure-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba como configurar um ambiente no Azure Time Series Insights Gen2 usando o CLI do Azure.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: ed185413cff155610b2b088b1791169e33f6ce7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464395"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Criar um ambiente de Azure Time Series Insights Gen2 usando o CLI do Azure

Este documento orientará você na criação de um novo ambiente de Time Series Insights Gen2.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Criar uma conta de armazenamento do Azure para o [armazenamento frio](./concepts-storage.md#cold-store) de seu ambiente. Essa conta foi projetada para retenção e análise de longo prazo de dados históricos.

> [!NOTE]
> No código, substitua `mytsicoldstore` por um nome exclusivo para sua conta de armazenamento frio.

Primeiro, crie a conta de armazenamento:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Criando o ambiente

Agora que a conta de armazenamento foi criada e seu nome e a chave de gerenciamento são atribuídos às variáveis, execute o comando a seguir para criar o ambiente de Azure Time Series Insights:

> [!NOTE]
> Em seu código, substitua o seguinte por nomes exclusivos para seu cenário:
>
> * `my-tsi-env` pelo nome do seu ambiente.
> * `my-ts-id-prop` pelo nome da sua propriedade de ID da série temporal.

> [!IMPORTANT]
> A ID da série temporal do seu ambiente é como uma chave de partição de banco de dados. A ID da série temporal também atua como a chave primária do seu modelo de série temporal.
>
> Para obter mais informações, consulte [práticas recomendadas para escolher uma ID de série temporal.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Remover um ambiente de Azure Time Series Insights

Você pode usar o CLI do Azure para excluir um recurso individual, como um ambiente de Time Series Insights, ou excluir um grupo de recursos e todos os seus recursos, incluindo quaisquer ambientes Time Series Insights.

Para [excluir um time Series insights ambientes](/cli/azure/ext/timeseriesinsights/tsi/environment?view=azure-cli-latest#ext_timeseriesinsights_az_tsi_environment_delete), execute o seguinte comando:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Para [excluir a conta de armazenamento](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete), execute o seguinte comando:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Para [excluir um grupo de recursos](/cli/azure/group#az-group-delete) e todos os seus recursos, execute o seguinte comando:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [fontes de evento de ingestão de streaming](./concepts-streaming-ingestion-event-sources.md) para seu ambiente de Azure Time Series insights Gen2.
* Saiba como se conectar a um [Hub IOT](./how-to-ingest-data-iot-hub.md)

---
title: Criar, exibir e gerenciar tópicos do sistema de grade de eventos do Azure usando a CLI
description: Este artigo mostra como usar CLI do Azure para criar, exibir e excluir tópicos do sistema.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c847c7f25e3a656b798e186a408e560b9ee9e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633215"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Criar, exibir e gerenciar tópicos do sistema de grade de eventos usando CLI do Azure
Este artigo mostra como criar e gerenciar tópicos do sistema usando o CLI do Azure. Para obter uma visão geral dos tópicos do sistema, consulte [Tópicos do sistema](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Instalar extensão para CLI do Azure
Para Azure CLI, é necessária a [extensão da Grade de Eventos](/cli/azure/azure-cli-extensions-list).

Em Cloud Shell:

- Se você instalou a extensão anteriormente, atualize-a: `az extension update -n eventgrid`
- Se você ainda não instalou a extensão, instale-a:  `az extension add -n eventgrid`

Para uma instalação local:

1. [Instale a CLI do Azure](/cli/azure/install-azure-cli). Certifique-se de você tem a versão mais recente, verificando com o `az --version`.
2. Desinstalar versões anteriores da extensão: `az extension remove -n eventgrid`
3. Instalar a extensão eventgrid com `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Criar um tópico do sistema

- Para criar um tópico do sistema primeiro em uma origem do Azure e, em seguida, criar uma assinatura de evento para esse tópico, consulte os seguintes tópicos de referência:
    - [AZ eventgrid System-tópico Create](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Para obter uma lista de `topic-type` valores que você pode usar para criar um tópico do sistema, execute o comando a seguir. Esses valores de tipo de tópico representam as fontes de eventos que dão suporte à criação de tópicos do sistema. Ignore `Microsoft.EventGrid.Topics` e `Microsoft.EventGrid.Domains` na lista. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [AZ eventgrid System-tópico Event-assinatura Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Para criar um tópico do sistema (implicitamente) ao criar uma assinatura de evento para uma origem do Azure, use o método [AZ eventgrid Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) . Veja um exemplo:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Para obter um tutorial com instruções detalhadas, consulte [assinar a conta de armazenamento](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Exibir todos os tópicos do sistema
Para exibir todos os tópicos e detalhes do sistema de um tópico do sistema selecionado, use os seguintes comandos:

- [AZ eventgrid System-lista de tópicos](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [AZ eventgrid System – tópico show](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Excluir um tópico do sistema
Para excluir um tópico do sistema, use o seguinte comando: 

- [AZ eventgrid System – tópico Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Próximas etapas
Consulte a seção [Tópicos do sistema na grade de eventos do Azure](system-topics.md) para saber mais sobre os tópicos do sistema e os tipos de tópico com suporte na grade de eventos do Azure. 
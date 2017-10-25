---
title: "Encaminhar eventos de Armazenamento de Blobs do Azure para um ponto de extremidade da Web personalizado (versão prévia) | Microsoft Docs"
description: Use a Grade de Eventos do Azure para assinar eventos de Armazenamento de Blobs.
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/18/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: 1a489f199bcc955fd14e82d16670d854305b00c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>Encaminhar eventos de Armazenamento de Blobs para um ponto de extremidade da Web personalizado (versão prévia)

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste artigo, você usa a CLI do Azure para assinar eventos de Armazenamento de Blobs e acionar o evento a fim de exibir o resultado. 

> [!IMPORTANT]
> Você deve estar registrado na versão prévia de eventos do Armazenamento de Blobs para concluir este tutorial.  Saiba mais sobre o programa de versão prévia [aqui](storage-blob-event-overview.md#join-the-preview).

Normalmente, você envia eventos para um ponto de extremidade que responde ao evento, como um webhook ou uma Função do Azure. Para simplificar o exemplo mostrado neste artigo, enviamos os eventos para uma URL que apenas coleta as mensagens. Você cria essa URL usando uma ferramenta de terceiros de software livre chamada [RequestBin](https://requestb.in/).

> [!NOTE]
> **RequestBin** é uma ferramenta de software livre que não se destina ao uso com altas taxas de transferência. O uso da ferramenta aqui é meramente demonstrativo. Se você efetuar push de mais de um evento por vez, talvez não veja todos os eventos na ferramenta.

Quando você concluir as etapas descritas neste artigo, verá que os dados do evento foi enviados para um ponto de extremidade.

![Dados de evento](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando versão da CLI do Azure mais recente (2.0.14 ou posterior). Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos de Grade de Eventos são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). 

O exemplo a seguir cria um grupo de recursos chamado `<resource_group_name>` na localização *westcentralus*.  Substitua `<resource_group_name>` por um nome exclusivo para o grupo de recursos.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>Criar uma conta de armazenamento de blobs

Para usar o Armazenamento do Azure, você precisa de uma conta de armazenamento.  Os eventos de Armazenamento de Blobs estão, no momento, disponíveis apenas para contas de Armazenamento de Blobs.

Uma conta de Armazenamento de Blobs é uma conta de armazenamento especializada para armazenar dados não estruturados como blobs (objetos) no Armazenamento do Azure. As contas de armazenamento de Blobs são semelhantes a contas de armazenamento de finalidade geral existentes e compartilham todos os excelentes recursos de durabilidade, disponibilidade, escalabilidade e desempenho que você usa atualmente, incluindo 100% de consistência de API para blobs de bloco e blobs de acréscimo. Para aplicativos que exigem apenas o armazenamento de blobs em bloco ou acréscimo, recomendamos o uso de contas de Armazenamento de Blobs.

> [!NOTE]
> Para a versão prévia, os eventos de Armazenamento de Blobs estão disponíveis apenas para contas de armazenamento com localização **westcentralus**.

Substitua `<storage_account_name>` por um nome exclusivo para sua conta de armazenamento e `<resource_group_name>` pelo grupo de recursos criado anteriormente.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar eventos da conta de Armazenamento de Blobs, vamos criar o ponto de extremidade para mensagens de evento. Em vez de escrever código para responder ao evento, vamos criar um ponto de extremidade que coleta as mensagens, para que você possa exibi-las. O RequestBin é uma ferramenta de terceiros de software livre que permite criar um ponto de extremidade e exibir as solicitações enviadas a ele. Vá para [RequestBin](https://requestb.in/)e clique em **Criar um RequestBin**.  Copie a URL do compartimento, pois você precisará dela para assinar o tópico.

## <a name="subscribe-to-your-blob-storage-account"></a>Assinar sua conta de Armazenamento de Blobs

Assine um tópico para indicar à Grade de Eventos quais eventos você deseja acompanhar. O exemplo a seguir assina a conta de armazenamento de blobs que você criou e transmite a URL do RequestBin como o ponto de extremidade para notificação de eventos. Substitua `<event_subscription_name>` por um nome exclusivo para a sua assinatura do evento e `<URL_from_RequestBin>` pelo valor da seção anterior. A especificação de um ponto de extremidade durante a assinatura faz com que a Grade de Eventos manipule o roteamento de eventos para esse ponto de extremidade. Em `<resource_group_name>` e `<storage_account_name>`, use os valores criados anteriormente. 

```azurecli-interactive
az eventgrid resource event-subscription create \
--endpoint <URL_from_RequestBin> \
--name <event_subscription_name> \
--provider-namespace Microsoft.Storage \
--resource-type storageAccounts \
--resource-group <resource_group_name> \
--resource-name <storage_account_name>
```

## <a name="trigger-an-event-from-blob-storage"></a>Acionar um evento do Armazenamento de Blobs

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Primeiro, vamos configurar o nome e a chave da conta de armazenamento. Em seguida, vamos criar um contêiner para depois criar e carregar um arquivo. Novamente, use os valores de `<storage_account_name>` e `<resource_group_name>` criados anteriormente.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Você disparou o evento e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Navegue até a URL do RequestBin que você criou anteriormente. Ou clique em Atualizar no navegador do RequestBin aberto. Você verá o evento que acabou de ser enviado. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  }
}]

```

## <a name="clean-up-resources"></a>Limpar recursos
Se você planeja continuar a trabalhar com essa assinatura de evento e conta de armazenamento, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados por você neste artigo.

Substitua `<resource_group_name>` pelo recurso de grupo criado acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Próximas etapas

Agora que sabe como criar tópicos e assinaturas de evento, saiba mais sobre os Eventos de Armazenamento de Blobs e o que a Grade de Eventos pode fazer por você:

- [Reagindo a eventos de Armazenamento de Blobs](storage-blob-event-overview.md)
- [Sobre a Grade de Eventos](../../event-grid/overview.md)

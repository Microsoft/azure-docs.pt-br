---
title: Enviar eventos do armazenamento de BLOBs do Azure para o ponto de extremidade da Web-PowerShell | Microsoft Docs
description: Use a grade de eventos do Azure para assinar eventos de armazenamento de BLOBs, disparar um evento e exibir o resultado. Use Azure PowerShell para rotear eventos de armazenamento para um ponto de extremidade da Web.
author: normesta
ms.author: normesta
ms.reviewer: dastanfo
ms.date: 08/23/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 06be168ff9dfd55a56578b3afcdab8d984416756
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078003"
---
# <a name="quickstart-route-storage-events-to-web-endpoint-with-powershell"></a>Início rápido: rotear eventos de armazenamento para o ponto de extremidade da Web com o PowerShell

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste artigo, você usa Azure PowerShell para assinar eventos de Armazenamento de Blobs, acionar um evento e exibir o resultado. 

Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No entanto, para simplificar este artigo, você enviará os eventos para um aplicativo Web que coleta e exibe as mensagens.

Ao concluir, você verá que os dados do evento foram enviados para um aplicativo Web.

![Exibir os resultados](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Instalação

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo requer que você esteja executando a versão mais recente do Azure PowerShell. Se você precisa instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

Este exemplo usa **westus2** e armazena a seleção em uma variável para uso.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos de Grade de Eventos são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

O exemplo a seguir cria um grupo de recursos chamado **gridResourceGroup** no local **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Os eventos de armazenamento de blobs estão disponíveis nas contas de armazenamento de uso geral v2 e nas contas de armazenamento de blobs. As contas de armazenamento de **uso geral v2** dão suporte a todos os recursos para todos os serviços de armazenamento, incluindo BLOBs, arquivos, filas e tabelas. Uma **conta de armazenamento de BLOBs** é uma conta de armazenamento especializada para armazenar dados não estruturados como BLOBs (objetos) no armazenamento do Azure. As contas de armazenamento de Blobs são como contas de armazenamento de finalidade geral existentes e compartilham todos os excelentes recursos de durabilidade, disponibilidade, escalabilidade e desempenho que você usa atualmente, incluindo 100% de consistência de API para blobs de bloco e blobs de acréscimo. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

Criar uma conta de armazenamento de Blob com replicação LRS usando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) e, em seguida, recupere o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de fornecer repetidamente as credenciais. Esse exemplo cria uma conta de armazenamento chamada **gridstorage** com LRS (armazenamento com redundância local). 

> [!NOTE]
> Nomes de conta de armazenamento estão em um espaço de nome global, você precisa adicionar alguns caracteres aleatórios ao nome fornecido no script.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar o tópico, vamos criar o ponto de extremidade para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Para simplificar este início rápido, você implanta um [aplicativo Web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de eventos. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para o aplicativo Web. O nome do aplicativo Web deve ser exclusivo, pois é parte de uma entrada DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Você deve ver o site sem mensagens exibidas no momento.

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>Assinar a sua conta de armazenamento

Você assina um tópico para informar à grade de eventos quais eventos você deseja rastrear. O exemplo a seguir assina a conta de armazenamento que você criou e passa a URL do seu aplicativo Web como o ponto de extremidade para notificação de eventos. O ponto de extremidade para seu aplicativo Web deve incluir o sufixo `/api/updates/`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. Selecione o ícone de olho para expandir os dados de evento. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O aplicativo Web inclui o código para validar a assinatura.

![Exibição do evento de assinatura](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Acionar um evento do Armazenamento de Blobs

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Primeiro, vamos criar um contêiner e um objeto. Em seguida, vamos carregar o objeto no contêiner.

```powershell
$containerName = "gridcontainer"
New-AzStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Você disparou o evento, e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Exiba seu aplicativo Web para ver o evento que você acabou de enviar.

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Limpar os recursos
Se você planeja continuar trabalhando com essa assinatura de evento e conta de armazenamento, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados neste artigo.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que sabe como criar tópicos e assinaturas de evento, saiba mais sobre os Eventos de Armazenamento de Blobs e o que a Grade de Eventos pode fazer por você:

- [Reagindo a eventos de Armazenamento de Blobs](storage-blob-event-overview.md)
- [Sobre a Grade de Eventos](../../event-grid/overview.md)

---
title: Gerenciar capturas de pacote com o Observador de Rede do Azure - CLI do Azure | Microsoft Docs
description: Esta página explica como gerenciar o recurso de captura de pacote do Observador de Rede usando a CLI do Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: efb8fba655570a661b6b001eb93942475eb2170d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698630"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Gerenciar as capturas de pacote com o Observador de Rede do Azure usando a CLI do Azure

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI do Azure](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

Captura de pacote do Observador de Rede permite que você crie sessões de captura para controlar o tráfego em uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que somente o tráfego que você deseja capturar. Captura de pacote ajuda a diagnosticar problemas de rede reativo e proativo. Outros usos incluem a coleta de estatísticas de rede, obter informações sobre as invasões de rede, para depurar comunicações cliente-servidor e muito mais. Por poder remotamente disparar a captura de pacote, esse recurso alivia a carga da execução de uma captura de pacote e manualmente no computador desejado, o que economiza tempo.

Para executar as etapas neste artigo, você precisa [instalar a interface de Command-Line do Azure para Mac, Linux e Windows (CLI do Azure)](/cli/azure/install-azure-cli).

Este artigo o guiará durante as tarefas de gerenciamento diferentes que estão atualmente disponíveis para captura de pacote.

- [**Iniciar uma captura de pacote**](#start-a-packet-capture)
- [**Parar uma captura de pacotes**](#stop-a-packet-capture)
- [**Excluir uma captura de pacote**](#delete-a-packet-capture)
- [**Baixar uma captura de pacote**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha os seguintes recursos:

- Uma instância do Observador de Rede na região na qual você deseja criar uma captura de pacotes
- Uma máquina virtual com a extensão da captura de pacotes habilitada.

> [!IMPORTANT]
> Captura de pacote requer um agente está em execução na máquina virtual. O agente é instalado como uma extensão. Para obter instruções sobre extensões de VM, visite [recursos e extensões de máquina Virtual](../virtual-machines/extensions/features-windows.md).

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Etapa 1

Execute o `az vm extension set` comando para instalar o agente de captura de pacote na máquina virtual convidada.

Para máquinas virtuais do Windows:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Para máquinas virtuais Linux:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Etapa 2

Para garantir que o agente esteja instalado, execute o `vm extension show` comando e passe-o para o grupo de recursos e o nome da máquina virtual. Verifique a lista resultante para garantir que o agente está instalado.

Para máquinas virtuais do Windows:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Para máquinas virtuais Linux:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

O exemplo a seguir é um exemplo de resposta de execução`az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacotes

Depois que as etapas anteriores forem concluídas, o agente de captura de pacote está instalado na máquina virtual.

### <a name="step-1"></a>Etapa 1

Recupere uma conta de armazenamento. Essa conta de armazenamento é usada para armazenar o arquivo de captura de pacote.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Etapa 2

Neste ponto, você está pronto para criar uma captura de pacote.  Primeiro, vamos examinar os parâmetros que talvez você queira configurar. Os filtros são um desses parâmetros que podem ser usados para limitar os dados armazenados pela captura de pacotes. O exemplo a seguir configura uma captura de pacote com vários filtros.  Os três primeiros filtros coletam tráfego de saída TCP apenas de IP local 10.0.0.3 para portas de destino de 20, 80 e 443.  O último filtro coleta apenas o tráfego UDP.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

O exemplo a seguir é a saída esperada da execução do `az network watcher packet-capture create` comando.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Obter uma captura de pacote

Executar o `az network watcher packet-capture show-status` comando recupera o status de uma captura de pacote em execução no momento ou concluído.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

O exemplo a seguir é a saída do `az network watcher packet-capture show-status` comando. O exemplo a seguir ilustra o momento em que a captura é Stopped, com um StopReason de TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Ao executar o `az network watcher packet-capture stop` comando, se uma sessão de captura estiver em andamento, ela será interrompida.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> O comando não retorna nenhuma resposta quando executado em uma sessão de captura atualmente em execução ou em uma sessão existente que já foi interrompida.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Excluir uma captura de pacotes não exclui o arquivo na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Baixar um pacote de capturas

Quando a sessão de captura de pacote for concluído, o arquivo de captura pode ser carregado no Armazenamento de Blobs ou em um arquivo local na VM. O local de armazenamento da captura de pacotes é definido na criação da sessão. Uma ferramenta conveniente para acessar esses arquivos de captura salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixado aqui: https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Próximas etapas

Saiba como automatizar a captura de pacote com alertas de máquina Virtual por meio da exibição [criar uma captura de pacote acionado alerta](network-watcher-alert-triggered-packet-capture.md)

Localize se determinado tráfego é permitido dentro ou fora de sua VM visitando [Verificar o fluxo do IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
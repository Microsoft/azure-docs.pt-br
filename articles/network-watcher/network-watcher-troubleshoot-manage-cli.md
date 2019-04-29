---
title: Solução de problemas de conexões e do gateway de rede virtual do Azure da CLI do Azure | Microsoft Docs
description: Esta página explica como usar a CLI do Azure para solucionar problemas do Observador de rede do Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 8910b4963c7f40b5b6ecdde06c7c7a0c9b86e97a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680328"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Como solucionar problemas de conexões e gateway de rede virtual do usando a CLI do Azure do Observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O observador de rede oferece muitos recursos que dizem respeito às noções básicas sobre os recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A solução de problemas de recursos pode ser chamada pelo Portal, pelo PowerShell, pela CLI ou pela API REST. Quando chamado, o Observador de rede inspeciona a integridade de uma conexão ou um gateway de rede virtual e faz um relatório sobre suas descobertas.

Para executar as etapas deste artigo, será necessário [instalar a interface de linha de comando do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de rede](network-watcher-create.md) para criar um Observador de rede.

Para obter uma lista de tipos de gateway com suporte, visite [Tipos de Gateway com suporte](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Visão geral

A solução de problemas de recursos fornece a capacidade de solucionar problemas que podem surgir com as conexões e o gateway de rede virtual. Quando uma solução de problemas de recursos recebe uma solicitação, os logs são consultados e inspecionadas. Quando a inspeção estiver concluída, você receberá um relatório com os resultados. As solicitações da solução de problemas de recursos são solicitações de execução longa e podem demorar para gerar um relatório. Os logs de solução de problemas são armazenados em um contêiner em uma conta de armazenamento especificada.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Como recuperar uma conexão de gateway de rede virtual

Neste exemplo, a solução de problemas de recursos está sendo executada em uma conexão. Você também pode passá-lo por um gateway de rede virtual. O cmdlet a seguir lista as conexões vpn em um grupo de recursos.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Assim que você tiver o nome da conexão, poderá executar esse comando para obter sua ID de recurso:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A solução de problemas de recursos produz relatório de dados sobre a integridade do recurso, ela também salva os logs em uma conta de armazenamento a ser revisada. Nesta etapa, criaremos uma conta de armazenamento. Você pode usar uma conta de armazenamento existente se já tiver uma.

1. Criar a conta de armazenamento

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Obter as chaves da conta de armazenamento

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Criar o contêiner

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Como executar a solução de problemas de recursos do Observador de rede

Utilize o cmdlet `az network watcher troubleshooting` para solucionar os problemas dos recursos. Aprovamos o cmdlet no grupo de recursos, no nome do Observador de rede, no ID da conexão, no ID da conta de armazenamento e no caminho para o blob para armazenar o resultado da solução de problemas.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Depois que você executar o cmdlet, o Observador de rede revisará o recurso para verificar a integridade. Ele envia um relatório com os resultados para o shell e armazena os logs dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Como entender os resultados

O texto de ação fornece orientação geral sobre como resolver o problema. Se for possível executar uma ação para solucionar o problema, você receberá um link com orientações adicionais. Nos casos em que não há orientações adicionais, a resposta fornecerá a url para abrir um caso de suporte.  Para obter mais informações sobre as propriedades da resposta e o do que está incluído, acesse [Visão geral da solução de problemas do observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre como baixar os arquivos de contas de armazenamento do Azure, confira [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Gerenciador de armazenamento. Para saber mais sobre o Gerenciador de Armazenamento, acesse o link: [Gerenciador de Armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Próximas etapas

Se as configurações para a conectividade VPN foram alteradas, confira [Gerenciamento de grupos de segurança de rede](../virtual-network/manage-network-security-group.md) para acompanhar quais são as regras de segurança e o grupo de segurança de rede envolvidos na questão.

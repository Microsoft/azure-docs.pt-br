---
title: Azure PowerShell – inscrever-se em uma conta de armazenamento de Blob
description: Este artigo fornece um script de exemplo do Azure PowerShell que mostra como assinar eventos da Grade de Eventos para uma conta de Armazenamento de Blobs.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 15daa9843eb721ebf9caa2ae49e75fadbd0a4be8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460789"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Inscreva-se em eventos para uma conta de armazenamento de Blob com o PowerShell

Este script cria uma assinatura de Grade de Eventos para os eventos para uma conta de armazenamento de Blob.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Exemplo de script - estável

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o seguinte comando para criar a assinatura do evento. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Criar uma assinatura na Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/get-started-azureps).

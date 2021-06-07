---
title: Criar ou modificar um emparelhamento do Exchange usando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um emparelhamento do Exchange usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d098f1ff0f78e37601544619c603b776e0cf22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89071747"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Criar ou modificar um emparelhamento do Exchange usando o PowerShell

Este artigo descreve como criar um emparelhamento do Microsoft Exchange usando cmdlets do PowerShell e o modelo de implantação do Gerenciador de recursos. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [portal](howto-exchange-portal.md)do Azure.

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [Guia passo a passo de emparelhamento do Exchange](walkthrough-exchange-all.md) antes de começar a configuração.
* Se você já tiver emparelhamentos do Exchange com a Microsoft que não são convertidos em recursos do Azure, consulte [converter um emparelhamento do Exchange herdado em um recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Criar e provisionar um emparelhamento do Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Obter a lista de locais de emparelhamento com suporte para o emparelhamento do Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um emparelhamento do Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Obter emparelhamento do Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Desprovisionar um emparelhamento do Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento direto usando o PowerShell](howto-direct-powershell.md)
* [Converter um emparelhamento direto herdado para um recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, confira [Perguntas frequentes sobre emparelhamento de Internet](faqs.md).

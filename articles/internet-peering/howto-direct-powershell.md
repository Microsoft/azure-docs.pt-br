---
title: Criar ou modificar um emparelhamento direto usando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um emparelhamento direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be4bab10d369fcefe626bbe890acfff7fc7004b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079057"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Criar ou modificar um emparelhamento direto usando o PowerShell

Este artigo descreve como criar um emparelhamento direto da Microsoft usando cmdlets do PowerShell e o modelo de implantação Azure Resource Manager. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [portal](howto-direct-portal.md)do Azure.

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [Guia passo a passo de emparelhamento Direto](walkthrough-direct-all.md) antes de começar a configuração.
* Se você já tiver conexões diretas de emparelhamento com a Microsoft que não são convertidas em recursos do Azure, consulte [converter um emparelhamento direto herdado para um recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Criar e provisionar um emparelhamento Direto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Obter a lista de locais de emparelhamento com suporte para emparelhamento direto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um emparelhamento Direto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar emparelhamento Direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um emparelhamento Direto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionar um emparelhamento Direto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o emparelhamento do Exchange usando o PowerShell](howto-exchange-powershell.md)
* [Converter um emparelhamento do Exchange herdado para um recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, confira [Perguntas frequentes sobre emparelhamento de Internet](faqs.md).

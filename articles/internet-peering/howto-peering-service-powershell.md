---
title: Habilitar o serviço de emparelhamento do Azure em um emparelhamento direto usando o PowerShell
titleSuffix: Azure
description: Habilitar o serviço de emparelhamento do Azure em um emparelhamento direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079040"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Habilitar o serviço de emparelhamento do Azure em um emparelhamento direto usando o PowerShell

Este artigo descreve como habilitar o [serviço de emparelhamento](overview-peering-service.md) do Azure em um emparelhamento direto usando cmdlets do PowerShell e o modelo de implantação Azure Resource Manager.

Se preferir, você pode concluir este guia usando o [portal](howto-peering-service-portal.md)do Azure.

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) antes de começar a configuração.
* Escolha um emparelhamento direto em sua assinatura para o qual você deseja habilitar o serviço de emparelhamento. Se você não tiver uma, converta um emparelhamento direto herdado ou crie um novo emparelhamento direto:
    * Para converter um emparelhamento direto herdado, siga as instruções em [converter um emparelhamento direto herdado para um recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md).
    * Para criar um novo emparelhamento direto, siga as instruções em [criar ou modificar um emparelhamento direto usando o PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o Serviço de Emparelhamento em um Emparelhamento direto

### <a name="view-direct-peering"></a><a name= get></a>Exibir emparelhamento direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Habilitar o emparelhamento direto para o serviço de emparelhamento

Depois de obter o emparelhamento direto na etapa anterior, habilite-o para o serviço de emparelhamento.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma conexão de emparelhamento direto

Se você precisar modificar as configurações de conexão, consulte a seção "modificar um emparelhamento direto" em [criar ou modificar um emparelhamento direto usando o PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o emparelhamento do Exchange usando o PowerShell](howto-exchange-powershell.md)
* [Converter um emparelhamento do Exchange herdado para um recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para perguntas frequentes, consulte as [perguntas frequentes sobre o serviço de emparelhamento](service-faqs.md).

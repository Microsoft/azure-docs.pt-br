---
title: Habilitar o serviço de emparelhamento do Azure em um emparelhamento direto usando o portal do Azure
titleSuffix: Azure
description: Habilitar o serviço de emparelhamento do Azure em um emparelhamento direto usando o portal do Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700035"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Habilitar o serviço de emparelhamento do Azure em um emparelhamento direto usando o portal do Azure

Este artigo descreve como habilitar o [serviço de emparelhamento](overview-peering-service.md) do Azure em um emparelhamento direto usando o portal do Azure.

Se preferir, conclua este guia usando o [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) antes de começar a configuração.
* Escolha um emparelhamento direto em sua assinatura para o qual você deseja habilitar o serviço de emparelhamento. Se você não tiver uma, converta um emparelhamento direto herdado ou crie um novo emparelhamento direto:
    * Para converter um emparelhamento direto herdado, siga as instruções em [converter um emparelhamento direto herdado para um recurso do Azure usando o portal](howto-legacy-direct-portal.md).
    * Para criar um novo emparelhamento direto, siga as instruções em [criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o Serviço de Emparelhamento em um Emparelhamento direto

### <a name="view-direct-peering"></a><a name= get></a>Exibir emparelhamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Habilitar o emparelhamento direto para o serviço de emparelhamento

Depois de abrir um emparelhamento direto na etapa anterior, habilite-o para o serviço de emparelhamento.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma conexão de emparelhamento direto

Para modificar as configurações de conexão, consulte a seção "modificar um emparelhamento direto" em [criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para um recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para perguntas frequentes, consulte as [perguntas frequentes sobre o serviço de emparelhamento](service-faqs.md).
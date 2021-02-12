---
title: Solucionar problemas de uso de download de reserva do Azure
description: Este artigo ajuda você a entender por que o download de detalhes de uso da instância reservada não está disponível no portal do Azure e a solucionar esses problemas.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147310"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Solucionar problemas de uso de download de reserva do Azure

Este artigo ajuda você a entender por que o download de detalhes de uso da instância reservada não está disponível no portal do Azure e a solucionar esses problemas.

## <a name="symptoms"></a>Sintomas

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até **Reservas**.
1. Selecione uma reserva.
1. Na página de visão geral de reserva, a exibição padrão mostra o uso dos últimos sete dias. Você pode selecionar **Baixar como CSV** para baixar os detalhes de uso da reserva.
1. Quando você altera o intervalo de tempo, a opção para **Baixar como CSV** não fica disponível.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Exemplo mostrando Baixar como CSV não disponível" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Causa

Devido a limitações técnicas, o Azure não dá suporte ao download de dados por mais de um período de sete dias. Períodos longos para clientes com grandes quantidades de reservas geram grandes quantidades de dados. Retornar dados por meio de APIs coloca uma sobrecarga nos recursos do Azure.

## <a name="solution"></a>Solução

Entendemos que os clientes desejam baixar dados por períodos mais longos. No entanto, atualmente não há como baixar dados de uso de reserva por longos períodos.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre reservas, confira [O que são as Reservas do Azure?](save-compute-costs-reservations.md).
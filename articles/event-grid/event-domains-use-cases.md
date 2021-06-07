---
title: Casos de uso para domínios de evento na grade de eventos do Azure
description: Este artigo descreve alguns casos de uso para o uso de domínios de evento na grade de eventos do Azure.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204282"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Casos de uso para domínios de evento na grade de eventos do Azure
Este artigo descreve alguns casos de uso para o uso de domínios de evento na grade de eventos do Azure. 

## <a name="use-case-1"></a>Caso de uso 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Caso de uso 2
Há um limite de 500 assinaturas de evento ao usar os tópicos do sistema. Se você precisar de mais de 500 assinaturas de evento para um tópico do sistema, poderá usar domínios. 

Suponha que você tenha criado um tópico do sistema para um armazenamento de BLOBs do Azure e precise criar mais de 500 assinaturas para o tópico, mas isso não é possível devido à limitação (500 assinaturas por tópico). Nesse caso, você pode usar a solução a seguir que usa um domínio de evento. 

1. Crie um domínio, que pode dar suporte a até 100.000 tópicos e cada tópico de domínio pode ter 500 assinaturas de evento. Esse modelo forneceria 500 * 100.000 assinaturas de evento. 
1. Crie uma assinatura de função do Azure para o tópico do sistema de armazenamento do Azure. Quando a função recebe eventos do armazenamento do Azure, ele pode enriquecer e publicar eventos em um tópico de domínio apropriado. Por exemplo, a função pode analisar o nome do arquivo de BLOB para determinar o tópico domínio de destino e publicar o evento no tópico de domínio. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Domínios de grade do AzureEvent – caso de uso 2":::


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como configurar domínios de evento, criar tópicos, criar assinaturas de evento e publicar eventos, consulte [gerenciar domínios de evento](./how-to-event-domains.md).

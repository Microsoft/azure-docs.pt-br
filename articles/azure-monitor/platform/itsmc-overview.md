---
title: Visão geral de Conector de Gerenciamento de Serviços de TI
description: Este artigo fornece uma visão geral de Conector de Gerenciamento de Serviços de TI (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: d22bb05ad6db3630e9b0242e098fd81f65e34b05
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007305"
---
# <a name="it-service-management-connector-overview"></a>Visão geral de Conector de Gerenciamento de Serviços de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

O Conector de Gerenciamento de Serviços de TI (ITSMC) permite que você conecte o Azure a um produto ou serviço de ITSM (gerenciamento de serviços de ti) com suporte.

Os serviços do Azure, como o Azure Log Analytics e Azure Monitor fornecem ferramentas para detectar, analisar e solucionar problemas com seus recursos do Azure e não Azure. Mas os itens de trabalho relacionados a um problema normalmente residem em um produto ou serviço de ITSM. O ITSMC fornece uma conexão bidirecional entre o Azure e as ferramentas de ITSM para ajudá-lo a resolver problemas com mais rapidez.

## <a name="configuration-steps"></a>Etapas de configuração

O ITSMC é compatível com conexões com as seguintes ferramentas de ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> A partir de 1-out-2020 Cherwell e as integrações de ITSM Provance com o alerta do Azure não serão mais habilitadas para novos clientes. Não haverá suporte para novas conexões de ITSM.
> Haverá suporte para conexões de ITSM existentes.

Com o ITSMC, você pode:

-  Crie itens de trabalho em sua ferramenta de ITSM, com base em seus alertas do Azure (alertas de métrica, alertas do log de atividades e alertas de Log Analytics).
-  Opcionalmente, você pode sincronizar seu incidente e alterar os dados de solicitação na ferramenta de ITSM para um espaço de trabalho do Azure Log Analytics.

Para obter informações sobre os termos legais e a política de privacidade, consulte a [declaração de privacidade da Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Você pode começar a usar o ITSMC concluindo as seguintes etapas:

1. [Configure seu ambiente de ITSM para aceitar alertas do Azure.](./itsmc-connections.md)
1. [Configurar a solução de ITSM do Azure](./itsmc-definition.md#add-it-service-management-connector)
1. [Configure o conector do Azure ITSM para seu ambiente de ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Configure o grupo de ações para aproveitar o conector ITSM.](./itsmc-definition.md#define-a-template)

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)

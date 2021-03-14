---
title: Conector de Gerenciamento de Serviços de TI no Azure Monitor
description: Este artigo fornece informações sobre como conectar seus produtos/serviços de ITSM com o Conector de Gerenciamento de Serviço de TI (ITSMC) no Azure Monitor para monitorar e gerenciar os itens de trabalho ITSM de forma centralizada.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009310"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Conectar produtos/serviços de ITSM ao Conector de Gerenciamento de Serviços de TI
Este artigo fornece informações sobre como configurar a conexão entre seu produto/serviço de ITSM e o ITSMC (Conector de Gerenciamento de Serviços de TI) no Log Analytics para gerenciar seus itens de trabalho de forma centralizada. Para obter mais informações sobre o ITSMC, consulte [Visão geral](./itsmc-overview.md).

Há suporte para os seguintes produtos/serviços ITSM. Selecione o produto para exibir informações detalhadas sobre como conectar o produto ao ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Sugerimos que nossos clientes Cherwell e Provance usem a [ação de webhook](./action-groups.md#webhook) para o ponto de extremidade Cherwell e Provance como outra solução para a integração.

## <a name="ip-ranges-for-itsm-partners-connections"></a>Intervalos de IP para conexões de parceiros de ITSM
Para listar os endereços IP de ITSM a fim de permitir conexões de ITSM das ferramentas de ITSM de parceiros, recomendamos que o liste todo o intervalo de IP público da região do Azure em que o espaço de trabalho LogAnalytics pertence. [detalhes aqui](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Para regiões EUS/WEU/EUS2/WUS2/centro-sul dos EUA, o cliente pode listar somente a marca de rede do usuário.

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)

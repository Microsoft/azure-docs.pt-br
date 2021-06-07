---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294135"
---
Agora, você pode usar [Pontos de Extremidade Privados](../articles/private-link/private-endpoint-overview.md) para fazer backup de dados de maneira segura de servidores dentro de uma rede virtual para seu cofre dos Serviços de Recuperação. O ponto de extremidade privado usa um IP do espaço de endereço da VNET para o cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre passa pela rede virtual e por um link privado na rede de backbone da Microsoft. Isso elimina a exposição à Internet pública. Os Pontos de Extremidade Privados podem ser usados para fazer backup e restaurar os bancos de dados SQL e SAP HANA executados dentro de suas VMs do Azure. Eles também podem ser usados para servidores locais que usam o agente MARS.

O backup da VM do Azure não requer conectividade com a Internet e, portanto, não requer Pontos de Extremidade Privados para permitir o isolamento de rede.

Leia mais sobre pontos de extremidade privados para o Backup do Azure [aqui](../articles/backup/private-endpoints.md).
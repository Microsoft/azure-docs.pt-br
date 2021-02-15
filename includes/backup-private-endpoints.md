---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556944"
---
Agora, você pode usar [Pontos de Extremidade Privados](../articles/private-link/private-endpoint-overview.md) para fazer backup de dados de maneira segura de servidores dentro de uma rede virtual para seu cofre dos Serviços de Recuperação. O ponto de extremidade privado usa um IP do espaço de endereço da VNET para o cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre passa pela rede virtual e por um link privado na rede de backbone da Microsoft. Isso elimina a exposição à Internet pública. Os Pontos de Extremidade Privados podem ser usados para fazer backup e restaurar os bancos de dados SQL e SAP HANA executados dentro de suas VMs do Azure. Eles também podem ser usados para servidores locais que usam o agente MARS.

O backup da VM do Azure não requer conectividade com a Internet e, portanto, não requer Pontos de Extremidade Privados para permitir o isolamento de rede.

Leia mais sobre pontos de extremidade privados para o Backup do Azure [aqui](../articles/backup/private-endpoints.md).
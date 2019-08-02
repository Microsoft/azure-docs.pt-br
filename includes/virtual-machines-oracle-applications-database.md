---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361525"
---
### <a name="database-tier"></a>Camada de banco de dados

A camada de banco de dados contém as instâncias de banco de dados para o aplicativo. O banco de dados pode ser um sistema de banco de dados Oracle DB, Oracle RAC ou Oracle Exadata. 

Se a opção for usar Oracle DB, a instância do banco de dados poderá ser implantada no Azure por meio das imagens de Oracle DB disponíveis no Azure Marketplace. Como alternativa, você pode usar a interconexão entre o Azure e o OCI para implantar o Oracle DB em um modelo de PaaS no OCI.

Para o Oracle RAC, você pode implantar o Oracle RAC no Azure CloudSimple no modelo de IaaS ou em OCI no modelo de PaaS. É recomendável que você use um sistema de RAC de dois nós. 

Por fim, para sistemas Exadata, use a interconexão de OCI e implante o sistema Exadata em OCI. O diagrama de arquitetura anterior mostra um sistema Exadata implantado no OCI em duas sub-redes.

Para cenários de produção, implante várias instâncias do banco de dados em duas zonas de disponibilidade (se implantando no Azure) ou dois domínios de disponibilidade (em OCI). Use o Oracle active Data Guard para sincronizar os bancos de dados primário e em espera.

A camada de banco de dados recebe apenas solicitações da camada intermediária. É recomendável que você configure um grupo de segurança de rede (lista de segurança se estiver implantando o banco de dados em OCI) para permitir apenas solicitações na porta 1521 da camada intermediária e a porta 22 do servidor de bastiões por motivos administrativos.

Para bancos de dados implantados no OCI, uma rede de nuvem virtual separada deve ser configurada com um DRG (gateway de roteamento dinâmico) que está conectado ao seu circuito FastConnect.
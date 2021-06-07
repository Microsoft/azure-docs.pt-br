---
title: Arquitetura de rede do Azure
description: Este artigo fornece uma descrição geral da rede de produção Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89567853"
---
# <a name="azure-network-architecture"></a>Arquitetura de rede do Azure
A arquitetura de rede do Azure fornece conectividade da Internet para os data centers do Azure. Qualquer carga de trabalho implantada (IaaS, PaaS e SaaS) no Azure está aproveitando a rede de datacenter do Azure.

## <a name="network-topology"></a>Topologia de rede
A arquitetura de rede de um datacenter do Azure consiste nos seguintes componentes:

- Rede de borda
- Rede de longa distância
- Rede de gateways regionais
- Rede de datacenter

![Diagrama da rede do Azure](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Componentes de rede
Uma breve descrição dos componentes de rede.

- Rede de borda

   - Ponto de demarcação entre a rede da Microsoft e outras redes (por exemplo, Internet, rede corporativa)
   - Fornece emparelhamento de Internet e [ExpressRoute](../../expressroute/expressroute-introduction.md) no Azure

- Rede de longa distância

   - Rede de backbone inteligente da Microsoft que abrange o globo
   - Fornece conectividade entre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

- Gateway regional

   - Ponto de agregação para todos os data centers em uma região do Azure
   - Fornece conectividade maciça entre data centers em uma região do Azure (por exemplo, várias centenas de terabits por Datacenter)

- Rede de datacenter

   - Fornece conectividade entre os servidores no datacenter com baixa largura de banda superscrição

Os componentes de rede acima foram projetados para fornecer disponibilidade máxima para dar suporte aos negócios de nuvem sempre ativas e sempre disponíveis. A redundância é projetada e incorporada à rede a partir do aspecto físico até o protocolo de controle.

## <a name="datacenter-network-resiliency"></a>Resiliência de rede do datacenter
Vamos ilustrar o princípio de design de resiliência usando a rede do datacenter.

A rede do datacenter é uma versão modificada de uma [rede FEC](https://en.wikipedia.org/wiki/Clos_network), fornecendo alta largura de banda de alto nível para o tráfego de escala de nuvem. A rede é construída usando um grande número de dispositivos de mercadoria para reduzir o impacto causado por uma falha de hardware individual. Esses dispositivos estão estrategicamente localizados em locais físicos diferentes com um domínio de energia e resfriamento separado para reduzir o impacto de um evento de ambiente.  No plano de controle, todos os dispositivos de rede estão sendo executados como o modo de roteamento da camada 3 do modelo OSI, o que elimina a questão histórica do loop de tráfego. Todos os caminhos entre diferentes camadas estão ativos para fornecer alta redundância e largura de banda usando o roteamento de Equal-Cost vários caminhos (ECMP).

O diagrama a seguir demonstra que a rede do datacenter é construída por diferentes camadas de dispositivos de rede. As barras no diagrama representam grupos de dispositivos de rede que fornecem conectividade de alta largura de banda e redundância.

![Rede de datacenter](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Recursos, local e segurança física do Azure](physical-security.md)
- [Disponibilidade da infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](protection-customer-data.md)

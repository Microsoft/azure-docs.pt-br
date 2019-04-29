---
title: Disponibilidade da infraestrutura do Azure
description: O artigo descreve os níveis de redundância para fornecer a máxima disponibilidade possível para dados dos clientes.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a9a55f61f032512be032897d5f21ece405844634
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587182"
---
# <a name="azure-infrastructure-availability"></a>Disponibilidade da infraestrutura do Azure
O Azure fornece disponibilidade robusta, com base na redundância abrangente obtida com a tecnologia de virtualização. O Azure fornece vários níveis de redundância para fornecer a máxima disponibilidade possível aos dados dos clientes.

## <a name="temporary-outages-and-natural-disaster"></a>Desastres naturais e interrupções temporárias
A equipe de Operações e infraestrutura de nuvem da Microsoft projeta, cria, opera e aprimora a segurança da infraestrutura de nuvem. Essa equipe garante que a infraestrutura do Azure esteja fornecendo alta disponibilidade e confiabilidade, alta eficiência e escalabilidade inteligente. A equipe fornece uma nuvem mais segura, privada e confiável.

Fontes de alimentação ininterrupta e grandes bancos de baterias garantem que a eletricidade permaneça contínua caso ocorra uma interrupção de energia de curto prazo. Geradores de emergência fornecem energia de backup para interrupções prolongadas e manutenção planejada. Se ocorrer um desastre natural, o datacenter pode usar reservas de combustível no local.

Redes de fibra ótica robustas e de alta velocidade conectam datacenters com outros grandes hubs e usuários de internet. Os nós de computação hospedam cargas de trabalho mais próximas dos usuários para reduzir a latência, fornecer redundância geográfica e aumentar a resiliência geral do serviço. Uma equipe de engenheiros trabalha sem parar para garantir que os serviços estejam permanentemente disponíveis.

A Microsoft garante alta disponibilidade por meio de monitoramento avançado e resposta a incidentes, suporte a serviços e capacidade de failover de backup. Centros de operações da Microsoft distribuídos geograficamente operam 24 horas por dia, 7 dias por semana, 365 dias por ano. A rede do Azure é uma das maiores do mundo. A rede de fibra ótica e de distribuição de conteúdo conecta datacenters e nós de borda para garantir alto desempenho e confiabilidade.

## <a name="disaster-recovery"></a>Recuperação de desastre
O Azure mantém seus dados duráveis em dois locais. Você pode escolher o local do site de backup. Em ambos os locais, o Azure mantém constantemente três réplicas íntegras de seus dados.

## <a name="database-availability"></a>Disponibilidade do banco de dados
O Azure garante que um banco de dados seja acessível pela Internet por meio de um gateway de Internet com disponibilidade de banco de dados sustentada. O monitoramento avalia a saúde e o estado dos bancos de dados ativos em intervalos de cinco minutos.

## <a name="storage-availability"></a>Disponibilidade de armazenamento
O Azure fornece armazenamento por meio de um serviço de armazenamento altamente escalonável e durável, que fornece pontos de extremidade de conectividade. Isso significa que um aplicativo pode acessar o serviço de armazenamento diretamente. O serviço de armazenamento processa as solicitações de armazenamento de entrada com eficiência, com integridade transacional.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](azure-protection-of-customer-data.md)

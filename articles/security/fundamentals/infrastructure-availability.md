---
title: Disponibilidade de infraestrutura do Azure-segurança do Azure
description: Este artigo fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure e fornecer a máxima disponibilidade dos dados dos clientes.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68727226"
---
# <a name="azure-infrastructure-availability"></a>Disponibilidade da infraestrutura do Azure
Este artigo fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure e fornecer a máxima disponibilidade dos dados dos clientes. O Azure fornece disponibilidade robusta, com base na redundância abrangente obtida com a tecnologia de virtualização.

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

- [Recursos, local e segurança física do Azure](physical-security.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](protection-customer-data.md)

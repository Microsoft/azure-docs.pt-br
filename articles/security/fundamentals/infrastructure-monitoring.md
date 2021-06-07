---
title: Monitoramento da infraestrutura do Azure
description: Saiba mais sobre os aspectos de monitoramento de infraestrutura da rede de produção do Azure, como a verificação de vulnerabilidade.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87542935"
---
# <a name="azure-infrastructure-monitoring"></a>Monitoramento da infraestrutura do Azure   

## <a name="configuration-and-change-management"></a>Configuração e gerenciamento de alterações
O Azure revisa e atualiza as definições de configuração e as configurações de linha de base de hardware, software e dispositivos de rede anualmente. As alterações são desenvolvidas, testadas e aprovadas antes de entrar no ambiente de produção de um ambiente de desenvolvimento e/ou teste.

As configurações de linha de base necessárias para os serviços baseados no Azure são revisadas pela equipe de segurança e conformidade do Azure e pelas equipes de serviço. Uma revisão da equipe de serviço faz parte do teste que ocorre antes da implantação de seu serviço de produção.

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades
O gerenciamento de atualizações de segurança ajuda a proteger os sistemas de vulnerabilidades conhecidas. O Azure usa sistemas de implantação integrados para gerenciar a distribuição e a instalação de atualizações de segurança para software da Microsoft. O Azure também pode se basear nos recursos do Microsoft Security Response Center (MSRC). O MSRC identifica, monitora, responde e resolve incidentes de segurança e vulnerabilidades na nuvem o tempo todo, todos os dias do ano.

## <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade
A verificação de vulnerabilidade é executada em sistemas operacionais de servidor, bancos de dados e dispositivos de rede. As verificações de vulnerabilidade são executadas, no mínimo, a cada trimestre. O Azure contrata assessores independentes para realizar testes de penetração do limite do Azure. Os exercícios da equipe vermelha também são rotineiramente realizados e os resultados são usados para melhorar a segurança.

## <a name="protective-monitoring"></a>Monitoramento de proteção
A segurança do Azure definiu requisitos para monitoramento ativo. As equipes de serviço configuram ferramentas de monitoramento ativo de acordo com esses requisitos. As ferramentas de monitoramento ativo incluem o Microsoft Monitoring Agent (MMA) e o System Center Operations Manager. Essas ferramentas são configuradas para fornecer alertas de tempo ao pessoal de segurança do Azure em situações que exigem ação imediata.

## <a name="incident-management"></a>Gerenciamento de incidentes
A Microsoft implementa um processo de gerenciamento de incidentes de segurança para facilitar uma resposta coordenada a incidentes caso eles ocorram.

Se a Microsoft tomar conhecimento do acesso não autorizado a dados de clientes armazenados em seus equipamentos ou instalações, ou tomar conhecimento de acesso não autorizado a tais equipamentos ou instalações, resultando em perda, divulgação ou alteração de dados de clientes, a Microsoft executará as seguintes ações:

- Avisa prontamente o cliente do incidente de segurança.
- Investiga imediatamente o incidente de segurança e fornece aos clientes informações detalhadas sobre o incidente de segurança.
- Toma medidas razoáveis e imediatas para atenuar os efeitos e minimizar os danos resultantes do incidente de segurança.

Uma estrutura de gerenciamento de incidentes foi estabelecida para definir funções e alocar responsabilidades. A equipe de gerenciamento de incidentes de segurança do Azure é responsável pelo gerenciamento de incidentes de segurança, incluindo escalonamento, e garantindo o envolvimento de equipes de especialistas quando necessário. Os gerenciadores de operações do Azure são responsáveis por supervisionar a investigação e a resolução de incidentes de segurança e privacidade.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](physical-security.md)
- [Disponibilidade da infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](infrastructure-operations.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](protection-customer-data.md)

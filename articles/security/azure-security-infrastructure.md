---
title: Segurança da infraestrutura do Azure | Microsoft Docs
description: O artigo descreve como a Microsoft trabalha para proteger nossos datacenters do Azure.
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
ms.openlocfilehash: dc9b4db37e811d8bac6df2d532fd3629d98c9650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586537"
---
# <a name="azure-infrastructure-security"></a>Segurança da infraestrutura do Azure
O Microsoft Azure é executado em datacenters gerenciados e operados pela Microsoft. Esses datacenters geograficamente dispersos estão em conformidade com os principais padrões do setor, como ISO/IEC 27001:2013 e o NIST SP 800-53, em relação a segurança e confiabilidade. Os datacenters são gerenciados, monitorados e administrados pela equipe de operações da Microsoft. A equipe de operações tem anos de experiência no fornecimento dos maiores serviços online do mundo, com continuidade 24 horas por dia, 7 dias por semana.

Esta série de artigos fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure. O artigos aborda:

- [Segurança física](azure-physical-security.md)
- [Disponibilidade](azure-infrastructure-availability.md)
- [Componentes e limites](azure-infrastructure-components.md)
- [Arquitetura de rede](azure-infrastructure-network.md)
- [Rede de produção](azure-production-network.md)
- [Banco de Dados SQL](azure-infrastructure-sql.md)
- [Operações](azure-infrastructure-operations.md)
- [Monitoramento](azure-infrastructure-monitoring.md)
- [Integridade](azure-infrastructure-integrity.md)
- [Proteção de dados](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade compartilhada
É importante entender a divisão da responsabilidade entre você e a Microsoft. No local, você possui a pilha inteira, mas à medida que você se muda para a nuvem, algumas responsabilidades são transferidas para a Microsoft. O gráfico a seguir ilustra as áreas de responsabilidade, de acordo com o tipo de implantação de sua pilha (software como um serviço [SaaS], plataforma como um serviço [PaaS], infraestrutura como um serviço [IaaS] e local).

![Gráfico mostrando responsabilidades][1]

Você sempre é responsáveis pelo seguinte, independentemente do tipo de implantação:

- Dados
- Pontos de extremidade
- Conta
- gerenciamento de acesso

Tenha certeza de que você entende a divisão da responsabilidade entre você e a Microsoft em uma implantação de SaaS, PaaS e IaaS. Para obter mais informações, consulte [compartilhado responsabilidades para a computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png

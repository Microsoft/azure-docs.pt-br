---
title: Azure Defender para SQL – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para SQL.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 21f07ed76ef0b900a723677acc2bf576649fe861
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702004"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introdução ao Azure Defender para SQL

O Azure defender para SQL inclui dois planos do Azure Defender que estendem o [pacote de segurança de dados](../azure-sql/database/azure-defender-for-sql.md) da Central de Segurança do Azure para proteger os bancos de dados e os respectivos dados, onde quer que estejam localizados. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|**Azure Defender para Servidores de Banco de Dados SQL do Azure** – GA (Disponibilidade Geral)<br>**Azure Defender para servidores SQL em computadores** – Em GA (disponibilidade geral) |
|Preço:|Os dois planos que formam o **Azure Defender para SQL** são cobrados conforme mostrado na [página de preços](security-center-pricing.md)|
|Versões do SQL protegidas:|[SQL nas máquinas virtuais do Azure](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Servidores SQL habilitados para Azure Arc](/sql/sql-server/azure-arc/overview)<br>Servidores SQL locais em computadores Windows sem o Azure Arc<br>[Bancos de dados individuais](../azure-sql/database/single-database-overview.md) e [pools elásticos](../azure-sql/database/elastic-pool-overview.md) do SQL do Azure<br>[Instância Gerenciada do SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Pool de SQL dedicado do Azure Synapse Analytics (antigo SQL DW)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) US Gov<br>![Sim](./media/icons/yes-icon.png) Governo da China (**Parcial**: subconjunto de alertas e avaliação de vulnerabilidade para servidores SQL. Proteções contra ameaças comportamentais não estão disponíveis.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>O que o Azure Defender para SQL protege?

**O Azure Defender para SQL** inclui dois planos separados do Azure Defender:

- O **Azure Defender para Servidores de Banco de Dados SQL do Azure** protege:
    - [Banco de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md)
    - [Instância Gerenciada do SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Pool de SQL dedicado no Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- O **Azure Defender para servidores SQL em computadores** estende as proteções para os servidores SQL nativos do Azure a fim de oferecer suporte total a ambientes híbridos e proteger os servidores SQL (todas as versões compatíveis) hospedados no Azure, em outros ambientes de nuvem e até mesmo nos computadores locais:
    - [SQL Server em Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Servidores SQL locais:
        - [SQL Server habilitado para Azure Arc (versão prévia)](/sql/sql-server/azure-arc/overview)
        - [SQL Server em execução em computadores Windows sem o Azure Arc](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quais são os benefícios do Azure Defender para SQL?

Esses dois planos incluem a funcionalidade para identificar e atenuar possíveis vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar ameaças aos seus banco de dados:

- [Avaliação de vulnerabilidade](../azure-sql/database/sql-vulnerability-assessment.md) – Serviço de verificação para descobrir, acompanhar e ajudar você a corrigir possíveis vulnerabilidades de banco de dados. As verificações de avaliação fornecem uma visão geral do estado de segurança dos computadores SQL e detalhes sobre eventuais descobertas de segurança.

- [Proteção avançada contra ameaças](../azure-sql/database/threat-detection-overview.md) – Serviço de detecção que monitora continuamente os SQL Servers em busca de ameaças como injeção de SQL, ataques de força bruta e abuso de privilégios. Esse serviço fornece alertas de segurança orientados a ações na Central de Segurança do Azure com detalhes sobre a atividade suspeita, orientação sobre como atenuar as ameaças e opções para continuar suas investigações com o Azure Sentinel. 
    > [!TIP]
    > Veja uma lista de alertas de segurança para servidores SQL [na página de referência de alertas](alerts-reference.md#alerts-sql-db-and-warehouse).


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Quais tipos de alertas são fornecidos pelo Azure Defender para SQL?

Os alertas de segurança aprimorados da inteligência contra ameaças são disparados quando há:

- **Possíveis ataques de injeção de SQL** – Incluindo vulnerabilidades detectadas quando os aplicativos geram uma instrução SQL com falha no banco de dados
- **Padrões anômalos de consulta e acesso a banco de dados** – Por exemplo, um número estranhamente elevado de tentativas de entrada sem sucesso usando credenciais diferentes (uma tentativa de força bruta)
- **Atividade de banco de dados suspeitas** – Por exemplo, um usuário legítimo acessando um SQL Server de um computador violado que se comunicou com um servidor C&C de mineração de criptografia

Um alerta inclui detalhes sobre o incidente que o disparou, bem como recomendações sobre como investigar e corrigir as ameaças.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para SQL. Para usar os serviços descritos:

- Usar o Azure Defender para servidores SQL em computadores para [verificar se há vulnerabilidades nos servidores SQL](defender-for-sql-usage.md)
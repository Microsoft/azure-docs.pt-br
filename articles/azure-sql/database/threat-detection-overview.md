---
title: Proteção Avançada contra Ameaças
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças à segurança no banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 931e914cd3c184136395a9bb9a7e148a90e9fb91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461922"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Proteção avançada contra ameaças para o banco de dados SQL do Azure, o SQL Instância Gerenciada e o Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Proteção avançada contra ameaças para o [banco de dados SQL do](sql-database-paas-overview.md)Azure, o [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) e o [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A proteção avançada contra ameaças faz parte da oferta do [Azure defender para SQL](azure-defender-for-sql.md) , que é um pacote unificado para recursos avançados de segurança do SQL. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do Azure defender central para o portal do SQL.

## <a name="overview"></a>Visão geral

A proteção avançada contra ameaças fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de consultas e acesso a banco de dados. A proteção avançada contra ameaças integra alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomendação de ações sobre como investigar e atenuar a ameaça. A proteção avançada contra ameaças simplifica a endereçamento de ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados.

Para uma experiência de investigação completa, é recomendável habilitar a auditoria, que grava eventos de banco de dados em um log de auditoria em sua conta de armazenamento do Azure.  Para habilitar a auditoria, consulte [auditoria de banco de dados SQL do Azure e Synapse do Azure](../../azure-sql/database/auditing-overview.md) ou [auditoria para o Azure SQL instância gerenciada](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Alertas

A Proteção Avançada contra Ameaças para o Banco de Dados SQL do Azure detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Para obter uma lista de alertas para o banco de dados SQL do Azure, consulte os [alertas para o banco de dados SQL e o Azure Synapse Analytics na central de segurança do Azure](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-detection-of-a-suspicious-event"></a>Explorar a detecção de um evento suspeito

Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anômalas, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornecerá informações sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.

![Relatórios de atividades anômalas](./media/threat-detection-overview/anomalous_activity_report.png)

1. Clique no link **exibir alertas recentes do SQL** no email para iniciar o portal do Azure e mostrar a página de alertas da central de segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no banco de dados.

   ![Ameaças de atividade](./media/threat-detection-overview/active_threats.png)

1. Clique em um alerta específico para obter os detalhes e as ações adicionais para investigar essa ameaça e corrigir ameaças futuras.

   Por exemplo, a injeção de SQL é um dos problemas mais comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos orientados a dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, violando ou modificando os dados no banco de dados. Para os alertas de Injeção de SQL, os detalhes do alerta incluem a instrução SQL vulnerável que foi explorada.

   ![Alerta específico](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Explorar alertas no portal do Azure

A proteção avançada contra ameaças integra seus alertas à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/). Blocos de proteção avançada contra ameaças do SQL Live no banco de dados e SQL Azure as lâminas do defender no portal do Azure acompanhar o status das ameaças ativas.

Clique em **alerta de proteção avançada contra ameaças** para iniciar a página de alertas da central de segurança do Azure e obtenha uma visão geral das ameaças do SQL ativas detectadas no banco de dados.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="Visão geral de alertas da proteção avançada contra ameaças no banco de dados":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="proteção avançada contra ameaças na central de segurança":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [proteção avançada contra ameaças no banco de dados SQL do azure & Azure Synapse](threat-detection-configure.md).
- Saiba mais sobre a [proteção avançada contra ameaças no Azure SQL instância gerenciada](../managed-instance/threat-detection-configure.md).
- Saiba mais sobre o [Azure Defender para SQL](azure-defender-for-sql.md).
- Saiba mais sobre a [auditoria do banco de dados SQL do Azure](../../azure-sql/database/auditing-overview.md)
- Saiba mais sobre a [central de segurança do Azure](../../security-center/security-center-introduction.md)
- Para obter mais informações sobre preços, consulte a [página de preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/)
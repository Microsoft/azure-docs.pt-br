---
title: Usar Azure Resource Health para monitorar a integridade do banco de dados
description: Use Azure Resource Health para monitorar o banco de dados SQL do Azure e a integridade do SQL Instância Gerenciada do Azure, ajuda você a diagnosticar e obter suporte quando um problema do Azure Impacta os recursos do SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 03/24/2021
ms.openlocfilehash: 4434c796a41126444b8785440427123e88e0b528
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107781"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Use Resource Health para solucionar problemas de conectividade do banco de dados SQL do Azure e do SQL Azure Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Resource Health](../../service-health/resource-health-overview.md#get-started) para o banco de dados SQL do Azure e o SQL instância gerenciada do Azure o ajuda a diagnosticar e obter suporte quando um problema do Azure Impacta os recursos do SQL. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.

![Visão geral](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Verificações de integridade

O Azure Resource Health determina a integridade de seu recurso SQL examinando o êxito e a falha de logins no recurso. Atualmente, Resource Health para o recurso do banco de dados SQL examina apenas as falhas de logon devido a um erro do sistema e não a um erro do usuário. O status de Resource Health é atualizado a cada 1 a 2 minutos.

## <a name="health-states"></a>Estados de integridade

### <a name="available"></a>Disponível

Um status **disponível** significa que Resource Health não detectou falhas de logon devido a erros do sistema em seu recurso SQL.

![Disponível](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

Um status **Degradado** significa que o Resource Health detectou a maioria dos logins bem-sucedidos, mas também algumas falhas. Esses são erros de logon transitório mais prováveis. Para reduzir o impacto dos problemas de conexão causados por erros de logon transitórios, implemente a [lógica de repetição](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) em seu código.

![Degradado](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Indisponível

Um status **Indisponível** significa que o Resource Health detectou falhas de login consistentes em seu recurso SQL. Se o recurso permanecer nesse estado por um longo período de tempo, contate o suporte.

![Indisponível](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

O status de integridade de **Desconhecido** indica que o Resource Health não recebeu informações sobre esse recurso por mais de 10 minutos. Embora esse status não seja uma indicação definitiva sobre o estado do recurso, é um ponto de dados importante no processo de solução de problemas. Se o seu recurso permanecer nesse estado por um período prolongado, entre em contato com o suporte. Se você está tendo problemas com o recurso, o status de integridade Desconhecido pode sugerir que um evento na plataforma está afetando o recurso.

![Unknown](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informações de histórico

Você pode acessar até 14 dias de histórico de integridade na seção Histórico de integridade de Resource Health. A seção também conterá o motivo do tempo de inatividade (quando disponível) para os tempos de inatividade reportados pelo Resource Health. Atualmente, o Azure mostra o tempo de inatividade para seu recurso de banco de dados em uma granularidade de dois minutos. Provavelmente, o tempo real de inatividade é inferior a um minuto. A média é de 8 segundos.

### <a name="downtime-reasons"></a>Motivos de tempo de inatividade

Quando seu banco de dados apresentar tempo de inatividade, a análise é executada para determinar um motivo. Quando disponível, o motivo do tempo de inatividade é relatado na seção Histórico de Integridade do Resource Health. Os motivos do tempo de inatividade são normalmente publicados em 45 minutos após um evento.

#### <a name="planned-maintenance"></a>Manutenção planejada

A infraestrutura do Azure realiza periodicamente a manutenção planejada – a atualização de componentes de hardware ou software no datacenter. Enquanto o banco de dados passa por manutenção, o SQL do Azure pode encerrar algumas conexões existentes e recusar novas. As falhas de logon experimentadas durante a manutenção planejada são normalmente transitórias e a [lógica de repetição](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) ajuda a reduzir o impacto. Se você continuar a ter erros de logon, contate o suporte.

#### <a name="reconfiguration"></a>Reconfiguração

As reconfigurações são consideradas condições transitórias e são esperadas de tempos em tempos. Esses eventos podem ser acionados por balanceamento de carga ou falhas de software/hardware. Qualquer aplicativo de produção de cliente que se conecta a um banco de dados de nuvem deve implementar uma [lógica de nova tentativa](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) de conexão robusta, pois isso ajudaria a mitigar essas situações e geralmente tornaria os erros transparentes para o usuário final.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [a lógica de repetição para erros transitórios](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [Solucionar problemas, diagnosticar e impedir erros de conexão SQL](troubleshoot-common-connectivity-issues.md).
- Saiba mais sobre como [configurar alertas de Resource Health](../../service-health/resource-health-alert-arm-template-guide.md).
- Obtenha uma visão geral do [Resource Health](../../service-health/resource-health-overview.md).
- Examine as [perguntas frequentes Resource Health](../../service-health/resource-health-faq.md).

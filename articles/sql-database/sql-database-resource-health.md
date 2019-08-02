---
title: Use o Azure Resource Health para monitorar a integridade do banco de dados SQL | Microsoft Docs
description: Use o Azure Resource Health para monitorar a integridade do Banco de Dados SQL, ajuda a diagnosticar e obter suporte quando um problema do Azure afeta seus recursos SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 4757236d179e4d6ceb626f58f12cfe48799eed7a
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854357"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Use o Resource Health para solucionar problemas de conectividade do Banco de Dados SQL do Azure

## <a name="overview"></a>Visão geral

A [Resource Health](../service-health/resource-health-overview.md#get-started) para o SQL Database ajuda você a diagnosticar e obter suporte quando um problema do Azure afeta seus recursos SQL. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. O Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.

![Visão geral](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Verificações de integridade

O Azure Resource Health determina a integridade de seu recurso SQL examinando o êxito e a falha de logins no recurso. Atualmente, o Azure Resource Health para seu recurso de banco de dados SQL examina somente falhas de logon devido a erro do sistema e não erro do usuário. O Azure Resource Health é atualizado a cada 1-2 minutos.

## <a name="health-states"></a>Estados de integridade

### <a name="available"></a>Disponível

Um status **Disponível** significa que o Resource Health não detectou falhas de logins devido a erros do sistema em seu recurso SQL.

![Disponível](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

Um status **Degradado** significa que o Resource Health detectou a maioria dos logins bem-sucedidos, mas também algumas falhas. Esses são erros de logon transitório mais prováveis. Para reduzir o impacto de problemas de conexão causados por erros de login temporários, implemente [lógica de nova tentativa](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) em seu código.

![Degradado](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Indisponível

Um status **Indisponível** significa que o Resource Health detectou falhas de login consistentes em seu recurso SQL. Se o seu recurso permanecer nesse estado por um período prolongado, entre em contato com o suporte.

![Indisponível](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

O status de integridade de **Desconhecido** indica que o Resource Health não recebeu informações sobre esse recurso por mais de 10 minutos. Embora esse status não seja uma indicação definitiva sobre o estado do recurso, é um ponto de dados importante no processo de solução de problemas. Se o seu recurso permanecer nesse estado por um período prolongado, entre em contato com o suporte. Se você está tendo problemas com o recurso, o status de integridade Desconhecido pode sugerir que um evento na plataforma está afetando o recurso.

![Unknown](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informações de histórico

Você pode acessar até 14 dias de histórico de integridade na seção Histórico de integridade da Resource Health. A seção também conterá o motivo do tempo de inatividade (quando disponível) para os tempos de inatividade reportados pelo Resource Health. Atualmente, o Azure mostra o tempo de inatividade do seu recurso de banco de dados SQL em uma granularidade de dois minutos. O tempo de inatividade real é provavelmente menor que um minuto - a média é de 8s.

### <a name="downtime-reasons"></a>Motivos de tempo de inatividade

Quando o banco de dados do SQL do Microsoft Azure sofre um tempo de inatividade, a análise é executada para determinar um motivo. Quando disponível, o motivo do tempo de inatividade é relatado na seção Histórico de Integridade do Resource Health. Razões de tempo de inatividade são normalmente publicadas 30 minutos após um evento.

#### <a name="planned-maintenance"></a>Manutenção planejada

A infraestrutura do Azure executa periodicamente a manutenção planejada - atualização de componentes de hardware ou software no datacenter. Enquanto o banco de dados passa por manutenção, o SQL pode terminar algumas conexões existentes e recusar novas. As falhas de logon ocorridas durante a manutenção planejada normalmente são transitórias e a [lógica de nova tentativa](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) ajuda a reduzir o impacto. Se você continuar a experimentar erros de logon, contate o suporte.

#### <a name="reconfiguration"></a>Reconfiguração

As reconfigurações são consideradas condições transitórias e são esperadas de tempos em tempos. Esses eventos podem ser acionados por balanceamento de carga ou falhas de software/hardware. Qualquer aplicativo de produção de cliente que se conecta a um banco de dados de nuvem deve implementar uma [lógica de nova tentativa](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) de conexão robusta, pois isso ajudaria a mitigar essas situações e geralmente tornaria os erros transparentes para o usuário final.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [nova lógica para erros transitórios](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Solucionar problemas, diagnosticar e evitar erros de conexão do SQL](./sql-database-connectivity-issues.md)
- Saiba mais sobre [configuração de alertas do Resource Health](../service-health/resource-health-alert-arm-template-guide.md)
- Obtenha uma visão geral de [Resource Health](../service-health/resource-health-overview.md)
- [Perguntas frequentes sobre o Azure Resource Health](../service-health/resource-health-faq.md)

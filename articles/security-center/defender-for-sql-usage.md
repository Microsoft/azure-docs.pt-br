---
title: Como configurar o Azure defender para SQL
description: Saiba como habilitar o plano opcional do Azure defender para SQL da central de segurança do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: a91329d3bd0247932614233ef5b1ec71bf4d2a6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465456"
---
# <a name="enable-azure-defender-for-sql-servers-on-machines"></a>Habilitar o Azure defender para SQL Servers em computadores 

Este plano do Azure defender detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Você receberá alertas quando ocorrerem atividades suspeitas no banco de dados possíveis vulnerabilidades ou ataques de injeção de SQL, além de padrões anômalos de consultas e acesso a banco de dados.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|O **Azure defender para SQL Servers em computadores** é cobrado conforme mostrado na [central de segurança preços](https://azure.microsoft.com/pricing/details/security-center/)|
|Versões do SQL protegidas:|SQL Server do Azure (todas as versões cobertas pelo suporte da Microsoft)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) Governo da China e outros governos|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Configurar o Azure defender para SQL Servers em computadores

Para habilitar este plano:

[Etapa 1. Provisione o agente de Log Analytics no host do SQL Server:](#step-1-provision-the-log-analytics-agent-on-your-sql-servers-host)

[Etapa 2. Habilite o plano opcional na página de preços e configurações da central de segurança:](#step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page)


### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Etapa 1. Provisione o agente de Log Analytics no host do SQL Server:

- **SQL Server na VM do Azure** – se o seu computador SQL estiver hospedado em uma VM do Azure, você poderá [habilitar o provisionamento automático do agente <a name="auto-provision-mma"></a> de log Analytics](security-center-enable-data-collection.md#auto-provision-mma). Como alternativa, você pode seguir o procedimento manual para [carregar suas VMs de Hub de Azure Stack](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms).
- **SQL Server no arco do Azure** – se sua SQL Server for gerenciada por servidores habilitados para [Arc do Azure](../azure-arc/index.yml) , você poderá implantar o agente de log Analytics usando a recomendação da central de segurança "log Analytics Agent deve ser instalado em suas máquinas de arco do Azure baseadas no Windows (versão prévia)". Como alternativa, você pode seguir os métodos de instalação descritos na [documentação do Arc do Azure](../azure-arc/servers/manage-vm-extensions.md).

- **SQL Server local** – se o SQL Server estiver hospedado em um computador Windows local sem o Arc do Azure, você terá duas opções para conectá-lo ao Azure:
    
    - **Implantar o arco do Azure** -você pode conectar qualquer computador Windows à central de segurança. No entanto, o Arc do Azure fornece uma integração mais profunda em *todo* o seu ambiente do Azure. Se você configurar o arco do Azure, verá a página **SQL Server – arco do Azure** no portal e seus alertas de segurança aparecerão em uma guia **segurança** dedicada nessa página. Portanto, a primeira e recomendada opção é [Configurar o arco do Azure no host](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) e seguir as instruções para **SQL Server no arco do Azure**, acima.
        
    - **Conectar o computador Windows sem o arco do Azure** – se você optar por conectar um SQL Server em execução em um computador Windows sem usar o Arc do Azure, siga as instruções em [conectar computadores Windows ao Azure monitor](../azure-monitor/agents/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Etapa 2. Habilite o plano opcional na página de preços e configurações da central de segurança:

1. No menu da central de segurança, abra a página de **configurações de & de preços** .

    - Se você estiver usando o **espaço de trabalho padrão da central de segurança do Azure** (chamado "defaultworkspace-[sua ID de assinatura]-[Região]"), selecione a **assinatura** relevante.

    - Se você estiver usando **um espaço de trabalho não padrão**, selecione o **espaço** de trabalho relevante (Insira o nome do espaço de trabalho no filtro, se necessário):

        ![Localizando seu espaço de trabalho não padrão por título](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Defina a **opção para o** **Azure defender para SQL Servers em computadores** que planejam. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="Página de preços da central de segurança com planos opcionais":::

    O plano será habilitado em todos os SQL Servers conectados ao espaço de trabalho selecionado. A proteção estará totalmente ativa após a primeira reinicialização da instância de SQL Server.

    >[!TIP] 
    > Para criar um novo espaço de trabalho, siga as instruções em [criar um log Analytics espaço de trabalho](../azure-monitor/logs/quick-create-workspace.md).


1. Opcionalmente, configure a notificação por email para alertas de segurança. 
    Você pode definir uma lista de destinatários para receber uma notificação por email quando os alertas da central de segurança são gerados. O email contém um link direto para o alerta na central de segurança do Azure com todos os detalhes relevantes. Para obter mais informações, consulte [configurar notificações por email para alertas de segurança](security-center-provide-security-contact-details.md).


## <a name="azure-defender-for-sql-alerts"></a>Alertas do Azure defender para SQL
Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar máquinas do SQL. Esses eventos podem disparar alertas mostrados na [página de referência de alertas](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Explorar e investigar alertas de segurança

Os alertas do Azure defender para SQL estão disponíveis na página de alertas da central de segurança, na guia Segurança do recurso, no [painel do Azure defender](azure-defender-dashboard.md)ou por meio do link direto nos emails de alerta.

1. Para exibir alertas, selecione **alertas de segurança** no menu da central de segurança e selecione um alerta.

1. Os alertas são projetados para serem independentes, com etapas de correção detalhadas e informações de investigação em cada um. Você pode investigar ainda mais usando outros recursos da central de segurança do Azure e do Azure Sentinel para uma visão mais ampla:

    * Habilite o recurso de auditoria do SQL Server para investigações adicionais. Se você for um usuário do Azure Sentinel, poderá carregar os logs de auditoria do SQL dos eventos do log de segurança do Windows para sentinela e aproveitar uma experiência de investigação rica. [Saiba mais sobre a auditoria de SQL Server](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Para melhorar sua postura de segurança, use as recomendações da central de segurança para o computador host indicado em cada alerta. Isso reduzirá os riscos de futuros ataques. 

    [Saiba mais sobre como gerenciar e responder a alertas](security-center-managing-and-responding-alerts.md).


## <a name="next-steps"></a>Próximas etapas

Para ver materiais relacionados, confira o seguinte artigo:

- [Alertas de segurança para o banco de dados SQL e o Azure Synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configurar notificações por email para alertas de segurança](security-center-provide-security-contact-details.md)
- [Saiba mais sobre o Azure Sentinel](../sentinel/index.yml)
---
title: Segurança de dados avançada para IaaS na central de segurança do Azure | Microsoft Docs
description: " Saiba como habilitar a segurança de dados avançada para IaaS na central de segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0b83575baa2221f0b502abbf919654492c7ab6cf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295748"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Segurança de dados avançada para servidores SQL em máquinas virtuais do Azure (visualização pública)
A segurança de dados avançada para servidores SQL em máquinas virtuais do Azure é um pacote unificado para recursos avançados de segurança do SQL. Atualmente, ele (em visualização pública) inclui a funcionalidade de identificando e a redução de vulnerabilidades de banco de dados potenciais e a detecção de atividades anormais que podem indicar uma ameaça ao seu banco de dados. 

Esta oferta de segurança para SQL Servers de VMs do Azure baseia-se na mesma tecnologia fundamental usada no [pacote de segurança de dados avançado do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Visão geral

A segurança avançada de dados fornece um conjunto de recursos avançados de segurança do SQL, que consistem em avaliação de vulnerabilidades e proteção avançada contra ameaças.

* A [Avaliação de vulnerabilidade ](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. Ele fornece visibilidade sobre seu estado de segurança e inclui as etapas para resolver problemas de segurança e aprimorar seu banco de dados fortifications.
* A [proteção avançada contra ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar o SQL Server. Ele monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança orientados a ações em padrões de acesso de banco de dados anormais. Esses alertas fornecem os detalhes da atividade suspeita e as ações recomendadas para investigar e atenuar a ameaça.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Introdução à segurança de dados avançada para SQL em VMs do Azure

As etapas a seguir o ajudarão a começar a usar a segurança de dados avançada para SQL na visualização pública de VMs do Azure.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurar a segurança de dados avançada para SQL em VMs do Azure

**Antes de começar**: Você precisa de um espaço de trabalho Log Analytics para armazenar os logs de segurança que estão sendo analisados. Se você não tiver um, poderá criar um facilmente, conforme explicado em [criar um log Analytics espaço de trabalho no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Conecte a VM que hospeda o SQL Server ao espaço de trabalho Log Analytics. Para obter instruções, consulte [conectar computadores Windows ao Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. No Azure Marketplace, vá para a [solução de segurança de dados do SQL Advanced](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Você pode encontrá-lo usando a opção de pesquisa do Marketplace, como Veja na imagem a seguir.) A página **SQL Advanced Data Security** é aberta.

    ![Segurança de dados avançada para IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Clique em **Criar**. Os locais de trabalho são exibidos.

    ![Criação de segurança de dados avançada](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Selecione o espaço de trabalho a ser usado e clique em **criar**.

   ![Selecione o workspace](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Reinicie o [SQL Server da VM](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Explorar e investigar alertas de segurança

Você pode exibir e gerenciar seus alertas de segurança atuais.

1. Clique em**alertas de segurança**da **central** > de segurança e clique em um alerta.

    ![Localizar alerta](./media/security-center-advanced-iaas-data/find-alert.png)

1. Na coluna **recurso atacado** , clique em um recurso que foi atacado.

1. Para exibir detalhes e ações de alerta para investigar a ameaça atual e tratar futuras ameaças, role para baixo na página **informações gerais** e, na seção **etapas de correção** , clique no link **etapas de investigação** .

    ![Etapas de correção](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Para exibir os logs associados ao disparo do alerta, vá para **espaços de trabalho do log Analytics** e execute as seguintes etapas:

     > [!NOTE]
     > Se os **espaços de trabalho do log Analytics** não aparecerem no menu à esquerda, clique em **todos os serviços**e pesquise os **espaços de trabalho do log Analytics**.

    1. Verifique se as colunas estão exibindo o **tipo de preço** e as colunas **workspaceid** . (Os**espaços de trabalho** > do log Analytics editam**colunas**, adicionam **tipo de preço** e **workspaceid**.)

     ![Editar colunas](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Clique no espaço de trabalho que tem os logs de alerta.

    1. No menu **geral** , clique em **logs**

    1. Clique nos olhos ao lado da tabela **SQLAdvancedThreatProtection** . Os logs são listados.

     ![Exibir logs](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Configurar notificação por email para alertas ATP 

Você pode definir uma lista de destinatários para receber uma notificação por email quando os alertas de ASC são gerados. O email contém um link direto para o alerta na central de segurança do Azure com todos os detalhes relevantes. 

1. Vá para a **central** > de segurança**preços & configurações** e clique na assinatura relevante

    ![Configurações de assinatura](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. No menu **configurações** , clique em **notificações de email**. 
1. Na caixa de texto **endereço de email** , insira os endereços de email para receber as notificações. Você pode inserir mais de um endereço de email separando os endereços de email com uma vírgula (,).  Por exemplo admin1@mycompany.com,admin2@mycompany.comadmin3@mycompany.com

      ![Configurações de email](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nas configurações de **notificação de email** , defina as seguintes opções:
  
    * **Enviar notificação por email para alertas de alta gravidade**: Em vez de enviar emails para todos os alertas, envie somente para alertas de alta gravidade.
    * **Também enviar notificações por email aos proprietários da assinatura**:  Envie notificações também para os proprietários de assinaturas.

1. Na parte superior da tela de **notificações de email** , clique em **salvar**.

  > [!NOTE]
  > Certifique-se de clicar em **salvar** antes de fechar a janela, ou as novas configurações de **notificação de email** não serão salvas.

## <a name="explore-vulnerability-assessment-reports"></a>Explorar relatórios de avaliação de vulnerabilidade

O painel de avaliação de vulnerabilidade fornece uma visão geral dos resultados da avaliação em todos os seus bancos de dados. Você pode exibir a distribuição de bancos de dados de acordo com a versão SQL Server, juntamente com um resumo de falhas em vez de passar bancos de dados e um resumo geral das verificações com falha de acordo com a distribuição de risco.

Você pode exibir os resultados e relatórios da avaliação de vulnerabilidade diretamente do Log Analytics.

1. Navegue até seu espaço de trabalho do Log Analytics com a solução de segurança de dados avançada.
1. Navegue até **soluções** e selecione a solução de **avaliação de vulnerabilidade do SQL** .
1. No painel **Resumo** , clique em **Exibir Resumo** e selecione o **relatório de avaliação de vulnerabilidade do SQL**.

    ![Relatório de Avaliação do SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    O painel de relatório é carregado. Verifique se a janela de tempo está definida para pelo menos **7 dias** desde que as verificações de avaliação de vulnerabilidade sejam executadas em seus bancos de dados em um agendamento fixo de uma vez por 7 dias.

    ![Definir os últimos 7 dias](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Para fazer uma busca detalhada para obter mais detalhes, clique em qualquer um dos elementos do painel. Por exemplo:

   1. Clique em uma verificação de vulnerabilidade na seção **Resumo de verificações com falha** para exibir uma tabela de log Analytics com os resultados dessa verificação em todos os bancos de dados. Os que têm resultados são listados primeiro.

   1. Em seguida, clique para exibir detalhes de cada vulnerabilidade, incluindo a descrição e o impacto da vulnerabilidade, o status, o risco associado e os resultados reais neste banco de dados. Você também pode ver a consulta real que foi executada para executar essa verificação e informações de correção para resolver essa vulnerabilidade.

    ![Selecione o workspace](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Selecione o workspace](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Você pode executar qualquer consulta de Log Analytics em seus dados de resultados da avaliação de vulnerabilidade, para dividir e refinar os dados de acordo com suas necessidades.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Proteção avançada contra ameaças para servidores SQL em alertas de VMs do Azure
Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores SQL. Esses eventos podem disparar os alertas a seguir:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Alertas de padrão de acesso anormal (com suporte na visualização pública)

* **Acesso de local incomum:** este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém faz logon no SQL Server de um local geográfico incomum. Possíveis causas:
     * Um invasor ou um antigo emprego mal-intencionado acessou seu SQL Server.
     * Um usuário legítimo acessou seu SQL Server de um novo local.
* **Acesso de um aplicativo potencialmente prejudicial**: seu alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Possíveis causas:
     * Um invasor tentando violar o SQL usando as ferramentas de ataque comuns.
     * Um teste de penetração legítimo em ação.
* **Acesso de uma entidade de segurança desconhecida**: esse alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém faz logon no SQL Server de uma entidade de segurança incomum (usuário SQL). Possíveis causas:
     * Um invasor ou um antigo emprego mal-intencionado acessou seu SQL Server. 
     * Um usuário legítimo acessou seu SQL Server de com uma nova entidade de segurança.
* **Credenciais SQL de força bruta**: esse alerta é disparado quando há um número alto e anormal de logons com falha com credenciais diferentes. Possíveis causas:
     * Um invasor tentando violar o SQL usando a força bruta.
     * Um teste de penetração legítimo em ação.

### <a name="potential-sql-injection-attacks-coming"></a>Possíveis ataques de injeção de SQL (chegando)

* **Vulnerabilidade à injeção de SQL**: esse alerta é disparado quando um aplicativo gera uma instrução SQL com erro no banco de dados. Esse alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Possíveis causas:
     * Um defeito no código do aplicativo que cria a instrução SQL com erro
     * O código do aplicativo ou procedimentos armazenados não limpam a entrada do usuário ao construir a instrução SQL com erro, o que pode ser explorado para injeção de SQL
* **Potencial injeção de SQL**: esse alerta é disparado quando acontece uma exploração ativa contra uma vulnerabilidade identificada do aplicativo para injeção de SQL. Isso significa que o invasor está tentando inserir instruções SQL maliciosas usando o código de aplicativo ou procedimentos armazenados vulneráveis.

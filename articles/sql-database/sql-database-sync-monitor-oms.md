---
title: Monitorar a sincronização de dados SQL do Azure com logs do Azure Monitor | Microsoft Docs
description: Saiba como monitorar a sincronização de dados SQL do Azure usando os logs do Azure Monitor
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 6e94aac47ce5b45e700e2413d2e86d5f36596348
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614935"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorar a sincronização de dados do SQL com logs do Azure Monitor 

Para verificar o log de atividades da Sincronização de Dados SQL e detectar erros e avisos, anteriormente você precisava verificar a Sincronização de Dados SQL manualmente no portal do Azure ou usar o PowerShell ou a API REST. Siga as etapas deste artigo para configurar uma solução personalizada que melhora a experiência de monitoramento da Sincronização de Dados. Personalize essa solução de acordo com seu cenário.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Atualmente, a Sincronização de Dados SQL **não** dá suporte para Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de Monitoramento para todos os Grupos de Sincronização 

Você não precisa mais examinar os logs de cada Grupo de Sincronização individualmente para procurar problemas. Você pode monitorar todos os seus grupos de sincronização de qualquer uma das suas assinaturas em um só lugar usando uma exibição personalizada do Azure Monitor. Essa exibição apresenta as informações importantes para os clientes da Sincronização de Dados SQL.

![Painel de monitoramento da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações automatizadas por email

Você não precisa mais verificar o log manualmente no Portal do Azure ou por meio do PowerShell ou da API REST. Com o [registra em log do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), você pode criar alertas que vão diretamente para os endereços de email das pessoas que precisam vê-los quando ocorre um erro.

![Notificações por email da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configurar esses recursos de monitoramento? 

Implementar um Monitor personalizado do Azure logs de solução de monitoramento para sincronização de dados SQL em menos de uma hora, realizando os procedimentos a seguir:

É necessário configurar três componentes:

-   Um runbook do PowerShell para alimentar os dados de log de sincronização de dados SQL para logs do Azure Monitor.

-   Um alerta do Azure Monitor para notificações por email.

-   Uma exibição de Monitor do Azure para monitoramento.

### <a name="samples-to-download"></a>Amostras para download

Baixe os dois seguintes exemplos:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição do Azure Monitor de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você configurou o seguinte:

-   Uma conta da Automação do Azure

-   Espaço de trabalho do Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook do PowerShell para obter o Log da Sincronização de Dados SQL 

Use um runbook do PowerShell hospedado na automação do Azure para efetuar pull dos dados de log de sincronização de dados SQL e enviá-lo para os logs do Azure Monitor. Um script de exemplo é incluído. Como pré-requisito, você precisa ter uma conta da Automação do Azure. Em seguida, você precisa criar um runbook e agendá-lo para execução. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre como criar um runbook, consulte [Meu primeiro runbook do PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Em sua conta da Automação do Azure, selecione a guia **Runbooks** em Automação de Processo.

2.  Selecione **Adicionar um Runbook** no canto superior esquerdo da página Runbooks.

3.  Selecione **Importar um runbook existente**.

4.  Em **Arquivo de runbook**, use o determinado arquivo `DataSyncLogPowerShellRunbook`. Defina o **Tipo de runbook** como `PowerShell`. Forneça um nome para o runbook.

5.  Selecione **Criar**. Agora você tem um runbook.

6.  Em sua Conta da Automação do Azure, selecione a guia **Variáveis** em Recursos Compartilhados.

7.  Selecione **Adicionar uma variável** na página Variáveis. Crie uma variável para armazenar o último tempo de execução para o runbook. Caso você tenha vários runbooks, precisará de uma variável para cada runbook.

8.  Defina o nome da variável como `DataSyncLogLastUpdatedTime` e defina seu Tipo como DateTime.

9.  Selecione o runbook e clique no botão Editar na parte superior da página.

10. Faça as alterações necessárias em sua conta e na configuração da Sincronização de Dados SQL. (Consulte o script de exemplo para obter informações mais detalhadas.)

    1.  Informações do Azure.

    2.  Informações do Grupo de Sincronização.

    3.  O Azure Monitor registra informações. Encontre essas informações em portal do Azure | Configurações | Fontes Conectadas. Para obter mais informações sobre como enviar dados para os logs do Azure Monitor, consulte [enviar dados para logs do Azure Monitor com a API do coletor de dados HTTP (visualização)](../azure-monitor/platform/data-collector-api.md).

11. Execute o runbook no painel Teste. Verifique se ele foi bem-sucedido.

    Caso você encontre erros, verifique se você tem o último módulo do PowerShell instalado. Você pode instalar o módulo mais recente do PowerShell nos **Galeria de Módulos** na sua Conta de Automação.

12. Clique em **Publicar**

### <a name="schedule-the-runbook"></a>Agendar o runbook

Para agendar o runbook:

1.  No runbook, selecione a guia **Agendamentos** em Recursos.

2.  Selecione **Adicionar um Agendamento** na página Agendamentos.

3.  Selecione **Vincular um agendamento ao runbook**.

4.  Selecione **Criar um novo agendamento**.

5.  Defina **Recorrência** como Recorrente e defina o intervalo desejado. Use o mesmo intervalo aqui, no script e nos logs do Azure Monitor.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verificar a automação

Para monitorar se a automação está funcionando conforme esperado, em **Visão Geral** de sua conta de automação, localize a exibição **Estatísticas de Trabalho** em **Monitoramento**. Fixe isto no painel para facilitar a visualização. As execuções com êxito do runbook são mostradas como “Concluídas” e as execuções com falha são mostradas como “Com Falha."

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Criar um alerta do leitor Azure Monitor para notificações por Email

Para criar um alerta que usa os logs do Azure Monitor, faça o seguinte. Como pré-requisito, você precisa ter os logs do Azure Monitor vinculados a um espaço de trabalho do Log Analytics.

1.  No portal do Azure, selecione **Pesquisa de Logs**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo de sincronização dentro do intervalo selecionado. Por exemplo: 

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Depois de executar a consulta, selecione o sino que indica **Alerta**.

4.  Em **Gerar alerta com base em**, selecione **Medição Métrica**.

    1.  Defina o Valor de Agregação como **Maior que**.

    2.  Após **Maior que**, insira o limite a ser decorrido antes que você receba notificações. Erros transitórios são esperados na Sincronização de Dados. Para reduzir o ruído, defina o limite para 5.

5.  Em **Ações**, defina **Notificação por email** como “Sim." Insira os destinatários de email desejados.

6.  Clique em **Salvar**. Agora, os destinatários especificados recebem notificações por email quando ocorrem erros.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Criar uma exibição de Monitor do Azure para monitoramento

Esta etapa cria uma exibição do Azure Monitor para monitorar visualmente todos os grupos de sincronização especificado. A exibição inclui vários componentes:

-   Um bloco de visão geral, que mostra quantos erros, êxitos e avisos existem em todos os grupos de sincronização.

-   Um bloco para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não são exibidos neste bloco.

-   Um bloco para cada Grupo de Sincronização, que mostra o número de erros, êxitos e avisos, bem como as mensagens de erro recentes.

Para configurar o modo de exibição do Monitor do Azure, faça o seguinte:

1.  Na home page do espaço de trabalho do Log Analytics, selecione o sinal de adição à esquerda para abrir o **designer de exibição**.

2.  Selecione **Importação** na barra superior do designer de exibição. Em seguida, selecione o arquivo de exemplo “DataSyncLogOMSView”.

3.  A exibição de exemplo serve para gerenciar dois grupos de sincronização. Edite essa exibição de acordo com seu cenário. Clique em **Editar** e faça as seguintes alterações:

    1.  Crie novos objetos de “Rosca e Lista” por meio da Galeria, conforme necessário.

    2.  Em cada bloco, atualize as consultas com suas informações.

        1.  Em cada bloco, altere o intervalo de TimeStamp_t conforme desejado.

        2.  Nos blocos de cada Grupo de Sincronização, atualize os nomes do Grupo de Sincronização.

    3.  Em cada bloco, atualize o título, conforme necessário.

4.  Clique em **Salvar** e a exibição estará pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, essa solução é gratuita.

**Automação do Azure**: Pode haver um custo com a conta da Automação do Azure, dependendo do uso. Os primeiros 500 minutos do tempo de execução do trabalho por mês são gratuitos. Na maioria dos casos, espera-se que essa solução use menos de 500 minutos por mês. Para evitar encargos, agende o runbook para que ele seja executado em um intervalo de duas horas ou mais. Para obter mais informações, consulte [Preços da Automação](https://azure.microsoft.com/pricing/details/automation/).

**Logs do Azure Monitor:** Pode haver um custo associado com os logs do Azure Monitor, dependendo do uso. A camada gratuita inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, espera-se que essa solução ingira menos de 500 MB por dia. Para diminuir o uso, use a filtragem somente falha incluída no runbook. Caso você esteja usando mais de 500 MB por dia, faça atualização para a camada paga para evitar o risco da interrupção da análise quando o limite for atingido. Para obter mais informações, consulte [do Azure Monitor registra preços](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Baixe os exemplos de código descritos neste artigo nos seguintes locais:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição do Azure Monitor de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Sincronização de Dados SQL, veja:

-   Visão geral - [Sincronize dados em vários bancos de dados locais e na nuvem com o Azure SQL Data Sync](sql-database-sync-data.md)
-   Configurar sincronização de dados
    - No portal - [Tutorial: Configurar sincronização de dados SQL para sincronizar dados entre o banco de dados SQL do Azure e SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](sql-database-data-sync-agent.md)
-   Práticas recomendadas - [Práticas recomendadas para a Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   Solucionar problemas - [Solucionar problemas com o SQL Data Sync do Azure](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com Transact-SQL - [Automatize a replicação de alterações de esquema no Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)

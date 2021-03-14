---
title: Auditoria do SQL do Azure para o Banco de Dados SQL do Azure e Azure Synapse Analytics
description: Use a auditoria do banco de dados SQL do Azure para acompanhar eventos de banco de dados em um log de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/09/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: 82445ce7c1ebfc365459bbeba7e04d660221eaf2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551552"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Auditoria do banco de dados SQL do Azure e do Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A auditoria do [banco de dados SQL do Azure](sql-database-paas-overview.md) e [do Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure, no espaço de trabalho log Analytics ou nos hubs de eventos.

A auditoria também:

- Ajuda a manter conformidade com as normas, entender a atividade do banco de dados e aprofundar-se sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou violações suspeitas de segurança.

- Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre os programas do Azure que dão suporte à conformidade com padrões, consulte a [central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do SQL do Azure.

> [!NOTE]
> Para obter informações sobre a auditoria do Azure SQL Instância Gerenciada, consulte o artigo a seguir, [introdução à auditoria do sql instância gerenciada](../managed-instance/auditing-configure.md).

## <a name="overview"></a><a id="overview"></a>Visão geral

É possível usar a auditoria do Banco de Dados SQL para:

- **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
- **Relate** sobre a atividade do banco de dados. Você pode usar relatórios pré-configurados e um painel para se familiarizar rapidamente com a atividade e o relatório de eventos.
- **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

> [!IMPORTANT]
> A auditoria do banco de dados SQL do Azure e do Azure Synapse é otimizada para disponibilidade e desempenho. Durante uma atividade muito alta, ou alta carga de rede, o banco de dados SQL do Azure e o Azure Synapse permitem que as operações continuem e não possam registrar alguns eventos auditados.

### <a name="auditing-limitations"></a>Limitações da auditoria

- Atualmente, **não há suporte** para o **Armazenamento Premium**.
- **O namespace hierárquico** da **conta de armazenamento do Azure Data Lake Storage Gen2** não tem **suporte** no momento.
- Não há suporte para habilitar a auditoria em um **Synapse do Azure** em pausa. Para habilitar a auditoria, retome o Azure Synapse.
- A auditoria de **pools do SQL Synapse do Azure** dá suporte **apenas** a grupos de ação de auditoria padrão.


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definir a política de auditoria no nível do servidor versus no nível do banco de dados

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política de [servidor](logical-servers.md) padrão no Azure (que hospeda o banco de dados SQL ou o Azure Synapse):

- Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

- Se a opção *auditoria de servidor estiver habilitada*, ela *sempre será aplicada ao banco de dados*. O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

- Quando a política de auditoria é definida no nível do banco de dados para um Log Analytics espaço de trabalho ou um destino do hub de eventos, as seguintes operações não manterão a política de auditoria no nível do banco de dados de origem:
    - [Cópia do banco de dados](database-copy.md)
    - [Restauração em um momento determinado](recovery-using-backups.md)
    - [Replicação geográfica](active-geo-replication-overview.md) (o banco de dados secundário não terá auditoria no nível do banco de dados)

- Habilitar a auditoria no banco de dados, além de habilitá-lo no servidor, *não substitui nem* altera as configurações da auditoria do servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, o banco de dados é auditado duas vezes em paralelo: uma vez pela política de servidor e outra, pela política de banco de dados.

   > [!NOTE]
   > Evite habilitar a auditoria de servidor e a auditoria do blob de banco de dados juntas, a menos que:
    >
    > - Você deseja usar outra *conta de armazenamento*, outro *período de retenção* ou outro *workspace do Log Analytics* para um banco de dados específico.
    > - Você deseja auditar tipos de evento ou categorias de um banco de dados específico diferentes do restante dos bancos de dados no servidor. Por exemplo, talvez você tenha inserções de tabela que precisam ser auditadas somente em um banco de dados específico.
   >
   > Caso contrário, recomendamos habilitar somente a auditoria no nível do servidor e deixar a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.

#### <a name="remarks"></a>Comentários

- Os logs de auditoria são gravados nos **Blob de acréscimo** em um armazenamento de blob do Azure em sua assinatura do Azure
- Os logs de auditoria estão no formato. xel e podem ser abertos usando [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- Para configurar um repositório de logs imutável para os eventos de auditoria no nível do servidor ou do banco de dados, siga as [instruções fornecidas pelo armazenamento do Azure](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Verifique se você selecionou **permitir acréscimos adicionais** ao configurar o armazenamento de blob imutável.
- Você pode gravar logs de auditoria em uma conta do Armazenamento do Microsoft Azure atrás de uma VNet ou um firewall. Para ver instruções específicas, consulte [Gravar auditoria em uma conta de armazenamento atrás de uma VNet e um firewall](audit-write-storage-account-behind-vnet-firewall.md).
- Para obter detalhes sobre o formato de log, a hierarquia da pasta de armazenamento e as convenções de nomenclatura, consulte a [Referência de formato do log de auditoria de blob](./audit-log-format.md).
- A auditoria em [réplicas somente leitura](read-scale-out.md) é habilitada automaticamente. Para obter mais detalhes sobre a hierarquia das pastas de armazenamento, as convenções de nomenclatura e o formato do log, consulte [Formato do log de auditoria do Banco de Dados SQL](audit-log-format.md).
- Ao usar a autenticação do Azure AD, os registros de logons com falha *não* serão exibidos no log de auditoria do SQL. Para exibir logs de auditoria de logon com falha, você precisa visitar o [portal do Azure Active Directory](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), que registra os detalhes desses eventos.
- Os logons são encaminhados pelo gateway para a instância específica em que o banco de dados está localizado.  No caso de logons do AAD, as credenciais são verificadas antes de tentar utilizar esse usuário para fazer logon no banco de dados solicitado.  Em caso de falha, o banco de dados solicitado nunca é acessado, portanto, a auditoria não é realizada.  No caso de logons do SQL, as credenciais são verificadas nos dados solicitados, portanto, nesse caso, elas podem ser auditadas.  Os logons bem-sucedidos, que obviamente alcançaram o banco de dados, são auditados em ambos os casos.
- Depois de definir as configurações de auditoria, você poderá ativar o novo recurso de detecção de ameaças e configurar emails para receber alertas de segurança. Ao usar a detecção de ameaças, você recebe alertas proativos sobre atividades anômalas do banco de dados que podem indicar possíveis ameaças à segurança. Para obter mais informações, consulte [Introdução à detecção de ameaças](threat-detection-overview.md).

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Configurar a auditoria para o servidor

A política de auditoria padrão inclui todas as ações e o seguinte conjunto de grupos de ação, que farão a auditoria de todas as consultas e todos os procedimentos armazenados executados no banco de dados, bem como os logons com falha e bem-sucedidos:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  
Você pode configurar a auditoria para diferentes tipos de ações e grupos de ação usando o PowerShell, conforme descrito na seção [gerenciar a auditoria do banco de dados SQL usando Azure PowerShell](#manage-auditing) .

O banco de dados SQL do Azure e o Azure Synapse Audit armazenam 4000 caracteres de data para campos de caractere em um registro de auditoria. Quando os valores de **instrução** ou **data_sensitivity_information** retornados de uma ação auditável contêm mais de 4000 caracteres, os dados após os primeiros 4000 caracteres serão **truncados e não auditados**.
A seção a seguir descreve a configuração de auditoria usando o Portal do Azure.

  > [!NOTE]
  > - Não é possível habilitar a auditoria em um pool SQL dedicado em pausa. Para habilitar a auditoria, cancele a pausa do pool SQL dedicado. Saiba mais sobre o [pool SQL dedicado](../..//synapse-analytics/sql/best-practices-sql-pool.md).
  > - Quando a auditoria é configurada para um espaço de trabalho Log Analytics ou para um destino de Hub par por meio do cmdlet portal do Azure ou do PowerShell, uma [configuração de diagnóstico](../../azure-monitor/essentials/diagnostic-settings.md) é criada com a categoria "SQLSecurityAuditEvents" habilitada.

1. Acesse o [portal do Azure](https://portal.azure.com).
2. Navegue até **auditoria** no título segurança no seu banco de **dados SQL** ou no painel do **SQL Server** .
3. Se preferir configurar uma política de auditoria de servidor, selecione o link **Exibir configurações do servidor** na página de auditoria do banco de dados. Depois, é possível exibir ou modificar as configurações de auditoria do servidor. As políticas de auditoria de servidor se aplicam a todos os bancos de dados existentes e recém-criados neste servidor.

    ![Captura de tela que mostra o link exibir configurações do servidor realçado na página de auditoria do banco de dados.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Se você preferir habilitar a auditoria no nível do banco de dados, alterne **Auditoria** para **LIGADO**. Se a auditoria do servidor estiver habilitada, a auditoria configurada para o banco de dados existirá lado a lado com a auditoria do servidor.

5. Você tem várias opções para configurar o local em que os logs de auditoria serão gravados. Você pode gravar logs em uma conta de armazenamento do Azure, em um espaço de trabalho Log Analytics para consumo por Azure Monitor logs ou no Hub de eventos para consumo usando o Hub de eventos. Você pode configurar qualquer combinação dessas opções, e os logs de auditoria serão gravados em cada uma.
  
   ![opções de armazenamento](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Auditoria de operações de Suporte da Microsoft

A auditoria de operações de Suporte da Microsoft para o Azure SQL Server permite a auditoria de operações de engenheiros de suporte da Microsoft quando eles precisam acessar o servidor durante uma solicitação de suporte. O uso desse recurso, juntamente com a auditoria, permite mais transparência em sua força de seus funcionários e permite a detecção de anomalias, a visualização de tendências e a prevenção de perda de dados.

Para habilitar a auditoria de Suporte da Microsoft operações, navegue até **auditoria** no título segurança no painel do **SQL Server do Azure** e alterne a **auditoria de operações de suporte da Microsoft** para **ativado**.

  > [!IMPORTANT]
  > A auditoria de operações de suporte da Microsoft não dá suporte ao destino da conta de armazenamento. Para habilitar a funcionalidade, um espaço de trabalho Log Analytics ou um destino do hub de eventos deve ser configurado.

![Captura de tela de operações de Suporte da Microsoft](./media/auditing-overview/support-operations.png)

Para examinar os logs de auditoria das operações de Suporte da Microsoft em seu espaço de trabalho do Log Analytics, use a seguinte consulta:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Auditoria para destino de armazenamento

Para configurar a gravação de logs de auditoria para uma conta de armazenamento, selecione **Armazenamento** e abra **Detalhes do armazenamento**. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e, em seguida, selecione o período de retenção. Em seguida, clique em **OK**. Os logs anteriores ao período de retenção são excluídos.

- O valor padrão do período de retenção é 0 (retenção ilimitada). Você pode alterar esse valor movendo o controle deslizante de **Retenção (dias)** nas **Configurações de armazenamento** ao configurar a conta de armazenamento para auditoria.
  - Se você alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, observe que a retenção será aplicada somente aos logs gravados após a alteração do valor de retenção (os logs gravados durante o período em que a retenção era definida como ilimitada serão preservados, mesmo após a retenção ser habilitada).

  ![do Azure](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Auditoria para destino do Log Analytics
  
Para configurar a gravação de logs de auditoria em um espaço de trabalho do Log Analytics, selecione **log Analytics** e abra **detalhes do log Analytics**. Selecione ou crie o espaço de trabalho do Log Analytics, onde os logs serão gravados e, em seguida, clique em **Ok**.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Para obter mais detalhes sobre Azure Monitor Log Analytics espaço de trabalho, consulte [projetando sua implantação de logs de Azure monitor](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Auditoria para destino do hub de eventos

Para configurar a gravação de logs de auditoria para um hub de eventos, selecione **Hub de eventos** e abra detalhes do **Hub de eventos**. Selecione o hub de eventos no qual os logs serão gravados e, em seguida, clique em **OK**. Verifique se o hub de eventos está na mesma região que o banco de dados e o servidor.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analisar os logs e relatórios de auditoria

Se você tiver escolhido gravar logs de auditoria nos logs do Azure Monitor:

- Use o [portal do Azure](https://portal.azure.com). Abra o banco de dados relevante. Na parte superior da página de **auditoria** do banco de dados, selecione **Exibir logs de auditoria**.

    ![exibir logs de auditoria](./media/auditing-overview/auditing-view-audit-logs.png)

- Em seguida, você tem duas maneiras de exibir os logs:

    Clicar no **Log Analytics** na parte superior da página **Registros de auditoria** abrirá a exibição de logs no workspace do Log Analytics, onde você pode personalizar o intervalo de tempo e a consulta de pesquisa.

    ![abrir no workspace do Log Analytics](./media/auditing-overview/auditing-log-analytics.png)

    Clicar em **Exibir painel** na parte superior da tela **Registros de auditoria** abrirá um painel que mostra as informações dos logs de auditoria, onde você poderá analisar detalhadamente insights de segurança, aceso a dados confidenciais e muito mais. Esse painel foi projetado para ajudar a obter informações de segurança dos seus dados.
    Você também pode personalizar o intervalo de tempo e a consulta de pesquisa.
    ![Exibir painel do Log Analytics](media/auditing-overview/auditing-view-dashboard.png)

    ![Painel do Log Analytics](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Insights de segurança do Log Analytics](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Como alternativa, você também pode acessar os logs de auditoria na folha Log Analytics. Abra seu espaço de trabalho do Log Analytics e, na seção **Geral**, clique em **Logs**. Você pode começar com uma consulta simples, como: *pesquisar "SQLSecurityAuditEvents"* para exibir logs de auditoria.
    A partir desse ponto, você também pode usar os [logs do Azure Monitor](../../azure-monitor/logs/log-query-overview.md) para executar pesquisas avançadas em seus dados de log de auditoria. Os logs do Azure Monitor fornecem insights operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar prontamente milhões de registros em todas as suas cargas de trabalho e servidores. Para ver informações úteis adicionais sobre o idioma e os comandos da pesquisa de logs do Azure Monitor, consulte [Referência de pesquisa dos logs do Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

Se você tiver escolhido gravar logs de auditoria no Hub de Eventos:

- Para consumir dados de logs de auditoria do Hub de Eventos, você precisará configurar um fluxo para consumir eventos e gravá-las em um destino. Para obter mais informações, veja a [Documentação de Hubs de Eventos do Azure](../index.yml).
- Os logs de auditoria no Hub de Eventos são capturados no corpo dos eventos do [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar as [Ferramentas Avro](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas similares que processam esse formato.

Se você optar por gravar logs de auditoria em uma conta de Armazenamento do Azure, poderá usar vários métodos para exibir os logs:

- Os logs de auditoria são agregados na conta escolhida durante a instalação. Explore os logs de auditoria usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/). No Armazenamento do Azure, os logs de auditoria de blob são salvos como uma coleção de arquivos de blob em um contêiner chamado **sqldbauditlogs**. Para obter mais detalhes sobre a hierarquia das pastas de armazenamento, as convenções de nomenclatura e o formato do log, consulte [Formato do log de auditoria do Banco de Dados SQL](./audit-log-format.md).

- Use o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da página **Auditoria** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Captura de tela que mostra o botão Exibir logs de auditoria realçado na página de auditoria do banco de dados.](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    **Registros de auditoria** é aberto, no qual você pode exibir os logs.

  - Exiba datas específicas clicando em **Filtro** na parte superior da página **Registros de auditoria**.
  - Você pode alternar entre os registros de auditoria que foram criados pela *política de auditoria de servidor* e o *política de auditoria de banco de dados* ativando/desativando **origem auditoria**.
  - Você pode exibir apenas os registros de auditoria relacionados de injeção de SQL clicando na caixa de seleção **Mostrar apenas registros das injeções de SQL de auditoria**.

       ![Captura de tela que mostra as opções para exibir os registros de auditoria.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Use a função do sistema **sys.fn_get_audit_file** (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, veja [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Use a opção **Mesclar Arquivos de Auditoria** no SQL Server Management Studio (a partir do SSMS 17):
    1. No menu do SSMS, selecione **Arquivo** > **Abrir** > **Mesclar Arquivos de Auditoria**.

        ![Captura de tela que mostra a opção de menu mesclar arquivos de auditoria.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. A caixa de diálogo **Adicionar Arquivos de Auditoria** será aberta. Selecione uma das opções **Adicionar**, escolha se deseja mesclar arquivos de auditoria de um disco local ou importá-los do Armazenamento do Azure. Você deve fornecer os detalhes do Armazenamento do Azure e a chave de conta.

    3. Depois que todos os arquivos a serem mesclados forem adicionados, clique em **OK** para concluir a operação de mesclagem.

    4. O arquivo mesclado é aberto no SSMS, no qual você pode exibi-lo e analisá-lo, bem como exportá-lo para um arquivo XEL ou CSV ou para uma tabela.

- Use o Power BI. Você pode exibir e analisar dados do log de auditoria no Power BI. Para obter mais informações e para acessar um modelo para download, confira [Analisar dados de log de auditoria no Power BI](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895).
- Baixe os arquivos de log do contêiner de Azure Storage Blob por meio do portal ou usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/).
  - Depois de baixar um arquivo de log localmente, clique duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.
  - Baixe também vários arquivos simultaneamente por meio do Gerenciador de Armazenamento do Azure. Para isso, clique com o botão direito do mouse em uma subpasta específica e selecione **Salvar como** para salvar em uma pasta local.

- Métodos adicionais:

  - Depois de baixar vários arquivos ou uma subpasta que contém arquivos de log, você pode mesclá-los localmente, conforme descrito nas instruções de Arquivos de Auditoria de Mesclagem do SSMS indicadas anteriormente.
  - Exibir logs de auditoria de blob programaticamente: [consultar arquivos de eventos estendidos](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) usando o PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Práticas de produção


### <a name="auditing-geo-replicated-databases"></a>Auditoria de bancos de dados replicados geograficamente

Com bancos de dados com replicação geográfica, quando você habilitar a auditoria no banco de dados primário, o banco de dados secundário terá uma política de auditoria idêntica. Também é possível configurar a auditoria no banco de dados secundário, habilitando a auditoria no **servidor secundário**, independentemente do banco de dados primário.

- Nível do servidor (**recomendado**): Ativa a auditoria do **servidor primário** e do **servidor secundário** - os bancos de dados primário e secundário serão auditados independentemente, com base na respectiva política no nível do servidor.
- Nível do banco de dados: A auditoria de nível do banco de dados para bancos de dados secundários só pode ser definida nas configurações de auditoria do banco de dados primário.
  - A auditoria precisa estar habilitada no *banco de dados primário*, não no servidor.
  - Depois que a auditoria estiver habilitada no banco de dados primário, ela também será habilitada no banco de dados secundário.

    > [!IMPORTANT]
    > Com a auditoria de nível de banco de dados, as configurações de armazenamento do banco de dados secundário serão idênticas às do banco de dados primário, causando um tráfego entre regiões. Recomendamos habilitar somente a auditoria no nível do servidor e deixar a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.

### <a name="storage-key-regeneration"></a>Regeneração de chave de armazenamento

Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao gravar logs de auditoria no armazenamento do Azure, é necessário salvar novamente sua política de auditoria ao atualizar suas chaves. O processo é o seguinte:

1. Abra **Detalhes de Armazenamento**. Na caixa **Chave de Acesso de Armazenamento**, selecione **Secundária** e clique em **OK**. Em seguida, clique em **Salvar** na parte superior da página de configuração de auditoria.

    ![Captura de tela que mostra o processo para selecionar uma chave de acesso de armazenamento secundária.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Acesse a página de configuração de armazenamento e gere novamente a chave de acesso primária.

    ![Painel de navegação](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Volte para a página de configuração de auditoria, alterne a chave de acesso de armazenamento de secundária para primária e, depois, clique em **OK**. Em seguida, clique em **Salvar** na parte superior da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regenere a chave de acesso secundária (em preparação para o próximo ciclo de atualização da chave).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Gerenciar a auditoria do banco de dados SQL do Azure

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

**Cmdlets do PowerShell (incluindo suporte para a cláusula WHERE da filtragem adicional)** :

- [Criar ou atualizar a política de auditoria de banco de dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Obter a política de auditoria do banco de dados (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obter a política de auditoria do servidor (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover a política de auditoria do banco de dados (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover a política de auditoria do servidor (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Para obter um exemplo de script, confira [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

### <a name="using-rest-api"></a>Usando a API REST

**API REST**:

- [Create or Update Database Auditing Policy](/rest/api/sql/database%20auditing%20settings/createorupdate) (Criar ou atualizar a política de auditoria de tabela do banco de dados)
- [Create or Update Server Auditing Policy](/rest/api/sql/server%20auditing%20settings/createorupdate) (Criar ou atualizar a política de auditoria de tabela do servidor)
- [Get Database Auditing Policy](/rest/api/sql/database%20auditing%20settings/get) (Obter a política de auditoria do banco de dados)
- [Get Server Auditing Policy](/rest/api/sql/server%20auditing%20settings/get) (Obter a política de auditoria do servidor)

Diretiva estendida com suporte em que a cláusula de filtragem adicional:

- [Criar ou atualizar a *política de* auditoria de banco de dados estendida](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou Atualizar a *política* de auditoria de servidor estendida](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter a *política* de auditoria de bando de dados estendida](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obter a *política* de auditoria de servidor estendida](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Usando a CLI do Azure

- [Gerenciar a política de auditoria de um servidor](/cli/azure/sql/server/audit-policy)
- [Gerenciar a política de auditoria de um banco de dados](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Usar modelos do Azure Resource Manager

Você pode gerenciar a auditoria do banco de dados SQL do Azure usando modelos de [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , conforme mostrado nestes exemplos:

- [Implantar um banco de dados SQL do Azure com auditoria habilitada para gravar logs de auditoria na conta de armazenamento de BLOBs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implante um banco de dados SQL do Azure com auditoria habilitada para gravar logs de auditoria para Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implantar um banco de dados SQL do Azure com auditoria habilitada para gravar logs de auditoria para hubs de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Os exemplos vinculados estão em um repositório público externo e são fornecidos “no estado em que se encontram”, sem garantias e não têm suporte de nenhum programa/serviço de suporte da Microsoft.

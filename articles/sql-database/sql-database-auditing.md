---
title: Introdução à auditoria do banco de dados SQL do Azure | Microsoft Docs
description: Use a auditoria de banco de dados SQL do Azure para rastrear eventos de banco de dados para um log de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 1b3a6a18d10b9d9f6ab6456ae2911e54f5c56a71
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544095"
---
# <a name="get-started-with-sql-database-auditing"></a>Introdução à auditoria do banco de dados SQL

A auditoria do [Banco de Dados SQL](sql-database-technical-overview.md) e do [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) do Azure rastreia eventos do banco de dados e os grava em um log de auditoria na sua conta de armazenamento do Azure, espaço de trabalho do OMS ou Hub de Eventos. A auditoria também:

- Ajuda você a manter a conformidade regulatória, entender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações para os negócios ou suspeitas de violações de segurança.

- Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre o Azure programas de conformidade com padrões esse suporte, consulte o [Central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual das certificações de conformidade do banco de dados SQL.


> [!NOTE] 
> Este tópico aplica-se ao servidor SQL do Azure e aos bancos de dados SQL e SQL Data Warehouse criados no servidor do SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Visão geral da auditoria do banco de dados SQL do Azure

É possível usar a auditoria do banco de dados SQL para:

- **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
- **Relate** sobre a atividade do banco de dados. Você pode usar relatórios pré-configurados e um painel para se familiarizar rapidamente com a atividade e o relatório de eventos.
- **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

> [!IMPORTANT]
> Os logs de auditoria são gravados nos **Blobs de Acréscimo** em um armazenamento de Blob do Azure em sua assinatura do Azure.
>
> - Há suporte para todos os tipos de armazenamento (v1, v2, blob).
> - Há suporte para todas as configurações de replicação de armazenamento.
> - Atualmente, **não há suporte** para o **Armazenamento Premium**.
> - Atualmente, o **armazenamento na VNet** **não tem suporte**.
> - Atualmente, **não há suporte** para o **Armazenamento atrás de um firewall**

## <a id="subheading-8"></a>Definir a política de auditoria no nível do servidor versus no nível do banco de dados

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política padrão de servidor:

- Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

- Se a opção *auditoria de blob do servidor estiver habilitada*, ela *sempre será aplicada ao banco de dados*. O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

- Ativar a auditoria blob no banco de dados ou no data warehouse, além de ativá-lo no servidor, *não* substitui ou altera qualquer uma das configurações da auditoria de blob do servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, o banco de dados é auditado duas vezes em paralelo: uma vez pela política de servidor e outra, pela política de banco de dados.

   > [!NOTE]
   > Evite habilitar a auditoria de blobs de servidor e a auditoria de blobs do banco de dados juntas, a menos que:
    > - Você deseja usar outra *conta de armazenamento* ou outro *período de retenção* para um banco de dados específico.
    > - Você deseja auditar tipos de evento ou categorias de um banco de dados específico diferentes do restante dos bancos de dados no servidor. Por exemplo, talvez você tenha inserções de tabela que precisam ser auditadas somente em um banco de dados específico.
   >
   > Caso contrário, recomendamos habilitar somente a auditoria de blob no nível do servidor e deixar a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.

## <a id="subheading-2"></a>Configurar a auditoria do banco de dados

A seção a seguir descreve a configuração de auditoria usando o Portal do Azure.

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Navegue até **auditoria** sob o título de segurança no seu painel de servidor/banco de dados SQL.

    <a id="auditing-screenshot"></a> ![Painel de navegação][1]

3. Se preferir configurar uma política de auditoria de servidor, selecione o link **Exibir configurações do servidor** na página de auditoria do banco de dados. Depois, é possível exibir ou modificar as configurações de auditoria do servidor. As políticas de auditoria de servidor se aplicam a todos os bancos de dados existentes e recém-criados nesse servidor.

    ![Painel de navegação][2]

4. Se você preferir habilitar a auditoria no nível do banco de dados, alterne **Auditoria** para **LIGADO**.

    Se a auditoria do servidor estiver habilitada, a auditoria configurada para o banco de dados existirá lado a lado com a auditoria do servidor.

    ![Painel de navegação][3]

5. **Novo** – agora você tem várias opções para configurar o local em que os logs de auditoria serão gravados. Você pode gravar logs para uma conta de armazenamento do Azure, um espaço de trabalho do Log Analytics para consumo pelos logs do Azure Monitor ou hub de eventos para consumo usando o hub de eventos. Você pode configurar qualquer combinação dessas opções, e os logs de auditoria serão gravados em cada uma.

   > [!WARNING]
   > Habilitação da auditoria ao Log Analytics incorrerá em custos com base nas taxas de ingestão. Esteja ciente do custo associado com o uso isso [opção](https://azure.microsoft.com/pricing/details/monitor/), ou considere armazenar a auditoria registra em log em uma conta de armazenamento do Azure.

    ![opções de armazenamento](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Para configurar a gravação de logs de auditoria para uma conta de armazenamento, selecione **Armazenamento** e abra **Detalhes do armazenamento**. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e, em seguida, selecione o período de retenção. Os logs antigos serão excluídos. Em seguida, clique em **OK**.

    ![do Azure](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Para configurar a gravação de logs de auditoria em um espaço de trabalho do Log Analytics, selecione **Log Analytics (Visualizar)** e abra **detalhes do Log Analytics**. Selecione ou crie o espaço de trabalho do Log Analytics, onde os logs serão gravados e, em seguida, clique em **Ok**.

    ![Espaço de trabalho do Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Para configurar a gravação de logs de auditoria para um hub de eventos, selecione **Hub de Eventos (versão prévia)** e abra **Detalhes do Hub de Eventos**. Selecione o hub de eventos no qual os logs serão gravados e, em seguida, clique em **OK**. Verifique se o hub de eventos está na mesma região que o banco de dados e o servidor.

    ![Hub de Eventos](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Clique em **Salvar**.
10. Se quiser personalizar os eventos auditados, você poderá fazer isso por meio de [cmdlets do PowerShell](#subheading-7) ou da [API REST](#subheading-9).
11. Depois de definir as configurações de auditoria, você poderá ativar o novo recurso de detecção de ameaças e configurar emails para receber alertas de segurança. Ao usar a detecção de ameaças, você recebe alertas proativos sobre atividades anômalas do banco de dados que podem indicar possíveis ameaças à segurança. Para obter mais informações, consulte [Introdução à detecção de ameaças](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Não é possível habilitar a auditoria em um Azure SQL Data Warehouse em pausa. Para habilitá-lo, un-pausar o Data Warehouse.

> [!WARNING]
> Habilitar a auditoria em um servidor que tem um Azure SQL Data Warehouse nele **resultará no Data Warehouse que está sendo continuado e novamente em pausa novamente** que pode incorrer em encargos de cobrança.

## <a id="subheading-3"></a>Analisar os logs e relatórios de auditoria

Se você escolheu gravar logs de auditoria do Azure Monitor logs:

- Use o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da página **Auditoria** do banco de dados, clique em **Exibir logs de auditoria**.

    ![exibir logs de auditoria](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Em seguida, clicar em **Abrir no OMS** na parte superior da página **Registros de auditoria** abrirá a exibição de Logs no Log Analytics, na qual você poderá personalizar o intervalo de tempo e a consulta de pesquisa.

    ![Abrir o Log Analytics](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Como alternativa, você também pode acessar os logs de auditoria na folha Log Analytics. Abra seu espaço de trabalho do Log Analytics e, na seção **Geral**, clique em **Logs**. Você pode começar com uma consulta simples, como: *pesquisar "SQLSecurityAuditEvents"* para exibir logs de auditoria.
    A partir daqui, você também pode usar [registra em log do Azure Monitor](../log-analytics/log-analytics-log-search.md) para executar pesquisas avançadas em seus dados de log de auditoria. Os logs do Azure Monitor fornece informações operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar prontamente milhões de registros em todas as suas cargas de trabalho e servidores. Para obter informações adicionais úteis sobre a linguagem de pesquisa de logs do Azure Monitor e comandos, consulte [referência de pesquisa de logs do Azure Monitor](../log-analytics/log-analytics-log-search.md).

Se você tiver escolhido gravar logs de auditoria no Hub de Eventos:

- Para consumir dados de logs de auditoria do Hub de Eventos, você precisará configurar um fluxo para consumir eventos e gravá-las em um destino. Para obter mais informações, veja a [Documentação de Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/).
- Os logs de auditoria no Hub de Eventos são capturados no corpo dos eventos do [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar as [Ferramentas Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou ferramentas similares que processam esse formato.

Se você optar por gravar logs de auditoria em uma conta de Armazenamento do Azure, poderá usar vários métodos para exibir os logs:

- Os logs de auditoria são agregados na conta escolhida durante a instalação. Explore os logs de auditoria usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/). No Armazenamento do Azure, os logs de auditoria de blob são salvos como uma coleção de arquivos de blob em um contêiner chamado **sqldbauditlogs**. Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, convenções de nomenclatura e formato de log, consulte a [formato de Log de auditoria de banco de dados SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Use o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da página **Auditoria** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação][7]

    **Registros de auditoria** é aberto, no qual você pode exibir os logs.

  - Exiba datas específicas clicando em **Filtro** na parte superior da página **Registros de auditoria**.
  - Você pode alternar entre os registros de auditoria que foram criados pela *política de auditoria de servidor* e o *política de auditoria de banco de dados* ativando/desativando **origem auditoria**.
  - Você pode exibir apenas os registros de auditoria relacionados de injeção de SQL clicando na caixa de seleção **Mostrar apenas registros das injeções de SQL de auditoria**.

       ![Painel de navegação][8]

- Use a função do sistema **sys.fn_get_audit_file** (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, veja [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Use a opção **Mesclar Arquivos de Auditoria** no SQL Server Management Studio (a partir do SSMS 17):
    1. No menu do SSMS, selecione **Arquivo** > **Abrir** > **Mesclar Arquivos de Auditoria**.

        ![Painel de navegação][9]
    2. A caixa de diálogo **Adicionar Arquivos de Auditoria** será aberta. Selecione uma das opções **Adicionar**, escolha se deseja mesclar arquivos de auditoria de um disco local ou importá-los do Armazenamento do Azure. Você deve fornecer os detalhes do Armazenamento do Azure e a chave de conta.

    3. Depois que todos os arquivos a serem mesclados forem adicionados, clique em **OK** para concluir a operação de mesclagem.

    4. O arquivo mesclado é aberto no SSMS, no qual você pode exibi-lo e analisá-lo, bem como exportá-lo para um arquivo XEL ou CSV ou para uma tabela.

- Use o Power BI. Você pode exibir e analisar dados do log de auditoria no Power BI. Para obter mais informações e para acessar um modelo para download, confira [Analisar dados de log de auditoria no Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Baixe os arquivos de log do contêiner de Azure Storage Blob por meio do portal ou usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/).
  - Depois de baixar um arquivo de log localmente, clique duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.
  - Baixe também vários arquivos simultaneamente por meio do Gerenciador de Armazenamento do Azure. Para isso, clique com o botão direito do mouse em uma subpasta específica e selecione **Salvar como** para salvar em uma pasta local.

- Métodos adicionais:

  - Depois de baixar vários arquivos ou uma subpasta que contém arquivos de log, você pode mesclá-los localmente, conforme descrito nas instruções de Arquivos de Auditoria de Mesclagem do SSMS indicadas anteriormente.
  - Exiba os logs de auditoria de blob de forma programática:

    - [Consulte Arquivos de Eventos Estendidos usando o PowerShell](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/).

## <a id="subheading-5"></a>Práticas de produção

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditoria de bancos de dados replicados geograficamente</a>

Com bancos de dados com replicação geográfica, quando você habilitar a auditoria no banco de dados primário, o banco de dados secundário terá uma política de auditoria idêntica. Também é possível configurar a auditoria no banco de dados secundário, habilitando a auditoria no **servidor secundário**, independentemente do banco de dados primário.

- Nível do servidor (**recomendado**): Ativa a auditoria do **servidor primário** e do **servidor secundário** - os bancos de dados primário e secundário serão auditados independentemente, com base na respectiva política no nível do servidor.
- Nível do banco de dados: A auditoria de nível do banco de dados para bancos de dados secundários só pode ser definida nas configurações de auditoria do banco de dados primário.
  - A auditoria precisa estar habilitada no *banco de dados primário*, não no servidor.
  - Depois que a auditoria estiver habilitada no banco de dados primário, ela também será habilitada no banco de dados secundário.

    >[!IMPORTANT]
    >Com a auditoria de nível de banco de dados, as configurações de armazenamento do banco de dados secundário serão idênticas às do banco de dados primário, causando um tráfego entre regiões. Recomendamos habilitar somente a auditoria no nível do servidor e deixar a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.
    > [!WARNING]
    > Usar o hub de eventos ou logs do Azure Monitor como destinos para logs de auditoria no nível do servidor no momento, não há suporte para bancos de dados secundário replicado geograficamente.

### <a id="subheading-6">Regeneração de chave de armazenamento</a>

Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao gravar logs de auditoria no armazenamento do Azure, é necessário salvar novamente sua política de auditoria ao atualizar suas chaves. O processo é o seguinte:

1. Abra **Detalhes de Armazenamento**. Na caixa **Chave de Acesso de Armazenamento**, selecione **Secundária** e clique em **OK**. Em seguida, clique em **Salvar** na parte superior da página de configuração de auditoria.

    ![Painel de navegação][5]
2. Acesse a página de configuração de armazenamento e gere novamente a chave de acesso primária.

    ![Painel de navegação][6]
3. Volte para a página de configuração de auditoria, alterne a chave de acesso de armazenamento de secundária para primária e, depois, clique em **OK**. Em seguida, clique em **Salvar** na parte superior da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regenere a chave de acesso secundária (em preparação para o próximo ciclo de atualização da chave).

## <a name="additional-information"></a>Informações adicionais

- Para obter detalhes sobre o formato de log, a hierarquia da pasta de armazenamento e as convenções de nomenclatura, consulte a [Referência de formato do log de auditoria de blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > A auditoria de Banco de Dados SQL do Azure armazena 4000 caracteres de dados para campos de caracteres em um registro de auditoria. Quando os valores de **instrução** ou **data_sensitivity_information** retornados de uma ação auditável contêm mais de 4000 caracteres, os dados após os primeiros 4000 caracteres serão **truncados e não auditados**.

- Os logs de auditoria são gravados nos **Blobs Acrescentados** em um armazenamento de Blob do Azure em sua assinatura do Azure:
  - O **Armazenamento Premium** atualmente **não tem suporte** por Blobs de Acréscimo.
  - Atualmente, o **armazenamento na VNet** **não tem suporte**.

- A política de auditoria padrão inclui todas as ações e o seguinte conjunto de grupos de ação, que farão a auditoria de todas as consultas e todos os procedimentos armazenados executados no banco de dados, bem como os logons com falha e bem-sucedidos:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP

    Você pode configurar a auditoria para diferentes tipos de ações e grupos de ação usando o PowerShell, conforme descrito na seção [Gerenciar a auditoria do Banco de Dados SQL usando o Azure PowerShell](#subheading-7).

- Ao usar a autenticação do AAD, falha logons registros serão *não* aparecem no log de auditoria do SQL. Para exibir logs de auditoria de logon com falha, você precisa visitar o [portal do Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), que registra os detalhes desses eventos.


## <a id="subheading-7"></a>Gerenciar a auditoria do Banco de Dados SQL usando o Azure PowerShell

**Cmdlets do PowerShell (incluindo suporte para a cláusula WHERE da filtragem adicional)** :

- [Criar ou atualizar o banco de dados (conjunto AzSqlDatabaseAudit) da política de auditoria](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria de servidor (Set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Obter (Get-AzSqlDatabaseAudit) da política de auditoria de banco de dados](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obter (Get-AzSqlServerAudit) de diretiva de auditoria de servidor](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover (Remove-AzSqlDatabaseAudit) da política de auditoria de banco de dados](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover a política de auditoria de servidor (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Para obter um exemplo de script, confira [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Gerenciar a auditoria do banco de dados SQL usando a API REST

**API REST**:

- [Create or Update Database Auditing Policy](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate) (Criar ou atualizar a política de auditoria de tabela do banco de dados)
- [Create or Update Server Auditing Policy](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate) (Criar ou atualizar a política de auditoria de tabela do servidor)
- [Get Database Auditing Policy](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get) (Obter a política de auditoria do banco de dados)
- [Get Server Auditing Policy](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get) (Obter a política de auditoria do servidor)

Diretiva estendida com suporte em que a cláusula de filtragem adicional:

- [Criar ou atualizar o banco de dados *estendido* política de auditoria](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou atualizar servidor *estendido* política de auditoria](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter banco de dados *estendido* política de auditoria](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obtenha o servidor *estendido* política de auditoria](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Gerenciar a auditoria do banco de dados SQL usando os modelos ARM

Você pode gerenciar a auditoria de banco de dados SQL do Azure usando os modelos do[ Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), de acordo com estes exemplos:

- [Implantar um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria para a conta de armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implantar um SQL Server do Azure comauditoria habilitada para gravar logs de auditoria no Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implantar um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria no Hubs de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Os exemplos vinculados estão em um repositório público externo e são fornecidos "como está", sem garantia e não têm suporte em qualquer programa/serviço suporte da Microsoft.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

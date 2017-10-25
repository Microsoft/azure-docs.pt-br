---
title: "Introdução à auditoria do banco de dados SQL do Azure | Microsoft Docs"
description: "Introdução à auditoria do banco de dados SQL do Azure"
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.openlocfilehash: 04c402709560775592e5500362b0d9a4ccbf5a6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-sql-database-auditing"></a>Introdução à auditoria do banco de dados SQL
A auditoria do banco de dados SQL do Azure acompanha eventos do banco de dados e grava-os em um log de auditoria em sua conta de armazenamento do Azure. A auditoria também:

* Ajuda você a manter a conformidade regulatória, entender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações para os negócios ou suspeitas de violações de segurança.

* Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Visão geral da auditoria do banco de dados SQL do Azure
É possível usar a auditoria do banco de dados SQL para:


* **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
* **Relate** sobre a atividade do banco de dados. Utilizar relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
* **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

Você pode configurar a auditoria para diferentes tipos de categorias de evento, conforme explicado na seção [Configurar a auditoria do banco de dados](#subheading-2).

Os logs de auditoria são gravados no Armazenamento de blobs do Azure em sua assinatura do Azure.


## <a id="subheading-8"></a>Definir a política de auditoria no nível do servidor versus no nível do banco de dados

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política padrão de servidor:

* Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

* Se a opção *auditoria de blob do servidor estiver habilitada*, ela *sempre será aplicada ao banco de dados*. O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

* A habilitação da auditoria de blob no banco de dados, além de sua habilitação no servidor, *não* substitui nem altera as configurações de auditoria de blob do servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, o banco de dados é auditado duas vezes em paralelo: uma vez pela política de servidor e outra, pela política de banco de dados.

   > [!NOTE]
   > Evite habilitar a auditoria de blobs de servidor e a auditoria de blobs do banco de dados juntas, a menos que:
    > * Você deseja usar outra *conta de armazenamento* ou outro *período de retenção* para um banco de dados específico.
    > * Você deseja auditar tipos de evento ou categorias de um banco de dados específico diferentes do restante dos bancos de dados no servidor. Por exemplo, talvez você tenha inserções de tabela que precisam ser auditadas somente em um banco de dados específico.
   >
   > Caso contrário, recomendamos habilitar somente a auditoria de blob no nível do servidor e deixar a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.


## <a id="subheading-2"></a>Configurar a auditoria do banco de dados
A seção a seguir descreve a configuração de auditoria usando o Portal do Azure.

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Acesse a folha **Configurações** do banco de dados SQL/SQL Server que você deseja auditar. Na folha **Configurações**, selecione **Auditoria e Detecção de ameaças**.

    <a id="auditing-screenshot"></a> ![Painel de navegação][1]
3. Se preferir configurar uma política de auditoria de servidor, selecione o link **Exibir configurações do servidor** na folha de auditoria do banco de dados. Depois, é possível exibir ou modificar as configurações de auditoria do servidor. As políticas de auditoria de servidor se aplicam a todos os bancos de dados existentes e recém-criados nesse servidor.

    ![Painel de navegação][2]
4. Se preferir habilitar a auditoria de blob no nível do banco de dados, em **Auditoria**, selecione **ATIVADO** e, em **Tipo de auditoria**, selecione **Blob**.

    Se a auditoria de blob do servidor estiver habilitada, a auditoria configurada para o banco de dados existirá lado a lado com a auditoria de blob do servidor.

    ![Painel de navegação][3]
5. Para abrir a folha **Armazenamento de Logs de Auditoria**, selecione **Detalhes de Armazenamento**. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e, em seguida, selecione o período de retenção. Os logs antigos serão excluídos. Em seguida, clique em **OK**.
   >[!TIP]
   >Para aproveitar ao máximo os modelos de relatórios de auditoria, use a mesma conta de armazenamento para todos os bancos de dados auditados.

    <a id="storage-screenshot"></a> ![Painel de navegação][4]
6. Se quiser personalizar os eventos auditados, você poderá fazer isso por meio do PowerShell ou da API REST. 
7. Depois de definir as configurações de auditoria, você poderá ativar o novo recurso de detecção de ameaças e configurar emails para receber alertas de segurança. Ao usar a detecção de ameaças, você recebe alertas proativos sobre atividades anômalas do banco de dados que podem indicar possíveis ameaças à segurança. Para obter mais informações, consulte [Introdução à detecção de ameaças](sql-database-threat-detection-get-started.md).
8. Clique em **Salvar**.





## <a id="subheading-3"></a>Analisar os logs e relatórios de auditoria
Os logs de auditoria são agregados na conta do Azure Storage escolhida durante a instalação. Explore os logs de auditoria usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).

Os logs de auditoria de blob são salvos como uma coleção de arquivos de blob em um contêiner chamado **sqldbauditlogs**.

Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, as convenções de nomenclatura e o formato do log, consulte a [Referência de formato do log de auditoria de blob](https://go.microsoft.com/fwlink/?linkid=829599).

Há vários métodos que podem ser usados para exibir os logs de auditoria de blob:

* Use o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da folha **Auditoria e Detecção de ameaças** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação][7]

    Uma folha **Registros de auditoria** será aberta, na qual você poderá exibir os logs.

    - Exiba datas específicas clicando em **Filtro** na parte superior da folha **Registros de auditoria**.
    - Alterne entre os registros de auditoria que foram criados por uma auditoria da política de servidor ou da política de banco de dados.

       ![Painel de navegação][8]

* Use a função do sistema **sys.fn_get_audit_file** (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, consulte a [documentação de sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Use a opção **Mesclar Arquivos de Auditoria** no SQL Server Management Studio (a partir do SSMS 17):
    1. No menu do SSMS, selecione **Arquivo** > **Abrir** > **Mesclar Arquivos de Auditoria**.

        ![Painel de navegação][9]
    2. A caixa de diálogo **Adicionar Arquivos de Auditoria** será aberta. Selecione uma das opções **Adicionar**, escolha se deseja mesclar arquivos de auditoria de um disco local ou importá-los do Armazenamento do Azure. Você deve fornecer os detalhes do Armazenamento do Azure e a chave de conta.

    3. Depois que todos os arquivos a serem mesclados forem adicionados, clique em **OK** para concluir a operação de mesclagem.

    4. O arquivo mesclado é aberto no SSMS, no qual você pode exibi-lo e analisá-lo, bem como exportá-lo para um arquivo XEL ou CSV ou para uma tabela.

* Use o [aplicativo de sincronização](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que criamos. Ele é executado no Azure e utiliza APIs públicas do Log Analytics do OMS (Operations Management Suite) para enviar os logs de auditoria do SQL por push para o OMS. O aplicativo de sincronização envia os logs de auditoria do SQL por push para o Log Analytics do OMS para consumo por meio do painel do Log Analytics do OMS.

* Use o Power BI. Você pode exibir e analisar dados do log de auditoria no Power BI. Saiba mais sobre o [Power BI e acesse um modelo para download](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Baixe os arquivos de log do contêiner de Azure Storage Blob por meio do portal ou usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).
    * Depois de baixar um arquivo de log localmente, clique duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.
    * Baixe também vários arquivos simultaneamente por meio do Gerenciador de Armazenamento do Azure. Clique com o botão direito do mouse em uma subpasta específica e selecione **Salvar como** para salvar em uma pasta local.

* Métodos adicionais:
   * Depois de baixar vários arquivos ou uma subpasta que contém arquivos de log, você pode mesclá-los localmente, conforme descrito nas instruções de Arquivos de Auditoria de Mesclagem do SSMS indicadas anteriormente.

   * Exiba os logs de auditoria de blob de forma programática:

     * Use a biblioteca C# do [Leitor de Eventos Estendidos](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/).
     * [Consulte Arquivos de Eventos Estendidos usando o PowerShell](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/).




## <a id="subheading-5"></a>Práticas de produção
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditoria de bancos de dados replicados geograficamente</a>
Com bancos de dados com replicação geográfica, quando você habilitar a auditoria no banco de dados primário, o banco de dados secundário terá uma política de auditoria idêntica. Também é possível configurar a auditoria no banco de dados secundário, habilitando a auditoria no **servidor secundário**, independentemente do banco de dados primário.

* Nível do servidor (**recomendado**): ative a auditoria do **servidor primário** e do **servidor secundário** – os bancos de dados primário e secundário serão auditados independentemente, com base em sua respectiva política no nível do servidor.

* Nível de banco de dados: a auditoria de nível de banco de dados para bancos de dados secundários só pode ser configurada nas configurações de auditoria do banco de dados primário.
   * A auditoria de blob precisa estar habilitada no *banco de dados primário*, não no servidor.
   * Depois que a auditoria de blob estiver habilitada no banco de dados primário, ela também será habilitada no banco de dados secundário.

     >[!IMPORTANT]
     >Com a auditoria de nível de banco de dados, as configurações de armazenamento do banco de dados secundário serão idênticas às do banco de dados primário, causando um tráfego entre regiões. Recomendamos habilitar somente a auditoria no nível do servidor e deixar a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.
<br>

### <a id="subheading-6">Regeneração de chave de armazenamento</a>
Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao atualizar as chaves, é necessário salvar novamente a política de auditoria. O processo é o seguinte:

1. Abra a folha **Detalhes de Armazenamento**. Na caixa **Chave de Acesso de Armazenamento**, selecione **Secundária** e clique em **OK**. Em seguida, clique em **Salvar** na parte superior da folha de configuração de auditoria.

    ![Painel de navegação][5]
2. Acesse a folha de configuração de armazenamento e regenere a chave de acesso primária.

    ![Painel de navegação][6]
3. Volte para a folha de configuração de auditoria, alterne a chave de acesso de armazenamento de secundária para primária e, depois, clique em **OK**. Em seguida, clique em **Salvar** na parte superior da folha de configuração de auditoria.
4. Volte para a folha de configuração de armazenamento e regenere a chave de acesso secundária (em preparação para o próximo ciclo de atualização da chave).

## <a name="manage-sql-database-auditing-using-azure-powershell"></a>Gerenciar a auditoria do Banco de Dados SQL usando o Azure PowerShell


* **Cmdlets do PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditing][101]
   * [Get-AzureRMSqlServerAuditing][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditing][105]
   * [Set-AzureRMSqlServerAuditing][106]

   Para obter um exemplo de script, confira [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="manage-sql-database-auditing-using-rest-api"></a>Gerenciar a auditoria do Banco de Dados SQL usando a API REST

* **API REST – Auditoria de blob**:

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx) (Criar ou atualizar a política de auditoria de blob do banco de dados)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx) (Criar ou atualizar uma política de auditoria de blob de servidor)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx) (Obter a política de auditoria de blob do banco de dados)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx) (Obter a política de auditoria de blob do servidor)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx) (Obter o resultado da operação de auditoria do blob do servidor)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)

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

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing

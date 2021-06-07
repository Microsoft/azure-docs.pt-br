---
title: Fazer backup de bancos de dados do SQL Server para o Azure
description: Este artigo explica como fazer backup do SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316827"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre o Backup do SQL Server nas VMs do Azure

O [backup do Azure](backup-overview.md) oferece uma solução especializada baseada em fluxo para fazer backup SQL Server em execução em VMs do Azure. Essa solução se alinha com os benefícios do backup do Azure de backup sem infraestrutura, retenção de longo prazo e gerenciamento central. Além disso, ele fornece as seguintes vantagens especificamente para SQL Server:

1. Backups com reconhecimento de carga de trabalho que dão suporte a todos os tipos de backup-completo, diferencial e log
2. RPO de 15 minutos (objetivo de ponto de recuperação) com backups de log frequentes
3. Recuperação pontual de até um segundo
4. Backup e restauração de nível de banco de dados individual

Para exibir os cenários de backup e restauração aos quais damos suporte hoje, consulte a [matriz de suporte](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Processo de backup

Essa solução aproveita as APIs nativas do SQL para fazer backups dos bancos de dados SQL.

* Depois que você especificar a VM do SQL Server que você deseja proteger e na qual consultar os bancos de dados, o serviço de Backup do Azure instalará uma extensão de backup de carga de trabalho na VM pelo nome da extensão `AzureBackupWindowsWorkload`.
* Essa extensão consiste em um coordenador e um plugin do SQL. Ao passo que o coordenador é responsável por disparar fluxos de trabalho para várias operações, como configurar o backup, backup e restauração, o plugin é responsável por fluxo de dados real.
* Para poder descobrir bancos de dados nesta VM, o Backup do Azure cria a conta `NT SERVICE\AzureWLBackupPluginSvc`. Essa conta é usada para backup e restauração e exige permissões de sysadmin do SQL. A `NT SERVICE\AzureWLBackupPluginSvc` conta é uma [conta de serviço virtual](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)e, portanto, não requer nenhum gerenciamento de senha. O backup do Azure usa a `NT AUTHORITY\SYSTEM` conta de descoberta/consulta de banco de dados, portanto, essa conta precisa ser um logon público no SQL. Caso não tenha criado a VM do SQL Server no Azure Marketplace, você poderá receber o erro **UserErrorSQLNoSysadminMembership**. Se isso ocorrer, [siga estas instruções](#set-vm-permissions).
* Depois que o gatilho configurar a proteção nos bancos de dados selecionados, o serviço de backup configura o coordenador com as agendas de backup e outros detalhes da política, que aumenta os caches localmente na VM.
* No horário agendado, o coordenador se comunica com o plugin e ele começa a transmissão dos dados de backup do SQL Server usando o VDI.  
* O plug-in envia os dados diretamente para o cofre dos serviços de recuperação, eliminando assim a necessidade de um local de preparo. Os dados são criptografados e armazenados pelo serviço de Backup do Microsoft Azure em contas de armazenamento.
* Quando a transferência de dados for concluída, o coordenador confirma com o serviço de backup.

  ![Arquitetura de backup SQL](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique os seguintes requisitos:

1. Garanta que você tenha uma Instância do SQL Server em execução no Azure. Você pode [criar uma Instância do SQL Server rapidamente](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) no marketplace.
2. Examine as [considerações de recursos](sql-support-matrix.md#feature-considerations-and-limitations) e o [suporte ao cenário](sql-support-matrix.md#scenario-support).
3. [Examine as perguntas comuns](faq-backup-sql-server.md) sobre esse cenário.

## <a name="set-vm-permissions"></a>Definir permissões da VM

  Ao executar a descoberta em um SQL Server, o Backup do Azure faz o seguinte:

* Adiciona a extensão AzureBackupWindowsWorkload.
* Cria uma conta NT SERVICE\AzureWLBackupPluginSvc para descobrir bancos de dados na máquina virtual. Essa conta é usada para backup e restauração e exige permissões de sysadmin do SQL.
* Descobre os bancos de dados que estão em execução em uma VM, o Backup do Azure usa a conta NT AUTHORITY\SYSTEM. Essa conta deve estar conectada publicamente no SQL.

Se você não criou a VM SQL Server no Azure Marketplace ou se você está no SQL 2008 ou 2008 R2, você pode receber um erro **UserErrorSQLNoSysadminMembership** .

Para conceder permissões no caso do **SQL 2008** e **2008 R2** em execução no Windows 2008 R2, consulte [aqui](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Para todas as outras versões, corrija as permissões com as seguintes etapas:

  1. Use uma conta com permissões de sysadmin do SQL Server para entrar no SSMS (SQL Server Management Studio). A menos que você precise de permissões de acesso especiais, a autenticação do Windows deverá funcionar.
  2. No SQL Server, abra a pasta **Segurança/Logons**.

      ![Abra a pasta Segurança/Logons para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

  3. Clique com o botão direito do mouse na pasta **logons** e selecione **novo logon**. Em **Logon – Novo**, selecione **Pesquisar**.

      ![Na caixa de diálogo Logon – Novo, selecione Pesquisar](./media/backup-azure-sql-database/new-login-search.png)

  4. A conta de serviço virtual do Windows **NT SERVICE\AzureWLBackupPluginSvc** foi criada durante a fase de descoberta do SQL e do registro da máquina virtual. Insira o nome da conta, conforme mostrado em **Inserir o nome do objeto a ser selecionado**. Selecione **Verificar Nomes** para resolver o nome. Selecione **OK**.

      ![Selecione Verificar Nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

  5. Em **Funções de Servidor**, verifique se a função **sysadmin** está selecionada. Selecione **OK**. As permissões necessárias agora devem existir.

      ![Verifique se a função de servidor sysadmin está selecionada](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Agora, associe o banco de dados ao cofre dos Serviços de Recuperação. No portal do Azure, na lista **Servidores Protegidos**, clique com o botão direito do mouse no servidor que está em um estado de erro > **Redescobrir BDs**.

      ![Verifique se o servidor tem as permissões apropriadas](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verifique o progresso na área **Notificações**. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

      ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Se o SQL Server tiver várias instâncias do SQL Server instaladas, você precisará adicionar a permissão sysadmin para a conta **NT Service\AzureWLBackupPluginSvc** em todas as instâncias do SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Conceder permissões de sysadmin do SQL para SQL 2008 e SQL 2008 R2

Adicione logins do **NT AUTHORITY\SYSTEM** e do **NT Service\AzureWLBackupPluginSvc** à instância do SQL Server:

1. Vá até a instância do SQL Server no Pesquisador de Objetos.
2. Navegue até Segurança -> Logons
3. Clique com o botão direito do mouse em logons e selecione *novo logon...*

    ![Novo logon usando o SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Vá até a guia Geral e insira **NT AUTHORITY\SYSTEM** como o nome de logon.

    ![Nome de logon do SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Vá até *Funções de Servidor* e escolha as funções *public* e *sysadmin*.

    ![Escolhendo funções no SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Vá até *Status*. *Conceda* a permissão para conectar ao mecanismo de banco de dados e faça logon como *Habilitado*.

    ![Conceder permissões no SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Selecione OK.
8. Repita a mesma sequência de etapas (de 1 a 7 acima) para adicionar o logon de NT Service\AzureWLBackupPluginSvc à instância do SQL Server. Se o logon já existe, verifique se tem a função de servidor sysadmin e, em Status, se tem a permissão de concessão para se conectar ao mecanismo de banco de dados e faça logon como Habilitado.
9. Depois de conceder a permissão, **redescubra os bancos** de trabalho no Portal: **->** backup do cofre infraestrutura **->** de carga em VM do Azure:

    ![Redescobrir bancos de dados no portal do Azure](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Como alternativa, é possível automatizar o fornecimento de permissões executando os seguintes comandos do PowerShell no modo de administrador. Por padrão, o nome da instância é definido como MSSQLSERVER. Altere o argumento do nome da instância no script se precisar:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre](backup-sql-server-database-azure-vms.md) como realizar backup de bancos de dados do SQL Server.
* [Saiba mais sobre](restore-sql-database-azure-vm.md) como restaurar bancos de dados do SQL Server copiados em backup.
* [Saiba mais sobre](manage-monitor-sql-database-backup.md) como gerenciar bancos de dados do SQL Server copiados em backup.

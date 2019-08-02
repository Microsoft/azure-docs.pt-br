---
title: Fazer backup de bancos de dados SQL Server para o Azure | Microsoft Docs
description: Este tutorial explica como fazer backup do SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: cddb540eb0d6892426c4857b152ab6caa746f6da
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639810"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre o Backup do SQL Server nas VMs do Azure

Os bancos de dados SQL Server são cargas de trabalho críticas que exigem um baixo RPO (objetivo de ponto de recuperação) e retenção de longo prazo. É possível fazer backup de bancos de dados do SQL Server em execução em VMs do Azure usando o [Backup do Azure](backup-overview.md).

## <a name="backup-process"></a>Processo de backup

Essa solução aproveita as APIs nativas do SQL para fazer backups dos bancos de dados SQL.

* Depois que você especificar a VM do SQL Server que você deseja proteger e na qual consultar os bancos de dados, o serviço de Backup do Azure instalará uma extensão de backup de carga de trabalho na VM pelo nome da extensão `AzureBackupWindowsWorkload` .
* Essa extensão consiste em um coordenador e um plugin do SQL. Ao passo que o coordenador é responsável por disparar fluxos de trabalho para várias operações, como configurar o backup, backup e restauração, o plugin é responsável por fluxo de dados real.
* Para poder descobrir bancos de dados nesta VM, o Backup do Microsoft Azure cria a conta `NT SERVICE\AzureWLBackupPluginSvc`. Essa conta é usada para backup e restauração e exige permissões de sysadmin do SQL. O Backup do Microsoft Azure aproveita a conta  `NT AUTHORITY\SYSTEM`  para descoberta/consulta de banco de dados e, portanto, essa conta precisa ser um logon público no SQL. Se você não criou a VM do SQL Server no Azure Marketplace, talvez você receba um erro  **UserErrorSQLNoSysadminMembership**. Se isso ocorrer,  [siga essas instruções](backup-azure-sql-database.md).
* Depois que o gatilho configurar a proteção nos bancos de dados selecionados, o serviço de backup configura o coordenador com as agendas de backup e outros detalhes da política, que aumenta os caches localmente na VM 
* No horário agendado, o coordenador se comunica com o plugin e ele começa a transmissão dos dados de backup do SQL Server usando o VDI.  
* O plugin envia os dados diretamente para o cofre de serviços de recuperação, eliminando a necessidade de um local de preparo. Os dados são criptografados e armazenados pelo serviço de Backup do Microsoft Azure em contas de armazenamento.
* Quando a transferência de dados for concluída, o coordenador confirma com o serviço de backup.

  ![Arquitetura de backup SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique o que está descrito abaixo:

1. Garanta que você tenha uma Instância do SQL Server em execução no Azure. Você pode [criar uma Instância do SQL Server rapidamente](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) no marketplace.
2. Examine os [recurso consideração](#feature-consideration-and-limitations) e [suporte a cenários](#scenario-support).
3. [Examine as perguntas comuns](faq-backup-sql-server.md) sobre esse cenário.

## <a name="scenario-support"></a>Suporte ao cenário

**Suporte** | **Detalhes**
--- | ---
**Implantações com suporte** | Há suporte para VMs do Azure no Marketplace do SQL e VMs que não são do Marketplace (do SQL Server instaladas manualmente).
**Áreas geográficas com suporte** | Sudeste da Austrália (ASE), Leste da Austrália (AE) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Leste do Canadá (CE)<br> Sudeste Asiático (SEA), Ásia Oriental (EA) <br> Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Centro-Oeste dos EUA (WCUS), Oeste dos EUA (WUS); Oeste dos EUA 2 (WUS 2) Centro-Norte dos EUA (NCUS) EUA Central (CUS) Centro-Sul dos EUA (SCUS) <br> Índia Central (INC), Sul da Índia (INS) <br> Oeste do Japão (JPE), Leste do Japão (JPW) <br> Coreia Central (KRC), Sul da Coreia (KRS) <br> Norte da Europa (NE), Oeste da Europa <br> Sul do Reino Unido (UKS), Oeste do Reino Unido (UKW)
**Sistemas operacionais com suporte** | Windows Server 2016, Windows Server 2012 R2 e Windows Server 2012<br/><br/> Não há suporte para Linux no momento.
**Versões do SQL Server com suporte** | SQL Server 2017, conforme detalhado [aqui](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SPs, conforme detalhado [aqui](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014 e SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer e Express.
**Versões do .NET com suporte** | .NET Framework 4.5.2 e superior instalado na VM

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Suporte para SQL Server 2008 e SQL Server 2008 R2

O Backup do Azure anunciou recentemente o suporte para [EOS SQL Servers](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support), SQL Server 2008 e SQL Server 2008 R2. A solução está atualmente em versão prévia para o EOS SQL Server e oferece suporte à configuração a seguir:

1. SQL Server 2008 e SQL Server 2008 R2 em execução no Windows 2008 R2 SP1
2. .NET Framework 4.5.2 e superior precisa estar instalado na VM
3. Não há suporte para o backup para FCI e bancos de dados espelhados

Os usuários não serão cobrados por esse recurso até a hora em que ele estiver disponível. Todas as outras [considerações e limitações de recursos](#feature-consideration-and-limitations) aplicam-se também a essas versões. Confira os [pré-requisitos](backup-sql-server-database-azure-vms.md#prerequisites) antes de configurar a proteção no SQL Server 2008 e no SQL Server 2008 R2, que inclui a configuração da [chave do registro](backup-sql-server-database-azure-vms.md#add-registry-key-to-enable-registration) (essa etapa não será necessária quando o recurso estiver disponível para o público em geral).


## <a name="feature-consideration-and-limitations"></a>Considerações e limitações de recurso

- O Backup do SQL Server pode ser configurado no portal do Azure ou **PowerShell**. Não oferecemos CLI de suporte.
- A solução é compatível em ambos os tipos de [implantações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – VMs do Azure Resource Manager e VMs clássicas.
- VM que executa o SQL Server exige conectividade com a Internet para acessar os endereços IP públicos do Azure.
- Não há suporte para o SQL Server **FCI (Instância de Cluster de Failover)** e o SQL Server Always On na Instância de Cluster de Failover.
- Não há suporte para as operações de backup e de restauração para bancos de dados de espelho e instantâneos do banco de dados.
- Usar mais de uma solução de backup para fazer backup de sua instância do SQL Server autônoma ou do grupo de disponibilidade SQL Always On pode levar à falha de backup. Evite fazer isso.
- Fazer backup de dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também poderá levar à falha de backup.
- Backup do Azure dá suporte a apenas tipos de backup Completo e Somente Cópia para banco de dados **Somente leitura**
- Bancos de dados com um grande número de arquivos não podem ser protegidos. O número máximo de arquivos com suporte não é **~1000**.  
- É possível fazer backup de até **~2000** bancos de dados do SQL Server em um cofre. Caso você tenha um número maior de bancos de dados, poderá criar vários cofres.
- Você pode configurar o backup de até **50** bancos de dados de uma vez; essa restrição ajuda a otimizar cargas de backup.
- Damos suporte a bancos de dados de até **2TB** em tamanho; para tamanhos maiores do que isso, poderá haver problemas de desempenho.
- Para ter uma ideia de sobre quantos bancos de dados podem ser protegidos por servidor, precisamos considerar fatores como a largura de banda, tamanho da VM, frequência de backup, tamanho do banco de dados, etc. [Baixe](http://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) o planejador de recursos que fornece o número aproximado de bancos de dados que você pode ter por servidor com base nos recursos da VM e na política de backup.
- No caso de grupos de disponibilidade, os backups são feitos de nós diferentes com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade está resumido abaixo.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Faça backup do comportamento no caso de grupos de disponibilidade Always On

É recomendável que o backup seja configurado em apenas um nó de um grupo de disponibilidade. O backup sempre deve ser configurado na mesma região que o nó primário. Em outras palavras, você sempre precisa que o nó primário esteja presente na região na qual você está configurando o backup. Se todos os nós do grupo de disponibilidade estiverem na mesma região na qual o backup é configurado, não haverá preocupação.

**Para grupos de disponibilidade entre regiões**
- Independentemente da preferência de backup, os backups não acontecerão em nós que não estão na mesma região em que o backup é configurado. Isso ocorre porque os backups entre regiões não são compatíveis. Se você tiver apenas dois nós e o nó secundário estiver em outra região; nesse caso, os backups continuarão ocorrendo no nó primário (a menos que sua preferência de backup seja “somente secundária”).
- Se ocorresse um failover em uma região diferente da região em que o backup está configurado, eles falhariam nos nós na região de failover.

Dependendo da preferência de backup e os tipos de backups (completo/diferencial/log/somente cópia completos), os backups serão feitos em um nó específico (primário/secundário).

- **Preferência de Backup: Primário**

**Tipo de backup** | **Node**
    --- | ---
    Completo | Primário
    Diferencial | Primário
    Registro |  Primário
    Completo somente de cópia |  Primário

- **Preferência de Backup: Apenas secundário**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

- **Preferência de Backup: Secundário**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

- **Nenhuma preferência de Backup**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

## <a name="set-vm-permissions"></a>Definir permissões da VM

  Ao executar a descoberta em um SQL Server, o Backup do Azure faz o seguinte:

* Adiciona a extensão AzureBackupWindowsWorkload.
* Cria uma conta NT SERVICE\AzureWLBackupPluginSvc para descobrir bancos de dados na máquina virtual. Essa conta é usada para backup e restauração e exige permissões de sysadmin do SQL.
* Descobre os bancos de dados que estão em execução em uma VM, o Backup do Azure usa a conta NT AUTHORITY\SYSTEM. Essa conta deve estar conectada publicamente no SQL.

Se você não criou a VM do SQL Server no Azure Marketplace ou se estiver no SQL 2008 e 2008 R2, talvez receba um erro **UserErrorSQLNoSysadminMembership**.

Para conceder permissões no caso de **SQL 2008** e **2008 R2** em execução no Windows 2008 R2, confira [aqui](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Para todas as outras versões, corrija as permissões com as seguintes etapas:

  1. Use uma conta com permissões de sysadmin do SQL Server para entrar no SSMS (SQL Server Management Studio). A menos que você precise de permissões de acesso especiais, a autenticação do Windows deverá funcionar.
  2. No SQL Server, abra a pasta **Segurança/Logons**.

      ![Abra a pasta Segurança/Logons para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

  3. Clique com o botão direito do mouse na pasta **Logons** e selecione **Novo Logon**. Em **Logon – Novo**, selecione **Pesquisar**.

      ![Na caixa de diálogo Logon – Novo, selecione Pesquisar](./media/backup-azure-sql-database/new-login-search.png)

  4. A conta de serviço virtual do Windows **NT SERVICE\AzureWLBackupPluginSvc** foi criada durante a fase de descoberta do SQL e do registro da máquina virtual. Insira o nome da conta, conforme mostrado em **Inserir o nome do objeto a ser selecionado**. Selecione **Verificar Nomes** para resolver o nome. Clique em **OK**.

      ![Selecione Verificar Nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

  5. Em **Funções de Servidor**, verifique se a função **sysadmin** está selecionada. Clique em **OK**. As permissões necessárias agora devem existir.

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
3. Clique com o botão direito do mouse em Logons e clique em *Novo Logon…*

    ![Novo logon usando o SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Vá até a guia Geral e insira **NT AUTHORITY\SYSTEM** como o nome de logon.

    ![nome de logon para o SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Vá até *Funções de Servidor* e escolha as funções *public* e *sysadmin*.

    ![escolhendo funções no SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Vá até *Status*. *Conceda* a permissão para conectar ao mecanismo de banco de dados e faça logon como *Habilitado*.

    ![Conceder permissões no SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Clique em OK.
8. Repita a mesma sequência de etapas (de 1 a 7 acima) para adicionar o logon de NT Service\AzureWLBackupPluginSvc à instância do SQL Server. Se o logon já existe, verifique se tem a função de servidor sysadmin e, em Status, se tem a permissão de concessão para se conectar ao mecanismo de banco de dados e faça logon como Habilitado.
9. Depois de conceder a permissão, **redescubra bancos de dados** no portal: Cofre **->** Fazer Backup da Infraestrutura **->** Carga de trabalho na VM do Azure:

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

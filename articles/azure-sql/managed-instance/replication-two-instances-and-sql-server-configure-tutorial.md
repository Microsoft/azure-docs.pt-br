---
title: Configurar a replicação transacional entre a Instância Gerenciada de SQL do Azure e o SQL Server
description: Um tutorial que configura a replicação entre uma instância gerenciada do editor, uma instância gerenciada do distribuidor e um assinante do SQL Server em uma VM do Azure, juntamente com os componentes de rede necessários, como a zona DNS privado e o Emparelhamento VNET.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94593919"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Tutorial: Configurar a replicação transacional entre a Instância Gerenciada de SQL do Azure e o SQL Server
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional permite replicar dados de um banco de dados para outro hospedado no SQL Server ou na [Instância Gerenciada de SQL do Azure](sql-managed-instance-paas-overview.md). A Instância Gerenciada de SQL pode ser um publicador, um distribuidor ou um assinante na topologia de replicação. Confira [configurações de replicação transacional](replication-transactional-overview.md#common-configurations) para obter as configurações disponíveis. 

A replicação transacional está atualmente em versão prévia pública para a Instância Gerenciada de SQL. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Configurar uma instância gerenciada como um publicador de replicação.
> - Configurar uma instância gerenciada como um distribuidor de replicação.
> - Configurar o SQL Server como um assinante.

![Replicação entre um publicador da instância gerenciada, um distribuidor de instância gerenciada e o assinante d SQL Server](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Este tutorial destina-se a um público-alvo experiente e pressupõe que o usuário esteja familiarizado com a implantação e a conexão com as instâncias gerenciadas e as VMs do SQL Server no Azure. 


> [!NOTE]
> Este artigo descreve o uso de [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) na Instância Gerenciada de SQL do Azure. Ele não está relacionado a [grupos de failover](../database/auto-failover-group-overview.md), um recurso de Instância Gerenciada de SQL do Azure que permite criar réplicas legíveis completas de instâncias individuais. Há considerações adicionais ao configurar [replicação transacional com grupos de failover](replication-transactional-overview.md#with-failover-groups).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você tem os seguintes pré-requisitos:

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Experiência com a implantação de duas instâncias gerenciadas na mesma rede virtual.
- Um assinante do SQL Server, local ou em uma VM do Azure. Este tutorial usa uma VM do Azure.  
- [SSMS (SQL Server Management Studio) 18.0 ou posterior](/sql/ssms/download-sql-server-management-studio-ssms).
- A versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps).
- As portas 445 e 1433 permitem o tráfego do SQL no firewall do Azure e no firewall do Windows.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Use o seguinte snippet de código do PowerShell para criar um grupo de recursos:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Criar duas instâncias gerenciadas

Crie duas instâncias gerenciadas nesse novo grupo de recursos usando o [portal do Azure](https://portal.azure.com).

- O nome da instância gerenciada do publicador deve ser `sql-mi-publisher` (juntamente com alguns caracteres para aleatoriedade), e o nome da rede virtual deve ser `vnet-sql-mi-publisher`.
- O nome da instância gerenciada do distribuidor deve ser `sql-mi-distributor` (juntamente com alguns caracteres para aleatoriedade), e a instância deve estar _na mesma rede virtual da instância gerenciada do publicador_.

   ![Usar a VNet do publicador para o distribuidor](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Para obter mais informações sobre como criar uma instância gerenciada, confira [Criar uma instância gerenciada no portal](instance-create-quickstart.md).

  > [!NOTE]
  > Para simplificar e por esta ser a configuração mais comum, este tutorial sugere a colocação da instância gerenciada do distribuidor na mesma rede virtual do publicador. No entanto, é possível criar o distribuidor em uma rede virtual separada. Para fazer isso, você precisará configurar o Emparelhamento VNET entre as redes virtuais do editor e do distribuidor e, depois, configurar o Emparelhamento VNET entre as redes virtuais do distribuidor e do assinante.

## <a name="create-a-sql-server-vm"></a>Criar uma VM do SQL Server

Crie uma máquina virtual do SQL Server no [portal do Azure](https://portal.azure.com). A máquina virtual do SQL Server deve ter as seguintes características:

- Nome: `sql-vm-sub`
- Imagem: SQL Server 2016 ou posterior
- Grupo de recursos: o mesmo da instância gerenciada
- Rede virtual: `sql-vm-sub-vnet`

Para obter mais informações sobre como implantar uma VM do SQL Server no Azure, confira [Início Rápido: Criar uma VM do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>Configurar o Emparelhamento VNET

Configure o Emparelhamento VNET para habilitar a comunicação entre a rede virtual das duas instâncias gerenciadas e a rede virtual do SQL Server. Para fazer isso, use este snippet de código do PowerShell:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Quando o Emparelhamento VNET for estabelecido, teste a conectividade iniciando o SSMS (SQL Server Management Studio) no SQL Server e se conectando a ambas as instâncias gerenciadas. Para obter mais informações sobre como se conectar a uma instância gerenciada usando o SSMS, confira [Usar o SSMS para se conectar à Instância Gerenciada de SQL](point-to-site-p2s-configure.md#connect-with-ssms).

![Testar a conectividade com as instâncias gerenciadas](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Criar uma zona DNS privada

Uma zona DNS privada permite o roteamento DNS entre as instâncias gerenciadas e o SQL Server.

### <a name="create-a-private-dns-zone"></a>Criar uma zona DNS privada

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** para criar um recurso do Azure.
1. Pesquise `private dns zone` no Azure Marketplace.
1. Escolha o recurso de **Zona DNS privado** publicado pela Microsoft e, em seguida, selecione **Criar** para criar a zona DNS.
1. Escolha a assinatura e o grupo de recursos na lista suspensa.
1. Forneça um nome arbitrário para a zona DNS, como `repldns.com`.

   ![Criar uma zona DNS privado](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Selecione **Examinar + criar**. Examine os parâmetros da zona DNS privado e, em seguida, selecione **Criar** para criar o recurso.

### <a name="create-an-a-record"></a>Criar um registro A

1. Acesse a nova **Zona DNS privado** e selecione **Visão geral**.
1. Selecione **+ Conjunto de registros** para criar um registro A.
1. Forneça o nome da VM do SQL Server, bem como o endereço IP interno privado.

   ![Configurar um registro A](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Selecione **OK** para criar o registro A.

### <a name="link-the-virtual-network"></a>Vincular a rede virtual

1. Acesse a nova **Zona DNS privado** e selecione **Links de rede virtual**.
1. Selecione **+ Adicionar**.
1. Forneça um nome para o link, como `Pub-link`.
1. Selecione a sua assinatura na lista suspensa e, em seguida, a rede virtual da instância gerenciada do publicador.
1. Marque a caixa ao lado de **Habilitar o registro automático**.

   ![Criar um link da VNet](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Selecione **OK** para vincular a rede virtual.
1. Repita essas etapas para adicionar um link para a rede virtual do assinante, com um nome como `Sub-link`.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

[Crie uma conta de armazenamento do Azure](../../storage/common/storage-account-create.md#create-a-storage-account) para o diretório de trabalho e, em seguida, crie um [compartilhamento de arquivo](../../storage/files/storage-how-to-create-file-share.md) na conta de armazenamento.

Copie o caminho do compartilhamento de arquivo no formato: `\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie a cadeia de conexão da chave de acesso de armazenamento no formato: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).

## <a name="create-a-database"></a>Criar um banco de dados

Crie um banco de dados na instância gerenciada do publicador. Para fazer isso, siga estas etapas:

1. Inicialize o SQL Server Management Studio no SQL Server.
1. Conecte-se à instância gerenciada do `sql-mi-publisher`.
1. Abra uma janela **Nova Consulta** e execute a seguinte consulta T-SQL para criar o banco de dados.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="configure-distribution"></a>Configurar distribuição

Depois que a conectividade for estabelecida e você tiver um banco de dados de exemplo, configure a distribuição na instância gerenciada `sql-mi-distributor`. Para fazer isso, siga estas etapas:

1. Inicialize o SQL Server Management Studio no SQL Server.
1. Conecte-se à instância gerenciada do `sql-mi-distributor`.
1. Abra uma janela **Nova Consulta** e execute o seguinte código Transact-SQL para configurar a distribuição na instância gerenciada do distribuidor:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Lembre-se de usar apenas barras invertidas (`\`) para o parâmetro @working_directory. O uso de uma barra (`/`) pode causar um erro ao se conectar ao compartilhamento de arquivo.

1. Conecte-se à instância gerenciada do `sql-mi-publisher`.
1. Abra uma janela **Nova Consulta** e execute o seguinte código Transact-SQL para registrar o distribuidor no publicador:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>Criar a publicação

Depois que a distribuição for configurada, você poderá criar a publicação. Para fazer isso, siga estas etapas:

1. Inicialize o SQL Server Management Studio no SQL Server.
1. Conecte-se à instância gerenciada do `sql-mi-publisher`.
1. No **Pesquisador de Objetos**, expanda o nó **Replicação** e clique com o botão direito do mouse na pasta **Publicação Local**. Selecione **Nova Publicação...** .
1. Selecione **Avançar** para ir para a página inicial.
1. Na página **Banco de Dados de Publicação**, selecione o banco de dados `ReplTutorial` criado anteriormente. Selecione **Avançar**.
1. Na página **Tipo de publicação**, selecione **Publicação transacional**. Selecione **Avançar**.
1. Na página **Artigos**, marque a caixa ao lado de **Tabelas**. Selecione **Avançar**.
1. Na página **Filtrar Linhas da Tabela**, selecione **Avançar** sem adicionar nenhum filtro.
1. Na página **Agente de Instantâneo**, marque a caixa ao lado de **Criar instantâneo imediatamente e manter o instantâneo disponível para inicializar assinaturas**. Selecione **Avançar**.
1. Na página **Segurança do Agente**, selecione **Configurações de Segurança...** . Forneça as credenciais de logon do SQL Server a serem usadas para o Agente de Instantâneo e para se conectar ao publicador. Selecione **OK** para fechar a página **Segurança do Agente de Instantâneo**. Selecione **Avançar**.

   ![Configurar a segurança do Agente de Instantâneo](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. Na página **Ações do Assistente**, escolha **Criar a publicação** e (opcionalmente) escolha **Gerar um arquivo de script com etapas para criar a publicação**, caso deseje salvar esse script para uso posterior.
1. Na página **Concluir o Assistente**, nomeie a publicação `ReplTest` e selecione **Avançar** para criar a publicação.
1. Depois que a publicação for criada, atualize o nó **Replicação** no **Pesquisador de Objetos** e expanda **Publicações Locais** para ver a nova publicação.

## <a name="create-the-subscription"></a>Criar a assinatura

Depois que a publicação for criada, você poderá criar a assinatura. Para fazer isso, siga estas etapas:

1. Inicialize o SQL Server Management Studio no SQL Server.
1. Conecte-se à instância gerenciada do `sql-mi-publisher`.
1. Abra uma janela **Nova Consulta** e execute o código Transact-SQL a seguir para adicionar a assinatura e o agente de distribuição. Use o DNS como parte do nome do assinante.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Testar a replicação

Depois que a replicação for configurada, teste-a inserindo novos itens no publicador e observando as alterações se propagarem para o assinante.

Execute o seguinte snippet T-SQL para ver as linhas no assinante:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Execute o snippet T-SQL a seguir para inserir linhas adicionais no publicador e, em seguida, verifique as linhas novamente no assinante.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar os recursos

1. Procure o grupo de recursos no [portal do Azure](https://portal.azure.com).
1. Selecione as instâncias gerenciadas e, em seguida, selecione **Excluir**. Digite `yes` na caixa de texto para confirmar se deseja excluir o recurso e, em seguida, selecione **Excluir**. Esse processo poderá levar algum tempo para ser concluído em segundo plano e, até que ele seja concluído, você não poderá excluir o *cluster virtual* nem os outros recursos dependentes. Monitore a exclusão na guia **Atividade** para confirmar se a instância gerenciada foi excluída.
1. Depois que a instância gerenciada for excluída, exclua o *cluster virtual* selecionando-o no grupo de recursos e, em seguida, escolhendo **Excluir**. Digite `yes` na caixa de texto para confirmar se deseja excluir o recurso e, em seguida, selecione **Excluir**.
1. Exclua todos os recursos restantes. Digite `yes` na caixa de texto para confirmar se deseja excluir o recurso e, em seguida, selecione **Excluir**.
1. Exclua o grupo de recursos selecionando **Excluir grupo de recursos**, digitando o nome do grupo de recursos, `myResourceGroup`, e, em seguida, selecionando **Excluir**.

## <a name="known-errors"></a>Erros conhecidos

### <a name="windows-logins-are-not-supported"></a>Não há suporte para logons do Windows

`Exception Message: Windows logins are not supported in this version of SQL Server.`

O agente foi configurado com um logon do Windows e precisa usar um logon do SQL Server. Use a página **Segurança do Agente** das **Propriedades da publicação** para alterar as credenciais de logon para um logon do SQL Server.

### <a name="failed-to-connect-to-azure-storage"></a>Falha ao se conectar ao Armazenamento do Azure

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Obtenção da cadeia de conexão do Armazenamento do Azure para replstorage 2019-11-19 02:21:05.07 Conexão com o Armazenamento de Arquivos do Azure '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Falha ao se conectar ao Armazenamento do Azure '' com o erro do sistema operacional: 53.

Isso provavelmente ocorre porque a porta 445 está fechada no Firewall do Azure, no Firewall do Windows ou em ambos.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

O uso de uma barra "/" em vez de uma barra invertida no caminho do arquivo para o compartilhamento de arquivo pode causar esse erro.
  
  - Não há problemas nisso: `\\replstorage.file.core.windows.net\replshare`
  - Isso pode causar um erro do sistema operacional 55: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Não foi possível se conectar ao assinante

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Soluções possíveis:

- Verifique se a porta 1433 está aberta.
- Verifique se o TCP/IP está habilitado no assinante.
- Confirme se o nome DNS foi usado ao criar o assinante.
- Verifique se as redes virtuais estão corretamente vinculadas na zona DNS privado.
- Verifique se o registro A está configurado corretamente.
- Verifique se o Emparelhamento VNET está configurado corretamente.

### <a name="no-publications-to-which-you-can-subscribe"></a>Não há publicações que você pode assinar

Ao adicionar uma nova assinatura usando o assistente de **Nova Assinatura**, na página **Publicação**, você poderá achar que não há publicações e bancos de dados listados como opções disponíveis e ver a seguinte mensagem de erro:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Embora seja possível que essa mensagem de erro seja precisa e realmente não haja publicações disponíveis no publicador ao qual você se conectou ou você não tenha permissões suficientes, esse erro também pode ser causado por uma versão mais antiga do SQL Server Management Studio. Tente fazer a atualização para o SQL Server Management Studio 18.0 ou posterior para excluir isso como uma causa raiz.

## <a name="next-steps"></a>Próximas etapas

### <a name="enable-security-features"></a>Habilitar recursos de segurança

Confira o artigo [O que é a Instância Gerenciada de SQL do Azure?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) para obter uma lista abrangente de maneiras de proteger seu banco de dados. Os seguintes recursos de segurança são discutidos:

- [Auditoria da Instância Gerenciada de SQL](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detecção de ameaças](threat-detection-configure.md)
- [Mascaramento de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security)
- [Transparent data encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Funcionalidades da Instância Gerenciada de SQL

Para obter uma visão geral completa das funcionalidades da instância gerenciada, confira:

> [!div class="nextstepaction"]
> [Funcionalidades da Instância Gerenciada de SQL](sql-managed-instance-paas-overview.md)
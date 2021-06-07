---
title: Regras de firewall de IP
description: Configure regras de firewall de IP de nível de servidor para um banco de dados no banco de dados SQL do Azure ou no firewall do Azure Synapse Analytics. Gerenciar o acesso e configurar regras de firewall de IP no nível de banco de dados para o banco de dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: bbad7dcaa1d92df4969c88e4ba86a62987509e39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632792"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Banco de dados SQL do Azure e regras de firewall de IP do Azure Synapse
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Quando você cria um novo servidor no banco de dados SQL do Azure ou no Azure Synapse Analytics chamado *MySqlServer*, por exemplo, um firewall de nível de servidor bloqueia todo o acesso ao ponto de extremidade público para o servidor (que pode ser acessado em *MySqlServer.Database.Windows.net*). Para simplificar, o *banco de dados SQL* é usado para fazer referência ao banco de dados SQL e ao Azure Synapse Analytics.

> [!IMPORTANT]
> Este artigo *não* se aplica à *Instância Gerenciada de SQL do Azure*. Para obter informações sobre a configuração de rede, consulte [conectar seu aplicativo ao Azure SQL instância gerenciada](../managed-instance/connect-application-instance.md).
>
> O Azure Synapse dá suporte apenas a regras de firewall de IP no nível de servidor. Ele não dá suporte a regras de firewall de IP no nível de banco de dados.

## <a name="how-the-firewall-works"></a>Como o firewall funciona

As tentativas de conexão da Internet e do Azure devem passar pelo firewall antes que cheguem ao seu servidor ou banco de dados, como mostra o diagrama a seguir.

   ![Diagrama de configuração do firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Regras de firewall de IP no nível de servidor

Essas regras permitem que os clientes acessem seu servidor inteiro, ou seja, todos os bancos de dados gerenciados pelo servidor. As regras são armazenadas no banco de dados *mestre* . Você pode ter no máximo 128 regras de firewall de IP no nível do servidor para um servidor. Se você tiver a configuração **permitir que serviços e recursos do Azure acessem este servidor** habilitado, isso contará como uma única regra de firewall para o servidor.
  
Você pode configurar regras de firewall de IP no nível de servidor usando as instruções portal do Azure, PowerShell ou Transact-SQL.

- Para usar o portal ou o PowerShell, você deve ser o proprietário da assinatura ou um colaborador da assinatura.
- Para usar o Transact-SQL, você deve se conectar ao banco de dados *mestre* como o logon da entidade de segurança no nível do servidor ou como o administrador do Azure Active Directory. (Uma regra de firewall de IP de nível de servidor deve ser criada primeiro por um usuário que tenha permissões de nível do Azure.)

> [!NOTE]
> Por padrão, durante a criação de um novo SQL Server lógico do portal do Azure, a configuração **permitir que os serviços e recursos do Azure acessem este servidor** está definida como **não**.

### <a name="database-level-ip-firewall-rules"></a>Regras de firewall de IP no nível de banco de dados

As regras de firewall de IP no nível de banco de dados permitem que os clientes acessem determinados bancos (seguros). Você cria as regras para cada banco de dados (incluindo o banco de dados *mestre* ) e elas são armazenadas no banco de dados individual.
  
- Você só pode criar e gerenciar regras de firewall de IP no nível de banco de dados para bancos de dados mestre e de usuário usando instruções Transact-SQL e somente depois de configurar o primeiro firewall no nível de servidor.
- Se você especificar um intervalo de endereços IP na regra de firewall de IP de nível de banco de dados que está fora do intervalo na regra de firewall de IP no nível de servidor, somente os clientes que têm endereços IP no intervalo no nível de banco de dados poderão acessar o banco de dados.
- Você pode ter no máximo 128 regras de firewall de IP no nível do banco de dados para um banco de dados. Para obter mais informações sobre como configurar regras de firewall de IP no nível de banco de dados, consulte o exemplo mais adiante neste artigo e veja [sp_set_database_firewall_rule (banco de dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Recomendações sobre como definir regras de firewall

É recomendável que você use regras de firewall de IP no nível de banco de dados sempre que possível. Essa prática aprimora a segurança e torna seu banco de dados mais portátil. Use regras de firewall de IP de nível de servidor para administradores. Além disso, use-os quando tiver muitos bancos de dados que têm os mesmos requisitos de acesso e não quiser configurar cada um individualmente.

> [!NOTE]
> Para obter informações sobre bancos de dados portáteis no contexto de continuidade de negócios, confira [Requisitos de autenticação para a recuperação de desastres](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regras de firewall de IP no nível de servidor versus no nível de banco de dados

*Os usuários de um banco de dados devem ser totalmente isolados de outro banco de dados?*

Em caso *afirmativo*, use regras de firewall de IP no nível de banco de dados para conceder acesso. Esse método evita o uso de regras de firewall de IP no nível de servidor, que permitem o acesso por meio do firewall a todos os bancos de dados. Isso reduziria a profundidade de suas defesas.

*Os usuários nos endereços IP precisam de acesso a todos os bancos de dados?*

Em caso *afirmativo*, use regras de firewall de IP no nível de servidor para reduzir o número de vezes que você precisa configurar regras de firewall IP.

*A pessoa ou equipe que configura as regras de firewall de IP só tem acesso por meio do portal do Azure, do PowerShell ou da API REST?*

Nesse caso, você deve usar regras de firewall de IP no nível de servidor. Regras de firewall de IP no nível de banco de dados só podem ser configuradas por meio do Transact-SQL.  

*A pessoa ou a equipe que configura as regras de firewall IP proibidas de ter uma permissão de alto nível no nível do banco de dados?*

Nesse caso, use regras de firewall de IP no nível de servidor. Você precisa de pelo menos a permissão *Control Database* no nível de banco de dados para configurar regras de firewall de IP no nível de banco de dados por meio do TRANSACT-SQL.  

*A pessoa ou a equipe que configura ou audita as regras de firewall de IP gerencia centralmente as regras de firewall de IP para muitas (talvez centenas) de bancos de dados?*

Nesse cenário, as práticas recomendadas são determinadas por suas necessidades e seu ambiente. As regras de firewall de IP no nível de servidor podem ser mais fáceis de serem configuradas, mas o script pode configurar regras no nível de banco de dados. E mesmo que você use regras de firewall de IP no nível de servidor, talvez seja necessário auditar as regras de firewall de IP no nível de banco de dados para ver se os usuários com a permissão *Control* no banco de dados criam regras de firewall IP de nível de banco de dados.

*Posso usar uma combinação de regras de firewall de IP no nível de servidor e de banco de dados?*

Sim. Alguns usuários, como administradores, podem precisar de regras de firewall de IP de nível de servidor. Outros usuários, como usuários de um aplicativo de banco de dados, podem precisar de regras de firewall de IP no nível de banco de dados.

### <a name="connections-from-the-internet"></a>Conexões pela Internet

Quando um computador tenta se conectar ao seu servidor pela Internet, o firewall primeiro verifica o endereço IP de origem da solicitação em relação às regras de firewall de IP no nível de banco de dados para o banco de dados que a conexão solicita.

- Se o endereço estiver dentro de um intervalo especificado nas regras de firewall de IP no nível de banco de dados, a conexão será concedida ao banco de dados que contém a regra.
- Se o endereço não estiver dentro de um intervalo nas regras de firewall de IP no nível de banco de dados, o firewall verificará as regras de firewall de IP no nível de servidor. Se o endereço estiver dentro de um intervalo que está nas regras de firewall de IP no nível de servidor, a conexão será concedida. As regras de firewall de IP de nível de servidor se aplicam a todos os bancos de dados gerenciados pelo servidor.  
- Se o endereço não estiver dentro de um intervalo que esteja em qualquer uma das regras de firewall de IP no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

> [!NOTE]
> Para acessar o banco de dados SQL do Azure do computador local, verifique se o firewall na rede e no computador local permitem a comunicação de saída na porta TCP 1433.

### <a name="connections-from-inside-azure"></a>Conexões de dentro do Azure

Para permitir que aplicativos hospedados no Azure se conectem ao SQL Server, as conexões do Azure devem ser habilitadas. Para habilitar as conexões do Azure, deve haver uma regra de firewall com endereços IP inicial e final definidos como 0.0.0.0.

Quando um aplicativo do Azure tenta se conectar ao servidor, o Firewall verifica se as conexões do Azure são permitidas verificando se essa regra de firewall existe. Isso pode ser ativado diretamente na folha portal do Azure, alternando o **permitir que os serviços e recursos do Azure acessem este servidor** como **ativado** nas configurações **firewalls e redes virtuais** . A configuração para ON cria uma regra de firewall de entrada para o IP 0.0.0.0-0.0.0.0 chamado **AllowAllWindowsIP**. Use o PowerShell ou o CLI do Azure para criar uma regra de firewall com os endereços IP inicial e final definidos como 0.0.0.0 se você não estiver usando o Portal. 

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Se você selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.

## <a name="permissions"></a>Permissões

Para criar e gerenciar regras de firewall de IP para o SQL Server do Azure, você precisará ter:

- na função [colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- na função do [Gerenciador de segurança do SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- o proprietário do recurso que contém o SQL Server do Azure

## <a name="create-and-manage-ip-firewall-rules"></a>Criar e gerenciar regras de firewall de IP

Você cria a primeira configuração de firewall no nível de servidor usando o [portal do Azure](https://portal.azure.com/) ou programaticamente usando [Azure PowerShell](/powershell/module/az.sql), [CLI do Azure](/cli/azure/sql/server/firewall-rule)ou uma [API REST](/rest/api/sql/firewallrules/createorupdate)do Azure. Você cria e gerencia regras adicionais de firewall de IP no nível de servidor usando esses métodos ou o Transact-SQL.

> [!IMPORTANT]
> As regras de firewall de IP de nível de banco de dados só podem ser criadas e gerenciadas usando o Transact-SQL.

Para melhorar o desempenho, as regras de firewall de IP de nível de servidor são temporariamente armazenadas em cache no nível do banco de dados. Para atualizar o cache, veja [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Você pode usar a [auditoria de banco de dados](../../azure-sql/database/auditing-overview.md) para auditar alterações de firewall no nível de servidor e de banco de dados.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Usar o portal do Azure para gerenciar regras de firewall de IP no nível de servidor

Para definir uma regra de firewall de IP no nível de servidor no portal do Azure, vá para a página Visão geral do seu banco de dados ou do seu servidor.

> [!TIP]
> Para obter um tutorial, consulte [criar um banco de dados usando o portal do Azure](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Na página Visão geral do banco de dados

1. Para definir uma regra de firewall de IP no nível de servidor na página Visão geral do banco de dados, selecione **definir Firewall do servidor** na barra de ferramentas, como mostra a imagem a seguir.

    ![Regra de firewall de IP do servidor](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    A página **Configurações do firewall** do servidor será aberta.

2. Selecione **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando e, em seguida, selecione **salvar**. Uma regra de firewall de IP no nível do servidor é criada para seu endereço IP atual.

    ![Definir regra de firewall de IP de nível de servidor](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Na página Visão geral do servidor

A página Visão geral do servidor é aberta. Ele mostra o nome do servidor totalmente qualificado (como *mynewserver20170403.Database.Windows.net*) e fornece opções para configuração adicional.

1. Para definir uma regra de nível de servidor nesta página, selecione **Firewall** no menu **configurações** no lado esquerdo.

2. Selecione **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando e, em seguida, selecione **salvar**. Uma regra de firewall de IP no nível do servidor é criada para seu endereço IP atual.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Usar o Transact-SQL para gerenciar regras de firewall de IP

| Exibição de catálogo ou procedimento armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys. firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Servidor |Exibe as regras de firewall de IP atuais no nível de servidor |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Servidor |Cria ou atualiza as regras de firewall de IP no nível de servidor |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Servidor |Remove as regras de firewall de IP no nível de servidor |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Banco de dados |Exibe as regras de firewall de IP atuais no nível de banco de dados |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Banco de dados |Cria ou atualiza as regras de firewall de IP no nível de banco de dados |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Bancos de dados |Remove as regras de firewall de IP no nível de banco de dados |

O exemplo a seguir revisa as regras existentes, habilita um intervalo de endereços IP no servidor *contoso* e exclui uma regra de firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Em seguida, adicione uma regra de firewall de IP no nível do servidor.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para excluir uma regra de firewall de IP no nível de servidor, execute o procedimento armazenado *sp_delete_firewall_rule* . O exemplo a seguir exclui a regra *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Usar o PowerShell para gerenciar regras de firewall de IP no nível de servidor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda é suportado pelo banco de dados SQL do Azure, mas todo o desenvolvimento agora é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos nos módulos AZ e AzureRm são substancialmente idênticos.

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Servidor |Retorna as regras de firewall atuais no nível de servidor |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Servidor |Cria as regras de firewall no nível de servidor |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall existente no nível de servidor |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Servidor |Remove as regras de firewall no nível de servidor |

O exemplo a seguir usa o PowerShell para definir uma regra de firewall de IP no nível de servidor:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Por $servername especifique o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, especifique **mysqldbserver** em vez de **mysqldbserver.Database.Windows.net**
>
> Para obter exemplos do PowerShell no contexto de um guia de início rápido, consulte [criar DB-PowerShell](powershell-script-content-guide.md) e [criar um banco de dados individual e configurar uma regra de firewall de IP no nível de servidor usando o PowerShell](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Usar a CLI para gerenciar regras de firewall de IP no nível de servidor

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Servidor|Cria uma regra de firewall de IP do servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Servidor|Lista as regras de firewall de IP em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Servidor|Mostra o detalhe de uma regra de firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Servidor|Atualiza uma regra de firewall de IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Servidor|Exclui uma regra de firewall IP|

O exemplo a seguir usa a CLI para definir uma regra de firewall de IP no nível de servidor:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Por $servername especifique o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, especifique **mysqldbserver** em vez de **mysqldbserver.Database.Windows.net**
>
> Para obter um exemplo de CLI no contexto de um guia de início rápido, consulte [Create DB-CLI do Azure](az-cli-script-samples-content-guide.md) e [criar um banco de dados individual e configurar uma regra de firewall de IP no nível de servidor usando o CLI do Azure](scripts/create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Usar uma API REST para gerenciar regras de firewall de IP no nível de servidor

| API | Nível | Descrição |
| --- | --- | --- |
| [Listar regras de firewall](/rest/api/sql/firewallrules/listbyserver) |Servidor |Exibe as regras de firewall de IP atuais no nível de servidor |
| [Criar ou atualizar regras de firewall](/rest/api/sql/firewallrules/createorupdate) |Servidor |Cria ou atualiza as regras de firewall de IP no nível de servidor |
| [Excluir regras de firewall](/rest/api/sql/firewallrules/delete) |Servidor |Remove as regras de firewall de IP no nível de servidor |
| [Obter regras de firewall](/rest/api/sql/firewallrules/get) | Servidor | Obtém regras de firewall de IP no nível de servidor |

## <a name="troubleshoot-the-database-firewall"></a>Solucionar problemas do firewall do banco de dados

Considere os seguintes pontos quando o acesso ao banco de dados SQL do Azure não se comportar conforme o esperado.

- **Configuração de firewall local:**

  Antes que o computador possa acessar o Banco de Dados SQL do Azure, talvez seja necessário criar uma exceção de firewall em seu computador para a porta TCP 1433. Para fazer conexões dentro do limite de nuvem do Azure, talvez seja necessário abrir portas adicionais. Para obter mais informações, consulte a seção "banco de dados SQL: externamente vs Inside" de [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL do Azure](adonet-v12-develop-direct-route-ports.md).

- **Conversão de endereços de rede:**

  Devido à NAT (conversão de endereços de rede), o endereço IP usado pelo computador para se conectar ao banco de dados SQL do Azure pode ser diferente do endereço IP nas definições de configuração de IP do seu computador. Para exibir o endereço IP que seu computador está usando para se conectar ao Azure:
    1. Entre no portal.
    1. Vá para a guia **Configurar** no servidor que hospeda seu banco de dados.
    1. O **endereço IP do cliente atual** é exibido na seção **endereços IP permitidos** . Selecione **Adicionar** para **endereços IP permitidos** para permitir que este computador acesse o servidor.

- **As alterações na lista de permissões ainda não entraram em vigor:**

  Pode haver um atraso de até cinco minutos para que as alterações na configuração do firewall do banco de dados SQL do Azure entrem em vigor.

- **O logon não está autorizado ou uma senha incorreta foi usada:**

  Se um logon não tiver permissões no servidor ou a senha estiver incorreta, a conexão com o servidor será negada. A criação de uma configuração de firewall apenas dá aos clientes uma *oportunidade* de tentar se conectar ao servidor. O cliente ainda deve fornecer as credenciais de segurança necessárias. Para obter mais informações sobre como preparar logons, consulte [controlando e concedendo acesso ao banco de dados](logins-create-manage.md).

- **Endereço IP dinâmico:**

  Se você tiver uma conexão com a Internet que usa endereçamento IP dinâmico e tiver problemas ao passar pelo firewall, tente uma das seguintes soluções:
  
  - Pergunte ao seu provedor de serviços de Internet o intervalo de endereços IP atribuído aos seus computadores cliente que acessam o servidor. Adicione esse intervalo de endereços IP como uma regra de firewall de IP.
  - Obtenha endereçamento IP estático em vez disso para seus computadores cliente. Adicione os endereços IP como regras de firewall de IP.

## <a name="next-steps"></a>Próximas etapas

- Confirme se seu ambiente de rede corporativa permite a comunicação de entrada dos intervalos de endereços IP de computação (incluindo intervalos SQL) que são usados pelos data centers do Azure. Talvez seja necessário adicionar esses endereços IP à lista de permissões. Consulte [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653).  
- Consulte nosso início rápido sobre como [criar um banco de dados individual no banco de dados SQL do Azure](single-database-create-quickstart.md).
- Para obter ajuda com a conexão a um banco de dados no banco de dados SQL do Azure de aplicativos de software livre ou de terceiros, consulte [exemplos de código de início rápido do cliente para banco de dados SQL do Azure](connect-query-content-reference-guide.md#libraries).
- Para obter informações sobre portas adicionais que talvez precisem ser abertas, consulte a seção "banco de dados SQL: fora vs." de [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](adonet-v12-develop-direct-route-ports.md)
- Para obter uma visão geral da segurança do banco de dados SQL do Azure, consulte [protegendo seu banco de dados](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png

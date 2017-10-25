---
title: Regras de firewall do Banco de dados SQL do Azure | Microsoft Docs
description: "Saiba como configurar um firewall de banco de dados SQL com regras de firewall no nível de servidor e banco de dados para gerenciar o acesso."
keywords: firewall do banco de dados
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: a99ef3117cba8fea6a69e8f5cef15cf8fe711715
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Regras de firewall de nível do servidor de banco de dados do Banco de Dados SQL do Azure 

O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger seus dados, os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.

#### <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Regras de rede virtual como alternativas às regras IP

Além das regras IP, o firewall também gerencia *regras de rede virtual*. As regras de rede virtual são baseadas nos pontos de extremidade de serviço da rede virtual. Regras de rede virtual podem ser preferíveis a regras de IP em alguns casos. Para saber mais, consulte [Pontos de extremidade de serviço de rede virtual para o Banco de dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Visão geral

Inicialmente, todo acesso Transact-SQL ao seu Azure SQL Server é bloqueado pelo firewall. Para começar a usar o servidor SQL do Azure, especifique uma ou mais regras de firewall no nível do servidor que permitem acesso ao seu servidor SQL do Azure. Use as regras de firewall para especificar quais intervalos de endereço IP da Internet têm permissão e se os aplicativos do Azure podem tentar se conectar ao seu Azure servidor SQL do Azure.

Para conceder acesso seletivo a apenas um dos bancos de dados em seu servidor SQL do Azure, crie uma regra no nível do banco de dados para o banco de dados exigido. Especifique um intervalo de endereços IP na regra de firewall do banco de dados que esteja fora do intervalo de endereços IP especificado na regra de firewall no nível do servidor, e certifique-se de que o endereço IP do cliente se enquadre no intervalo especificado na regra no nível de banco de dados.

As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Azure SQL Server ou Banco de Dados SQL, conforme exibido no diagrama a seguir:

   ![Diagrama descrevendo a configuração de firewall.][1]

* **Regras de firewall no nível do servidor:** essas regras permitem que os clientes acessem todo o Azure SQL Server, ou seja, todos os bancos dentro do mesmo servidor lógico. Essas regras são armazenadas no banco de dados **mestre** . As regras de firewall no nível de servidor podem ser configuradas por meio do portal ou usando instruções Transact-SQL. Para criar regras de firewall de nível de servidor usando o portal do Azure ou o PowerShell, você deve ser o proprietário da assinatura ou um colaborador da assinatura. Para criar uma regra de firewall de nível de servidor usando o Transact-SQL, você deve se conectar à instância do Banco de Dados SQL como o logon principal no nível do servidor ou o administrador do Azure Active Directory (o que significa que uma regra de firewall de nível de servidor deve ser criada primeiro por um usuário com permissões do nível do Azure).
* **Regras de firewall no nível do banco de dados:** essas regras permitem que os clientes acessem certos bancos de dados (seguros) dentro do mesmo servidor lógico. Você pode criar essas regras para cada banco de dados (incluindo o banco de dados **mestre**), e elas serão armazenadas em bancos de dados individuais. As regras de firewall no nível do banco de dados para bancos de dados mestre e usuário só podem ser criadas e gerenciadas por instruções Transact-SQL, e somente depois de ter configurado o primeira firewall no nível do servidor. Se você especificar um intervalo de endereços IP na regra de firewall no nível do banco de dados que está fora do intervalo especificado na regra de firewall no nível do servidor, somente os clientes com endereços IP no intervalo de nível de banco de dados poderão acessar o banco de dados. Você pode ter no máximo 128 regras de firewall no nível do banco de dados para um banco de dados. Para saber mais sobre como configurar regras de firewall no nível do banco de dados, veja o exemplo posteriormente neste artigo e consulte [sp_set_database_firewall_rule (Bancos de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

**Recomendação:** a Microsoft recomenda o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de aumentar a segurança e tornar seu banco de dados mais portátil. Use regras de firewall no nível do servidor para administradores e quando você tem muitos bancos de dados com os mesmos requisitos de acesso e não quer gastar tempo configurando cada um individualmente.

> [!Note]
> Para obter informações sobre bancos de dados portáteis no contexto de continuidade de negócios, confira [Requisitos de autenticação para a recuperação de desastres](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Conectando pela Internet

Quando um computador tenta se conectar ao seu servidor de banco de dados pela Internet, o firewall primeiro verifica o endereço IP de origem da solicitação com base nas regras de firewall no nível de banco de dados para o banco de dados que a conexão está solicitando:

* Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do banco de dados, a conexão será concedida ao Banco de Dados SQL que contém a regra.
* Se o endereço IP da solicitação não estiver dentro de um dos intervalos especificados nas regras de firewall no nível do banco de dados, ocorrerá a verificação das regras de firewall no nível do servidor. Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, a conexão será concedida. Regras de firewall de nível de servidor se aplicam a todos os bancos de dados SQL no SQL Azure server.  
* Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em uma das regras de firewall no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

> [!NOTE]
> Para acessar o Banco de Dados SQL do Azure de seu computador local, verifique se o firewall em sua rede e computador local permite a comunicação de saída na porta TCP 1433.
> 

### <a name="connecting-from-azure"></a>Conexão pelo Azure
Para permitir que os aplicativos do Azure se conectem ao seu SQL Server do Azure, as conexões do Azure devem estar habilitadas. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que essas conexões são permitidas. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor de Banco de Dados SQL do Azure.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

## <a name="creating-and-managing-firewall-rules"></a>Criar e gerenciar regras de firewall
A primeira configuração de firewall no nível do servidor pode ser criada usando o [Portal do Azure](https://portal.azure.com/) ou com programação usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [CLI do Azure](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) ou a [API REST](https://docs.microsoft.com/rest/api/sql/firewallrules). As regras de firewall no nível de servidor subsequentes podem ser criadas e gerenciados usando esses métodos e por meio de Transact-SQL. 

> [!IMPORTANT]
> As regras de firewall no nível de banco de dados só podem ser criadas e gerenciadas usando com o Transact-SQL. 
>

Para melhorar o desempenho, as regras de firewall de nível de servidor são temporariamente armazenadas em cache no nível do banco de dados. Para atualizar o cache, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> Você pode usar a [Auditoria de Banco de Dados SQL](sql-database-auditing.md) para auditar alterações de firewall no nível do servidor e no nível de banco de dados.
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Gerenciar regras de firewall usando o Portal do Azure

Para definir uma regra de firewall no nível do servidor no Portal do Azure, você pode acessar a página de Visão geral de seu Banco de Dados SQL do Azure ou a página de Visão geral do seu servidor lógico do Banco de Dados do Azure.

> [!TIP]
> Para ver um tutorial, consulte [Criar um banco de dados usando o Portal do Azure](sql-database-get-started-portal.md).
>

**Na página de visão geral de banco de dados**

1. Para definir uma regra de firewall de nível de servidor da página de visão geral do banco de dados, clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem a seguir: a página **Configurações de Firewall** é exibida para o servidor do Banco de Dados SQL.

      ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando no momento e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para seu endereço IP atual.

      ![definir regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**Da página de visão geral de servidor**

A página de visão geral de seu servidor é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170403.database.windows.net**) e fornece opções para configurações adicionais.

1. Para definir uma regra no nível de servidor na página de visão geral do servidor, clique em **Firewall** no menu esquerdo em Configurações: 

2. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando no momento e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para seu endereço IP atual.

## <a name="manage-firewall-rules-using-transact-sql"></a>Gerenciar regras de firewall usando o Transact-SQL
| Exibição do catálogo ou Procedimento armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Servidor |Exibe as regras de firewall atuais no nível de servidor |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Servidor |Cria ou atualiza as regras de firewall no nível de servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Servidor |Remove as regras de firewall no nível de servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Banco de dados |Exibe as regras de firewall atuais no nível de banco de dados |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Banco de dados |Cria ou atualiza as regras de firewall no nível de banco de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bancos de dados |Remove as regras de firewall no nível de banco de dados |


Os exemplos a seguir examinam as regras existentes, habilitam um intervalo de endereços IP no servidor Contoso e excluem uma regra de firewall:
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
Em seguida, adicione uma regra de firewall.
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para excluir uma regra de firewall no nível de servidor, execute o procedimento armazenado sp_delete_firewall_rule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule:
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

## <a name="manage-firewall-rules-using-azure-powershell"></a>Gerenciar regras de firewall usando o Azure PowerShell
| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Servidor |Retorna as regras de firewall atuais no nível de servidor |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Servidor |Cria as regras de firewall no nível de servidor |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall existente no nível de servidor |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Servidor |Remove as regras de firewall no nível de servidor |


O exemplo a seguir define uma regra de firewall no nível de servidor usando o PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Para exemplos do PowerShell no contexto de um início rápido, consulte [Criar banco de dados – PowerShell](sql-database-get-started-powershell.md) e [Criar um banco de dados individual e configurar uma regra de firewall usando o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
>

## <a name="manage-firewall-rules-using-azure-cli"></a>Gerenciar regras de firewall usando a CLI do Azure
| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Servidor|Cria uma regra de firewall de servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Servidor|Lista as regras de firewall em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Servidor|Mostra os detalhes de uma regra de firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Servidor|Atualiza uma regra de firewall|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Servidor|Exclui uma regra de firewall|

O exemplo a seguir define uma regra de firewall no nível de servidor usando a CLI do Azure: 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Para exemplos da CLI do Azure no contexto de um início rápido, consulte [Criar DDB – CLI do Azure](sql-database-get-started-cli.md) e [Criar um banco de dados individual e configurar uma regra de firewall usando a CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md)
>

## <a name="manage-firewall-rules-using-rest-api"></a>Gerenciar regras de firewall usando a API REST
| API | Nível | Descrição |
| --- | --- | --- |
| [Listar regras de firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) |Servidor |Exibe as regras de firewall atuais no nível de servidor |
| [Criar ou Atualizar uma Regra de Firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) |Servidor |Cria ou atualiza as regras de firewall no nível de servidor |
| [Excluir regra de firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) |Servidor |Remove as regras de firewall no nível de servidor |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Regra de firewall de nível de servidor em vez de uma regra de firewall de nível de banco de dados
P. Os usuários de um banco de dados devem ser totalmente isolados de outro banco de dados?   
  Em caso afirmativo, conceda o acesso usando regras de firewall no nível de banco de dados. Isso evita o uso de regras de firewall no nível de servidor, o que permite o acesso pelo firewall a todos os bancos de dados, reduzindo a profundidade das defesas.   
 
P. Os usuários no endereço IP precisam de acesso a todos os bancos de dados?   
  Use regras de firewall no nível de servidor para reduzir o número de vezes que você deverá configurar as regras de firewall.   

P. A pessoa ou a equipe que configura as regras de firewall tem acesso somente por meio do portal do Azure, do PowerShell ou da API REST?   
  É necessário usar regras de firewall no nível de servidor. As regras de firewall no nível de banco de dados só podem ser configuradas com o Transact-SQL.  

P. A pessoa ou a equipe que configura as regras de firewall proíbe a permissão de alto nível no nível de banco de dados?   
  Use regras de firewall no nível de servidor. A configuração de regras de firewall no nível de banco de dados com o Transact-SQL exige, no mínimo, a permissão `CONTROL DATABASE` no nível de banco de dados.  

P. A pessoa ou a equipe que configura ou audita as regras de firewall gerencia centralmente as regras de firewall para vários (talvez centenas) de bancos de dados?   
  Essa seleção depende de suas necessidades e do ambiente. As regras de firewall no nível de servidor podem ser mais fáceis de serem configuradas, mas o script pode configurar regras no nível de banco de dados. E mesmo se você usar regras de firewall no nível de servidor, poderá precisar auditar as regras de firewall do banco de dados para ver se os usuários com a permissão `CONTROL` no banco de dados criaram regras de firewall no nível de banco de dados.   

P. Posso usar uma combinação das regras de firewall no nível de servidor e de banco de dados?   
  Sim. Alguns usuários, como administradores, podem precisar de regras de firewall no nível de servidor. Outros usuários, como usuários de um aplicativo de banco de dados, podem precisar de regras de firewall no nível de banco de dados.   

## <a name="troubleshooting-the-database-firewall"></a>Solucionando problemas do firewall de banco de dados
Considere os seguintes pontos quando o acesso ao serviço de Banco de Dados SQL do Microsoft Azure não se comportar conforme o esperado:

* **Configuração de firewall local:** antes que o computador possa acessar o Banco de Dados SQL do Azure, talvez seja necessário criar uma exceção de firewall em seu computador para a porta TCP 1433. Talvez seja necessário abrir portas adicionais se você estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, consulte a seção **Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
* **NAT (Conversão de endereços de rede):** devido à NAT, o endereço IP usado pelo computador para se conectar ao Banco de Dados SQL do Azure pode ser diferente do endereço IP exibido nas configurações de IP do computador. Para exibir o endereço IP usado pelo computador para se conectar ao Azure, faça logon no Portal e navegue até a guia **Configurar** no servidor que hospeda o seu banco de dados. Na seção **Endereços IP Permitidos**, o **Endereço IP do Cliente Atual** é exibido. Clique em **Adicionar** nos **Endereços IP Permitidos** para permitir que este computador acesse o servidor.
* **As alterações à lista de permissões ainda não entraram em vigor:** pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados SQL do Azure entrem em vigor.
* **O logon não está autorizado ou uma senha incorreta foi usada:** se um logon não tiver permissões no servidor do Banco de Dados SQL, ou se a senha usada estiver incorreta, a conexão com o servidor do Banco de Dados SQL será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente deve fornecer as credenciais de segurança necessárias. Para saber mais sobre a preparação de logons, consulte Gerenciando bancos de dados, logons e usuários no Banco de Dados SQL do Azure.
* **Endereço IP dinâmico:** se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:
  
  * Peça ao seu Provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores clientes que acessarão o servidor de Banco de Dados SQL do Azure e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.
  * Obtenha o endereçamento IP estático para os computadores cliente e, em seguida, adicione os endereços IP como regras de firewall.

## <a name="next-steps"></a>Próximas etapas

- Para um início rápido sobre como criar um banco de dados e uma regra de firewall de nível de servidor, consulte [Criar um Banco de Dados SQL do Azure](sql-database-get-started-portal.md).
- Para obter ajuda com a conexão de um Banco de Dados SQL do Azure a partir de aplicativos de fonte aberta ou de terceiros, consulte [Exemplos de código do cliente de início rápido para o Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para obter mais informações sobre as portas adicionais que você pode precisar abrir, consulte a seção **Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para obter uma visão geral de segurança do Banco de Dados SQL do Azure, consulte [Protegendo seu banco de dados](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

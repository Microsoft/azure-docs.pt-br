---
title: Habilitar insights do SQL
description: Habilitar insights do SQL no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: e8dd887d151eb553131048f232940555dbef324b
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025026"
---
# <a name="enable-sql-insights-preview"></a>Habilitar insights do SQL (versão prévia)
Este artigo descreve como habilitar o [SQL insights](sql-insights-overview.md) para monitorar suas implantações do SQL. O monitoramento é executado de uma máquina virtual do Azure que faz uma conexão com suas implantações SQL e usa DMVs (exibições de gerenciamento dinâmico) para coletar dados de monitoramento. Você pode controlar quais conjuntos de valores são coletados e a frequência da coleta usando um perfil de monitoramento.

## <a name="create-log-analytics-workspace"></a>Criar espaço de trabalho do Log Analytics
O SQL insights armazena seus dados em um ou mais [espaços de trabalho do log Analytics](../logs/data-platform-logs.md#log-analytics-workspaces).  Antes de poder habilitar o SQL insights, você precisa [criar um espaço de trabalho](../logs/quick-create-workspace.md) ou selecionar um existente. Um único espaço de trabalho pode ser usado com vários perfis de monitoramento, mas o espaço de trabalho e os perfis devem estar localizados na mesma região do Azure. Para habilitar e acessar os recursos no SQL insights, você deve ter a [função de colaborador de log Analytics](../logs/manage-access.md) no espaço de trabalho. 

## <a name="create-monitoring-user"></a>Criar usuário de monitoramento 
Você precisa de um usuário nas implantações do SQL que deseja monitorar. Siga os procedimentos abaixo para diferentes tipos de implantações do SQL.

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
Abra o banco de dados SQL do Azure com [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou o [Editor de consultas (versão prévia)](../../azure-sql/database/connect-query-portal.md) no portal do Azure.

Execute o script a seguir para criar um usuário com as permissões necessárias. Substitua *User* por um nome de usuário e *mystrongpassword* por uma senha.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Criar script de usuário Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Verifique se o usuário foi criado.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Verifique o script de usuário Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Instância Gerenciada do Azure SQL
Faça logon em seu Instância Gerenciada de SQL do Azure e use [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou ferramenta semelhante para executar o script a seguir para criar o usuário de monitoramento com as permissões necessárias. Substitua *User* por um nome de usuário e *mystrongpassword* por uma senha.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Faça logon em sua máquina virtual do Azure executando SQL Server e use [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou ferramenta semelhante para executar o script a seguir para criar o usuário de monitoramento com as permissões necessárias. Substitua *User* por um nome de usuário e *mystrongpassword* por uma senha.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Criar máquina virtual do Azure 
Será necessário criar uma ou mais máquinas virtuais do Azure que serão usadas para coletar dados para monitorar o SQL.  

> [!NOTE]
>  Os [perfis de monitoramento](#create-sql-monitoring-profile) especificam quais dados serão coletados dos diferentes tipos de SQL que você deseja monitorar. Cada máquina virtual de monitoramento pode ter apenas um perfil de monitoramento associado a ela. Se você tiver uma necessidade de vários perfis de monitoramento, será necessário criar uma máquina virtual para cada um.

### <a name="azure-virtual-machine-requirements"></a>Requisitos de máquina virtual do Azure
As máquinas virtuais do Azure têm os seguintes requisitos.

- Sistema operacional: Ubuntu 18, 4 
- Tamanhos de máquina virtual do Azure recomendados: Standard_B2s (2 CPUs, 4 GiB de memória) 
- Regiões com suporte: qualquer [região com suporte do agente de Azure monitor](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> O tamanho da máquina virtual Standard_B2s (2 CPUs, 4 GiB Memory) dará suporte a até 100 cadeias de conexão. Você não deve alocar mais de 100 conexões a uma única máquina virtual.

Dependendo das configurações de rede dos seus recursos SQL, as máquinas virtuais talvez precisem ser colocadas na mesma rede virtual que os recursos do SQL para que possam fazer conexões de rede para coletar dados de monitoramento.  

## <a name="configure-network-settings"></a>Definir as configurações de rede
Cada tipo de SQL oferece métodos para sua máquina virtual de monitoramento para acessar o SQL com segurança.  As seções a seguir abrangem as opções baseadas no tipo de SQL.

### <a name="azure-sql-databases"></a>Bancos de Dados SQL do Azure  

O SQL insights dá suporte ao acesso ao banco de dados SQL do Azure por meio de seu ponto de extremidade público, bem como da rede virtual.

Para acesso por meio do ponto de extremidade público, você adicionaria uma regra na página **configurações do firewall** e na seção Configurações do [Firewall de IP](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) .  Para especificar o acesso de uma rede virtual, você pode definir [regras de firewall de rede virtual](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) e definir as [marcas de serviço exigidas pelo agente de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).  [Este artigo](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) descreve as diferenças entre esses dois tipos de regras de firewall.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Definir firewall do servidor" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Configurações de firewall." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Instâncias Gerenciadas de SQL do Azure 

Se sua máquina virtual de monitoramento estiver na mesma VNet que seus recursos de MI do SQL, consulte [conectar dentro da mesma vnet](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet). Se sua máquina virtual de monitoramento estiver em uma VNet diferente de seus recursos de MI do SQL, consulte [conectar-se dentro de uma vnet diferente](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Máquina virtual do Azure e máquina virtual do Azure SQL  
Se sua máquina virtual de monitoramento estiver na mesma VNet que seus recursos de máquina virtual do SQL, consulte [conectar-se a SQL Server em uma rede virtual](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). Se sua máquina virtual de monitoramento estiver em uma VNet diferente de seus recursos de máquina virtual do SQL, consulte  [conectar-se a SQL Server pela Internet](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Armazenar a senha de monitoramento no Key Vault
Você deve armazenar suas senhas de conexão de usuário do SQL em uma Key Vault em vez de inseri-las diretamente nas cadeias de conexão do perfil de monitoramento.

Ao configurar seu perfil para o monitoramento do SQL, você precisará de uma das seguintes permissões no recurso de Key Vault que pretende usar:

- Microsoft.Authorization/roleAssignments/write 
- Permissões Microsoft. Authorization/roleAssignments/Delete, como administrador de acesso do usuário ou proprietário 

Uma nova política de acesso será criada automaticamente como parte da criação do seu perfil de SQL Monitoring que usa o Key Vault especificado. Use *permitir o acesso de todas as redes* para Key Vault configurações de rede.


## <a name="create-sql-monitoring-profile"></a>Criar perfil de monitoramento do SQL
Abra o SQL insights selecionando **SQL (visualização)** na seção **insights** do menu **Azure monitor** na portal do Azure. Clique **Criar novo perfil**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Criar novo perfil." lightbox="media/sql-insights-enable/create-new-profile.png":::

O perfil armazenará as informações que você deseja coletar dos seus sistemas SQL.  Ele tem configurações específicas para: 

- Banco de Dados SQL do Azure 
- Instâncias Gerenciadas de SQL do Azure 
- SQL Server em execução em máquinas virtuais  

Por exemplo, você pode criar um perfil chamado *SQL Production* e outro chamado *SQL staging* com configurações diferentes para frequência de coleta de dados, quais dados coletar e em qual espaço de trabalho os dados devem ser enviados. 

O perfil é armazenado como um recurso de [regra de coleta de dados](../agents/data-collection-rule-overview.md) na assinatura e no grupo de recursos que você selecionar. Cada perfil precisa do seguinte:

- Nome. Não pode ser editado depois de criado.
- Local. Esta é uma região do Azure.
- Log Analytics espaço de trabalho para armazenar os dados de monitoramento.
- Configurações de coleta para a frequência e o tipo de dados de monitoramento do SQL a serem coletados.

> [!NOTE]
> O local do perfil deve estar no mesmo local que o espaço de trabalho de Log Analytics para o qual você planeja enviar os dados de monitoramento.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Detalhes do perfil." lightbox="media/sql-insights-enable/profile-details.png":::

Clique em **Criar perfil de monitoramento** depois de inserir os detalhes do seu perfil de monitoramento. Pode levar até um minuto para que o perfil seja implantado.  Se você não vir o novo perfil listado na caixa de combinação **perfil de monitoramento** , clique no botão atualizar e ele deverá aparecer quando a implantação for concluída.  Depois de selecionar o novo perfil, selecione a guia **Gerenciar perfil** para adicionar um computador de monitoramento que será associado ao perfil.

### <a name="add-monitoring-machine"></a>Adicionar computador de monitoramento
Selecione **Adicionar computador de monitoramento** para abrir um painel de contexto para escolher a máquina virtual a ser configurada para monitorar as instâncias do SQL e fornecer as cadeias de conexão.

Selecione a assinatura e o nome da sua máquina virtual de monitoramento. Se você estiver usando Key Vault para armazenar sua senha para o usuário de monitoramento, selecione a Key Vault recursos com esses segredos e insira a URL e o nome do segredo a serem usados nas cadeias de conexão. Consulte a próxima seção para obter detalhes sobre como identificar a cadeia de conexão para diferentes implantações do SQL.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Adicionar computador de monitoramento." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Adicionar cadeias de conexão 
A cadeia de conexão especifica o nome de usuário que o SQL insights deve usar ao fazer logon no SQL para executar as exibições de gerenciamento dinâmico. Se você estiver usando um Key Vault para armazenar a senha para o usuário de monitoramento, forneça a URL e o nome do segredo a ser usado. 

A cadeia de conexão irá variar para cada tipo de recurso SQL:

#### <a name="azure-sql-databases"></a>Bancos de Dados SQL do Azure 
Insira a cadeia de conexão no formato:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Obtenha os detalhes do item de menu **cadeias de conexão** do banco de dados.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Cadeia de conexão do banco de dados SQL" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Para monitorar um secundário legível, inclua a chave-valor `ApplicationIntent=ReadOnly` na cadeia de conexão.


#### <a name="azure-virtual-machines-running-sql-server"></a>Máquinas virtuais do Azure executando SQL Server 
Insira a cadeia de conexão no formato:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Se sua máquina virtual de monitoramento estiver na mesma VNET, use o endereço IP privado do servidor.  Caso contrário, use o endereço IP público. Se você estiver usando a máquina virtual do SQL do Azure, poderá ver qual porta usar aqui na página de **segurança** para o recurso.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Segurança de máquina virtual do SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::

Para monitorar um secundário legível, inclua a chave-valor `ApplicationIntent=ReadOnly` na cadeia de conexão.


### <a name="azure-sql-managed-instances"></a>Instâncias Gerenciadas de SQL do Azure 
Insira a cadeia de conexão no formato:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Obtenha os detalhes do item de menu **cadeias de conexão** para a instância gerenciada.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="Cadeia de conexão do SQL Instância Gerenciada" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Para monitorar um secundário legível, inclua a chave-valor `ApplicationIntent=ReadOnly` na cadeia de conexão.



## <a name="monitoring-profile-created"></a>Perfil de monitoramento criado 

Selecione **Adicionar monitorando máquina virtual** para configurar a máquina virtual para coletar dados de seus recursos do SQL. Não retorne à guia **visão geral** .  Em alguns minutos, a coluna status deve ser alterada para ler "coleta", você deve ver os dados para os recursos do SQL que você escolheu monitorar.

Se você não vir dados, consulte [Solucionando problemas do SQL insights](sql-insights-troubleshoot.md) para identificar o problema. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Perfil criado" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Se precisar atualizar seu perfil de monitoramento ou as cadeias de conexão em suas VMs de monitoramento, você poderá fazer isso por meio da guia **Gerenciar perfil** do SQL insights.  Depois que as atualizações forem salvas, as alterações serão aplicadas em aproximadamente 5 minutos.

## <a name="next-steps"></a>Próximas etapas

- Consulte [Solucionando problemas do SQL insights](sql-insights-troubleshoot.md) se o SQL insights não estiver funcionando corretamente após ser habilitado.

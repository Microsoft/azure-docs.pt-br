---
title: Pré-requisitos para o serviço de migração de banco de dados do Azure
description: Saiba mais sobre uma visão geral dos pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure para executar migrações de banco de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: f6438fb1c21ce248f6e1b766e7e10cc79043db9f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961576"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Visão geral de pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure

Há vários pré-requisitos necessários para garantir que o serviço de migração de banco de dados do Azure funcione sem problemas ao executar migrações de banco de dados. Alguns dos pré-requisitos se aplicam em todos os cenários (pares de origem e destino) com suporte do serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Os pré-requisitos associados a usar o Serviço de Migração de Banco de Dados do Azure estão listados nas seções a seguir.

## <a name="prerequisites-common-across-migration-scenarios"></a>Pré-requisitos comuns em cenários de migração

Os pré-requisitos do Serviço de Migração de Banco de Dados do Azure que são comuns a todos os cenários de migração compatíveis incluem a necessidade de:

* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Verifique se suas regras de NSG (grupo de segurança de rede) de rede virtual não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Habilitar o protocolo TCP/IP, que está desabilitado por padrão durante a instalação do SQL Server Express, seguindo as instruções no artigo [Habilitar ou desabilitar um protocolo de rede de servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > A criação de uma instância do serviço de migração de banco de dados do Azure requer acesso às configurações de rede virtual que normalmente não estão dentro do mesmo grupo de recursos. Como resultado, o usuário que está criando uma instância do DMS requer permissão no nível da assinatura. Para criar as funções necessárias, que você pode atribuir conforme necessário, execute o seguinte script:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Pré-requisitos para migração do SQL Server para o Banco de Dados SQL do Azure

Além dos pré-requisitos do Serviço de Migração de Banco de Dados do Azure que são comuns a todos os cenários de migração, também há pré-requisitos que se aplicam especificamente para um cenário ou outro.

Ao usar o Serviço de Migração de Banco de Dados do Azure para executar migrações do SQL Server para Banco de Dados SQL do Azure, além dos pré-requisitos que são comuns a todos os cenários de migração, certifique-se de atender aos seguintes pré-requisitos adicionais:

* Crie uma instância da instância do banco de dados SQL do Azure, que você faz seguindo os detalhes no artigo [criar um banco de dados no banco de dados SQL do Azure no portal do Azure](../azure-sql/database/single-database-create-quickstart.md).
* Fazer download e instalar o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
* Se você estiver executando vários usando portas dinâmicas de instâncias nomeadas do SQL Server, talvez você queira habilitar o serviço navegador do SQL e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração do Banco de Dados do Azure possa se conectar a uma instância nomeada em seu código-fonte servidor.
* Crie uma [regra de firewall](../azure-sql/database/firewall-configure.md) no nível de servidor para o banco de dados SQL para permitir que o serviço de migração de banco de dados do Azure acesse os bancos dos dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
* Garantir que as credenciais usadas para se conectar à instância de origem do SQL Server tenham as permissões [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Verifique se as credenciais usadas para se conectar ao banco de dados de destino têm a permissão CONTROL DATABASE no banco de dados de destino.

   > [!NOTE]
   > Para obter uma lista completa de pré-requisitos necessários para usar o Serviço de Migração de Banco de Dados do Azure para executar migrações do SQL Server para o Banco de Dados do SQL Azure, consulte o tutorial [Migrar o SQL Server para o Banco de Dados SQL do Azure](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>Pré-requisitos para migrar SQL Server para o SQL do Azure Instância Gerenciada

* Crie um Instância Gerenciada do SQL seguindo os detalhes no artigo [criar uma instância gerenciada do SQL do Azure no portal do Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
* Abra os firewalls para permitir o tráfego SMB na porta 445 para o intervalo de sub-rede ou endereço IP do Serviço de Migração de Banco de Dados do Azure.
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
* Se você estiver executando vários usando portas dinâmicas de instâncias nomeadas do SQL Server, talvez você queira habilitar o serviço navegador do SQL e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração do Banco de Dados do Azure possa se conectar a uma instância nomeada em seu código-fonte servidor.
* Verificar se os logons usados para conectar o SQL Server de origem e a Instância Gerenciada de destino são membros da função de servidor sysadmin.
* Criar um compartilhamento de rede que pode ser usado pelo Serviço de Migração de Banco de Dados do Azure para fazer backup do banco de dados de origem.
* Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede que você criou e que a conta de computador do servidor de origem tem acesso de leitura/gravação para o mesmo compartilhamento.
* Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado anteriormente. O serviço de migração de banco de dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração.
* Crie um contêiner de blobs e recupere seu URI SAS usando as etapas descritas no artigo [Gerenciar os recursos do Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Selecione todas as permissões (Leitura, Gravação, Exclusão, Lista) na janela da política ao criar o URI SAS.

   > [!NOTE]
   > Para obter uma lista completa dos pré-requisitos necessários para usar o serviço de migração de banco de dados do Azure para executar migrações de SQL Server para SQL Instância Gerenciada, consulte o tutorial [migrar SQL Server para o sql instância gerenciada](./tutorial-sql-server-to-managed-instance.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional, consulte o artigo [ O que é o Serviço de Migração de Banco de Dados do Azure ](dms-overview.md).
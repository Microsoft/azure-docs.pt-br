---
title: Pontos de extremidade de rede virtual e regras para bancos de dados no banco de dados SQL do Azure
description: Marque uma sub-rede como um ponto de extremidade de serviço de rede virtual. Em seguida, adicione o ponto de extremidade como uma regra de rede virtual à ACL para seu banco de dados. Em seguida, seu banco de dados aceita comunicação de todas as máquinas virtuais e de outros nós na sub-rede.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99095351"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Use as regras e pontos de extremidade de serviço da rede virtual para os servidores no Banco de Dados SQL do Azure

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*As regras de rede virtual* são um recurso de segurança de firewall que controla se o servidor de seus bancos de dados e pools elásticos no [banco de dados SQL do Azure](sql-database-paas-overview.md) ou nos bancos de dado do pool SQL dedicado (anteriormente conhecido como SQL DW) no [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações enviadas de sub-redes específicas em redes virtuais. Este artigo explica por que as regras de rede virtual são, às vezes, a melhor opção para permitir a comunicação com segurança no banco de dados SQL e no Azure Synapse Analytics.

> [!NOTE]
> Este artigo se aplica ao banco de dados SQL e ao Azure Synapse Analytics. Para simplificar, o termo *banco de dados* refere-se a ambos os bancos de dados do SQL e do Azure Synapse Analytics. Da mesma forma, todas as referências ao *servidor* referem-se ao [SQL Server lógico](logical-servers.md) que hospeda o banco de dados SQL e o Azure Synapse Analytics.

Para criar uma regra da rede virtual, primeiro, é preciso que haja um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra de referência.

## <a name="create-a-virtual-network-rule"></a>Criar uma regra de rede virtual

Se você quiser criar apenas uma regra de rede virtual, poderá pular para as etapas e explicação [mais adiante neste artigo](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre as regras da rede virtual

Esta seção descreve vários detalhes sobre as regras da rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade de serviço de rede virtual aplica-se a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem comunicação da sub-rede.

Qualquer regra da rede virtual é limitada à região à qual seu ponto de extremidade subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de banco de dados

Cada regra de rede virtual se aplica a todo o servidor, não apenas a um banco de dados específico no servidor. Em outras palavras, as regras de rede virtual se aplicam no nível do servidor, não no nível do banco de dados.

Por outro lado, as regras de IP podem ser aplicadas em qualquer nível.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos de extremidade de serviço de rede virtual. A ação é necessária em cada uma das seguintes funções:

- **Administrador de rede (função [colaborador de rede](../../role-based-access-control/built-in-roles.md#network-contributor) ):** &nbsp; Ative o ponto de extremidade.
- **Administrador de banco de dados (função de [colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ):** &nbsp; Atualize a lista de controle de acesso (ACL) para adicionar a sub-rede fornecida ao servidor.

#### <a name="azure-rbac-alternative"></a>Alternativa do RBAC do Azure

As funções de Administrador de banco de dados e Administrador de rede têm mais recursos do que o necessário para gerenciar regras da rede virtual. É necessário apenas um subconjunto de seus recursos.

Você tem a opção de usar o [controle de acesso baseado em função (RBAC)][rbac-what-is-813s] no Azure para criar uma única função personalizada que tenha apenas o subconjunto necessário de recursos. A função personalizada pode ser usada em vez de envolver o administrador de rede ou o administrador de banco de dados. A área da superfície de sua exposição de segurança será menor se você adicionar um usuário a uma função personalizada em vez de adicionar o usuário às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, o banco de dados no banco de dados SQL e a sub-rede da rede virtual estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
>
> - Ambas as assinaturas devem estar no mesmo locatário Azure Active Directory (Azure AD).
> - O usuário tem as permissões necessárias para iniciar operações, como habilitar pontos de extremidade de serviço e adicionar uma sub-rede de rede virtual ao servidor especificado.
> - Ambas as assinaturas devem ter o provedor Microsoft.Sql registrado.

## <a name="limitations"></a>Limitações

Para o banco de dados SQL, o recurso de regras de rede virtual tem as seguintes limitações:

- No firewall para seu banco de dados no banco de dados SQL, cada regra de rede virtual faz referência a uma sub-rede. Todas essas sub-redes referenciadas devem ser hospedadas na mesma região geográfica que hospeda o banco de dados.
- Cada servidor pode ter até 128 entradas de ACL para qualquer rede virtual.
- As regras de rede virtual só se aplicam a Azure Resource Manager redes virtuais e não a redes de [modelo de implantação clássica][arm-deployment-model-568f] .
- A ativação de pontos de extremidade de serviço de rede virtual para o banco de dados SQL também habilita os pontos de extremidade do banco de dados do Azure para MySQL e banco de dados do Azure para PostgreSQL. Com os pontos de extremidade definidos como **on**, as tentativas de conexão a partir dos pontos de extremidade para as instâncias do banco de dados do Azure para MySQL ou do banco de dados do Azure para PostgreSQL podem falhar.
  - O motivo subjacente é que o banco de dados do Azure para MySQL e o banco de dados do Azure para PostgreSQL provavelmente não têm uma regra de rede virtual configurada. Você deve configurar uma regra de rede virtual para o banco de dados do Azure para MySQL e o banco de dados do Azure para PostgreSQL e a conexão terá sucesso.
  - Para definir regras de firewall de rede virtual em um servidor lógico do SQL que já está configurado com pontos de extremidade privados, defina **negar acesso à rede pública** como **não**.
- No firewall, os intervalos de endereços IP se aplicam aos seguintes itens de rede, mas as regras de rede virtual não:
  - [VPN (rede virtual privada) site a site (S2S)][vpn-gateway-indexmd-608y]
  - Local por meio [do Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Considerações ao usar pontos de extremidade de serviço

Ao usar pontos de extremidade de serviço para o banco de dados SQL, examine as seguintes considerações:

- **São necessários IPs públicos de saída para o banco de dados SQL do Azure.** Os NSGs (grupos de segurança de rede) devem ser abertos para IPs de banco de dados SQL para permitir a conectividade. Você pode fazer isso usando as [marcas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) NSG para o banco de dados SQL.

### <a name="expressroute"></a>ExpressRoute

Se você usar o [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de seu local, para emparelhamento público ou emparelhamento da Microsoft, você precisará identificar os endereços IP de NAT que são usados. Para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP de NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para o emparelhamento da Microsoft, os endereços IP de NAT que são usados são fornecidos pelo cliente ou pelo provedor de serviços. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Para saber mais sobre NAT para o emparelhamento público e da Microsoft para o ExpressRoute, confira [requisitos de NAT para o emparelhamento público do Azure](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Para permitir a comunicação de seu circuito com o banco de dados SQL, você deve criar regras de rede IP para os endereços IP públicos de seu NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Impacto do uso de pontos de extremidade de serviço de rede virtual com o armazenamento do Azure

O Armazenamento do Azure implementou o mesmo recurso que permite que você limite a conectividade à sua conta de Armazenamento do Azure. Se você optar por usar esse recurso com uma conta de armazenamento do Azure que o banco de dados SQL está usando, poderá encontrar problemas. Em seguida, há uma lista e uma discussão sobre os recursos do banco de dados SQL e do Azure Synapse Analytics afetados por isso.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics polybase e instrução de cópia

O polybase e a instrução de cópia são normalmente usados para carregar dados no Azure Synapse Analytics de contas de armazenamento do Azure para a ingestão de dados de alta taxa de transferência. Se a conta de armazenamento do Azure para a qual você está carregando dados limita o acesso somente a um conjunto de sub-redes de rede virtual, a conectividade quando você usa o polybase e a instrução de cópia para a conta de armazenamento serão interrompidas. Para habilitar cenários de importação e exportação usando o COPY e o polybase com o Azure Synapse Analytics se conectando ao armazenamento do Azure que é protegido para uma rede virtual, siga as etapas nesta seção.

#### <a name="prerequisites"></a>Pré-requisitos

- Instale o Azure PowerShell usando [este guia](/powershell/azure/install-az-ps).
- Se você tiver uma conta de uso geral v1 ou de armazenamento de BLOBs do Azure, primeiro será necessário atualizar para a versão de uso geral v2 seguindo as etapas em [atualizar para uma conta de armazenamento de uso geral v2](../../storage/common/storage-account-upgrade.md).
- Você deve ter **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** ativada no menu de configurações de **firewalls e redes virtuais** da conta de armazenamento do Azure. Habilitar essa configuração permitirá que o polybase e a instrução de cópia se conectem à conta de armazenamento usando a autenticação forte em que o tráfego de rede permanece no backbone do Azure. Para obter mais informações, consulte [este guia](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020. Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Etapas

1. Se você tiver um pool SQL dedicado autônomo, Registre seu SQL Server com o Azure AD usando o PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Essa etapa não é necessária para pools SQL dedicados em um espaço de trabalho do Azure Synapse Analytics.

1. Se você tiver um espaço de trabalho do Azure Synapse Analytics, registre a identidade gerenciada pelo sistema do seu espaço de trabalho:

   1. Vá para o espaço de trabalho do Azure Synapse Analytics na portal do Azure.
   2. Vá para o painel **identidades gerenciadas** .
   3. Verifique se a opção **permitir pipelines** está habilitada.
   
1. Crie uma **conta de armazenamento de uso geral v2** seguindo as etapas em [criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Se você tiver uma conta de armazenamento de blob v1 ou de uso geral, deverá *primeiro atualizar para a v2* seguindo as etapas em [atualizar para uma conta de armazenamento v2 de uso geral](../../storage/common/storage-account-upgrade.md).
   > - Para problemas conhecidos com o Azure Data Lake Storage Gen2, consulte [problemas conhecidos com o Azure data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Em sua conta de armazenamento, vá para **controle de acesso (iam)** e selecione **Adicionar atribuição de função**. Atribua a função de **colaborador de dados do blob de armazenamento** ao servidor ou espaço de trabalho que hospeda seu pool SQL dedicado, que você registrou com o Azure AD.

   > [!NOTE]
   > Somente membros com privilégio de proprietário na conta de armazenamento podem executar esta etapa. Para várias funções internas do Azure, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md).
  
1. Para habilitar a conectividade do polybase com a conta de armazenamento do Azure:

   1. Crie uma [chave mestra](/sql/t-sql/statements/create-master-key-transact-sql) de banco de dados se você não tiver criado uma anteriormente.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Crie uma credencial no escopo do banco de dados com **Identity = ' identidade de serviço gerenciada '**.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Não é necessário especificar o segredo com uma chave de acesso de armazenamento do Azure porque esse mecanismo usa a [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) nos bastidores.
       > - O nome da identidade deve ser **' identidade de serviço gerenciada '** para que a conectividade do polybase funcione com uma conta de armazenamento do Azure protegida em uma rede virtual.

   1. Crie uma fonte de dados externa com o `abfss://` esquema para se conectar à sua conta de armazenamento de uso geral v2 usando o polybase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Se você já tiver tabelas externas associadas a uma conta de armazenamento de blob v1 ou de uso geral, deverá primeiro descartar essas tabelas externas. Em seguida, descarte a fonte de dados externa correspondente. Em seguida, crie uma fonte de dados externa com o `abfss://` esquema que se conecta a uma conta de armazenamento de uso geral v2, como mostrado anteriormente. Em seguida, recrie todas as tabelas externas usando essa nova fonte de dados externa. Você pode usar o [Assistente para gerar e publicar scripts](/sql/ssms/scripting/generate-and-publish-scripts-wizard) para gerar a facilidade de criar scripts para todas as tabelas externas.
       > - Para obter mais informações sobre o `abfss://` esquema, consulte [usar o URI de Azure data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Para obter mais informações sobre como criar uma fonte de dados externa, consulte [este guia](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Consultar normalmente usando [tabelas externas](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>Auditoria de blob do banco de dados SQL

A auditoria de blob envia por push logs de auditoria para sua própria conta de armazenamento. Se essa conta de armazenamento usar o recurso de pontos de extremidade de serviço de rede virtual, a conectividade do banco de dados SQL com a conta de armazenamento será interrompida.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Adicionar uma regra de firewall de rede virtual ao servidor

Há muito tempo, antes que esse recurso fosse aprimorado, era necessário ativar os pontos de extremidade de serviço de rede virtual antes de implementar uma regra de rede virtual ao vivo no firewall. Os pontos de extremidade relacionados a uma determinada sub-rede de rede virtual a um banco de dados no banco de dados SQL. A partir de janeiro de 2018, você pode burlar esse requisito definindo o sinalizador **IgnoreMissingVNetServiceEndpoint** . Agora, você pode adicionar uma regra de firewall de rede virtual ao seu servidor sem ativar os pontos de extremidade de serviço de rede virtual.

Simplesmente definir uma regra de firewall não ajuda a proteger o servidor. Você também deve ativar os pontos de extremidade de serviço de rede virtual para que a segurança entre em vigor. Quando você ativa pontos de extremidade de serviço, sua sub-rede de rede virtual apresenta tempo de inatividade até que conclua a transição de desativado para ativado. Esse período de inatividade é especialmente verdadeiro no contexto de grandes redes virtuais. Use o sinalizador **IgnoreMissingVNetServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Defina o sinalizador **IgnoreMissingVNetServiceEndpoint** usando o PowerShell. Para obter mais informações, consulte [PowerShell para criar um ponto de extremidade de serviço de rede virtual e uma regra para o banco de dados SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

O erro de conexão 40914 está relacionado às *regras de rede virtual*, conforme especificado no painel **Firewall** no portal do Azure. O erro 40615 é semelhante, exceto pelo fato de que ele está relacionado às *regras de endereço IP* no firewall.

### <a name="error-40914"></a>Erro 40914

**Texto da mensagem:** "Não é possível abrir o servidor '*[nome-do-servidor]*' solicitado pelo logon. O cliente não tem permissão para acessar o servidor. "

**Descrição do erro:** o cliente está em uma sub-rede que tem pontos de extremidade de servidor de rede virtual. Mas o servidor não tem nenhuma regra de rede virtual que conceda à sub-rede o direito de se comunicar com o banco de dados.

**Resolução de erro:** No painel **Firewall** do portal do Azure, use o controle de regras de rede virtual para [Adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) para a sub-rede.

### <a name="error-40615"></a>Erro 40615

**Texto da mensagem:** "Não é possível abrir {0} o servidor ' ' solicitado pelo logon. O cliente com o endereço IP ' {1} ' não tem permissão para acessar o servidor. "

**Descrição do erro:** O cliente está tentando se conectar de um endereço IP que não está autorizado a se conectar ao servidor. O Firewall do servidor não tem nenhuma regra de endereço IP que permita que um cliente se comunique do endereço IP fornecido ao banco de dados.

**Resolução de erro:** digite o endereço IP do cliente como uma regra de IP. Use o painel **Firewall** no portal do Azure para realizar essa etapa.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Usar o portal para criar uma regra de rede virtual

Esta seção ilustra como você pode usar o [portal do Azure][http-azure-portal-link-ref-477t] para criar uma *regra de rede virtual* em seu banco de dados no banco de dados SQL. A regra diz ao banco de dados para aceitar a comunicação de uma determinada sub-rede que foi marcada como um *ponto de extremidade de serviço de rede virtual*.

> [!NOTE]
> Se você pretende adicionar um ponto de extremidade de serviço às regras de firewall de rede virtual do seu servidor, primeiro verifique se os pontos de extremidade de serviço estão ativados para a sub-rede.
>
> Se os pontos de extremidade de serviço não estiverem ativados para a sub-rede, o portal solicitará que você os habilite. Selecione o botão **habilitar** no mesmo painel no qual você adiciona a regra.

## <a name="powershell-alternative"></a>Alternativa do PowerShell

Um script também pode criar regras de rede virtual usando o cmdlet do PowerShell **New-AzSqlServerVirtualNetworkRule** ou [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). Se você estiver interessado, consulte [PowerShell para criar um ponto de extremidade de serviço de rede virtual e uma regra para o banco de dados SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa de API REST

Internamente, os cmdlets do PowerShell para ações de rede virtual do SQL chamam APIs REST. É possível chamar as APIs REST diretamente.

- [Regras de rede virtual: operações][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

Você já deve ter uma sub-rede marcada com o nome específico do *tipo* de ponto de extremidade de serviço de rede virtual relevante para o banco de dados SQL.

- O nome do tipo de ponto de extremidade relevante é **Microsoft.Sql**.
- Se a sua sub-rede não pode ser marcada com o nome do tipo, consulte [Verificar se sua sub-rede é um ponto de extremidade][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>etapas do portal do Azure

1. Entre no [portal do Azure][http-azure-portal-link-ref-477t].

1. Procure e selecione **servidores SQL** e, em seguida, selecione o servidor. Em **segurança**, selecione **firewalls e redes virtuais**.

1. Defina **permitir acesso aos serviços do Azure** como **desativado**.

    > [!IMPORTANT]
    > Se você deixar o controle definido como **ativado**, o servidor aceitará a comunicação de qualquer sub-rede dentro do limite do Azure. Essa é a comunicação que se origina de um dos endereços IP que são reconhecidos como aqueles dentro de intervalos definidos para os data centers do Azure. Deixar o controle definido como **on** pode ser o acesso excessivo de um ponto de vista de segurança. O recurso de ponto de extremidade de serviço Rede Virtual do Microsoft Azure em coordenação com o recurso de regras de rede virtual do banco de dados SQL pode reduzir sua área de superfície de segurança.

1. Selecione **+ Adicionar existente** na seção **redes virtuais** .

    ![Captura de tela que mostra a seleção de + adicionar existente (ponto de extremidade de sub-rede, como uma regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

1. No novo painel **criar/atualizar** , preencha as caixas com os nomes dos recursos do Azure.

    > [!TIP]
    > Você deve incluir o prefixo de endereço correto para sua sub-rede. Você pode encontrar o valor do **prefixo de endereço** no Portal. Ir para **todos os recursos** &gt; **todos os tipos** &gt; **redes virtuais**. O filtro exibe suas redes virtuais. Selecione sua rede virtual e, em seguida, selecione **sub-redes**. A coluna **intervalo de endereços** tem o prefixo de endereço de que você precisa.

    ![Captura de tela que mostra o preenchimento em caixas para a nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Selecione o botão **OK** próximo à parte inferior do painel.

1. Consulte a regra de rede virtual resultante no painel **Firewall** .

    ![Captura de tela que mostra a nova regra no painel Firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> O status ou os estados a seguir se aplicam às regras:
>
> - **Pronto**: indica que a operação iniciada foi bem-sucedida.
> - **Falha**: indica que a operação iniciada falhou.
> - **Excluído**: aplica-se somente à operação de exclusão e indica que a regra foi excluída e não se aplica mais.
> - Em **andamento**: indica que a operação está em andamento. A regra antiga é aplicável enquanto a operação está nesse estado.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Artigos relacionados

- [Pontos de extremidade de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall no nível de servidor e banco de dados][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Próximas etapas

- [Usar o PowerShell para criar um ponto de extremidade de serviço de rede virtual e depois uma regra de rede virtual para o banco de dados SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regras de rede virtual: operações][rest-api-virtual-network-rules-operations-862r] com APIs REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->

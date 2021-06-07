---
title: Controles de Acesso à Rede
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Visão geral de como gerenciar e controlar o acesso à rede para o banco de dados SQL do Azure e o Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460013"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Controles de acesso à rede do banco de dados SQL do Azure e do Azure Synapse Analytics

Quando você cria um SQL Server lógico do [portal do Azure](single-database-create-quickstart.md) para o banco de dados SQL do Azure e o Azure Synapse Analytics, o resultado é um ponto de extremidade público no formato *yourservername.Database.Windows.net*.

Você pode usar os seguintes controles de acesso à rede para permitir seletivamente o acesso a um banco de dados por meio do ponto de extremidade público:

- Permitir serviços do Azure: quando definido como ativado, outros recursos dentro do limite do Azure, por exemplo, uma máquina virtual do Azure, podem acessar o banco de dados SQL
- Regras de firewall de IP: Use esse recurso para permitir explicitamente conexões de um endereço IP específico, por exemplo, de computadores locais

Você também pode permitir o acesso privado ao banco de dados de [redes virtuais](../../virtual-network/virtual-networks-overview.md) por meio de:

- Regras de firewall de rede virtual: Use esse recurso para permitir o tráfego de uma rede virtual específica dentro do limite do Azure
- Link privado: Use este recurso para criar um ponto de extremidade privado para o [SQL Server lógico](logical-servers.md) em uma rede virtual específica

> [!IMPORTANT]
> Este artigo não *se aplica* ao **SQL instância gerenciada**. Para obter mais informações sobre a configuração de rede, consulte [conectando-se ao Azure SQL instância gerenciada](../managed-instance/connect-application-instance.md) .

Consulte o vídeo abaixo para obter uma explicação de alto nível desses controles de acesso e o que eles fazem:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Permitir serviços do Azure

Por padrão, durante a criação de um novo SQL Server lógico [a partir do portal do Azure](single-database-create-quickstart.md), essa configuração é definida como **off**. Essa configuração aparece quando a conectividade é permitida usando o ponto de extremidade de serviço público.

Você também pode alterar essa configuração por meio do painel Firewall depois que o SQL Server lógico for criado da seguinte maneira.
  
![Captura de tela de gerenciar o Firewall do servidor][2]

Quando definido como **on**, o servidor permite a comunicação de todos os recursos dentro do limite do Azure, que pode ou não fazer parte de sua assinatura.

Em muitos casos, a configuração **on** é mais permissiva do que a maioria dos clientes desejam. Talvez você queira definir essa configuração como **off** e substituí-la por regras de firewall IP mais restritivas ou regras de firewall de rede virtual. 

No entanto, isso afeta os seguintes recursos que são executados em máquinas virtuais no Azure que não fazem parte da sua rede virtual e, portanto, se conectam ao banco de dados por meio de um endereço IP do Azure:

### <a name="import-export-service"></a>Serviço de Importação/Exportação

O serviço de exportação de importação não funciona quando **permitir acesso aos serviços do Azure** está definido como **desativado**. No entanto, você pode contornar o problema [executando manualmente sqlpackage.exe de uma VM do Azure ou executando a exportação](./database-import-export-azure-services-off.md) diretamente em seu código usando a API DACFx.

### <a name="data-sync"></a>Sincronização de Dados

Para usar o recurso de sincronização de dados com **permitir acesso aos serviços do Azure** definidos como **desativado**, você precisa criar entradas de regra de firewall individuais para [adicionar endereços IP](firewall-create-server-level-portal-quickstart.md) da **marca de serviço do SQL** para a região que hospeda o banco de dados de **Hub** .
Adicione essas regras de firewall no nível de servidor aos servidores que hospedam bancos de dados de **Hub** e **membro** (que podem estar em regiões diferentes)

Use o seguinte script do PowerShell para gerar endereços IP correspondentes à marca de serviço do SQL para a região oeste dos EUA

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag retorna o intervalo global para a marca de serviço do SQL, apesar de especificar o parâmetro de local. Certifique-se de filtrá-lo para a região que hospeda o banco de dados de Hub usado pelo seu grupo de sincronização

Observe que a saída do script do PowerShell está na notação CIDR (roteamento sem classe Inter-Domain). Isso precisa ser convertido em um formato de endereço IP inicial e final usando [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) como este:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Você pode usar esse script do PowerShell adicional para converter todos os endereços IP do CIDR para o formato de endereço IP inicial e final.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Agora você pode adicioná-las como regras de firewall distintas e, em seguida, definir **permitir que os serviços do Azure acessem o servidor**  como desativado.

## <a name="ip-firewall-rules"></a>Regras de firewall de IP

O firewall baseado em IP é um recurso do SQL Server lógico no Azure que impede todo o acesso ao servidor até que você adicione explicitamente os [endereços IP](firewall-create-server-level-portal-quickstart.md) dos computadores cliente.

## <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Além das regras de IP, o Firewall do servidor permite que você defina *regras de rede virtual*.  
Para saber mais, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](vnet-service-endpoint-rule-overview.md) ou assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologia de rede do Azure

Esteja atento aos seguintes termos de rede do Azure ao explorar as regras de firewall de rede virtual

**Rede virtual:** Você pode ter redes virtuais associadas à sua assinatura do Azure

**Sub-rede:** uma rede virtual contém **sub-redes**. Todas as máquinas virtuais do Azure (VMs) que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computadores. Os nós de computação que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure sua segurança para permitir o acesso.

**Ponto de extremidade de serviço de rede virtual:** Um [ponto de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Neste artigo, estamos interessados no nome do tipo do **Microsoft. SQL**, que se refere ao serviço do Azure chamado banco de dados SQL.

**Regra de rede virtual:** Uma regra de rede virtual para seu servidor é uma sub-rede que está listada na ACL (lista de controle de acesso) do seu servidor. Para estar na ACL para seu banco de dados no banco de dados SQL, a sub-rede deve conter o nome do tipo **Microsoft. SQL** . Uma regra de rede virtual instrui o servidor a aceitar comunicações de cada nó que está na sub-rede.

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP versus regras de firewall de rede virtual

O Firewall do banco de dados SQL do Azure permite especificar intervalos de endereços IP dos quais as comunicações são aceitas no banco de dados SQL. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. No entanto, as máquinas virtuais (VMs) na rede privada do Azure são configuradas com endereços IP *dinâmicos* . Os endereços IP dinâmicos podem mudar quando sua VM é reiniciada e, por sua vez, invalidar a regra de firewall baseada em IP. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall, em um ambiente de produção.

Você pode contornar essa limitação obtendo um endereço IP *estático* para sua VM. Para obter detalhes, consulte [criar uma máquina virtual com um endereço IP público estático usando o portal do Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é dispendiosa quando feita em escala.

As regras de rede virtual são uma alternativa mais fácil de estabelecer e gerenciar o acesso de uma sub-rede específica que contém suas VMs.

> [!NOTE]
> Ainda não é possível ter o Banco de Dados SQL em uma sub-rede. Se o seu servidor era um nó em uma sub-rede em sua rede virtual, todos os nós na rede virtual podem se comunicar com o banco de dados SQL. Nesse caso, suas VMs podem se comunicar com o Banco de Dados SQL sem a necessidade de nenhuma regra da rede virtual ou regras de IP.

## <a name="private-link"></a>Link Privado

O link privado permite que você se conecte a um servidor por meio de um **ponto de extremidade privado**. Um ponto de extremidade privado é um endereço IP privado em uma [rede virtual](../../virtual-network/virtual-networks-overview.md) e sub-rede específica.

## <a name="next-steps"></a>Próximas etapas

- Para obter um início rápido sobre como criar uma regra de firewall de IP no nível de servidor, consulte [criar um banco de dados no banco de dados SQL](single-database-create-quickstart.md).

- Para obter um início rápido sobre como criar uma regra de firewall de rede virtual no nível de servidor, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](vnet-service-endpoint-rule-overview.md).

- Para obter ajuda com a conexão com um banco de dados no banco de dados SQL de software livre ou aplicativos de terceiros, consulte [exemplos de código de início rápido do cliente para banco de dados SQL](/previous-versions/azure/ee336282(v=azure.100)).

- Para obter mais informações sobre as portas adicionais que você pode precisar abrir, consulte a seção **Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](adonet-v12-develop-direct-route-ports.md)

- Para obter uma visão geral da conectividade do banco de dados SQL do Azure, consulte [arquitetura de conectividade do SQL do Azure](connectivity-architecture.md)

- Para obter uma visão geral de segurança do Banco de Dados SQL do Azure, consulte [Protegendo seu banco de dados](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png

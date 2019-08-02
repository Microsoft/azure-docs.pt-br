---
title: Arquitetura de conectividade de SQL Data Warehouse e banco de dados SQL do Azure | Microsoft Docs
description: Este documento explica a arquitetura de conectividade do SQL do Azure para conexões de banco de dados de dentro do Azure ou de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 07/02/2019
ms.openlocfilehash: 9dfc22be45b68ba4ff59d88810435db35bafc8b6
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494959"
---
# <a name="azure-sql-connectivity-architecture"></a>Arquitetura de conectividade do SQL do Azure

Este artigo explica a arquitetura de conectividade do Banco de Dados SQL do Azure e do SQL Data Warehouse, além de como os diferentes componentes funcionam para direcionar o tráfego para a instância do SQL do Azure. Esses componentes de conectividade funcionam para direcionar o tráfego de rede para o Banco de Dados SQL do Azure ou o SQL Data Warehouse com clientes que se conectam dentro e fora do Azure. Este artigo também fornece exemplos de script para alterar o modo como ocorre a conectividade e as considerações relacionadas à alteração das configurações de conectividade padrões.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura de conectividade do Banco de Dados SQL do Azure.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

As seguintes etapas descrevem como uma conexão é estabelecida com um banco de dados SQL do Azure:

- Os clientes se conectam ao gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de conexão efetiva, redireciona o tráfego ou usa um proxy para ele para o cluster de banco de dados correto.
- No cluster de banco de dados, o tráfego é encaminhado para o banco de dados SQL do Azure apropriado.

## <a name="connection-policy"></a>Política de Conexão

O Banco de Dados SQL do Azure oferece suporte às três opções a seguir para a configuração de diretiva de conexão de um servidor do Banco de Dados SQL:

- **Redirecionamento (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados. Para habilitar a conectividade, os clientes devem permitir regras de firewall de saída para todos os endereços IP do Azure na região usando NSG (grupos de segurança de rede) com [marcas de serviço](../virtual-network/security-overview.md#service-tags)) para as portas 11000-11999, não apenas os endereços IP do gateway do banco de dados SQL do Azure na porta 1433. Como os pacotes vão diretamente para o banco de dados, a latência e o rendimento melhoraram o desempenho.
- **Proxy:** Nesse modo, todas as conexões usam um proxy por meio dos gateways do Banco de Dados SQL do Azure. Para habilitar a conectividade, o cliente precisa ter regras de firewall de saída que permitam apenas os endereços IP do gateway do Banco de Dados SQL do Azure (geralmente, dois endereços IP por região). A escolha desse modo pode resultar em maior latência e menor rendimento, dependendo da natureza da carga de trabalho. É altamente recomendável usar a política de conexão `Redirect` em relação à política de conexão `Proxy` para a menor latência e maior taxa de transferência.
- **Padrão:** Essa é a política de conexão em vigor em todos os servidores após a criação, a menos que você altere explicitamente a política de conexão para `Proxy` ou `Redirect`. A política efetiva depende se as conexões se originam no Azure (`Redirect`) ou fora do Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Conectividade de dentro do Azure

Se você estiver se conectando de dentro do Azure, as conexões terão uma política de conexão de `Redirect` por padrão. Uma política de `Redirect` significa que, após a sessão TCP ser estabelecida com o Banco de Dados SQL do Azure, a sessão do cliente será redirecionada para o cluster de banco de dados correto com uma alteração do IP virtual de destino daquele do gateway do Banco de Dados SQL do Azure para aquele do cluster. Depois disso, todos os pacotes seguintes fluem diretamente para o cluster, ignorando o gateway do Banco de Dados SQL do Azure. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora do Azure

Se você estiver se conectando de fora do Azure, as conexões terão uma política de conexão de `Proxy` por padrão. Uma política de `Proxy` significa que a sessão TCP é estabelecida por meio do gateway do Banco de Dados SQL do Azure e todos os pacotes seguintes fluem por meio do gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços IP de gateway do Banco de Dados SQL do Azure

A tabela a seguir lista os endereços IP dos gateways por região. Para se conectar a um banco de dados SQL do Azure, você precisa permitir que o tráfego de rede & de **todos os** gateways para a região.

No futuro, adicionaremos mais gateways em cada região e desativaremos os gateways na coluna endereço IP do gateway descomissionado da tabela abaixo. Mais detalhes sobre o processo de encerramento fornecido no seguinte artigo: [Migração de tráfego do banco de dados SQL do Azure para gateways mais recentes](sql-database-gateway-migration.md)


| Nome da Região          | Endereço IP do gateway | Gateway encerrado </br> Endereço IP| Observações sobre descomissionamento | 
| --- | --- | --- | --- |
| Austrália Central    | 20.36.105.0 | | |
| Central2 da Austrália   | 20.36.113.0 | | |
| Leste da Austrália       | 13.75.149.87, 40.79.161.1 | | |
| Sudeste da Austrália | 191.239.192.109, 13.73.109.251 | | |
| Sul do Brasil         | 104.41.11.5        |                 | |
| Canadá Central       | 40.85.224.249      |                 | |
| Leste do Canadá          | 40.86.226.166      |                 | |
| EUA Central           | 13.67.215.62, 52.182.137.15 | 23.99.160.139 | Nenhuma conexão após 1º de setembro de 2019 |
| Leste da China           | 139.219.130.35     |                 | |
| Leste da China 2         | 40.73.82.1         |                 | |
| Norte da China          | 139.219.15.17      |                 | |
| Norte da China 2        | 40.73.50.0         |                 | |
| Ásia Oriental            | 191.234.2.139, 52.175.33.150 |       | |
| East US              | 40.121.158.30, 40.79.153.12 | 191.238.6.43 | Nenhuma conexão após 1º de setembro de 2019 |
| Leste dos EUA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0 | 191.239.224.107    | Nenhuma conexão após 1º de setembro de 2019 |
| Centro da França       | 40.79.137.0, 40.79.129.1 |           | |
| Alemanha Central      | 51.4.144.100       |                 | |
| Nordeste da Alemanha   | 51.5.144.179       |                 | |
| Índia Central        | 104.211.96.159     |                 | |
| Índia do Sul          | 104.211.224.146    |                 | |
| Índia Ocidental           | 104.211.160.80     |                 | |
| Leste do Japão           | 13.78.61.196, 40.79.184.8, 13.78.106.224 | 191.237.240.43 | Nenhuma conexão após 1º de setembro de 2019 |
| Oeste do Japão           | 104.214.148.156, 40.74.100.192 | 191.238.68.11 | Nenhuma conexão após 1º de setembro de 2019 |
| Coreia Central        | 52.231.32.42       |                 | |
| Sul da Coreia          | 52.231.200.86      |                 | |
| Centro-Norte dos EUA     | 23.96.178.199      | 23.98.55.75     | Nenhuma conexão após 1º de setembro de 2019 |
| Europa Setentrional         | 40.113.93.91       | 191.235.193.75  | Nenhuma conexão após 1º de setembro de 2019 |
| Norte da África do Sul   | 102.133.152.0      |                 | |
| Oeste da África do Sul    | 102.133.24.0       |                 | |
| Centro-Sul dos EUA     | 13.66.62.124       | 23.98.162.75    | Nenhuma conexão após 1º de setembro de 2019 |
| Sudeste Asiático      | 104.43.15.0        | 23.100.117.95   | Nenhuma conexão após 1º de setembro de 2019 |
| EAU Central          | 20.37.72.64        |                 | |
| Norte dos EAU            | 65.52.248.0        |                 | |
| Sul do Reino Unido             | 51.140.184.11      |                 | |
| Oeste do Reino Unido              | 51.141.8.11        |                 | |
| Centro-oeste dos EUA      | 13.78.145.25       |                 | |
| Europa Ocidental          | 40.68.37.158       | 191.237.232.75  | Nenhuma conexão após 1º de setembro de 2019 |
| Oeste dos EUA              | 104.42.238.205     | 23.99.34.75     | Nenhuma conexão após 1º de setembro de 2019 |
| Oeste dos EUA 2            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Alterar a política de conexão do Banco de Dados SQL do Azure

Para alterar a política de conexão de Banco de Dados SQL do Azure para um servidor do Banco de Dados SQL do Azure, use o comando [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Se a política de conexão for definida como `Proxy`, todos os pacotes de rede fluirão por meio do gateway do Banco de Dados SQL do Azure. Para essa configuração, você precisa permitir a saída para o IP de gateway do Banco de Dados SQL do Azure. O uso de uma configuração igual a `Proxy` resulta em uma latência maior do que uma configuração igual a `Redirect`.
- Se a política de conexão estiver definida como `Redirect`, todos os pacotes de rede fluirão diretamente para o cluster do banco de dados. Para essa configuração, você precisa permitir a saída para vários IPs.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para alterar as configurações de conexão via PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como alterar a política de conexão.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script para mudar as configurações de conexão pela CLI do Azure

> [!IMPORTANT]
> Este script requer a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash

> [!IMPORTANT]
> Este script requer a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O script da CLI a seguir mostra como alterar a política de conexão em um shell bash.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>CLI do Azure de um prompt de comando do Windows

> [!IMPORTANT]
> Este script requer a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O script da CLI a seguir mostra como alterar a política de conexão de um prompt de comando do Windows (com CLI do Azure instalado).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como alterar a política de conexão de Banco de Dados SQL do Azure para um servidor do Banco de Dados SQL do Azure, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Para obter mais informações sobre o comportamento de conexão do Banco de Dados SQL do Azure para clientes que usam ADO.NET 4.5 ou uma versão mais recente, consulte [Portas depois da 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para obter informações sobre a visão geral do desenvolvimento de aplicativos em geral, consulte [Visão Geral do Desenvolvimento de Aplicativos do Banco de Dados SQL](sql-database-develop-overview.md).

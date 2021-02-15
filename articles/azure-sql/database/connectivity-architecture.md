---
title: Arquitetura de Conectividade do Banco de Dados SQL do Azure
description: Este documento explica a arquitetura de conectividade do banco de dados SQL do Azure para conexões de banco de dados de dentro do Azure ou de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: cea561140d87333d35d223d062509c29febad5aa
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520571"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Banco de Dados SQL do Azure e arquitetura de conectividade do Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo explica a arquitetura de vários componentes que direcionam o tráfego de rede para um servidor no banco de dados SQL do Azure ou no Azure Synapse Analytics. Ele também explica políticas de conexão diferentes e como isso afeta os clientes que se conectam de dentro do Azure e os clientes que se conectam de fora do Azure.

> [!IMPORTANT]
> Este artigo *não* se aplica à **Instância Gerenciada de SQL do Azure**. Consulte [arquitetura de conectividade para uma instância gerenciada](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura de conectividade.

![Diagrama que mostra uma visão geral de alto nível da arquitetura de conectividade.](./media/connectivity-architecture/connectivity-overview.png)

As etapas a seguir descrevem como uma conexão é estabelecida com o banco de dados SQL do Azure:

- Os clientes se conectam ao gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de conexão efetiva, redireciona o tráfego ou usa um proxy para ele para o cluster de banco de dados correto.
- Dentro do cluster de banco de dados, o tráfego é encaminhado para o banco de dados apropriado.

## <a name="connection-policy"></a>Política de Conexão

Os servidores no banco de dados SQL e o Azure Synapse dão suporte às três opções a seguir para a configuração de política de conexão do servidor:

- **Redirecionar (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados, levando à latência reduzida e à taxa de transferência aprimorada. Para conexões usarem esse modo, os clientes precisam:
  - Permita a comunicação de saída do cliente para todos os endereços IP do Azure SQL na região em portas no intervalo de 11000 11999. Use as marcas de serviço do SQL para facilitar o gerenciamento.  
  - Permita a comunicação de saída do cliente para endereços IP do gateway do banco de dados SQL do Azure na porta 1433.

- **Proxy:** Nesse modo, todas as conexões são proxy por meio dos gateways de banco de dados SQL do Azure, levando a uma latência maior e taxa de transferência reduzida. Para conexões usarem esse modo, os clientes precisam permitir a comunicação de saída do cliente para endereços IP do gateway do banco de dados SQL do Azure na porta 1433.

- **Padrão:** Essa é a política de conexão em vigor em todos os servidores após a criação, a menos que você altere explicitamente a política de conexão para o `Proxy` ou o `Redirect` . A política padrão é `Redirect` para todas as conexões de cliente originadas dentro do Azure (por exemplo, de uma máquina virtual do Azure) e `Proxy` para todas as conexões de cliente que se originam fora (por exemplo, conexões de sua estação de trabalho local).

É altamente recomendável usar a política de conexão `Redirect` em relação à política de conexão `Proxy` para a menor latência e maior taxa de transferência. No entanto, será necessário atender aos requisitos adicionais para permitir o tráfego de rede, conforme descrito acima. Se o cliente for uma máquina virtual do Azure, você poderá fazer isso usando NSG (grupos de segurança de rede) com [marcas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags). Se o cliente estiver se conectando de uma estação de trabalho local, talvez seja necessário trabalhar com o administrador de rede para permitir o tráfego de rede por meio do firewall corporativo.

## <a name="connectivity-from-within-azure"></a>Conectividade de dentro do Azure

Se você estiver se conectando de dentro do Azure, as conexões terão uma política de conexão de `Redirect` por padrão. Uma política de `Redirect` significa que, depois que a sessão TCP é estabelecida com o banco de dados SQL do Azure, a sessão do cliente é redirecionada para o cluster de banco de dados correto com uma alteração no IP virtual de destino do gateway do banco de dados SQL do Azure para o do cluster. Depois disso, todos os pacotes seguintes fluem diretamente para o cluster, ignorando o gateway do Banco de Dados SQL do Azure. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora do Azure

Se você estiver se conectando de fora do Azure, as conexões terão uma política de conexão de `Proxy` por padrão. Uma política de `Proxy` significa que a sessão TCP é estabelecida por meio do gateway do Banco de Dados SQL do Azure e todos os pacotes seguintes fluem por meio do gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![Diagrama que mostra como a sessão TCP é estabelecida por meio do gateway do banco de dados SQL do Azure e todos os pacotes subsequentes fluem por meio do gateway.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Além disso, abra as portas TCP 1434 e 14000-14999 para habilitar a [conexão com o DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Endereços IP do gateway

A tabela a seguir lista os endereços IP dos gateways por região. Para se conectar ao banco de dados SQL ou ao Azure Synapse, você precisa permitir o tráfego de rede de e para **todos** os gateways para a região.

Os detalhes de como o tráfego deve ser migrado para novos gateways em regiões específicas estão no seguinte artigo: [migração de tráfego do banco de dados SQL do Azure para gateways mais recentes](gateway-migration.md)

| Nome da região          | Endereços IP do gateway |
| --- | --- |
| Austrália Central    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Austrália Central 2   | 20.36.113.0, 20.36.112.6 |
| Leste da Austrália       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Sudeste da Austrália | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| Brazil South         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Canadá Central       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Leste do Canadá          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| Centro dos EUA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Leste da China           | 139.219.130.35     |
| Leste da China 2         | 40.73.82.1         |
| Norte da China          | 139.219.15.17      |
| Norte da China 2        | 40.73.50.0         |
| Leste da Ásia            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| Leste dos EUA              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| Leste dos EUA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| França Central       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Sul da França         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Alemanha Central      | 51.4.144.100       |
| Nordeste da Alemanha   | 51.5.144.179       |
| Centro-Oeste da Alemanha | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Centro da Índia        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| Sul da Índia          | 104.211.224.146    |
| Oeste da Índia           | 104.211.160.80, 104.211.144.4 |
| Japan East           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5 |
| Oeste do Japão           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Coreia Central        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Sul da Coreia          | 52.231.200.86      |
| Centro-Norte dos EUA     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Norte da Europa         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Leste da Noruega          | 51.120.96.0        |
| Oeste da Noruega          | 51.120.216.0       |
| Norte da África do Sul   | 102.133.152.0, 102.133.120.2       |
| Oeste da África do Sul    | 102.133.24.0       |
| Centro-Sul dos Estados Unidos     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Sudeste da Ásia      | 104.43.15.0, 40.78.232.3   |
| Norte da Suíça    | 51.107.56.0, 51.107.57.0 |
| Oeste da Suíça     | 51.107.152.0, 51.107.153.0 |
| EAU Central          | 20.37.72.64        |
| Norte dos EAU            | 65.52.248.0        |
| Sul do Reino Unido             | 51.140.184.11, 51.105.64.0 |
| Oeste do Reino Unido              | 51.141.8.11        |
| Centro-Oeste dos EUA      | 13.78.145.25, 13.78.248.43        |
| Europa Ocidental          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| Oeste dos EUA              | 104.42.238.205, 13.86.216.196   |
| Oeste dos EUA 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como alterar a política de conexão do banco de dados SQL do Azure para um servidor, consulte [Conn-Policy](/cli/azure/sql/server/conn-policy).
- Para obter mais informações sobre o comportamento de conexão do Banco de Dados SQL do Azure para clientes que usam ADO.NET 4.5 ou uma versão mais recente, consulte [Portas depois da 1433 para ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Para obter informações sobre a visão geral do desenvolvimento de aplicativos em geral, consulte [Visão Geral do Desenvolvimento de Aplicativos do Banco de Dados SQL](develop-overview.md).

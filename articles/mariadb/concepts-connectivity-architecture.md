---
title: Arquitetura de conectividade no banco de dados do Azure para MariaDB
description: Descreve a arquitetura de conectividade do banco de dados do Azure para servidor MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: d49e4dff1664d6630c966583a722f8e136061de5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595270"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Arquitetura de conectividade no banco de dados do Azure para MariaDB
Este artigo explica o banco de dados do Azure para MariaDB, arquitetura de conectividade, bem como o tráfego é direcionado para seu banco de dados do Azure para MariaDB instância dos clientes, tanto dentro quanto fora do Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

Conexão ao banco de dados do Azure para MariaDB é estabelecida por meio de um gateway que é responsável pelo roteamentos conexões de entrada para o local físico do seu servidor na nossa clusters. O diagrama a seguir ilustra o fluxo de tráfego.

![Visão geral da arquitetura de conectividade](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Como o cliente se conectar ao banco de dados, elas obtém uma cadeia de caracteres de conexão que se conecta ao gateway. Este gateway tem um endereço IP público que escuta a porta 3306. Dentro do cluster de banco de dados, o tráfego é encaminhado para o banco de dados apropriado do Azure para MariaDB. Portanto, para se conectar ao seu servidor, como de redes corporativas, é necessário abrir o firewall do lado do cliente para permitir o tráfego de saída para ser capaz de alcançar nossos gateways. Abaixo você encontrará uma lista completa de endereços IP usados por nossos gateways por região.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Banco de dados do Azure para MariaDB endereços IP de gateway

A tabela a seguir lista os IPs primários e secundários do banco de dados do Azure para MariaDB gateway para todas as regiões de dados. O endereço IP primário é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP de failover no caso de falha do primário. Conforme mencionado, os clientes devem permitir a saída para os endereços IP. O segundo endereço IP não escutará todos os serviços até que ele seja ativado pelo banco de dados do Azure para MariaDB aceitar conexões.

| **Nome da região** | **Endereço IP primário** | **Endereço IP secundário** |
|:----------------|:-------------|:------------------------|
| Leste da Austrália | 13.75.149.87 | 40.79.161.1 |
| Sudeste da Austrália | 191.239.192.109 | 13.73.109.251 |
| Sul do Brasil | 104.41.11.5 | |
| Canadá Central | 40.85.224.249 | |
| Leste do Canadá | 40.86.226.166 | |
| Centro dos EUA | 23.99.160.139 | 13.67.215.62 |
| Leste da China 1 | 139.219.130.35 | |
| Leste da China 2 | 40.73.82.1 | |
| Norte da China 1 | 139.219.15.17 | |
| Norte da China 2 | 40.73.50.0 | |
| Ásia Oriental | 191.234.2.139 | 52.175.33.150 |
| Leste dos EUA 1 | 191.238.6.43 | 40.121.158.30 |
| Leste dos EUA 2 | 191.239.224.107 | 40.79.84.180 * |
| França Central | 40.79.137.0 | 40.79.129.1 |
| Alemanha Central | 51.4.144.100 | |
| Centro da Índia | 104.211.96.159 | |
| Sul da Índia | 104.211.224.146 | |
| Oeste da Índia | 104.211.160.80 | |
| Leste do Japão | 191.237.240.43 | 13.78.61.196 |
| Oeste do Japão | 191.238.68.11 | 104.214.148.156 |
| Coreia Central | 52.231.32.42 | |
| Sul da Coreia | 52.231.200.86 |  |
| Centro-Norte dos EUA | 23.98.55.75 | 23.96.178.199 |
| Norte da Europa | 191.235.193.75 | 40.113.93.91 |
| Centro-Sul dos Estados Unidos | 23.98.162.75 | 13.66.62.124 |
| Sudeste da Ásia | 23.100.117.95 | 104.43.15.0 |
| Sul do Reino Unido | 51.140.184.11 | |
| Oeste do Reino Unido | 51.141.8.11| |
| Europa Ocidental | 191.237.232.75 | 40.68.37.158 |
| Oeste dos EUA 1 | 23.99.34.75 | 104.42.238.205 |
| Oeste dos EUA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> O *Leste dos EUA 2* também tem um endereço IP terciário igual a `52.167.104.0`.

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Azure](./howto-manage-firewall-portal.md)
* [Criar e gerenciar o banco de dados do Azure para MariaDB as regras de firewall usando a CLI do Azure](./howto-manage-firewall-cli.md)

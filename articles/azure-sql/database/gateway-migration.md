---
title: Aviso de migração de tráfego de gateway
description: O artigo fornece um aviso aos usuários sobre a migração de endereços IP do gateway do banco de dados SQL do Azure
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 8fa61855b5647c1caefd939af17b70c9d08c2f8f
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517817"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego do banco de dados SQL do Azure para gateways mais recentes
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

À medida que a infraestrutura do Azure melhora, a Microsoft atualizará periodicamente o hardware para garantir que forneceremos a melhor experiência possível para o cliente. Nos próximos meses, planejamos adicionar gateways criados em gerações de hardware mais recentes, migrar o tráfego para eles e eventualmente descomissionar os gateways criados em um hardware mais antigo em algumas regiões.  

Os clientes serão notificados por meio de notificações de integridade do serviço com antecedência sobre qualquer alteração nos gateways disponíveis em cada região. Os clientes podem [usar o portal do Azure para configurar alertas do log de atividades](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications-portal).

As informações mais atualizadas serão mantidas na tabela [endereços IP do gateway do banco de dados SQL do Azure](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Atualizações de status

# <a name="in-progress"></a>[Em Andamento](#tab/in-progress-ip)
## <a name="march-2021"></a>Março de 2021
Os seguintes gateways SQL em várias regiões estão no processo de serem desativados:

- Sul do Brasil: 104.41.11.5
- Ásia Oriental: 191.234.2.139
- Leste dos EUA: 191.238.6.43
- Leste do Japão: 191.237.240.43
- Oeste do Japão: 191.238.68.11
- Europa Setentrional: 191.235.193.75
- EUA Central do Sul: 23.98.162.75
- Sudeste da Ásia: 23.100.117.95
- Europa Ocidental: 191.237.232.75
- Oeste dos EUA: 23.99.34.75

Nenhum impacto do cliente é previsto, pois esses gateways (em execução em hardware mais antigo) não estão roteando nenhum tráfego do cliente. Os endereços IP para esses gateways devem ser desativados em 15 de março de 2021.

## <a name="february-2021"></a>Fevereiro de 2021
Novos gateways do SQL estão sendo adicionados às seguintes regiões:

- EUA Central: 13.89.169.20

Esses gateways do SQL devem começar a aceitar o tráfego do cliente em 28 de fevereiro de 2021.

## <a name="january-2021"></a>Janeiro de 2021
Novos gateways do SQL estão sendo adicionados às seguintes regiões:

- Austrália Central: 20.36.104.6, 20.36.104.7 
- Austrália Central 2:20.36.112.6 
- Sul do Brasil: 191.234.144.16, 191.234.152.3 
- Leste do Canadá: 40.69.105.9, 40.69.105.10
- Índia central: 104.211.86.30, 104.211.86.31 
- Ásia Oriental: 13.75.32.14 
- França central: 40.79.137.8, 40.79.145.12 
- Sul da França: 40.79.177.10, 40.79.177.12
- Coreia central: 52.231.17.22, 52.231.17.23
- Índia ocidental: 104.211.144.4

Esses gateways do SQL devem começar a aceitar o tráfego do cliente em 31 de janeiro de 2021.

# <a name="completed"></a>[Concluído](#tab/completed-ip)
As seguintes migrações de gateway estão concluídas: 

### <a name="october-2020"></a>Outubro de 2020

Novos gateways do SQL estão sendo adicionados às seguintes regiões:

- Centro-oeste da Alemanha: 51.116.240.0, 51.116.248.0

Esses gateways do SQL devem começar a aceitar o tráfego do cliente em 12 de outubro de 2020. 

### <a name="september-2020"></a>Setembro de 2020
Novos gateways do SQL estão sendo adicionados às regiões a seguir. Esses gateways do SQL devem começar a aceitar o tráfego do cliente em **15 de setembro de 2020**:

- Sudeste da Austrália: 13.77.48.10
- Leste do Canadá: 40.86.226.166, 52.242.30.154
- Sul do Reino Unido: 51.140.184.11, 51.105.64.0

Os gateways SQL existentes começarão a aceitar o tráfego nas regiões a seguir. Esses gateways do SQL devem começar a aceitar o tráfego do cliente em **15 de setembro de 2020** :

- Sudeste da Austrália: 191.239.192.109 e 13.73.109.251
- EUA Central: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 e 104.208.21.1
- Ásia Oriental: 191.234.2.139, 52.175.33.150 e 13.75.32.4
- Leste dos EUA: 40.121.158.30, 40.79.153.12, 191.238.6.43 e 40.78.225.32
- Leste dos EUA 2:40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 e 104.208.150.3
- França central: 40.79.137.0 e 40.79.129.1
- Oeste do Japão: 104.214.148.156, 40.74.100.192, 191.238.68.11 e 40.74.97.10
- EUA Central Norte: 23.96.178.199, 23.98.55.75 e 52.162.104.33
- Sudeste Asiático: 104.43.15.0, 23.100.117.95 e 40.78.232.3
- Oeste dos EUA: 104.42.238.205, 23.99.34.75 e 13.86.216.196

Novos gateways do SQL estão sendo adicionados às regiões a seguir. Esses gateways do SQL devem começar a aceitar o tráfego do cliente em **10 de setembro de 2020**:

- EUA Central ocidental: 13.78.248.43 
- Norte da África do Sul: 102.133.120.2  

Novos gateways do SQL estão sendo adicionados às regiões a seguir. Esses gateways do SQL devem começar a aceitar o tráfego do cliente em **1 de setembro de 2020**:

- Europa Setentrional: 13.74.104.113 
- DOS EUA 2 ocidental: 40.78.248.10 
- Europa Ocidental: 52.236.184.163 
- EUA Central do Sul: 20.45.121.1, 20.49.88.1 

Os gateways SQL existentes começarão a aceitar o tráfego nas regiões a seguir. Esses gateways do SQL devem começar a aceitar o tráfego do cliente em **1 de setembro de 2020**:
- Leste do Japão: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Agosto de 2020

Novos gateways do SQL estão sendo adicionados às seguintes regiões:

- Leste da Austrália: 13.70.112.9
- Centro do Canadá: 52.246.152.0, 20.38.144.1 
- Oeste dos EUA 2:40.78.240.8

Esses gateways do SQL devem começar a aceitar o tráfego do cliente em 10 de agosto de 2020. 

### <a name="october-2019"></a>Outubro de 2019
- Brazil South
- Oeste dos EUA
- Europa Ocidental
- Leste dos EUA
- Centro dos EUA
- Sudeste da Ásia
- Centro-Sul dos Estados Unidos
- Norte da Europa
- Centro-Norte dos EUA
- Oeste do Japão
- Japan East
- Leste dos EUA 2
- Leste da Ásia

---

## <a name="impact-of-this-change"></a>Impacto dessa alteração

A migração de tráfego pode alterar o endereço IP público que o DNS resolve para seu banco de dados no banco de dados SQL do Azure.
Você poderá ser afetado se:

- O endereço IP embutido em código para qualquer gateway específico em seu firewall local
- Ter qualquer sub-rede usando Microsoft. SQL como um ponto de extremidade de serviço, mas não pode se comunicar com os endereços IP do gateway
- Usar a [configuração com redundância de zona para a camada de uso geral](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Usar a [configuração com redundância de zona para as camadas premium & Business Critical](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Você não será afetado se tiver:
 
- Redirecionamento como a política de conexão
- Conexões com o banco de dados SQL de dentro do Azure e usando marcas de serviço
- As conexões feitas usando as versões com suporte do driver JDBC para SQL Server não terão impacto. Para obter suporte para versões JDBC, consulte [baixar o Microsoft JDBC Driver para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se você for afetado

Recomendamos que você permita o tráfego de saída para endereços IP para todos os [endereços IP de gateway](connectivity-architecture.md#gateway-ip-addresses) na região na porta TCP 1433 e o intervalo de porta 11000-11999. Essa recomendação é aplicável aos clientes que se conectam do local e também aos que se conectam por meio de pontos de extremidade de serviço. Para obter mais informações sobre intervalos de portas, consulte [política de conexão](connectivity-architecture.md#connection-policy).

As conexões feitas de aplicativos usando o Microsoft JDBC Driver abaixo da versão 4,0 podem falhar na validação do certificado. Versões inferiores do Microsoft JDBC dependem do nome comum (CN) no campo assunto do certificado. A mitigação é garantir que a propriedade hostNameInCertificate esteja definida como *. database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [conectando-se com criptografia](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a atenuação acima não funcionar, entre em arquivo uma solicitação de suporte para o banco de dados SQL ou o SQL Instância Gerenciada usando a seguinte URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [arquitetura de conectividade do SQL do Azure](connectivity-architecture.md)

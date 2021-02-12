---
title: Arquitetura de conectividade-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve a arquitetura de conectividade do banco de dados do Azure para PostgreSQL-servidor único.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 23847c164ba59a8c46c2fdd5fb954b76ea251148
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98877672"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Arquitetura de conectividade no banco de dados do Azure para PostgreSQL
Este artigo explica a arquitetura de conectividade do banco de dados do Azure para PostgreSQL e também como o tráfego é direcionado para a instância do banco de dados do Azure para PostgreSQL de clientes dentro e fora do Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade
A conexão com o banco de dados do Azure para PostgreSQL é estabelecida por meio de um gateway que é responsável por rotear conexões de entrada para o local físico do servidor em nossos clusters. O diagrama a seguir ilustra o fluxo de tráfego.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Visão geral da arquitetura de conectividade":::


À medida que o cliente se conecta ao banco de dados, a cadeia de conexão para o servidor é resolvida para o endereço IP do gateway. O gateway escuta no endereço IP na porta 5432. Dentro do cluster de banco de dados, o tráfego é encaminhado para o banco de dados do Azure apropriado para PostgreSQL. Portanto, para se conectar ao servidor, como de redes corporativas, é necessário abrir o **Firewall do lado do cliente para permitir que o tráfego de saída seja capaz de acessar nossos gateways**. Abaixo, você pode encontrar uma lista completa dos endereços IP usados por nossos gateways por região.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Banco de dados do Azure para os endereços IP do gateway PostgreSQL

O serviço de gateway é hospedado em um grupo de nós de computação sem monitoração de estado situadas atrás de um endereço IP, o qual o cliente primeiro alcançaria ao tentar se conectar a um banco de dados do Azure para o servidor PostgreSQL. 

Como parte da manutenção contínua do serviço, atualizaremos periodicamente o hardware de computação que hospeda os gateways para garantir que forneceremos a experiência de conectividade mais segura e de alto desempenho. Quando o hardware de gateway é atualizado, um novo anel dos nós de computação é criado primeiro. Esse novo anel serve o tráfego para todos os servidores do banco de dados do Azure recém-criados para PostgreSQL e ele terá um endereço IP diferente dos anéis de gateway mais antigos na mesma região para diferenciar o tráfego. O hardware de gateway mais antigo continua atendendo a servidores existentes, mas está planejado para encerramento no futuro. Antes de encerrar um hardware de gateway, os clientes que executam seus servidores e se conectarem a anéis de gateway mais antigos serão notificados por email e na portal do Azure, três meses de antecedência antes do encerramento. O encerramento de gateways pode afetar a conectividade com seus servidores se 

* Você codifica os endereços IP do gateway na cadeia de conexão do seu aplicativo. Não é **recomendável**. Você deve usar o FQDN (nome de domínio totalmente qualificado) do servidor no formato <servername> . Postgres.Database.Azure.com, na cadeia de conexão do seu aplicativo. 
* Você não atualiza os endereços IP de gateway mais recentes no firewall do lado do cliente para permitir que o tráfego de saída seja capaz de alcançar nossos novos anéis de gateway.

A tabela a seguir lista os endereços IP do gateway do banco de dados do Azure para o gateway PostgreSQL para todas as regiões de data. As informações mais atualizadas dos endereços IP do gateway para cada região são mantidas na tabela a seguir. Na tabela abaixo, as colunas representam as seguintes:

* **Endereços IP do gateway:** Esta coluna lista os endereços IP atuais dos gateways hospedados na última geração de hardware. Se você estiver provisionando um novo servidor, recomendamos que você abra o Firewall do lado do cliente para permitir o tráfego de saída para os endereços IP listados nesta coluna.
* **Endereços IP do gateway (descomissionamento):** Esta coluna lista os endereços IP dos gateways hospedados em uma geração mais antiga de hardware que está sendo encerrado no momento. Se você estiver provisionando um novo servidor, poderá ignorar esses endereços IP. Se você tiver um servidor existente, continue a reter a regra de saída do firewall para esses endereços IP, pois ainda não o descomissionamos. Se você descartar as regras de firewall para esses endereços IP, poderá obter erros de conectividade. Em vez disso, espera-se que você adicione proativamente os novos endereços IP listados na coluna endereços IP do gateway à regra de firewall de saída assim que receber a notificação de encerramento. Isso garantirá quando o servidor for migrado para o hardware de gateway mais recente, não haverá interrupções na conectividade com o servidor.
* **Endereços IP do gateway (encerrado):** Essas colunas listam os endereços IP dos anéis de gateway, que são encerrados e não estão mais em operações. Você pode remover com segurança esses endereços IP da regra de firewall de saída. 


| **Nome da região** | **Endereços IP do gateway** |**Endereços IP do gateway (encerramento)** | **Endereços IP do gateway (encerrado)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Austrália Central| 20.36.105.0  | | |
| Central2 da Austrália     | 20.36.113.0  | | |
| Leste da Austrália | 13.75.149.87, 40.79.161.1     |  | |
| Sudeste da Austrália |191.239.192.109, 13.73.109.251   |  | |
| Brazil South |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Canadá Central |40.85.224.249  | | |
| Leste do Canadá | 40.86.226.166    | | |
| Centro dos EUA | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38 | | |
| Leste da China | 139.219.130.35    | | |
| Leste da China 2 | 40.73.82.1  | | |
| Norte da China | 139.219.15.17    | | |
| Norte da China 2 | 40.73.50.0     | | |
| Leste da Ásia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| Leste dos EUA |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| Leste dos EUA 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  | | |
| França Central | 40.79.137.0, 40.79.129.1  | | |
| Sul da França | 40.79.177.0     | | |
| Alemanha Central | 51.4.144.100     | | |
| Nordeste da Alemanha | 51.5.144.179  | | |
| Centro da Índia | 104.211.96.159     | | |
| Sul da Índia | 104.211.224.146  | | |
| Oeste da Índia | 104.211.160.80    | | |
| Japan East | 13.78.61.196, 191.237.240.43, 40.79.192.23 | | |
| Oeste do Japão | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    | | |
| Coreia Central | 52.231.32.42   | | |
| Sul da Coreia | 52.231.200.86    | | |
| Centro-Norte dos EUA | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    | | |
| Norte da Europa | 52.138.224.6, 52.138.224.7  |40.113.93.91 |191.235.193.75 |
| Norte da África do Sul  | 102.133.152.0    | | |
| Oeste da África do Sul | 102.133.24.0   | | |
| Centro-Sul dos Estados Unidos |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Sudeste da Ásia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     | | |
| EAU Central | 20.37.72.64  | | |
| Norte dos EAU | 65.52.248.0    | | |
| Sul do Reino Unido | 51.140.184.11   | | |
| Oeste do Reino Unido | 51.141.8.11  | | |
| Centro-Oeste dos EUA | 13.78.145.25     | | |
| Europa Ocidental |13.69.105.208,104.40.169.187 |40.68.37.158 | 191.237.232.75|
| Oeste dos EUA |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Oeste dos EUA 2 | 13.66.226.202  | | |
||||

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](./howto-manage-firewall-using-cli.md)

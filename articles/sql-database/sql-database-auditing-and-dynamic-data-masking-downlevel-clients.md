---
title: Auditoria de tabela, redirecionamento TDS e pontos de extremidade IP para Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre alterações de auditoria, redirecionamento TDS e ponto de extremidade IP ao implementar a auditoria de tabela no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: 2c95ec4d88e55af0becc73719bcc6126501267db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416519"
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>Banco de Dados SQL – Suporte a clientes de versão anterior e alterações de ponto de extremidade IP para Auditoria de tabela

> [!IMPORTANT]
> Este documento se aplica somente à Auditoria de Tabela, que **agora foi preterida**.<br>
> Use o novo método de [Auditoria de Blob](sql-database-auditing.md), que **não** exige modificações de cadeia de conexão de cliente de nível inferior. Informações adicionais sobre a Auditoria de Blob podem ser encontradas em [Introdução à auditoria de banco de dados SQL](sql-database-auditing.md).

A [Auditoria de Banco de Dados](sql-database-auditing.md) funciona automaticamente com clientes SQL que dão suporte ao redirecionamento de TDS. Observe que o redirecionamento não se aplica ao usar o método Auditoria de Blob.

## <a id="subheading-1"></a>Suporte a clientes de versão anterior

Qualquer cliente que implemente o protocolo TDS 7.4 também deve dar suporte a redirecionamento. As exceções incluem o JDBC 4.0, no qual o recurso de redirecionamento não tem suporte completo, e o Tedious para Node.JS, no qual o redirecionamento não foi implementado.

Para "clientes de versão anterior", ou seja, que oferecem suporte ao TDS versão 7.3 e inferior - o FQDN do servidor na cadeia de conexão deve ser modificado:

FQDN original do servidor na cadeia de conexão: <*nome do servidor*>.database.windows.net

FQDN do servidor modificado na cadeia de conexão: <*nome do servidor*>.database.**secure**.windows.net

Uma lista parcial de "Clientes de versão anterior" inclui:

* .NET 4.0 e inferior,
* ODBC 10.0 e inferior.
* JDBC (embora o JDBC dê suporte ao TDS 7.4, o recurso de redirecionamento de TDS não recebe suporte total)
* Tedious (para o Node.JS)

**Comentário:** a modificação do FQDN do servidor acima pode ser útil também para aplicar uma política de Auditoria no Nível do SQL Server sem a necessidade de uma etapa de configuração em cada banco de dados (redução temporária).

## <a id="subheading-2"></a>Alterações de ponto de extremidade IP ao habilitar a Auditoria

Observe que, quando você habilita a Auditoria de Tabela, o ponto de extremidade IP do seu banco de dados é alterado. Se você tiver configurações de firewall estritas, atualize as configurações de firewall adequadamente.

O novo ponto de extremidade IP do banco de dados dependerá da região de banco de dados:

| Região do Banco de Dados | Possíveis de extremidade IP possíveis |
| --- | --- |
| Norte da China |139.217.29.176, 139.217.28.254 |
| Leste da China |42.159.245.65, 42.159.246.245 |
| Leste da Austrália |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Sudeste da Austrália |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Sul do Brasil |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Centro dos EUA |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| EUA Central EUAP |52.180.178.16, 52.180.176.190 |
| Ásia Oriental |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Leste dos EUA 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Leste dos EUA |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Leste dos EUA EUAP |52.225.190.86, 52.225.191.187 |
| Índia Central |104.211.98.219, 104.211.103.71 |
| Sul da Índia |104.211.227.102, 104.211.225.157 |
| Índia Ocidental |104.211.161.152, 104.211.162.21 |
| Leste do Japão |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Oeste do Japão |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Centro-Norte dos EUA |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Norte da Europa |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Centro-Sul dos Estados Unidos |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sudeste Asiático |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa Ocidental |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Oeste dos EUA |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Oeste dos EUA 2 |13.66.224.156, 13.66.227.8 |
| Centro-Oeste dos EUA |52.161.29.186, 52.161.27.213 |
| Canadá Central |13.88.248.106, 13.88.248.110 |
| Leste do Canadá |40.86.227.82, 40.86.225.194 |
| Sul do Reino Unido |13.87.32.202, 13.87.32.226 |

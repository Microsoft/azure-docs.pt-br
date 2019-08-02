---
title: Artigo sobre conhecidos problemas/erros associados ao conectar-se o serviço de migração de banco de dados do Azure para bancos de dados de origem de solução de problemas | Microsoft Docs
description: Saiba mais sobre como solucionar problemas/erros conhecidos associados com a conexão de serviço de migração de banco de dados do Azure para bancos de dados de origem.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462844"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Solucionar problemas de erros DMS ao se conectar a bancos de dados de origem

O artigo a seguir fornece detalhes sobre como resolver problemas potenciais, você pode encontrar ao conectar-se o serviço de migração de banco de dados do Azure (DMS) para seu banco de dados de origem. As seções a seguir está relacionado a um tipo específico de banco de dados de origem, listando o erro pode ocorrer juntamente com detalhes e links para informações sobre como solucionar problemas de conectividade.

## <a name="sql-server"></a>SQL Server

Problemas potenciais associados com a conexão a um banco de dados do SQL Server de origem e como resolvê-los são fornecidos na tabela a seguir.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| Falha na conexão de SQL. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verificar se o nome da instância está correto e que o SQL Server está configurado para permitir conexões remotas.<br> | Esse erro ocorre se o serviço não puder localizar o servidor de origem. Para resolver o problema, consulte o artigo [erro ao conectar-se ao SQL Server de origem ao usar a porta dinâmica ou instância nomeada](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Erro 53** -falhado de conexão de SQL. (Além disso, para o erro de códigos de 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Esse erro ocorre se o serviço não pode se conectar ao servidor de origem. Para resolver o problema, consulte os seguintes recursos e, em seguida, tente novamente. <br><br>  [Guia do usuário interativo para solucionar o problema de conectividade](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Pré-requisitos para migração do SQL Server para o banco de dados SQL](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Pré-requisitos para migração do SQL Server para uma instância gerenciada do banco de dados SQL](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Erro 18456** -falha no logon.<br> | Esse erro ocorre se o serviço não pode se conectar à fonte de dados usando as credenciais fornecidas do T-SQL. Para resolver o problema, verifique se inseriu as credenciais. Você também pode consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou os documentos de solução de problemas listadas na observação abaixo dele, tabela e, em seguida, tente novamente. |
| Valor do AccountName malformado '{0}' fornecido. Formato esperado para AccountName é NomeDeDomínio \ nomedeusuário<br> | Esse erro ocorre se o usuário seleciona a autenticação do Windows, mas fornece o nome de usuário em um formato inválido. Para resolver o problema, forneça o nome de usuário no formato correto para autenticação do Windows ou selecione **autenticação do SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Problemas potenciais associados com a conexão a uma origem de banco de dados MySQL do AWS RDS e como resolvê-los são fornecidos na tabela a seguir.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| **Erro [2003]** [HY000] - falhado de conexão. Erro [HY000] [MySQL] [ODBC driver do x.x(w)] não pode se conectar ao servidor MySQL em '{server}' (10060) | Esse erro ocorre se o driver ODBC do MySQL não pode se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [2005]** [HY000] - falhado de conexão. Erro [HY000] host do servidor MySQL de desconhecido [MySQL] [ODBC driver do x.x(w)] '{server}' | Esse erro ocorrerá se o serviço não é possível localizar o host de origem no RDS. O problema pode ser porque a origem listado não existe ou há um problema com a infraestrutura RDS. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [1045]** [HY000] - falhado de conexão. Erro [HY000] acesso de [MySQL] [ODBC driver do x.x(w)] negado para o usuário '{user}'@'{server}' (usando a senha: SIM) | Esse erro ocorre se o driver ODBC do MySQL não pode se conectar ao servidor de origem devido a credenciais inválidas. Verifique se as credenciais que você inseriu. Se o problema persistir, verifique se que esse computador de origem tiver as credenciais corretas. Talvez você precise redefinir a senha no console do. Se você ainda pode encontrar o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [9002]** [HY000] - falhado de conexão. Erro [HY000] [MySQL] [ODBC driver do x.x(w)] a cadeia de caracteres de conexão pode não ser adequada. Visite o portal para referências.| Esse erro ocorre se a conexão estiver falhando devido a um problema com a cadeia de caracteres de conexão. Verifique se a cadeia de conexão fornecida é válida. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro no log binário. A variável binlog_format tem o valor '{value}'. Altere-a para 'row'.** | Esse erro ocorre se houver um erro no log binário; a variável binlog_format tem o valor errado. Para resolver o problema, altere o binlog_format no grupo de parâmetros para 'Linha' e, em seguida, reinicialize a instância. Para obter mais informações, consulte [opções de registro em log binário e as variáveis](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) ou [documentação de arquivos de Log do banco de dados do AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Para obter mais informações sobre como solucionar problemas relacionados à conexão com uma origem de banco de dados MySQL do AWS RDS, consulte os seguintes recursos:
> * [Solução de problemas para problemas de conectividade de RDS Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como resolvo problemas para se conectar à minha instância de banco de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Problemas potenciais associados com a conexão a uma origem de banco de dados PostgreSQL de RDS do AWS e como resolvê-los são fornecidos na tabela a seguir.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| **Erro [101]** [08001] - falhado de conexão. Tempo limite de erro [08001] expirou. | Esse erro ocorre se o driver de Postgres não pode se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tente novamente. |
| **Erro: Parâmetro wal_level tem o valor '{value}'. Altere-a para 'logical' para permitir a replicação.** | Esse erro ocorre se o parâmetro wal_level tem o valor incorreto. Para resolver o problema, altere o rds.logical_replication no grupo de parâmetros para 1 e, em seguida, reinicialize a instância. Para obter mais informações, consulte [pré-requisitos para migrar para o PostgreSQL do Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) ou [PostgreSQL no Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Para obter mais informações sobre como solucionar problemas relacionados à conexão com uma origem de banco de dados PostgreSQL de RDS do AWS, consulte os seguintes recursos:
> * [Solução de problemas para problemas de conectividade de RDS Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como resolvo problemas para se conectar à minha instância de banco de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Problemas potenciais associados com a conexão a um banco de dados do SQL Server do AWS RDS de origem e como resolvê-los são fornecidos na tabela a seguir.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| **Erro 53** -falhado de conexão de SQL. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verificar se o nome da instância está correto e que o SQL Server está configurado para permitir conexões remotas. (provedor: Provedor de Pipes nomeados, erro: 40 - pode não abrir uma conexão com o SQL Server | Esse erro ocorre se o serviço não pode se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tente novamente. |
| **Erro 18456** -falha no logon. Falha no logon do usuário '{user}' | Esse erro ocorre se o serviço não pode se conectar à fonte de dados com as credenciais de T-SQL fornecidas. Para resolver o problema, verifique se inseriu as credenciais. Você também pode consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou os documentos de solução de problemas listadas na observação abaixo dele, tabela e tente novamente. |
| **Erro 87** -cadeia de caracteres de Conexão não é válido. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verificar se o nome da instância está correto e que o SQL Server está configurado para permitir conexões remotas. (provedor: Interfaces de rede do SQL, erro: 25 - cadeia de caracteres de Conexão não é válido) | Esse erro ocorre se o serviço não pode se conectar ao servidor de origem devido a uma cadeia de caracteres de conexão inválido. Para resolver o problema, verifique se a cadeia de conexão fornecida. Se o problema persistir, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tente novamente. |
| **Erro: o certificado do servidor não confiável.** Uma conexão foi estabelecida com êxito com o servidor, mas ocorreu um erro durante o processo de logon. (provedor: Provedor de SSL, erro: 0 - a cadeia de certificado foi emitida por uma autoridade que não é confiável.) | Esse erro ocorre se o certificado usado não é confiável. Para resolver o problema, você precisa localizar um certificado que pode ser confiável e, em seguida, habilitá-lo no servidor. Como alternativa, você pode selecionar a opção de confiar em certificado durante a conexão. Execute esta ação apenas se você estiver familiarizado com o certificado usado e você confia nele. <br> Conexões de SSL que são criptografadas usando um certificado autoassinado não fornecem alta segurança--são suscetíveis a ataques man-in-the-middle. Não confie em SSL usando certificados autoassinados em um ambiente de produção ou em servidores que estão conectados à internet. <br> Para obter mais informações, consulte [usando o SSL com uma instância de banco de dados do Microsoft SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) ou [Tutorial: Migrar RDS SQL Server para o Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Erro 300** -usuário não tem as permissões necessárias. Permissão VIEW SERVER STATE foi negada no objeto '{server}', banco de dados ' {}' | Esse erro ocorre se o usuário não tem permissão para executar a migração. Para resolver o problema, consulte [CONCEDER permissões de servidor - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) ou [Tutorial: Migrar RDS SQL Server para o Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) para obter mais detalhes. |

> [!NOTE]
> Para obter mais informações sobre como solucionar problemas relacionados à conexão a uma fonte de AWS RDS SQL Server, consulte os seguintes recursos:
>
> * [Resolver erros de conectividade do SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Como resolvo problemas para se conectar à minha instância de banco de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemas conhecidos

* [Limitações de problemas conhecidos/migração com migrações on-line para o banco de dados SQL](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Limitações de problemas conhecidos/migração com migrações on-line para o banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Limitações de problemas conhecidos/migração com migrações on-line para o banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Próximas etapas

* Ler o artigo [PowerShell de serviço de migração de banco de dados do Azure](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Ler o artigo [como configurar parâmetros do servidor no banco de dados do Azure para MySQL usando o portal do Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Ler o artigo [visão geral de pré-requisitos para usar o serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte a [perguntas Frequentes sobre como usar o serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/faq).

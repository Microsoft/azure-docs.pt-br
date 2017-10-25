---
title: "Resolvendo diferenças do T-SQL durante migração para o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Instruções Transact-SQL que têm suporte menor que o total pelo Banco de Dados SQL"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: load & move data
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: rickbyh
ms.openlocfilehash: c4efec378a31852feacfce4ed4f040f80284e230
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolvendo diferenças de Transact-SQL durante a migração para o Banco de Dados SQL   
Quando [migrar seu banco de dados](sql-database-cloud-migrate.md) do SQL Server para o SQL Server do Azure, você poderá descobrir que seu banco de dados requer alguma reengenharia antes que o SQL Server possa ser migrado. Este tópico fornece diretrizes para ajudá-lo tanto a realizar essa reengenharia quanto a entender os motivos subjacentes pelos quais a reengenharia é necessária. Para detectar incompatibilidades, use o [DMA (Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Visão geral
Há suporte total, tanto no Microsoft SQL Server quanto no Banco de Dados SQL do Azure, para a maioria dos aplicativos e recursos Transact-SQL. Por exemplo, os componentes principais do SQL como tipos de dados, operadores, cadeia de caracteres, funções aritméticas, lógicas e de cursor funcionam da mesma maneira no SQL Server e no Banco de Dados SQL. No entanto, existem algumas diferenças de T-SQL em DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que têm suporte apenas parcial (o que discutiremos posteriormente neste tópico).

Além disso, há alguns recursos e sintaxe sem suporte nenhum, porque o Banco de Dados SQL do Azure foi criado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades no nível do servidor são inapropriadas para o Banco de Dados SQL. As opções e instruções Transact-SQL não estão disponíveis se elas configuram opções no nível do servidor, componentes do sistema operacional ou se especificam a configuração do sistema de arquivos. Quando essas funcionalidades são necessárias, uma alternativa apropriada costuma estar disponível de alguma forma no Banco de Dados SQL ou em outro recurso ou serviço do Azure. 

Por exemplo, alta disponibilidade é criada no Azure, então, configurar o Always On não é necessário (embora você talvez queira configurar a replicação geográfica ativa para recuperação mais rápida em caso de um desastre). Assim, não há suporte para as instruções Transact-SQL relacionadas aos grupos de disponibilidade no Banco de Dados SQL nem para as exibições de gerenciamento dinâmico relacionadas ao AlwaysOn.

Para obter uma lista dos recursos com e sem suporte no Banco de Dados SQL, consulte [Comparação de recursos do Banco de Dados SQL do Azure](sql-database-features.md). A lista nesta página complementa o tópico Diretrizes e recursos e se concentra nas instruções Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instruções de sintaxe Transact-SQL com diferenças parciais
As principais instruções de DDL (linguagem de definição de dados) estão disponíveis, mas algumas instruções DDL têm extensões relacionadas à localização de discos e a recursos sem suporte. 

- As instruções CREATE e ALTER DATABASE têm mais de três dúzias de opções. As instruções incluem o a localização de arquivos, FILESTREAM e opções do Service Broker que se aplicam somente ao SQL Server. Isso pode não importar se você criar bancos de dados antes de migrar, mas se você estiver migrando o código T-SQL que cria bancos de dados, você deverá comparar [CREATE DATABASE (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn268335.aspx) com a sintaxe do SQL Server em [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) para certificar-se de que todas as opções que você usar tenham suporte. CREATE DATABASE para o Banco de Dados SQL do Azure também tem as opções de objetivo de serviço e escala elástica que se aplicam apenas ao Banco de Dados SQL.
- As instruções CREATE e ALTER TABLE têm opções de FileTable que não podem ser usadas no Banco de Dados SQL, porque não há suporte para FILESTREAM.
- Há suporte para as instruções de logon CREATE e ALTER, mas o Banco de Dados SQL não oferece todas as opções. Para tornar seu banco de dados mais portátil, o Banco de Dados SQL estimula o uso de usuários de banco de dados independente em vez de logons, sempre que possível. Para obter mais informações, consulte [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) e [Controle e concessão de acesso de banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Sintaxe do Transact-SQL sem suporte no Banco de Dados SQL   
Além das instruções Transact-SQL relacionadas aos recursos sem suporte descritos em [Comparação de recursos do Banco de Dados SQL do Azure](sql-database-features.md), não há suporte para as instruções e grupos de instruções a seguir. Assim, se o banco de dados a ser migrado usa qualquer um dos recursos a seguir, faça a reengenharia de seu T-SQL para eliminar essas instruções e recursos do T-SQL.

- Agrupamento de objetos do sistema
- Relacionado à conexão: instruções de ponto de extremidade, `ORIGINAL_DB_NAME`. O Banco de dados SQL não dá suporte à autenticação do Windows, mas dá suporte à autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação exigem a versão mais recente do SSMS. Para obter mais informações, consulte [Conectar-se ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).
- Consultas cruzadas de banco de dados usando nomes de três ou quatro partes. (As consultas de bancos de dados somente leitura têm suporte por meio de [consulta de banco de dados elástico](sql-database-elastic-query-overview.md).)
- encadeamento de propriedades de bancos de dados, configuração `TRUSTWORTHY`
- `DATABASEPROPERTY` Em vez disso, use `DATABASEPROPERTYEX`.
- `EXECUTE AS LOGIN` Use “EXECUTE AS USER”.
- Há suporte para criptografia, exceto para o gerenciamento extensível de chaves
- Criação de eventos: eventos, notificações de eventos, notificações de consulta
- Localização de arquivos: sintaxe relacionada ao posicionamento de arquivos de banco de dados, ao tamanho e aos arquivos de banco de dados que são gerenciados automaticamente pelo Microsoft Azure.
- Alta disponibilidade: sintaxe relacionada à alta disponibilidade, gerenciada por meio de sua conta do Microsoft Azure. Isso inclui a sintaxe de backup, restauração, do AlwaysOn, espelhamento de banco de dados, envio de logs e dos modos de recuperação.
- Leitor de log: sintaxe que se baseia no leitor de log, que não está disponível no Banco de Dados SQL: Replicação por Push, Captura de Dados de Alteração. O Banco de Dados SQL pode ser um assinante de um artigo de replicação de push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Tabelas temporárias globais
- Hardware: sintaxe relacionada às configurações de servidor relacionadas ao hardware como memória, threads de trabalho, afinidade da CPU, sinalizadores de rastreamento. Em vez disso, use níveis de serviço.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` e nomes de quatro partes
- .NET Framework: integração CLR com o SQL Server
- Pesquisa semântica
- Credenciais do Servidor: em vez disso, use [credenciais no escopo do banco de dados](https://msdn.microsoft.com/library/mt270260.aspx).
- Itens no nível do servidor: funções do servidor, `IS_SRVROLEMEMBER`, `sys.login_token`. `GRANT`, `REVOKE` e `DENY` das permissões no nível do servidor não estão disponíveis, embora algumas delas sejam substituídas por permissões no nível do banco de dados. Algumas DMVs úteis no nível do servidor têm DMVs equivalentes no nível do banco de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opções `sp_configure` e `RECONFIGURE`. Algumas opções estão disponíveis usando [ALTERAR CONFIGURAÇÃO DE ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: sintaxe que se baseia no SQL Server Agent ou no banco de dados MSDB: alertas, operadores, servidores de gerenciamento central. Em vez disso, use scripts, como o Azure PowerShell.
- Auditoria do SQL Server: (use auditoria de Banco de Dados SQL em vez disso).
- Rastreamento do SQL Server
- Sinalizadores de rastreamento: alguns itens do sinalizador de rastreamento foram movidos para os modos de compatibilidade.
- Depuração de Transact-SQL
- Disparadores: no escopo do servidor ou gatilhos de logon
- Instrução `USE`: para alterar o contexto do banco de dados para um banco de dados diferente, é necessário estabelecer uma nova conexão com o novo banco de dados.

## <a name="full-transact-sql-reference"></a>Referência completa do Transact-SQL
Para obter mais informações sobre gramática, uso e exemplos do Transact-SQL, veja [Referência do Transact-SQL (mecanismo de banco de dados)](https://msdn.microsoft.com/library/bb510741.aspx) nos Manuais Online do SQL Server. 

### <a name="about-the-applies-to-tags"></a>Sobre as marcas "Aplica-se a"
A referência do Transact-SQL inclui tópicos relacionados a versões do SQL Server da 2008 à atual. Abaixo do título do tópico, há um ícone de barra, listando as quatro plataformas do SQL Server e que indica a aplicabilidade. Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O tópico [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indica que a instrução se aplica ao **SQL Server (a partir do 2012)**. A instrução não se aplica ao SQL Server 2008, ao SQL Server 2008 R2, ao Banco de Dados SQL do Azure, ao SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o assunto geral de um tópico pode ser usado em um produto, mas há pequenas diferenças entre produtos. As diferenças são indicadas em pontos médios no tópico, conforme apropriado. Em alguns casos, o assunto geral de um tópico pode ser usado em um produto, mas há pequenas diferenças entre produtos. As diferenças são indicadas em pontos médios no tópico, conforme apropriado. Por exemplo, o tópico CREATE TRIGGER está disponível no Banco de Dados SQL. No entanto, a opção **ALL SERVER** para gatilhos de nível de servidor indica que os gatilhos de nível de servidor não podem ser usados no Banco de Dados SQL. Use gatilhos de nível de banco de dados em vez disso.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos recursos com e sem suporte no Banco de Dados SQL, consulte [Comparação de recursos do Banco de Dados SQL do Azure](sql-database-features.md). A lista nesta página complementa o tópico Diretrizes e recursos e se concentra nas instruções Transact-SQL.


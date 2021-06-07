---
title: Resolvendo diferenças de T-SQL-migração
description: Instruções Transact-SQL que são menores que totalmente suportadas no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 1e286b2329cb98d580bbf64071ff8767db304a00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461876"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolvendo diferenças de Transact-SQL durante a migração para o Banco de Dados SQL

Ao [migrar seu banco de dados](migrate-to-database-from-sql-server.md) do SQL Server para o banco de dados SQL do Azure, você pode descobrir que seu banco de dados de SQL Server requer alguma reengenharia antes que possa ser migrado. Este artigo fornece diretrizes para ajudá-lo a realizar essa reengenharia quanto a entender os motivos subjacentes pelos quais a reengenharia é necessária. Para detectar incompatibilidades, use o [DMA (Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Visão geral

Há suporte total, tanto no Microsoft SQL Server quanto no Banco de Dados SQL do Azure, para a maioria dos aplicativos e recursos Transact-SQL. Por exemplo, os componentes principais do SQL, como tipos de dados, operadores, Cadeia de caracteres, aritmética, lógica e funções de cursor, funcionam de forma idêntica no SQL Server e no banco de dados SQL. No entanto, existem algumas diferenças de T-SQL em DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que têm suporte apenas parcial (o que discutiremos posteriormente neste artigo).

Além disso, há alguns recursos e sintaxe que não têm suporte porque o banco de dados SQL do Azure foi projetado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades no nível do servidor são inapropriadas para o Banco de Dados SQL. As instruções e opções T-SQL não estarão disponíveis se configurarem opções no nível do servidor, componentes do sistema operacional ou especificarão a configuração do sistema de arquivos. Quando essas funcionalidades são necessárias, uma alternativa apropriada costuma estar disponível de alguma forma no Banco de Dados SQL ou em outro recurso ou serviço do Azure.

Por exemplo, a alta disponibilidade é inserida no Banco de Dados SQL do Azure usando a tecnologia semelhante aos [Grupos de Disponibilidade Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Não há suporte para as instruções T-SQL relacionadas aos grupos de disponibilidade no Banco de Dados SQL nem para as exibições de gerenciamento dinâmico relacionadas ao Grupos de Disponibilidade AlwaysOn.

Para obter uma lista dos recursos com e sem suporte no Banco de Dados SQL, consulte [Comparação de recursos do Banco de Dados SQL do Azure](features-comparison.md). A lista nesta página complementa o artigo sobre diretrizes e recursos e se concentra nas instruções Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instruções de sintaxe Transact-SQL com diferenças parciais

As principais instruções de DDL (linguagem de definição de dados) estão disponíveis, mas algumas instruções DDL têm extensões relacionadas à localização de discos e a recursos sem suporte.

- As instruções CREATE e ALTER DATABASE têm mais de três dúzias de opções. As instruções incluem o a localização de arquivos, FILESTREAM e opções do Service Broker que se aplicam somente ao SQL Server. Isso pode não importar se você criar bancos de dados antes de migrar, mas se estiver migrando código T-SQL que cria bancos de dados, você deve comparar [CREATE DATABASE (banco de dados SQL do Azure)](/sql/t-sql/statements/create-database-transact-sql) com a sintaxe SQL Server em [create database (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) para garantir que todas as opções usadas tenham suporte. CREATE DATABASE para o Banco de Dados SQL do Azure também tem as opções de objetivo de serviço e escala elástica que se aplicam apenas ao Banco de Dados SQL.
- As instruções CREATE e ALTER TABLE têm opções de Filetable que não podem ser usadas no banco de dados SQL porque não há suporte para FILESTREAM.
- Há suporte para instruções CREATE e ALTER login, mas o banco de dados SQL não oferece todas as opções. Para tornar seu banco de dados mais portátil, o Banco de Dados SQL estimula o uso de usuários de banco de dados independente em vez de logons, sempre que possível. Para obter mais informações, consulte [criar/alterar logon](/sql/t-sql/statements/alter-login-transact-sql) e [gerenciar logons e usuários](logins-create-manage.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxe do Transact-SQL sem suporte no Banco de Dados SQL do Azure

Além das instruções Transact-SQL relacionadas aos recursos sem suporte descritos na [comparação de recursos do banco de dados SQL do Azure](features-comparison.md), não há suporte para as instruções e grupos de instruções a seguir. Assim, se o banco de dados a ser migrado usa qualquer um dos recursos a seguir, faça a reengenharia de seu T-SQL para eliminar essas instruções e recursos do T-SQL.

- Ordenação de objetos do sistema
- Relacionado à conexão: instruções de ponto de extremidade. O banco de dados SQL não dá suporte à autenticação do Windows, mas oferece suporte à autenticação Azure Active Directory semelhante. Alguns tipos de autenticação exigem a versão mais recente do SSMS. Para obter mais informações, consulte [conectando-se ao banco de dados SQL ou ao Azure Synapse Analytics usando a autenticação Azure Active Directory](authentication-aad-overview.md).
- Consultas cruzadas de banco de dados usando nomes de três ou quatro partes. (As consultas de bancos de dados somente leitura têm suporte por meio de [consulta de banco de dados elástico](elastic-query-overview.md).)
- encadeamento de propriedades de bancos de dados, configuração `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Use “EXECUTE AS USER”.
- Há suporte para criptografia, exceto para o gerenciamento extensível de chaves
- Criação de eventos: eventos, notificações de eventos, notificações de consulta
- Localização de arquivos: sintaxe relacionada ao posicionamento de arquivos de banco de dados, ao tamanho e aos arquivos de banco de dados que são gerenciados automaticamente pelo Microsoft Azure.
- Alta disponibilidade: sintaxe relacionada à alta disponibilidade, gerenciada por meio de sua conta do Microsoft Azure. Isso inclui a sintaxe de backup, restauração, do AlwaysOn, espelhamento de banco de dados, envio de logs e dos modos de recuperação.
- Leitor de log: a sintaxe que se baseia no leitor de log, que não está disponível no banco de dados SQL: replicação de envio por push, Data Capture de alteração. O Banco de Dados SQL pode ser um assinante de um artigo de replicação de push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: sintaxe relacionada às configurações de servidor relacionadas ao hardware como memória, threads de trabalho, afinidade da CPU, sinalizadores de rastreamento. Use as camadas de serviço e os tamanhos da computação em vez disso.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` e nomes de quatro partes
- .NET Framework: integração CLR com o SQL Server
- Pesquisa semântica
- Credenciais do Servidor: em vez disso, use [credenciais no escopo do banco de dados](/sql/t-sql/statements/create-database-scoped-credential-transact-sql).
- Itens no nível do servidor: funções de servidor, `sys.login_token`. `GRANT`, `REVOKE` e `DENY` de permissões no nível do servidor não estão disponíveis, embora algumas sejam substituídas por permissões de nível de banco de dados. Algumas DMVs úteis no nível do servidor têm DMVs equivalentes no nível do banco de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opções `sp_configure` e `RECONFIGURE`. Algumas opções estão disponíveis usando [ALTERAR CONFIGURAÇÃO DE ESCOPO DO BANCO DE DADOS](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).
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

Para obter mais informações sobre gramática, uso e exemplos do Transact-SQL, veja [Referência do Transact-SQL (mecanismo de banco de dados)](/sql/t-sql/language-reference) nos Manuais Online do SQL Server.

### <a name="about-the-applies-to-tags"></a>Sobre as marcas "Aplica-se a"

A referência do Transact-SQL inclui artigos relacionados a versões do SQL Server da 2008 à atual. Abaixo do título do artigo há uma barra de ícones, listando as quatro plataformas SQL Server e indicando aplicabilidade. Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O artigo [Criar grupo de disponibilidade](/sql/t-sql/statements/create-availability-group-transact-sql) indica que a instrução se aplica a **SQL Server (começando com 2012)**. A instrução não se aplica a SQL Server 2008, SQL Server 2008 R2, banco de dados SQL do Azure, análise de Synapse do Azure Azure ou data warehouse paralelas.

Em alguns casos, o assunto geral de um artigo pode ser usado em um produto, mas há pequenas diferenças entre os produtos. As diferenças são indicadas em pontos médios no artigo, conforme apropriado. Em alguns casos, o assunto geral de um artigo pode ser usado em um produto, mas há pequenas diferenças entre os produtos. As diferenças são indicadas em pontos médios no artigo, conforme apropriado. Por exemplo, o artigo criar gatilho está disponível no banco de dados SQL. Mas a opção **All Server** para gatilhos no nível do servidor indica que os gatilhos no nível do servidor não podem ser usados no banco de dados SQL. Use gatilhos de nível de banco de dados em vez disso.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos recursos com e sem suporte no Banco de Dados SQL, consulte [Comparação de recursos do Banco de Dados SQL do Azure](features-comparison.md). A lista nesta página complementa o artigo sobre diretrizes e recursos e se concentra nas instruções Transact-SQL.
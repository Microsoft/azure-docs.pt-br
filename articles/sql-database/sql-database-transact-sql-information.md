---
title: Resolvendo diferenças do T-SQL durante migração para o Banco de Dados SQL do Azure | Microsoft Docs
description: Instruções Transact-SQL que têm suporte menor que o total pelo Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 4d3f27d48819a4bd997cbb62177f5aae4afc85eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615390"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolvendo diferenças de Transact-SQL durante a migração para o Banco de Dados SQL

Quando [migrar seu banco de dados](sql-database-single-database-migrate.md) do SQL Server para o SQL Server do Azure, você poderá descobrir que seu banco de dados requer alguma reengenharia antes que o SQL Server possa ser migrado. Este artigo fornece diretrizes para ajudá-lo a realizar essa reengenharia quanto a entender os motivos subjacentes pelos quais a reengenharia é necessária. Para detectar incompatibilidades, use o [DMA (Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Visão geral

Há suporte total, tanto no Microsoft SQL Server quanto no Banco de Dados SQL do Azure, para a maioria dos aplicativos e recursos Transact-SQL. Por exemplo, os componentes principais do SQL como tipos de dados, operadores, cadeia de caracteres, funções aritméticas, lógicas e de cursor funcionam da mesma maneira no SQL Server e no Banco de Dados SQL. No entanto, existem algumas diferenças de T-SQL em DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que têm suporte apenas parcial (o que discutiremos posteriormente neste artigo).

Além disso, há alguns recursos e sintaxe sem suporte nenhum, porque o Banco de Dados SQL do Azure foi criado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades no nível do servidor são inapropriadas para o Banco de Dados SQL. As opções e instruções Transact-SQL não estão disponíveis se elas configuram opções no nível do servidor, componentes do sistema operacional ou se especificam a configuração do sistema de arquivos. Quando essas funcionalidades são necessárias, uma alternativa apropriada costuma estar disponível de alguma forma no Banco de Dados SQL ou em outro recurso ou serviço do Azure.

Por exemplo, a alta disponibilidade é inserida no Banco de Dados SQL do Azure usando a tecnologia semelhante aos [Grupos de Disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Não há suporte para as instruções T-SQL relacionadas aos grupos de disponibilidade no Banco de Dados SQL nem para as exibições de gerenciamento dinâmico relacionadas ao Grupos de Disponibilidade AlwaysOn.

Para obter uma lista dos recursos compatíveis e não suportados pelo Banco de Dados SQL, consulte  [Comparação de recursos do Banco de Dados SQL do Azure](sql-database-features.md). A lista nesta página complementa o artigo sobre diretrizes e recursos e se concentra nas instruções Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instruções de sintaxe Transact-SQL com diferenças parciais

As principais instruções de DDL (linguagem de definição de dados) estão disponíveis, mas algumas instruções DDL têm extensões relacionadas à localização de discos e a recursos sem suporte.

- As instruções CREATE e ALTER DATABASE têm mais de três dúzias de opções. As instruções incluem o a localização de arquivos, FILESTREAM e opções do Service Broker que se aplicam somente ao SQL Server. Isso pode não importar se você criar bancos de dados antes de migrar, mas se você estiver migrando o código T-SQL que cria bancos de dados, você deverá comparar [CREATE DATABASE (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn268335.aspx) com a sintaxe do SQL Server em [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) para certificar-se de que todas as opções que você usar tenham suporte. CREATE DATABASE para o Banco de Dados SQL do Azure também tem as opções de objetivo de serviço e escala elástica que se aplicam apenas ao Banco de Dados SQL.
- As instruções CREATE e ALTER TABLE têm opções de FileTable que não podem ser usadas no Banco de Dados SQL, porque não há suporte para FILESTREAM.
- Há suporte para as instruções de logon CREATE e ALTER, mas o Banco de Dados SQL não oferece todas as opções. Para tornar seu banco de dados mais portátil, o Banco de Dados SQL estimula o uso de usuários de banco de dados independente em vez de logons, sempre que possível. Para obter mais informações, consulte [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) e [Controle e concessão de acesso de banco de dados](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxe do Transact-SQL sem suporte no Banco de Dados SQL do Azure

Além das instruções Transact-SQL relacionadas aos recursos não suportados descritos na  [comparação de recursos do Banco de Dados SQL do Azure](sql-database-features.md), as seguintes declarações e grupos de instruções não são suportadas. Assim, se o banco de dados a ser migrado usa qualquer um dos recursos a seguir, faça a reengenharia de seu T-SQL para eliminar essas instruções e recursos do T-SQL.

- Ordenação de objetos do sistema
- Conexão relacionada: Instruções de ponto de extremidade. O Banco de dados SQL não dá suporte à autenticação do Windows, mas dá suporte à autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação exigem a versão mais recente do SSMS. Para obter mais informações, consulte [Conectar-se ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).
- Consultas cruzadas de banco de dados usando nomes de três ou quatro partes. (As consultas de bancos de dados somente leitura têm suporte por meio de [consulta de banco de dados elástico](sql-database-elastic-query-overview.md).)
- encadeamento de propriedades de bancos de dados, configuração `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Use “EXECUTE AS USER”.
- Há suporte para criptografia, exceto para o gerenciamento extensível de chaves
- Eventos: eventos, notificações de eventos, notificações de consulta
- Posicionamento do arquivo: Sintaxe relacionada ao posicionamento de arquivos de banco de dados, ao tamanho e aos arquivos de banco de dados que são gerenciados automaticamente pelo Microsoft Azure.
- Alta disponibilidade: Sintaxe relacionada à alta disponibilidade, gerenciada por meio de sua conta do Microsoft Azure. Isso inclui a sintaxe de backup, restauração, do AlwaysOn, espelhamento de banco de dados, envio de logs e dos modos de recuperação.
- Leitor de logs: sintaxe que se baseia no leitor de logs, que não está disponível no Banco de Dados SQL: replicação push, captura de dados de alterações. O Banco de Dados SQL pode ser um assinante de um artigo de replicação de push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: sintaxe relacionada às configurações de servidor relacionadas ao hardware como memória, threads de trabalho, afinidade da CPU, sinalizadores de rastreamento. Use as camadas de serviço e os tamanhos da computação em vez disso.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` e nomes de quatro partes
- .NET Framework: integração CLR com o SQL Server
- Pesquisa semântica
- Credenciais do servidor: Em vez disso, use [credenciais no escopo do banco de dados](https://msdn.microsoft.com/library/mt270260.aspx).
- Itens de nível de servidor: Funções de servidor, `sys.login_token`. `GRANT`, `REVOKE` e `DENY` das permissões no nível do servidor não estão disponíveis, embora algumas delas sejam substituídas por permissões no nível do banco de dados. Algumas DMVs úteis no nível do servidor têm DMVs equivalentes no nível do banco de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opções `sp_configure` e `RECONFIGURE`. Algumas opções estão disponíveis usando [ALTERAR CONFIGURAÇÃO DE ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Sintaxe que se baseia no SQL Server Agent ou no banco de dados MSDB: alertas, operadores, servidores de gerenciamento central. Em vez disso, use scripts, como o Azure PowerShell.
- Auditoria do SQL Server: Em vez disso, use a auditoria do Banco de Dados SQL.
- Rastreamento do SQL Server
- Sinalizadores de rastreamento: Alguns itens do sinalizador de rastreamento foram movidos para os modos de compatibilidade.
- Depuração de Transact-SQL
- Gatilhos: com escopo do servidor ou gatilhos de logon
- Instrução `USE`: para alterar o contexto do banco de dados para um banco de dados diferente, é necessário estabelecer uma nova conexão com o novo banco de dados.

## <a name="full-transact-sql-reference"></a>Referência completa do Transact-SQL

Para obter mais informações sobre a gramática, uso e exemplos do Transact-SQL, consulte  [Referência Transact-SQL (Mecanismo de Banco de Dados)](https://msdn.microsoft.com/library/bb510741.aspx) nos Manuais Online do SQL Server.

### <a name="about-the-applies-to-tags"></a>Sobre as marcas "Aplica-se a"

A referência do Transact-SQL inclui artigos relacionados a versões do SQL Server da 2008 à atual. Abaixo do título do artigo, há um ícone de barra, listando as quatro plataformas do SQL Server e que indica a aplicabilidade. Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O artigo  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx)  indica que a instrução se aplica a  **SQL Server (a partir de 2012)**. A instrução não se aplica ao SQL Server 2008, ao SQL Server 2008 R2, ao Banco de Dados SQL do Azure, ao SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o assunto geral de um artigo pode ser usado em um produto, mas há pequenas diferenças entre produtos. As diferenças são indicadas em pontos médios no artigo, conforme apropriado. Em alguns casos, o assunto geral de um artigo pode ser usado em um produto, mas há pequenas diferenças entre produtos. As diferenças são indicadas em pontos médios no artigo, conforme apropriado. Por exemplo, o artigo CREATE TRIGGER está disponível no Banco de Dados SQL. No entanto, a opção **ALL SERVER** para gatilhos de nível de servidor indica que os gatilhos de nível de servidor não podem ser usados no Banco de Dados SQL. Use gatilhos de nível de banco de dados em vez disso.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos recursos compatíveis e não suportados pelo Banco de Dados SQL, consulte  [Comparação de recursos do Banco de Dados SQL do Azure](sql-database-features.md). A lista nesta página complementa o artigo sobre diretrizes e recursos e se concentra nas instruções Transact-SQL.

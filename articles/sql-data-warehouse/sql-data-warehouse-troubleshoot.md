---
title: "Solução de problemas do Azure SQL Data Warehouse | Microsoft Docs"
description: "Solução de problemas do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/30/2017
ms.author: kevin;barbkess
ms.openlocfilehash: d269e62b8d49a6c96ce40c2e31c4096e16e07793
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solução de problemas do Azure SQL Data Warehouse
Este tópico lista algumas das perguntas mais comuns que ouvimos de nossos clientes sobre solução de problemas.

## <a name="connecting"></a>Conexão
| Problema | Resolução |
|:--- |:--- |
| Falha de logon do usuário 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, erro: 18456) |Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o SQL Data Warehouse que deseja conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral de segurança][Security overview] para obter mais detalhes. |
| A entidade de segurança do servidor "MyUserName" não é capaz de acessar o banco de dados "mestre" no contexto de segurança atual. Não é possível abrir o banco de dados padrão do usuário. Falha no logon. Falha de logon do usuário 'MyUserName'. (Microsoft SQL Server, erro: 916) |Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o SQL Data Warehouse que deseja conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral de segurança][Security overview] para obter mais detalhes. |
| Erro CTAIP |Esse erro pode ocorrer quando um logon tiver sido criado no banco de dados mestre do SQL Server, mas não no banco de dados do SQL Data Warehouse.  Se você encontrar esse erro, examine o artigo [Visão geral de segurança][Security overview].  Este artigo explica como criar um logon e um usuário no mestre e, em seguida, como criar um usuário no banco de dados do SQL Data Warehouse. |
| Bloqueado pelo firewall |Bancos de Dados SQL do Azure são protegidos pelo servidor e firewalls de nível de banco de dados para garantir que somente os endereços IP conhecidos possam acessar um bancos de dados. Os firewalls são seguros por padrão, o que significa que você deve habilitar explicitamente um endereço IP ou um intervalo de endereços antes de se conectar.  Para configurar seu firewall para acesso, siga as etapas na seção [Configurar o acesso ao servidor de firewall para o IP do cliente][Configure server firewall access for your client IP] nas [Instruções de provisionamento][Provisioning instructions]. |
| Não é possível conectar-se com a ferramenta ou driver |O SQL Data Warehouse recomenda usar o [SSMS][SSMS], o [SSDT para Visual Studio][SSDT for Visual Studio] ou o [sqlcmd][sqlcmd] para consultar os dados. Para obter mais detalhes sobre os drivers e como se conectar ao SQL Data Warehouse, confira os artigos [Drivers do Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] e [Conectar-se ao Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse]. |

## <a name="tools"></a>Ferramentas
| Problema | Resolução |
|:--- |:--- |
| O Pesquisador de objetos do Visual Studio não tem usuários de AAD |Esse é um problema conhecido.  Como alternativa, exiba os usuários em [sys.database_principals][sys.database_principals].  Confira [Autenticação do Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] para saber mais sobre como usar o Azure Active Directory com o SQL Data Warehouse. |
|Escrever script manualmente usando o assistente de script ou conectar-se via SSMS é lento, trava ou produz erros| Certifique-se de que os usuários foram criados no banco de dados mestre. Nas opções de script, certifique-se também de que a edição do mecanismo está definida como "Edição do SQL Data Warehouse do Microsoft Azure" e o tipo de mecanismo é "Banco de Dados SQL do Microsoft Azure".|

## <a name="performance"></a>Desempenho
| Problema | Resolução |
|:--- |:--- |
| Solucionar problemas de desempenho da consulta |Caso você esteja tentando solucionar problemas de uma determinada consulta, comece com [Aprendendo a monitorar suas consultas][Learning how to monitor your queries]. |
| Planos e consultas com um desempenho ruim normalmente são o resultado da falta de estatísticas |A causa mais comum do mau desempenho é a falta de estatísticas em tabelas.  Confira [Como manter estatísticas de tabela][Statistics] para obter detalhes sobre como criar estatísticas e por que elas são essenciais para o desempenho. |
| Baixa simultaneidade/consultas em fila |É importante entender o [Gerenciamento de carga de trabalho][Workload management] para compreender como equilibrar a alocação de memória com simultaneidade. |
| Como implementar as práticas recomendadas |O melhor lugar para começar a aprender maneiras de melhorar o desempenho de consulta é o artigo [Práticas recomendadas do SQL Data Warehouse][SQL Data Warehouse best practices]. |
| Como melhorar o desempenho com o dimensionamento |Às vezes, a solução para aprimorar o desempenho é simplesmente adicionar mais poder de computação às suas consultas [Dimensionando seu SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Desempenho ruim da consulta como resultado da baixa qualidade do índice |Às vezes, as consultas podem apresentar lentidão devido à [Baixa qualidade do índice columnstore][Poor columnstore index quality].  Consulte este artigo para saber mais e para saber como [Recompilar índices a fim de melhorar a qualidade do segmento][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Gerenciamento do sistema
| Problema | Resolução |
|:--- |:--- |
| Msg 40847: não foi possível executar a operação, pois o servidor excederia a cota de Unidade de Transação de Banco de Dados permitida de 45000. |Reduza a [DWU][DWU] do banco de dados que você está tentando criar ou [solicite um aumento de cota][request a quota increase]. |
| Investigação da utilização de espaço |Confira os [Tamanhos da tabela][Table sizes] para entender a utilização do espaço de seu sistema. |
| Ajuda com o gerenciamento de tabelas |Confira o artigo [Visão geral da tabela][Overview] para obter ajuda com o gerenciamento de suas tabelas.  Este artigo também inclui links para tópicos mais detalhados, como [Tipos de dados de tabela][Data types], [Distribuindo uma tabela][Distribute], [Indexando uma tabela][Index], [Particionando uma tabela][Partition], [Mantendo as estatísticas da tabela][Statistics] e [Tabelas temporárias][Temporary]. |
|A barra de progresso TDE (Transparent Data Encryption) não está atualizando no Portal do Azure|Você pode exibir o estado de TDE via [PowerShell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>Polybase
| Problema | Resolução |
|:--- |:--- |
| Falha no carregamento devido a linhas grandes |Atualmente, o suporte para linhas grandes não está disponível para o Polybase.  Isso significa que, se a tabela contiver VARCHAR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX), as Tabelas externas não poderão ser usadas para carregar seus dados.  Só há suporte no momento para carregamentos de linhas grandes por meio do Azure Data Factory (com BCP), Stream Analytics do Azure, SSIS, BCP ou a classe .NET SQLBulkCopy. O suporte do PolyBase para linhas grandes será adicionado em uma versão futura. |
| Falha de carregamento bcp de tabela com o tipo de dados MAX |Há um problema conhecido que exige o posicionamento de VARCHAR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX) no final da tabela em alguns cenários.  Tente mover as colunas MAX para o final da tabela. |

## <a name="differences-from-sql-database"></a>Diferenças do Banco de Dados SQL
| Problema | Resolução |
|:--- |:--- |
| Recursos do Banco de Dados SQL sem suporte |Confira [Recursos de tabela sem suporte][Unsupported table features]. |
| Tipos de dados do Banco de Dados SQL sem suporte |Confira [Tipos de dados sem suporte][Unsupported data types]. |
| Limitações de DELETE e UPDATE |Confira [Soluções alternativas de UPDATE][UPDATE workarounds], [Soluções alternativas de DELETE][DELETE workarounds] e [Como usar CTAS para contornar a sintaxe UPDATE e DELETE sem suporte][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Não há suporte para a instrução MERGE |Confira [Soluções alternativas para MERGE][MERGE workarounds]. |
| Limitações de procedimento armazenado |Confira [Limitações de procedimentos armazenados][Stored procedure limitations] para entender algumas dessas limitações. |
| UDFs não oferecem suporte a instruções SELECT |Esta é uma limitação atual de nossos UDFs.  Confira [CREATE FUNCTION][CREATE FUNCTION] para ver a sintaxe para a qual damos suporte. |

## <a name="next-steps"></a>Próximas etapas
Se você não é capaz de encontrar uma solução para seu problema acima, aqui estão alguns outros recursos que você pode experimentar.

* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da equipe CAT]
* [Criar um tíquete de suporte]
* [Fórum do MSDN]
* [Fórum Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da equipe CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

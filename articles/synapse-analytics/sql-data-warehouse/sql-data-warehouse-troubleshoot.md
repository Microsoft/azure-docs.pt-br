---
title: Solucionando problemas do pool de SQL dedicado (antigo SQL DW)
description: Solução de problemas do pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 949a89dd4e84bdb44ad15eaff9ca160fdd21b395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602121"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Solução de problemas do pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

Este artigo lista problemas comuns de solução de problemas no pool dedicado do SQL (anteriormente SQL DW) no Azure Synapse Analytics.

## <a name="connecting"></a>Connecting

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Falha de logon do usuário 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Erro: 18456) | Esse erro ocorre quando um usuário do Azure AD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o pool dedicado do SQL (antigo SQL DW) ao qual você deseja se conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md) para obter mais detalhes. |
| A entidade de segurança do servidor "MyUserName" não é capaz de acessar o banco de dados "mestre" no contexto de segurança atual. Não é possível abrir o banco de dados padrão do usuário. Falha no logon. Falha de logon do usuário 'MyUserName'. (Microsoft SQL Server, Erro: 916) | Esse erro ocorre quando um usuário do Azure AD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o pool dedicado do SQL (antigo SQL DW) ao qual você deseja se conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md) para obter mais detalhes. |
| Erro CTAIP                                                  | Esse erro pode ocorrer quando um logon é criado no banco de dados mestre do banco de dados SQL, mas não no banco de dados SQL específico.  Se você encontrar esse erro, examine o artigo [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md) .  Este artigo explica como criar um logon e um usuário no banco de dados mestre e como criar um usuário em um banco de dados SQL. |
| Bloqueado pelo firewall                                          | O pool dedicado do SQL (anteriormente conhecido como SQL DW) é protegido por firewalls para garantir que apenas endereços IP conhecidos tenham acesso a um banco de dados. Os firewalls são seguros por padrão, o que significa que você deve habilitar explicitamente um endereço IP ou um intervalo de endereços antes de se conectar.  Para configurar seu firewall para acesso, siga as etapas na seção [Configurar o acesso ao servidor de firewall para o IP do cliente](create-data-warehouse-portal.md) nas [Instruções de provisionamento](create-data-warehouse-portal.md). |
| Não é possível conectar-se com a ferramenta ou driver                           | O pool dedicado do SQL (anteriormente conhecido como SQL DW) recomenda usar o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), o [SSDT para Visual Studio](sql-data-warehouse-install-visual-studio.md)ou o [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) para consultar seus dados. Para obter mais informações sobre drivers e sobre como se conectar ao Azure Synapse, leia os artigos [Drivers do Azure Synapse](sql-data-warehouse-connection-strings.md) e [Conectar-se ao Azure Synapse](sql-data-warehouse-connect-overview.md). |

## <a name="tools"></a>Ferramentas

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| O Pesquisador de objetos do Visual Studio não tem usuários do Azure AD           | Esse é um problema conhecido.  Como alternativa, exiba os usuários em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Consulte [autenticação no Azure Synapse](sql-data-warehouse-authentication.md) para saber mais sobre como usar Azure Active Directory com o pool SQL dedicado (anteriormente conhecido como SQL DW). |
| A escrita de script manual usando o assistente de script ou a conexão via SSMS está lenta, travando ou produzindo erros | Certifique-se de que os usuários foram criados no banco de dados mestre. Em opções de script, verifique também se a edição do mecanismo está definida como "Microsoft Azure Synapse Analytics Edition" e o tipo de mecanismo é "Banco de Dados SQL do Microsoft Azure". |
| Gerar falhas de script no SSMS                               | A geração de um script para o pool dedicado do SQL (antigo SQL DW) falhará se a opção "gerar script para objetos dependentes" estiver definida como "true". Como alternativa, os usuários devem abrir manualmente **Ferramentas -> Opções -> SQL Pesquisador de Objetos do SQL Server -> Gerar script para opções dependentes e definidas como false** |

## <a name="data-ingestion-and-preparation"></a>Preparação e ingestão de dados

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A exportação de cadeias de caracteres vazias usando CETAS resultará em valores nulos nos arquivos parquet e ORC. Observação Se você estiver exportando cadeias de caracteres vazias de colunas com restrições NOT NULL, CETAS resultará em registros rejeitados e a exportação poderá potencialmente falhar. | Remova cadeias de caracteres vazias ou a coluna incorreta na instrução SELECT de seu CETAS. |
| Não há suporte para o carregamento de um valor fora do intervalo de 0-127 em uma coluna tinyint para o formato de arquivo parquet e ORC. | Especifique um tipo de dados maior para a coluna de destino.           |

## <a name="performance"></a>Desempenho

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Solucionar problemas de desempenho da consulta                            | Caso você esteja tentando solucionar problemas de uma determinada consulta, comece com [Aprendendo a monitorar suas consultas](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemas de espaço do TempDB | [Monitorar uso do espaço no TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb).  As causas comuns de que fazem com que haja espaço de armazenamento insuficiente no TempDB são:<br>– Falta de recursos suficientes alocados para a consulta, fazendo com que os dados sejam despejados no TempDB.  Confira [Gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) <br>– Ausência ou desatualização de estatísticas, causando uma movimentação de dados excessiva.  Confira [Manutenção de estatísticas de tabela](sql-data-warehouse-tables-statistics.md), para obter detalhes sobre como criar estatísticas<br>– Alocação de espaço de TempDB por nível de serviço.  [Dimensionar seu pool SQL dedicado (anteriormente conhecido como SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) para uma configuração de DWU maior aloca mais espaço de tempdb.|
| Planos e consultas com um desempenho ruim normalmente são o resultado da falta de estatísticas | A causa mais comum do mau desempenho é a falta de estatísticas em tabelas.  Confira [Como manter estatísticas de tabela](sql-data-warehouse-tables-statistics.md) para obter detalhes sobre como criar estatísticas e por que elas são essenciais para o desempenho. |
| Baixa simultaneidade/consultas em fila                             | É importante entender o [Gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) para compreender como equilibrar a alocação de memória com simultaneidade. |
| Como implementar as práticas recomendadas                              | O melhor lugar para começar a aprender maneiras de melhorar o desempenho da consulta é o artigo de [práticas recomendadas do pool SQL dedicado (antes do SQL DW)](sql-data-warehouse-best-practices.md) . |
| Como melhorar o desempenho com o dimensionamento                      | Às vezes, a solução para melhorar o desempenho é simplesmente adicionar mais poder de computação às suas consultas, [dimensionando seu pool SQL dedicado (anteriormente conhecido como SQL DW)](sql-data-warehouse-manage-compute-overview.md). |
| Desempenho ruim da consulta como resultado da baixa qualidade do índice     | Em outros, as consultas podem apresentar lentidão devido à [Baixa qualidade do índice columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Consulte este artigo para saber mais e para saber como [Recriar índices a fim de melhorar a qualidade do segmento](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Gerenciamento do sistema

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Não foi possível executar a operação, pois o servidor excederia a cota de Unidade de Transação de Banco de Dados permitida de 45000. | Reduza a [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) do banco de dados que você está tentando criar ou [solicite um aumento de cota](sql-data-warehouse-get-started-create-support-ticket.md). |
| Investigação da utilização de espaço                              | Confira [Tamanhos da tabela](sql-data-warehouse-tables-overview.md#table-size-queries) para entender a utilização do espaço de seu sistema. |
| Ajuda com o gerenciamento de tabelas                                    | Confira o artigo [Visão geral da tabela](sql-data-warehouse-tables-overview.md) para obter ajuda com o gerenciamento de suas tabelas.  Este artigo também inclui links para tópicos mais detalhados, como [Tipos de dados de tabela](sql-data-warehouse-tables-data-types.md), [Como distribuir uma tabela](sql-data-warehouse-tables-distribute.md), [Como indexar uma tabela](sql-data-warehouse-tables-index.md), [Como particionar uma tabela](sql-data-warehouse-tables-partition.md), [Manutenção de estatísticas da tabela](sql-data-warehouse-tables-statistics.md) e [Tabelas temporárias](sql-data-warehouse-tables-temporary.md). |
| A barra de progresso TDE (Transparent Data Encryption) não está sendo atualizada no Portal do Azure | Você pode exibir o estado de TDE por meio do [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Diferenças do Banco de Dados SQL

| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Recursos do Banco de Dados SQL sem suporte     | Confira [Recursos de tabela sem suporte](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipos de dados do Banco de Dados SQL sem suporte   | Confira [Tipos de dados sem suporte](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Limitações de procedimento armazenado          | Confira [Limitações de procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md#limitations) para entender algumas dessas limitações. |
| UDFs não oferecem suporte a instruções SELECT | Esta é uma limitação atual de nossos UDFs.  Confira [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para ver a sintaxe para a qual damos suporte. |
| sp_rename (visualização) para colunas não funciona em esquemas fora do *dbo* | Essa é uma limitação atual de Synapse [sp_rename (versão prévia) para colunas](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Colunas em objetos que não fazem parte do esquema *dbo* podem ser renomeadas por meio de um CTAS em uma nova tabela. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda na busca de soluções para o problema, a seguir são apresentados alguns outros recursos que você pode experimentar.

* [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Solicitações de recursos](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
* [Página de perguntas e respostas da Microsoft](/answers/topics/azure-synapse-analytics.html)
* [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
---
title: Regras de avaliação para SQL Server a migração do banco de dados SQL
description: Regras de avaliação para identificar problemas com a instância de SQL Server de origem que deve ser resolvida antes de migrar para o banco de dados SQL do Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054436"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>Regras de avaliação para SQL Server a migração do banco de dados SQL
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

As ferramentas de migração validam sua instância de SQL Server de origem executando várias regras de avaliação para identificar problemas que devem ser resolvidos antes de migrar seu banco de dados SQL Server para o banco de dados SQL do Azure. 

Este artigo fornece uma lista das regras usadas para avaliar a viabilidade de migrar seu banco de dados SQL Server para o banco de dados SQL do Azure. 


## <a name="bulk-insert"></a>Inserção em massa<a id="BulkInsert"></a>

**Título: não há suporte para BULK INSERT com fonte de dados de BLOB não Azure no Azure SQL Database.**   
**Categoria**: problema   

**Descrição**   
O banco de dados SQL do Azure não pode acessar compartilhamentos de arquivos ou pastas Windows. Consulte a seção "objetos afetados" para ver os usos específicos de BULK INSERT instruções que não fazem referência a um blob do Azure. Objetos com ' BULK INSERT ', em que a origem não é o armazenamento de BLOBs do Azure não funcionarão após a migração para o banco de dados SQL do Azure. 


**Recomendação**   
Você precisará converter BULK INSERT instruções que usam arquivos locais ou compartilhamentos de arquivos para usar arquivos do armazenamento de BLOBs do Azure em vez disso, ao migrar para o banco de dados SQL do Azure. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

## <a name="compute-clause"></a>Cláusula COMPUTE<a id="ComputeClause"></a>

**Título: a cláusula COMPUTE foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
A cláusula COMPUTE gera totais que aparecem como colunas de resumo adicionais no final do conjunto de resultados. No entanto, essa cláusula não tem mais suporte no banco de dados SQL do Azure. 


**Recomendação**   
Em vez disso, o módulo T-SQL precisa ser reescrito usando o operador de acúmulo. O código a seguir demonstra como a computação pode ser substituída por ROLLUP: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Captura de dados de alterações (CDC)<a id="CDC"></a>

**Título: a captura de dados de alterações (CDC) não é suportada no Azure SQL Database**   
**Categoria**: problema   


**Descrição**   
Não há suporte para a captura de dados de alterações (CDC) no banco de dado SQL do Azure. Avalie se Controle de Alterações pode ser usado em vez disso.  Como alternativa, migre para o Azure SQL Instância Gerenciada ou SQL Server em máquinas virtuais do Azure. 


**Recomendação**   
Não há suporte para a captura de dados de alterações (CDC) no banco de dado SQL do Azure. Avalie se Controle de Alterações pode ser usado em seu lugar ou considere migrar para o SQL Instância Gerenciada do Azure.

Mais informações: [habilitar o controle de alterações do SQL Azure](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>Assemblies CLR<a id="ClrAssemblies"></a>

**Título: não há suporte para assemblies SQL CLR no banco de dados SQL do Azure**   
**Categoria**: problema   


**Descrição**   
O banco de dados SQL do Azure não oferece suporte a assemblies CLR do SQL. 


**Recomendação**   
Atualmente, não há como conseguir isso no banco de dados SQL do Azure. As soluções alternativas recomendadas exigirão alterações no código do aplicativo e no banco de dados para usar somente assemblies com suporte do banco de dados SQL do Azure. Como alternativa, migre para o Azure SQL Instância Gerenciada ou SQL Server na máquina virtual do Azure

Mais informações: [diferenças de Transact-SQL sem suporte no banco de dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Provedor criptográfico<a id="CryptographicProvider"></a>

**Título: é encontrado um uso do provedor de criptografia de criação ou ALTER CRYPTOGRAPHIC, que não tem suporte no banco de dados SQL do Azure**   
**Categoria**: problema   

**Descrição**   
O banco de dados SQL do Azure não dá suporte a instruções de provedor CRIPTOGRÁFICO porque ele não pode acessar arquivos. Consulte a seção objetos afetados para usos específicos de instruções do provedor CRIPTOGRÁFICO. Objetos com `CREATE CRYPTOGRAPHIC PROVIDER` ou `ALTER CRYPTOGRAPHIC PROVIDER` não funcionarão corretamente após a migração para o banco de dados SQL do Azure.  


**Recomendação**   
Examine objetos com `CREATE CRYPTOGRAPHIC PROVIDER` ou `ALTER CRYPTOGRAPHIC PROVIDER` . Em qualquer um desses objetos necessários, remova os usos desses recursos. Como alternativa, migre para o SQL Server na máquina virtual do Azure

## <a name="cross-database-references"></a>Referências entre bancos de dados<a id="CrossDataseReferences"></a>

**Título: não há suporte para consultas entre bancos de dados no banco de dados SQL do Azure**   
**Categoria**: problema   

**Descrição**   
Os bancos de dados neste servidor usam consultas entre bancos, que não têm suporte no banco de dados SQL do Azure. 


**Recomendação**   
O banco de dados SQL do Azure não oferece suporte a consultas entre bancos de dados. As seguintes ações são recomendadas: 
- Migre os bancos de dados dependentes para o banco de dados SQL do Azure e use a funcionalidade de consulta de banco de dados elástico (atualmente em visualização) para consultar em bancos de dados SQL do Azure. 
- Mova os conjuntos de dados dependentes de outros bancos para o que está sendo migrado. 
- Migre para o Azure SQL Instância Gerenciada.
- Migre para o SQL Server na máquina virtual do Azure. 

Mais informações: [verificar consulta de banco de dados elástico do banco de dados SQL do Azure (versão prévia)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Compatibilidade de banco de dados<a id="DbCompatLevelLowerThan100"></a>

**Título: o banco de dados SQL do Azure não dá suporte a níveis de compatibilidade abaixo de 100.**   
**Categoria**: aviso   

**Descrição**   
O nível de compatibilidade do banco de dados é uma ferramenta valiosa para ajudar na modernização do banco de dados, permitindo que o Mecanismo de Banco de Dados de SQL Server seja atualizado, ao mesmo tempo em que mantém o status funcional dos aplicativos de conexão mantendo o mesmo nível de compatibilidade do banco de dados de pré-atualização. O banco de dados SQL do Azure não dá suporte a níveis de compatibilidade abaixo de 100. 


**Recomendação**   
Avalie se a funcionalidade do aplicativo está intacta quando o nível de compatibilidade do banco de dados é atualizado para 100 no Azure SQL Instância Gerenciada. Como alternativa, migre para o SQL Server na máquina virtual do Azure

## <a name="database-mail"></a>Database Mail<a id="DatabaseMail"></a>

**Título: não há suporte para Database Mail no banco de dados SQL do Azure.**   
**Categoria**: aviso   

**Descrição**   
Este servidor usa o recurso Database Mail, que não tem suporte no banco de dados SQL do Azure.


**Recomendação**   
Considere a migração para o Azure SQL Instância Gerenciada que oferece suporte a Database Mail.  Como alternativa, considere usar o Azure Functions e o Sendgrid para realizar a funcionalidade de email no banco de dados SQL do Azure.

Mais informações: [Enviar email do banco de dados SQL do Azure usando o script Azure Functions](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Alias de entidade do banco de dados<a id="DatabasePrincipalAlias"></a>

**Título: SYS. O DATABASE_PRINCIPAL_ALIASES foi descontinuado e foi removido.**   
**Categoria**: problema   

**Descrição**   
Sistema. A DATABASE_PRINCIPAL_ALIASES foi descontinuada e foi removida no banco de dados SQL do Azure.  


**Recomendação**   
Use funções em vez de aliases.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>Opção DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Título: a opção SET DISABLE_DEF_CNST_CHK foi descontinuada e foi removida.**   
**Categoria**: problema   

**Descrição**   
A opção SET DISABLE_DEF_CNST_CHK foi descontinuada e foi removida no banco de dados SQL do Azure.  


Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Dica FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Título: a dica de consulta FASTFIRSTROW foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
A dica de consulta FASTFIRSTROW foi descontinuada e foi removida no banco de dados SQL do Azure.  


**Recomendação**   
Em vez da opção de uso de dica de consulta FASTFIRSTROW (FAST n).

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Título: FILESTREAM não tem suporte no banco de dados SQL do Azure**   
**Categoria**: problema   

**Descrição**   
O recurso FileStream, que permite armazenar dados não estruturados, como documentos de texto, imagens e vídeos no sistema de arquivos NTFS, não tem suporte no banco de dados SQL do Azure. 


**Recomendação**   
Carregue os arquivos não estruturados no armazenamento de BLOBs do Azure e armazene os metadados relacionados a esses arquivos (nome, tipo, local da URL, chave de armazenamento etc.) no banco de dados SQL do Azure. Talvez seja necessário refazer a engenharia de seu aplicativo para habilitar os blobs de streaming de e para o banco de dados SQL do Azure. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [streaming de blobs de e para o blog SQL do Azure](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Servidor vinculado<a id="LinkedServer"></a>

**Título: não há suporte para a funcionalidade de servidor vinculado no banco de dados SQL do Azure**   
**Categoria**: problema   

**Descrição**   
Os servidores vinculados permitem que o SQL Server Mecanismo de Banco de Dados execute comandos em OLE DB fontes de dados fora da instância do SQL Server. 


**Recomendação**   
O banco de dados SQL do Azure não dá suporte à funcionalidade de servidor vinculado. As ações a seguir são recomendadas para eliminar a necessidade de servidores vinculados: 
- Identifique os conjuntos de dados dependentes de servidores SQL remotos e considere movê-los para o banco de dados que está sendo migrado. 
- Migre os bancos de dados dependentes para o Azure e use a funcionalidade de consulta de banco de dados elástico (versão prévia) para consultar em bancos de dados no banco de dados SQL do Azure. 

Mais informações: [verificar a consulta elástica do banco de dados SQL do Azure (visualização)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Título: não há suporte para iniciar transação DISTRIBUÍda no banco de dados SQL do Azure.**   
**Categoria**: problema   

**Descrição**   
A transação distribuída iniciada pelo Transact SQL inicia a transação DISTRIBUÍda e gerenciada pelo Microsoft Coordenador de Transações Distribuídas (MS DTC) não tem suporte no banco de dados SQL do Azure.  


**Recomendação**   
Examine a seção de objetos afetados em migrações para Azure para ver todos os objetos usando BEGIN nos TRANSACTION. Considere migrar os bancos de dados de participante para o Azure SQL Instância Gerenciada em que as transações distribuídas entre várias instâncias têm suporte (atualmente em visualização). Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [transações em vários servidores para o Azure SQL instância gerenciada ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (em massa)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Título: OpenRowSet usado em operação em massa com fonte de dados de armazenamento de BLOBs não Azure não tem suporte no banco de dado SQL do Azure.**   
**Categoria**: problema   

**Descrição** do O OPENROWSET dá suporte a operações em massa por meio de um provedor em massa interno que permite que dados de um arquivo sejam lidos e retornados como um conjunto de linhas. Não há suporte para OPENROWSET com uma fonte de dados de armazenamento de BLOBs do Azure no Azure SQL Database.


**Recomendação**   
O banco de dados SQL do Azure não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, os arquivos devem ser importados do armazenamento de BLOBs Portanto, somente o tipo de blob DATASOURCE tem suporte na função OPENROWSET. Como alternativa, migre para o SQL Server na máquina virtual do Azure

Mais informações: [resolvendo diferenças de Transact-SQL durante a migração para o banco de dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (provedor)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Título: OpenRowSet com SQL ou provedor não SQL não tem suporte no banco de dados SQL do Azure.**   
**Categoria**: problema   

**Descrição**   
O OpenRowSet com o provedor SQL ou não SQL é uma alternativa para acessar tabelas em um servidor vinculado e é um método ad hoc único de conectar e acessar dados remotos usando OLE DB. Não há suporte para OpenRowSet com o provedor SQL ou não-SQL no banco de dados SQL do Azure.


**Recomendação**   
O banco de dados SQL do Azure oferece suporte a OPENROWSET somente para importar do armazenamento de BLOBs do Azure Como alternativa, migre para o SQL Server na máquina virtual do Azure

Mais informações: [resolvendo diferenças de Transact-SQL durante a migração para o banco de dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Junção externa esquerda não-ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Título: a junção externa esquerda do estilo não-ANSI foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
A junção externa esquerda do estilo não-ANSI foi descontinuada e foi removida no banco de dados SQL do Azure. 


**Recomendação**   
Use a sintaxe de junção ANSI.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Junção externa direita não-ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Título: a junção externa direita do estilo não-ANSI foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
O estilo não-ANSI junção externa direita foi descontinuado e foi removido no banco de dados SQL do Azure. 


**Recomendação**   
Use a sintaxe de junção ANSI.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Próxima coluna<a id="NextColumn"></a>

**Título: as tabelas e colunas nomeadas a seguir resultarão em um erro no banco de dados SQL do Azure.**   
**Categoria**: problema   

**Descrição**   
As tabelas ou colunas nomeadas a seguir foram detectadas. Sequências, introduzidas em Microsoft SQL Server, usam a função ANSI padrão NEXT VALUE FOR. Se uma tabela ou coluna for nomeada NEXT, e a coluna tiver um alias VALUE, e se o padrão ANSI AS for omitido, a instrução resultante poderá causar um erro.  


**Recomendação**   
Reescreva instruções para incluir a palavra-chave AS do padrão ANSI no alias de uma tabela ou coluna. Por exemplo, quando uma coluna é denominada NEXT e essa coluna tem um alias como VALUE, a consulta `SELECT NEXT VALUE FROM TABLE` causará um erro e deverá ser reescrita como SELECT a seguir como o valor da tabela. Da mesma forma, quando uma tabela denominada NEXT e essa tabela tiver um alias como valor, a consulta `SELECT Col1 FROM NEXT VALUE` causará um erro e deverá ser reescrita como `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Título: chamadas RAISERROR de estilo herdado devem ser substituídas por equivalentes modernos.**   
**Categoria**: aviso   

**Descrição**   
As chamadas RAISERROR como o exemplo abaixo são denominadas como estilo herdado porque não incluem as vírgulas e os parênteses. `RAISERROR 50001 'this is a test'`. Esse método de chamar RAISERROR é descontinuado e removido no banco de dados SQL do Azure. 


**Recomendação**   
Reescreva a instrução usando a sintaxe RAISERROR atual ou avalie se a abordagem moderna do `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` é viável.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Auditorias de servidor<a id="ServerAudits"></a>

**Título: usar os recursos de auditoria do banco de dados SQL do Azure para substituir auditorias de servidor**   
**Categoria**: aviso   

**Descrição**   
Não há suporte para auditorias de servidor no banco de dados SQL do Azure.


**Recomendação**   
Considere os recursos de auditoria do banco de dados SQL do Azure para substituir auditorias de servidor.  O SQL do Azure dá suporte à auditoria e os recursos são mais ricos que SQL Server. O banco de dados SQL do Azure pode auditar várias ações e eventos de banco de dados, incluindo: acesso a data, alterações de esquema (DDL), alterações de dados (DML), contas, funções e permissões (DCL, exceções de segurança. A auditoria do banco de dados SQL do Azure aumenta a capacidade de uma organização de obter informações aprofundadas sobre eventos e alterações que ocorrem dentro de seu banco de dados, incluindo atualizações e consultas nos mesmos. Como alternativa, migre para o Azure SQL Instância Gerenciada ou SQL Server na máquina virtual do Azure.

Mais informações: [auditoria do banco de dados SQL do Azure ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Credenciais do servidor<a id="ServerCredentials"></a>

**Título: não há suporte para credencial no escopo do servidor no banco de dados SQL do Azure**   
**Categoria**: aviso   

**Descrição**   
Uma credencial é um registro que contém as informações de autenticação (credenciais) necessárias para se conectar a um recurso fora do SQL Server. O banco de dados SQL do Azure dá suporte a credenciais de banco de dados, mas não às criadas no escopo de SQL Server.   


**Recomendação**   
O banco de dados SQL do Azure suporta credenciais no escopo do banco de dados. Converta as credenciais no escopo do servidor em credenciais no escopo do banco de dados. Como alternativa, migre para o Azure SQL Instância Gerenciada ou SQL Server na máquina virtual do Azure

Mais informações: [criando credencial no escopo do banco de dados](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Agente de Serviço<a id="ServiceBroker"></a>

**Título: não há suporte para o recurso Service Broker no banco de dados SQL do Azure**   
**Categoria**: problema   

**Descrição**   
O SQL Server Service Broker fornece suporte nativo para aplicativos de mensagens e enfileiramento no Mecanismo de Banco de Dados de SQL Server. Não há suporte para Service Broker recurso no banco de dados SQL do Azure.


**Recomendação**   
Não há suporte para Service Broker recurso no banco de dados SQL do Azure. Considere a migração para o Azure SQL Instância Gerenciada que dá suporte ao Service Broker na mesma instância. Como alternativa, migre para o SQL Server na máquina virtual do Azure. 

## <a name="server-scoped-triggers"></a>Gatilhos no escopo do servidor<a id="ServerScopedTriggers"></a>

**Título: o gatilho no escopo do servidor não tem suporte no banco de dados SQL do Azure**   
**Categoria**: aviso   

**Descrição**   
Um gatilho é um tipo especial de procedimento armazenado que é executado em resposta a determinada ação em uma tabela, como inserção, exclusão ou atualização de dados. Não há suporte para gatilhos no escopo do servidor no banco de dados SQL do Azure. O banco de dados SQL do Azure não oferece suporte às seguintes opções para gatilhos: para LOGON, criptografia, com acréscimo, não para replicação, opção de nome externo (não há suporte de método externo), todas as opções de servidor (gatilho DDL), gatilho em um evento de LOGON (gatilho de logon), o banco de dados SQL do Azure não oferece suporte a gatilhos CLR.


**Recomendação**   
Use o gatilho de nível de banco de dados. Como alternativa, migre para o Azure SQL Instância Gerenciada ou SQL Server na máquina virtual do Azure

Mais informações: [resolvendo diferenças de Transact-SQL durante a migração para o banco de dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>Trabalhos do SQL Agent<a id="AgentJobs"></a>

**Título: SQL Server Agent trabalhos não estão disponíveis no banco de dados SQL do Azure**   
**Categoria**: aviso   

**Descrição**   
SQL Server Agent é um serviço do Microsoft Windows que executa tarefas administrativas agendadas, que são chamadas de trabalhos no SQL Server. SQL Server Agent trabalhos não estão disponíveis no banco de dados SQL do Azure. 


**Recomendação** Use trabalhos elásticos (versão prévia), que são a substituição para trabalhos de SQL Server Agent no banco de dados SQL do Azure. Trabalhos de Banco de Dados Elástico para o banco de dados SQL do Azure permitem que você execute com confiança scripts T-SQL que abrangem vários bancos de dados enquanto tentam novamente e fornecem garantias de conclusão eventual. Alternativamente, considere migrar para o Azure SQL Instância Gerenciada ou SQL Server em máquinas virtuais do Azure.

Mais informações: [introdução ao trabalhos de banco de dados elástico (versão prévia) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>Tamanho do banco de dados SQL<a id="SQLDBDatabaseSize"></a>

**Título: o banco de dados SQL do Azure não dá suporte ao tamanho de banco de dados maior que 100 TB.**   
**Categoria**: problema   

**Descrição**   
O tamanho do banco de dados é maior que o tamanho máximo com suporte de 100 TB. 


**Recomendação**   
Avalie se os dados podem ser arquivados ou compactados ou fragmentados em vários bancos de dado. Como alternativa, migre para o SQL Server na máquina virtual do Azure. 

Mais informações: [limites de recursos vCore](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Título: o SQL mail foi descontinuado.**   
**Categoria**: aviso   

**Descrição**   
O SQL mail foi descontinuado e removido no banco de dados SQL do Azure.


**Recomendação**   
Considere migrar para o Azure SQL Instância Gerenciada ou SQL Server em máquinas virtuais do Azure e usar Database Mail.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Título: instruções detectadas que referenciam procedimentos armazenados do sistema removidos que não estão disponíveis no banco de dados SQL do Azure.**   
**Categoria**: aviso   

**Descrição**   
O sistema sem suporte e os procedimentos armazenados estendidos a seguir não podem ser usados no banco de dados SQL do Azure-,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` , `sp_activedirectory_start` .


**Recomendação**    
Remova as referências a procedimentos de sistema sem suporte que foram removidos no banco de dados SQL do Azure.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Sinalizadores de rastreamento<a id="TraceFlags"></a>

**Título: o banco de dados SQL do Azure não dá suporte a sinalizadores de rastreamento**   
**Categoria**: aviso   

**Descrição**   
Os sinalizadores de rastreamento são usados para definir temporariamente características de servidor específicas ou para desativar um determinado comportamento. Os sinalizadores de rastreamento são usados com frequência para diagnosticar problemas de desempenho ou depurar procedimentos armazenados ou sistemas de computador complexos. O banco de dados SQL do Azure não oferece suporte a sinalizadores de rastreamento. 


**Recomendação**   
Examine a seção de objetos afetados no migrações para Azure para ver todos os sinalizadores de rastreamento que não têm suporte no banco de dados SQL do Azure e avalie se eles podem ser removidos. Como alternativa, migre para o SQL do Azure Instância Gerenciada que dá suporte ao número limitado de sinalizadores de rastreamento global ou SQL Server na máquina virtual do Azure.

Mais informações: [resolvendo diferenças de Transact-SQL durante a migração para o banco de dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Autenticação do Windows<a id="WindowsAuthentication"></a>

**Título: não há suporte para usuários de banco de dados mapeados com autenticação do Windows (segurança integrada) no banco de dados SQL do Azure.**   
**Categoria**: aviso   

**Descrição**   
O banco de dados SQL do Azure dá suporte a dois tipos de autenticação 
- Autenticação do SQL: usa um nome de usuário e senha 
- Autenticação de Azure Active Directory: usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. 

Os usuários de banco de dados mapeados com autenticação do Windows (segurança integrada) não têm suporte no banco de dados SQL do Azure. 



**Recomendação**   
Federar o Active Directory local com Azure Active Directory. A identidade do Windows pode ser substituída pelo equivalente Azure Active Directory identidades. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [recursos de segurança do banco de dados SQL](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Título: não há suporte para xp_cmdshell no banco de dados SQL do Azure.**   
**Categoria**: problema   

**Descrição**   
xp_cmdshell que gera um shell de comando do Windows e passa uma cadeia de caracteres para execução não tem suporte no banco de dados SQL do Azure. 


**Recomendação**   
Examine a seção de objetos afetados em migrações para Azure para ver todos os objetos que usam xp_cmdshell e avaliar se a referência a xp_cmdshell ou ao objeto afetado pode ser removida. Considere também explorar a automação do Azure que fornece automação baseada em nuvem e serviço de configuração. Como alternativa, migre para o SQL Server na máquina virtual do Azure. 

## <a name="next-steps"></a>Próximas etapas

Para iniciar a migração de seu SQL Server para o banco de dados SQL do Azure, consulte o [Guia de migração do SQL Server para o banco de dados SQL](sql-server-to-sql-database-guide.md).

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o banco de dados SQL, consulte:
   - [Visão geral do banco de dados SQL do Azure](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).

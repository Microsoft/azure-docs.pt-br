---
title: Regras de avaliação para SQL Server a migração do SQL Instância Gerenciada
description: Regras de avaliação para identificar problemas com a instância de SQL Server de origem que deve ser resolvida antes de migrar para o SQL Instância Gerenciada do Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054435"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>Regras de avaliação para SQL Server a migração do SQL Instância Gerenciada
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

As ferramentas de migração validam sua instância de SQL Server de origem executando várias regras de avaliação para identificar problemas que devem ser resolvidos antes de migrar seu banco de dados SQL Server para o Instância Gerenciada SQL do Azure. 

Este artigo fornece uma lista das regras usadas para avaliar a viabilidade de migrar seu banco de dados SQL Server para o SQL Instância Gerenciada do Azure. 

## <a name="analysiscommand-job"></a>Trabalho do AnalysisCommand<a id="AnalysisCommandJob"></a>

**Título: não há suporte para a etapa de trabalho AnalysisCommand no Azure SQL Instância Gerenciada.**   
**Categoria**: aviso   


**Descrição**   
É uma etapa de trabalho que executa um comando Analysis Services. Não há suporte para a etapa de trabalho AnalysisCommand no Azure SQL Instância Gerenciada.

**Recomendação**     
Examine a seção de objetos afetados no migrações para Azure para ver todos os trabalhos usando a etapa de trabalho de comando do Analysis Service e avalie se a etapa de trabalho ou o objeto afetado pode ser removido. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [SQL Server Agent diferenças no SQL do Azure instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>Trabalho do AnalysisQuery<a id="AnalysisQueryJob"></a>

**Título: não há suporte para a etapa de trabalho AnalysisQuery no Azure SQL Instância Gerenciada.**   
**Categoria**: aviso   

**Descrição**   
É uma etapa de trabalho que executa uma consulta Analysis Services. Não há suporte para a etapa de trabalho AnalysisQuery no Azure SQL Instância Gerenciada.


**Recomendação**   
Examine a seção de objetos afetados no migrações para Azure para ver todos os trabalhos usando a etapa de trabalho de consulta do Analysis Service e avalie se a etapa de trabalho ou o objeto afetado pode ser removido. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [SQL Server Agent diferenças no SQL do Azure instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Assembly do arquivo<a id="AssemblyFromFile"></a>

**Título: ' CREATE ASSEMBLY ' e ' ALTER ASSEMBLY ' com um parâmetro de arquivo não têm suporte no Azure SQL Instância Gerenciada.**   
**Categoria**: aviso   

**Descrição**   
O Azure SQL Instância Gerenciada não pode acessar compartilhamentos de arquivos ou pastas do Windows. Consulte a seção "objetos afetados" para ver os usos específicos de BULK INSERT instruções que não fazem referência a um blob do Azure. Objetos com ' BULK INSERT ' em que a origem não é o armazenamento de BLOBs do Azure não funcionarão após a migração para o SQL do Azure Instância Gerenciada.


**Recomendação**   
Você precisará converter BULK INSERT instruções que usam arquivos locais ou compartilhamentos de arquivos para usar arquivos do armazenamento de BLOBs do Azure em vez disso, ao migrar para o SQL do Azure Instância Gerenciada. Como alternativa, migre para o SQL Server na máquina virtual do Azure. 

Mais informações: [diferenças de CLR no Azure SQL instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Inserção em massa<a id="BulkInsert"></a>

**Título: não há suporte para BULK INSERT com fonte de dados de BLOB não Azure no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
O Azure SQL Instância Gerenciada não pode acessar compartilhamentos de arquivos ou pastas do Windows. Consulte a seção "objetos afetados" para ver os usos específicos de BULK INSERT instruções que não fazem referência a um blob do Azure. Objetos com ' BULK INSERT ' em que a origem não é o armazenamento de BLOBs do Azure não funcionarão após a migração para o SQL do Azure Instância Gerenciada.


**Recomendação**   
Você precisará converter BULK INSERT instruções que usam arquivos locais ou compartilhamentos de arquivos para usar arquivos do armazenamento de BLOBs do Azure em vez disso, ao migrar para o SQL do Azure Instância Gerenciada.

Mais informações: [diferenças em massa de inserção e OPENROWSET no Azure SQL instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>Segurança do CLR<a id="ClrStrictSecurity"></a>

**Título: os assemblies CLR marcados como seguros ou EXTERNAL_ACCESS são considerados inseguros**   
**Categoria**: problema   

**Descrição**   
O modo de segurança estrito do CLR é imposto no Azure SQL Instância Gerenciada. Esse modo é habilitado por padrão e apresenta alterações significativas para bancos de dados que contêm assemblies CLR definidos pelo usuário marcados como seguro ou EXTERNAL_ACCESS.


**Recomendação**   
O CLR usa o CAS (Segurança de Acesso do Código) no .NET Framework, para o qual não há mais suporte como um limite de segurança. Começando com o mecanismo de banco de dados SQL Server 2017 (14. x), uma `sp_configure` opção chamada CLR Strict Security é introduzida para aprimorar a segurança dos assemblies do CLR. A segurança estrita do CLR é habilitada por padrão e trata assemblies de CLR seguros e EXTERNAL_ACCESS como se estivessem marcados como inseguros. Quando a segurança estrita do CLR está desabilitada, um assembly CLR criado com PERMISSION_SET = SAFE pode ser capaz de acessar recursos do sistema externo, chamar código não gerenciado e adquirir privilégios de sysadmin. Depois de habilitar segurança estrita, os assemblies que não estão assinados não serão carregados. Além disso, se um banco de dados tiver assemblies seguros ou EXTERNAL_ACCESS, as instruções RESTOre ou ATTACH DATABASE poderão ser concluídas, mas os assemblies poderão falhar ao serem carregados. Para carregar os assemblies, você deve alterar ou remover e recriar cada assembly para que ele seja assinado com um certificado ou chave assimétrica que tenha um logon correspondente com a permissão de ASSEMBLY insegura no servidor.

Mais informações: [segurança estrita do CLR](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Cláusula COMPUTE<a id="ComputeClause"></a>

**Título: a cláusula COMPUTE foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
A cláusula COMPUTE gera totais que aparecem como colunas de resumo adicionais no final do conjunto de resultados. No entanto, essa cláusula não tem mais suporte no Azure SQL Instância Gerenciada.



**Recomendação**   
Em vez disso, o módulo T-SQL precisa ser reescrito usando o operador de acúmulo. O código a seguir demonstra como a computação pode ser substituída por ROLLUP: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Provedor criptográfico<a id="CryptographicProvider"></a>

**Título: é encontrado um uso do provedor de criptografia de criação ou ALTER CRYPTOGRAPHIC, que não tem suporte no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
O Azure SQL Instância Gerenciada não dá suporte a instruções de provedor CRIPTOGRÁFICO porque ele não pode acessar arquivos. Consulte a seção objetos afetados para usos específicos de instruções do provedor CRIPTOGRÁFICO. Os objetos com ' criar provedor CRIPTOGRÁFICO ' ou ' alterar provedor de criptografia ' não funcionarão corretamente após a migração para o Azure SQL Instância Gerenciada.


**Recomendação**   
Examine os objetos com ' criar provedor CRIPTOGRÁFICO ' ou ' alterar provedor CRIPTOGRÁFICO '. Em qualquer um desses objetos necessários, remova os usos desses recursos. Como alternativa, migre para o SQL Server na máquina virtual do Azure. 

Mais informações: [diferenças de provedor criptográfico no Azure SQL instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Compatibilidade de banco de dados<a id="DbCompatLevelLowerThan100"></a>

**Título: o nível de compatibilidade do banco de dados abaixo de 100 não é suportado**   
**Categoria**: aviso   

**Descrição**   
O nível de compatibilidade do banco de dados é uma ferramenta valiosa para ajudar na modernização do banco de dados, permitindo que o Mecanismo de Banco de Dados de SQL Server seja atualizado, ao mesmo tempo em que mantém o status funcional dos aplicativos de conexão mantendo o mesmo nível de compatibilidade do banco de dados de pré-atualização. O Azure SQL Instância Gerenciada não dá suporte aos níveis de compatibilidade abaixo de 100. Quando o banco de dados com nível de compatibilidade abaixo de 100 for restaurado no Azure SQL Instância Gerenciada, o nível de compatibilidade será atualizado para 100. 


**Recomendação**... Avalie se a funcionalidade do aplicativo está intacta quando o nível de compatibilidade do banco de dados é atualizado para 100 no Azure SQL Instância Gerenciada. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [níveis de compatibilidade com suporte no Azure SQL instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Alias de entidade do banco de dados<a id="DatabasePrincipalAlias"></a>

**Título: SYS. O DATABASE_PRINCIPAL_ALIASES foi descontinuado e foi removido.**   
**Categoria**: problema   

**Descrição**   
Sistema. O DATABASE_PRINCIPAL_ALIASES foi descontinuado e foi removido no Instância Gerenciada do SQL do Azure.


**Recomendação**   
Use funções em vez de aliases.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>Opção DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Título: a opção SET DISABLE_DEF_CNST_CHK foi descontinuada e foi removida.**   
**Categoria**: problema   

**Descrição**   
A opção SET DISABLE_DEF_CNST_CHK foi descontinuada e foi removida no Azure SQL Instância Gerenciada.


Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Dica FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Título: a dica de consulta FASTFIRSTROW foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
A dica de consulta FASTFIRSTROW foi descontinuada e foi removida no Azure SQL Instância Gerenciada.


**Recomendação**   
Em vez da opção de uso de dica de consulta FASTFIRSTROW (FAST n).

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Título: FileStream e Filetable não têm suporte no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
O recurso FileStream, que permite armazenar dados não estruturados, como documentos de texto, imagens e vídeos no sistema de arquivos NTFS, não tem suporte no Azure SQL Instância Gerenciada. **Este banco de dados não pode ser migrado porque o backup que contém grupos de dados FILESTREAM não pode ser restaurado no Azure SQL Instância Gerenciada.**


**Recomendação**   
Carregue os arquivos não estruturados no armazenamento de BLOBs do Azure e armazene os metadados relacionados a esses arquivos (nome, tipo, local da URL, chave de armazenamento etc.) no Azure SQL Instância Gerenciada. Talvez seja necessário refazer a engenharia de seu aplicativo para habilitar os blobs de streaming de e para o Azure SQL Instância Gerenciada. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [streaming de blobs de e para SQL Azure blog](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>MS DTC heterogêneo<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Título: não há suporte para iniciar transação DISTRIBUÍda com o servidor remoto não SQL Server no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
A transação distribuída iniciada pelo Transact SQL iniciar transação DISTRIBUÍda e gerenciada pelo Microsoft Coordenador de Transações Distribuídas (MS DTC) não tem suporte no Azure SQL Instância Gerenciada se o servidor remoto não estiver SQL Server. 


**Recomendação**   
Examine a seção de objetos afetados em migrações para Azure para ver todos os objetos usando BEGIN nos TRANSACTION. Considere migrar os bancos de dados de participante para o Azure SQL Instância Gerenciada em que as transações distribuídas entre várias instâncias têm suporte (atualmente em visualização). Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [transações em vários servidores para o Azure SQL instância gerenciada ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>MS DTC homogêneo<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Título: Iniciar transação DISTRIBUÍda tem suporte em vários servidores para o Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
A transação distribuída iniciada pelo Transact SQL inicia a transação DISTRIBUÍda e gerenciada pelo Microsoft Coordenador de Transações Distribuídas (MS DTC) tem suporte em vários servidores para o SQL Instância Gerenciada do Azure.


**Recomendação**   
Examine a seção de objetos afetados em migrações para Azure para ver todos os objetos usando BEGIN nos TRANSACTION. Considere migrar os bancos de dados de participante para o Azure SQL Instância Gerenciada em que as transações distribuídas entre várias instâncias têm suporte (atualmente em visualização). Como alternativa, migre para o SQL Server na máquina virtual do Azure. 

Mais informações: [transações em vários servidores para o Azure SQL instância gerenciada](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Servidor vinculado (provedor não SQL)<a id="LinkedServerWithNonSQLProvider"></a>

**Título: não há suporte para o servidor vinculado com provedor não SQL Server no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
Os servidores vinculados permitem que o SQL Server Mecanismo de Banco de Dados execute comandos em OLE DB fontes de dados fora da instância do SQL Server. Não há suporte para servidor vinculado com provedor não SQL Server no Azure SQL Instância Gerenciada. 


**Recomendação**   
O Azure SQL Instância Gerenciada não oferece suporte à funcionalidade de servidor vinculado se o provedor do servidor remoto não for SQL Server como Oracle, Sybase, etc. 

As ações a seguir são recomendadas para eliminar a necessidade de servidores vinculados: 
- Identifique os bancos de dados dependentes de servidores remotos não SQL e considere movê-los para o banco de dados que está sendo migrado. 
- Migre os bancos de dados dependentes para destinos com suporte como SQL Instância Gerenciada, banco de dados SQL, Azure Synapse SQL e instâncias de SQL Server. 
- Considere criar um servidor vinculado entre o Azure SQL Instância Gerenciada e o SQL Server na máquina virtual do Azure (VM do SQL).  Em seguida, da VM do SQL, crie um servidor vinculado para Oracle, Sybase, etc. Essa abordagem envolve dois saltos, mas pode ser usada como solução alternativa temporária.  
- Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [diferenças de servidor vinculado no Azure SQL instância gerenciada](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Trabalho de mesclagem<a id="MergeJob"></a>

**Título: não há suporte para a etapa de trabalho de mesclagem no Azure SQL Instância Gerenciada.**   
**Categoria**: aviso   

**Descrição**   
É uma etapa de trabalho que ativa o Agente de Mesclagem de replicação. O Agente de Mesclagem de Replicação é um executável utilitário que aplica o instantâneo inicial contido nas tabelas do banco de dados aos Assinantes. Ele também mescla as alterações incrementais de dados que ocorreram no Publicador depois que o instantâneo inicial foi criado e reconcilia conflitos de acordo com as regras que você configura ou usando um resolvedor personalizado que você cria. Não há suporte para a etapa de trabalho de mesclagem no Azure SQL Instância Gerenciada.


**Recomendação**   
Examine a seção de objetos afetados no migrações para Azure para ver todos os trabalhos usando a etapa de trabalho de mesclagem e avalie se a etapa de trabalho ou o objeto afetado pode ser removido. Como alternativa, migre para o SQL Server na máquina virtual do Azure

Mais informações: [SQL Server Agent diferenças no SQL do Azure instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>Tamanho do banco de dados MI<a id="MIDatabaseSize<"></a>

**Título: o Azure SQL Instância Gerenciada não dá suporte ao tamanho do banco de dados maior que 8 TB.**   
**Categoria**: problema   

**Descrição**   
O tamanho do banco de dados é maior do que o armazenamento reservado de instância máximo. **Este banco de dados não pode ser selecionado para migração, pois o tamanho excedeu o limite permitido.**


**Recomendação**   
Avalie se os dados podem ser arquivados compactados ou fragmentados em vários bancos de dado. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [características de geração de hardware do Azure SQL instância gerenciada ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Tamanho da instância MI<a id="MIInstanceSize<"></a>

**Título: o tamanho máximo do armazenamento de instância no Azure SQL Instância Gerenciada não pode ser maior que 8 TB.**   
**Categoria**: aviso   

**Descrição**   
O tamanho de todos os bancos de dados é maior do que o armazenamento reservado de instância máximo.  


**Recomendação**   
Considere migrar os bancos de dados para diferentes instâncias gerenciadas do Azure SQL ou para SQL Server na máquina virtual do Azure se todos os bancos de dados precisarem existir na mesma instância. 

Mais informações: [características de geração de hardware do Azure SQL instância gerenciada ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Vários arquivos de log<a id="MultipleLogFiles<"></a>

**Título: o Azure SQL Instância Gerenciada não dá suporte a vários arquivos de log.**   
**Categoria**: problema   

**Descrição**   
SQL Server permite que um banco de dados faça logon em vários arquivos. Este banco de dados tem vários arquivos de log que não têm suporte no Azure SQL Instância Gerenciada. * * Este banco de dados não pode ser migrado, pois o backup não pode ser restaurado no Azure SQL Instância Gerenciada. 
**

**Recomendação**   
O Azure SQL Instância Gerenciada dá suporte apenas a um único log por banco de dados. Você precisa excluir todos, exceto um dos arquivos de log, antes de migrar este banco de dados para o Azure: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Mais informações: [Opções de banco de dados sem suporte no Azure SQL instância gerenciada  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Próxima coluna<a id="NextColumn"></a>

**Título: as tabelas e colunas nomeadas a seguir resultarão em um erro no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
As tabelas ou colunas nomeadas a seguir foram detectadas. Sequências, introduzidas em Microsoft SQL Server, usam a função ANSI padrão NEXT VALUE FOR. Se uma tabela ou coluna for nomeada NEXT, e a coluna tiver um alias VALUE, e se o padrão ANSI AS for omitido, a instrução resultante poderá causar um erro.


**Recomendação**   
Reescreva instruções para incluir a palavra-chave AS do padrão ANSI no alias de uma tabela ou coluna. Por exemplo, quando uma coluna é nomeada em seguida e essa coluna é alias como valor, a consulta Selecionar próximo valor da tabela causará um erro e deverá ser reescrita como selecionar avançar como valor da tabela. Da mesma forma, quando uma tabela denominada NEXT e essa tabela tiver um alias como VALUE, a consulta selecionar col1 a partir do próximo valor causará um erro e deverá ser reescrita como SELECT Col1 a partir do valor NEXT AS.



## <a name="non-ansi-style-left-outer-join"></a>Junção externa esquerda do estilo não-ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Título: a junção externa esquerda do estilo não-ANSI foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
A junção externa esquerda do estilo não-ANSI foi descontinuada e foi removida no Azure SQL Instância Gerenciada. 


**Recomendação**   
Use a sintaxe de junção ANSI.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Junção externa direita do estilo não-ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Título: a junção externa direita do estilo não-ANSI foi descontinuada e foi removida.**   
**Categoria**: aviso   

**Descrição**   
O estilo não-ANSI junção externa direita foi descontinuado e foi removido no Azure SQL Instância Gerenciada. 



Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Recomendação**   
Use a sintaxe de junção ANSI.

## <a name="databases-exceed-100"></a>Os bancos de dados excedem 100 <a id="NumDbExceeds100"></a>

**Título: o Azure SQL Instância Gerenciada dá suporte a um máximo de 100 bancos de dados por instância.**   
**Categoria**: aviso   

**Descrição**   
O número máximo de bancos de dados com suporte no Azure SQL Instância Gerenciada é 100, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido.



**Recomendação**   
Considere migrar os bancos de dados para diferentes instâncias gerenciadas do Azure SQL ou para SQL Server na máquina virtual do Azure se todos os bancos de dados precisarem existir na mesma instância. 

Mais informações: [limites de recursos do SQL instância gerenciada do Azure ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (fonte de dados não BLOB)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Título: OpenRowSet usado em operação em massa com fonte de dados de armazenamento de BLOBs não Azure não tem suporte no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
O OPENROWSET dá suporte a operações em massa por meio de um provedor em massa interno que permite que dados de um arquivo sejam lidos e retornados como um conjunto de linhas. Não há suporte para OPENROWSET com fonte de dados de armazenamento de BLOBs não Azure no Azure SQL Instância Gerenciada. 



**Recomendação**   
A função OPENROWSET pode ser usada para executar consultas somente em instâncias de SQL Server (gerenciadas, locais ou em máquinas virtuais). Somente os valores SQLNCLI, SQLNCLI11 e SQLOLEDB têm suporte como provedor. Portanto, a ação de recomendação é identificar os bancos de dados dependentes de servidores remotos não SQL e considerar movê-los para o banco de dados que está sendo migrado. Como alternativa, migre para o SQL Server na máquina virtual do Azure

Mais informações: [diferenças em massa de inserção e OPENROWSET no Azure SQL instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (provedor não SQL)<a id="OpenRowsetWithNonSQLProvider"></a>

**Título: OpenRowSet com o provedor não SQL não tem suporte no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   

**Descrição**   
Este método é uma alternativa para acessar tabelas em um servidor vinculado e se trata de um método de uso único e ad hoc para conexão e acesso a dados remotos por meio de OLE DB. Não há suporte para OpenRowSet com provedor não SQL no Azure SQL Instância Gerenciada. 



**Recomendação**   
A função OPENROWSET pode ser usada para executar consultas somente em instâncias de SQL Server (gerenciadas, locais ou em máquinas virtuais). Somente os valores SQLNCLI, SQLNCLI11 e SQLOLEDB têm suporte como provedor. Portanto, a ação de recomendação é identificar os bancos de dados dependentes de servidores remotos não SQL e considerar movê-los para o banco de dados que está sendo migrado.

Mais informações: [diferenças em massa de inserção e OPENROWSET no Azure SQL instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Trabalho do PowerShell<a id="PowerShellJob"></a>

**Título: não há suporte para a etapa de trabalho do PowerShell no Azure SQL Instância Gerenciada.**   
**Categoria**: aviso   

**Descrição**   
É uma etapa de trabalho que executa um script do PowerShell. Não há suporte para a etapa de trabalho do PowerShell no Azure SQL Instância Gerenciada. 



**Recomendação**   
Examine a seção de objetos afetados no migrações para Azure para ver todos os trabalhos usando a etapa de trabalho do PowerShell e avalie se a etapa de trabalho ou o objeto afetado pode ser removido.  Avalie se a automação do Azure pode ser usada. Como alternativa, migre para o SQL Server na máquina virtual do Azure

Mais informações: [SQL Server Agent diferenças no SQL do Azure instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Trabalho do Queue Reader<a id="QueueReaderJob"></a>

**Título: a etapa de trabalho do Queue Reader não tem suporte no Azure SQL Instância Gerenciada.**   
**Categoria**: aviso   

**Descrição**   
É uma etapa de trabalho que ativa o Queue Reader Agent de replicação. O Queue Reader Agent de replicação é um executável que lê as mensagens armazenadas em uma fila Microsoft SQL Server ou uma fila de mensagens da Microsoft e, em seguida, aplica essas mensagens ao Publicador. O Queue Reader Agent é usado com publicações de instantâneo e transacionais que permitem atualização em fila. Não há suporte para a etapa de trabalho do Queue Reader no Azure SQL Instância Gerenciada.


**Recomendação**   
Examine a seção de objetos afetados no migrações para Azure para ver todos os trabalhos usando a etapa de trabalho de leitor de fila e avalie se a etapa de trabalho ou o objeto afetado pode ser removido. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [SQL Server Agent diferenças no SQL do Azure instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Título: chamadas RAISERROR de estilo herdado devem ser substituídas por equivalentes modernos.**   
**Categoria**: aviso   

**Descrição**   
As chamadas RAISERROR como o exemplo abaixo são denominadas como estilo herdado porque não incluem as vírgulas e os parênteses. RAISERROR 50001 ' Este é um teste '. Esse método de chamar RAISERROR é descontinuado e removido no Azure SQL Instância Gerenciada.



**Recomendação**   
Reescreva a instrução usando a sintaxe RAISERROR atual ou avalie se a abordagem moderna do `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` é viável.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**Título: o recurso Service Broker tem suporte parcial no Instância Gerenciada SQL do Azure.**   
**Categoria**: problema   

**Descrição**   
O SQL Server Service Broker fornece suporte nativo para aplicativos de mensagens e enfileiramento no Mecanismo de Banco de Dados de SQL Server. Este banco de dados tem Service Broker de instância cruzada habilitada, o que não tem suporte no Azure SQL Instância Gerenciada. 


**Recomendação**   
O Azure SQL Instância Gerenciada não dá suporte ao Service Broker de instância cruzada, ou seja, onde o endereço não é local. Você precisa desabilitar Service Broker usando o comando a seguir antes de migrar este banco de dados para o Azure: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Além disso, talvez você também precise remover ou parar o ponto de extremidade Service Broker para evitar que as mensagens cheguem na instância do SQL. Depois que o banco de dados tiver sido migrado para o Azure, você poderá examinar a funcionalidade do barramento de serviço do Azure para implementar um sistema de mensagens genérico baseado em nuvem em vez de Service Broker. Como alternativa, migre para o SQL Server na máquina virtual do Azure. 

Mais informações: [Service Broker diferenças no SQL do Azure instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Título: o SQL mail foi descontinuado.**   
**Categoria**: aviso   


**Descrição**   
O SQL mail foi descontinuado e removido no Azure SQL Instância Gerenciada.



**Recomendação**   
Use o Database Mail.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Título: instruções detectadas que referenciam procedimentos armazenados do sistema removidos que não estão disponíveis no Azure SQL Instância Gerenciada.**   
**Categoria**: aviso   

**Descrição**   
O sistema sem suporte e os procedimentos armazenados estendidos a seguir não podem ser usados no Azure SQL instância gerenciada-,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` e `sp_activedirectory_start` . 




**Recomendação**   
Remova as referências a procedimentos de sistema sem suporte que foram removidos no Azure SQL Instância Gerenciada.

Mais informações: [funcionalidade de mecanismo de banco de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Trabalho Transact-SQL<a id="TransactSqlJob"></a>

**Título: a etapa de trabalho TSQL inclui comandos sem suporte no Azure SQL Instância Gerenciada**   
**Categoria**: aviso   


**Descrição**   
É uma etapa de trabalho que executa scripts TSQL no horário agendado. A etapa de trabalho TSQL inclui comandos sem suporte que não têm suporte no Azure SQL Instância Gerenciada.



**Recomendação**   
Examine a seção de objetos afetados em migrações para Azure para ver todos os trabalhos que incluem comandos sem suporte no Azure SQL Instância Gerenciada e avalie se a etapa de trabalho ou o objeto afetado pode ser removido. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [SQL Server Agent diferenças no SQL do Azure instância gerenciada ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Sinalizadores de rastreamento<a id="TraceFlags"></a>

**Título: sinalizadores de rastreamento sem suporte no Azure SQL Instância Gerenciada foram encontrados**   
**Categoria**: aviso   


**Descrição**   
O Azure SQL Instância Gerenciada dá suporte apenas ao número limitado de sinalizadores de rastreamento globais. Não há suporte para sinalizadores de rastreamento de sessão.



**Recomendação**   
Examine a seção de objetos afetados em migrações para Azure para ver todos os sinalizadores de rastreamento que não têm suporte no Azure SQL Instância Gerenciada e avalie se eles podem ser removidos. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [sinalizadores de rastreamento](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Autenticação do Windows<a id="WindowsAuthentication"></a>

**Título: os usuários de banco de dados mapeados com autenticação do Windows (segurança integrada) não têm suporte no Azure SQL Instância Gerenciada**   
**Categoria**: aviso   


**Descrição**   
O Azure SQL Instância Gerenciada dá suporte a dois tipos de autenticação: 
- Autenticação do SQL, que usa um nome de usuário e senha
- Autenticação do Azure Active Directory, que usa identidades gerenciadas pelo Azure Active Directory e que tem suporte para domínios gerenciados e integrados. 

Os usuários de banco de dados mapeados com autenticação do Windows (segurança integrada) não têm suporte no Azure SQL Instância Gerenciada. 


**Recomendação**   
Federar o Active Directory local com Azure Active Directory. A identidade do Windows pode ser substituída pelo equivalente Azure Active Directory identidades. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [recursos de segurança do SQL instância gerenciada](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Título: não há suporte para xp_cmdshell no Azure SQL Instância Gerenciada.**   
**Categoria**: problema   


**Descrição**   
Xp_cmdshell que gera um shell de comando do Windows e passa uma cadeia de caracteres para execução não tem suporte no Azure SQL Instância Gerenciada. 



**Recomendação**   
Examine a seção de objetos afetados em migrações para Azure para ver todos os objetos que usam xp_cmdshell e avaliar se a referência a xp_cmdshell ou ao objeto afetado pode ser removida. Considere explorar a automação do Azure que fornece automação baseada em nuvem e serviço de configuração. Como alternativa, migre para o SQL Server na máquina virtual do Azure.

Mais informações: [diferenças de procedimento armazenado no Azure SQL instância gerenciada](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Próximas etapas

Para iniciar a migração de seu SQL Server para o SQL Instância Gerenciada do Azure, consulte o [SQL Server guia de migração de instância gerenciada do SQL](sql-server-to-managed-instance-guide.md).

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Azure SQL Instância Gerenciada consulte:
   - [Camadas de serviço no Azure SQL Instância Gerenciada](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferenças entre SQL Server e SQL Instância Gerenciada do Azure](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).
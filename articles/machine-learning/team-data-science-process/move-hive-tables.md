---
title: Criar tabelas do Hive e carregar dados do Armazenamento de Blobs – Processo de Ciência de Dados da Equipe
description: Use consultas do Hive para criar tabelas do Hive e carregar dados do Armazenamento de Blobs do Azure. Particione tabelas do Hive e use a formatação ORC (Colunar de Linha Otimizado) para melhorar o desempenho da consulta.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5d61c0f5f26bc46b9c4a5bc4a793df1e10710004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006722"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Criar tabelas do Hive e carregar dados do Armazenamento de Blobs do Azure

Neste artigo, são apresentadas consultas genéricas do Hive que criam tabelas do Hive e carregam dados do armazenamento de blobs do Azure. Também são fornecida algumas orientações sobre o particionamento de tabelas Hive e sobre como usar a formatação ORC (Colunar de Linha Otimizado) para melhorar o desempenho da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criar uma conta do Armazenamento do Microsoft Azure. Se precisar de instruções, consulte [Sobre contas do Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md).
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, confira [Configurar clusters no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Habilitou o acesso remoto para o cluster, conectou-se e abriu o Console de Linha de Comando do Hadoop. Se precisar de instruções, confira [Gerenciar clusters do Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados no armazenamento de blob do Azure
Se você criou uma máquina virtual do Azure seguindo as instruções fornecidas em [Configurar uma máquina virtual do Azure para análise avançada](../../machine-learning/data-science-virtual-machine/overview.md), esse arquivo de script deve ter sido baixado para o diretório *C: \\ Users \\ \<user name\> \\ Documents \\ Data Science scripts* na máquina virtual. Essas consultas de Hive exigem somente que você fornaça seu próprio esquema de dados e que a configuração de armazenamento de blobs do Azure nos campos apropriados esteja pronta para envio.

Supomos que os dados de tabelas Hive estejam em formato de tabela **descompactado** e que os dados foram carregado no contêiner padrão (ou em um adicional) da conta de armazenamento usada pelo cluster do Hadoop.

Se quiser praticar com os **Dados de viagens de táxi de NYC**, você precisará:

* **baixar** os 24 arquivos de [Dados de viagens de táxi de NYC](https://www.andresmh.com/nyctaxitrips) (12 arquivos de viagens e 12 arquivos de tarifas),
* **descompactar** todos os arquivos em arquivos .csv, e
* **carregue**-os para o padrão (ou o contêiner apropriado) da conta do Armazenamento do Microsoft Azure; as opções para essa conta aparecem no tópico [Usar o Armazenamento do Microsoft Azure com clusters do Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). O processo para carregar os arquivos .csv para o contêiner padrão na conta de armazenamento pode ser encontrado nesta [página](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Como enviar consultas de Hive
Consultas de Hive podem ser enviadas usando:

* [Enviar consultas de Hive por meio de Linha de comando do Hadoop no nó principal do cluster Hadoop](#headnode)
* [Enviar consultas de Hive com o Editor de Hive](#hive-editor)
* [Enviar consultas de Hive com comandos do PowerShell do Azure](#ps)

Consultas de Hive são semelhantes ao SQL. Se já estiver familiarizado com o SQL, talvez você considere o [Roteiro para usuários do Hive para SQL](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) útil.

Ao enviar uma consulta de Hive, você também pode controlar o destino da saída de consultas de Hive, seja na tela, para um arquivo local no nó principal ou para um blob do Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Enviar consultas de Hive por meio de Linha de comando do Hadoop no nó principal do cluster Hadoop
Se a consulta é complexa, o envio de consultas de Hive diretamente ao nó principal do cluster Hadoop normalmente leva a um resultado mais rápido do que enviá-la com scripts do Editor de Hive ou do PowerShell do Azure.

Faça logon no nó principal do cluster do Hadoop, abra a Linha de Comando do Hadoop na área de trabalho do nó principal e digite o comando `cd %hive_home%\bin`.

Você tem três maneiras de enviar consultas de Hive na Linha de Comando do Hadoop:

* diretamente
* usando arquivos “.hql”
* com o console de comando do Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Enviar consultas de Hive diretamente na Linha de Comando do Hadoop.
Você pode executar o comando como `hive -e "<your hive query>;` para enviar consultas de Hive simples diretamente na Linha de Comando do Hadoop. Veja um exemplo, no qual a caixa vermelha descreve o comando que envia a consulta Hive e a caixa verde descreve a saída da consulta Hive.

![Comando para enviar a consulta do Hive com a saída](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Enviar consultas de Hive em arquivos “.hql”
Quando a consulta Hive é mais complicada e tem várias linhas, não é prático editar consultas na linha de comando ou no console de comando de Hive. Uma alternativa é usar um editor de texto no nó principal do cluster do Hadoop para salvar as consultas de Hive em um arquivo “.hql” em um diretório local do nó principal. Em seguida, a consulta de Hive no arquivo “,hql” pode ser enviada usando o argumento `-f` da seguinte maneira:

```console
hive -f "<path to the '.hql' file>"
```

![Consulta do Hive em um arquivo “.hql”](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir a impressão da tela de status de progresso de consultas de Hive**

Por padrão, após a consulta Hive ser enviada na Linha de Comando do Hadoop, o andamento do trabalho de Mapear/Reduzir será impresso na tela. Para suprimir a impressão da tela de andamento do trabalho de Mapear/Reduzir, você pode usar um argumento `-S` ("S" em letra maiúscula) na linha de comando da seguinte maneira:

```console
hive -S -f "<path to the '.hql' file>"
hive -S -e "<Hive queries>"
```

#### <a name="submit-hive-queries-in-hive-command-console"></a>Enviar consultas de Hive no console de comando de Hive.
Você também pode entrar primeiro no console de comando de Hive executando o comando `hive` na Linha de Comando do Hadoop e enviar consultas de Hive no console de comando de Hive. Veja um exemplo. Neste exemplo, as duas caixas vermelhas realçam os comandos usados para inserir o console de comando de Hive e a consulta de Hive enviada no console de comando de Hive, respectivamente. A caixa verde realça a saída da consulta de Hive.

![Abra o console de comando do Hive, insira o comando e exiba a saída da consulta do Hive](./media/move-hive-tables/run-hive-queries-2.png)

Os exemplos anteriores mostram os resultados da consulta de Hive diretamente na tela. Você também pode gravar a saída em um arquivo local no nó principal ou em um blob do Azure. Em seguida, você pode usar outras ferramentas para analisar com mais detalhes a saída das consultas de Hive.

**Resultados da consulta de Hive em um arquivo local de saída.**
Para exibir os resultados da consulta de Hive em um diretório local no nó principal, você precisa enviar a consulta de Hive na Linha de Comando do Hadoop da seguinte maneira:

```console
hive -e "<hive query>" > <local path in the head node>
```

No exemplo a seguir, a saída da consulta de Hive é gravada em um arquivo `hivequeryoutput.txt` no diretório `C:\apps\temp`.

![Captura de tela mostra a saída da consulta de Hive em uma janela de linha de comando do Hadoop.](./media/move-hive-tables/output-hive-results-1.png)

**Exportar a saída de consulta de Hive para um blob do Azure**

Você também pode exportar a saída da consulta de Hive para um blob do Azure dentro do contêiner padrão do cluster do Hadoop. Veja a seguir a consulta de Hive para esse procedimento:

```console
insert overwrite directory wasb:///<directory within the default container> <select clause from ...>
```

No exemplo a seguir, a saída da consulta de Hive é gravada em um diretório de blob `queryoutputdir` no contêiner padrão do cluster do Hadoop. Aqui, você precisa fornecer apenas o nome do diretório, sem o nome do blob. Um erro será gerado se você fornecer os nomes do blob e do diretório, como `wasb:///queryoutputdir/queryoutput.txt`.

![Captura de tela mostra o comando anterior na janela de linha de comando do Hadoop.](./media/move-hive-tables/output-hive-results-2.png)

Se abrir o contêiner padrão do cluster do Hadoop usando o Gerenciador de Armazenamento do Azure, você verá a saída da consulta de Hive da forma indicada na imagem a seguir. Você pode aplicar o filtro (destacado pela caixa vermelha) para recuperar apenas o blob com letras específicas nos nomes.

![Gerenciador de Armazenamento do Azure mostrando a saída da consulta do Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Enviar consultas de Hive com o Editor de Hive
Você também pode usar o console de consulta (editor de Hive) inserindo uma URL do formulário *https: \/ / \<Hadoop cluster name> . azurehdinsight.net/Home/HiveEditor* em um navegador da Web. Você precisa estar conectado ao console, de forma que precisa de suas credenciais do cluster do Hadoop aqui.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Enviar consultas de Hive com comandos do Azure PowerShell
Você também pode usar o PowerShell para enviar consultas de Hive. Para obter instruções, confira [Enviar trabalhos do Hive usando o PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Criar banco de dados e tabelas Hive
As consultas de Hive são compartilhadas no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e podem ser baixadas de lá.

Veja aqui a consulta Hive que cria uma tabela Hive.

```hiveql
create database if not exists <database name>;
CREATE EXTERNAL TABLE if not exists <database name>.<table name>
(
    field1 string,
    field2 int,
    field3 float,
    field4 double,
    ...,
    fieldN string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");
```

Veja aqui as descrições dos campos de que você precisa para plug-ins e outras configurações:

* **\<database name\>**: o nome do banco de dados que você deseja criar. Se quiser apenas usar o banco de dados padrão, a consulta *create database...* poderá ser omitida.
* **\<table name\>**: o nome da tabela que você deseja criar dentro do banco de dados especificado. Se você quiser usar o banco de dados padrão, a tabela poderá ser referenciada diretamente *\<table name\>* sem \<database name\> .
* **\<field separator\>**: o separador que delimita os campos no arquivo de dados a serem carregados na tabela Hive.
* **\<line separator\>**: o separador que delimita as linhas no arquivo de dados.
* **\<storage location\>**: o local de armazenamento do Azure para salvar os dados de tabelas do hive. Se você não especificar *LOCATION \<storage location\>*, o banco de dados e as tabelas serão armazenadas no diretório *hive/warehouse/* no contêiner padrão do cluster DE Hive por padrão. Se você quiser especificar a localização de armazenamento, esta deverá estar dentro do contêiner padrão para o banco de dados e tabelas. Esse local deve ser referenciado como local relativo ao contêiner padrão do cluster no formato *' WASB:/// \<directory 1> /'* ou *' WASB:/// \<directory 1> / \<directory 2> /'*, etc. Depois que a consulta é executada, os diretórios relativos são criados dentro do contêiner padrão.
* **TBLPROPERTIES("skip.header.line.count"="1")** : Se o arquivo de dados tiver uma linha de cabeçalho, você precisará adicionar essa propriedade **ao final** da consulta *create table*. Caso contrário, a linha de cabeçalho será carregada como um registro para a tabela. Se o arquivo de dados não tiver uma linha de cabeçalho, essa configuração pode ser omitida na consulta.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Carregar dados para tabelas Hive
Veja aqui a consulta Hive que carrega dados em uma tabela Hive.

```hiveql
LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;
```

* **\<path to blob data\>**: Se o arquivo de blob a ser carregado na tabela Hive estiver no contêiner padrão do cluster HDInsight Hadoop, o *\<path to blob data\>* deverá estar no formato *' WASB:// \<directory in this container> / \<blob file name> '*. O arquivo de blob também pode estar em um contêiner adicional do cluster do Hadoop do HDInsight. Nesse caso, *\<path to blob data\>* deve estar no formato *' WASB:// \<container name> @ \<storage account name> . blob.Core.Windows.NET/ \<blob file name> '*.

  > [!NOTE]
  > Os dados blob a serem carregados na tabela Hive deve estar no contêiner padrão ou adicional da conta de armazenamento para o cluster do Hadoop. Caso contrário, a consulta *LOAD DATA* falhará reclamando que não pode acessar os dados.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Tópicos avançados: tabela e repositório de dados Hive particionados no formato ORC
Se os dados forem grandes, particionar a tabela é útil para consultas que só precisam verificar algumas partições da tabela. Por exemplo, é útil particionar os dados de log de um site da Web por datas.

Além do particionamento de tabelas Hive, também é útil armazenar os dados Hive no formato ORC (Colunar de Linha Otimizado). Para obter mais informações sobre a formatação ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Usando arquivos ORC melhora o desempenho quando o Hive lê, grava e processa dados</a>.

### <a name="partitioned-table"></a>Tabela particionada
Aqui está a consulta de Hive que cria uma tabela particionada e carrega dados nela.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
(field1 string,
...
fieldN string
)
PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
    PARTITION (<partitionfieldname>=<partitionfieldvalue>);
```

Ao consultar tabelas particionadas, recomendamos adicionar a condição de partição no **começo** da cláusula `where`, o que melhora a eficiência da pesquisa.

```hiveql
select
    field1, field2, ..., fieldN
from <database name>.<partitioned table name>
where <partitionfieldname>=<partitionfieldvalue> and ...;
```

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Armazenar dados Hive no formato ORC
Você não pode carregar dados diretamente do armazenamento de blobs em tabelas Hive armazenadas no formato ORC. Veja aqui etapas que você deve executar para carregar dados de blobs do Azure para tabelas Hive armazenadas no formato ORC.

Crie uma tabela externa **ARMAZENADA COMO ARQUIVO DE TEXTO** e carregue dados do armazenamento de blob na tabela.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' STORED AS TEXTFILE
    LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;
```

Crie uma tabela interna com o mesmo esquema que a tabela externa na etapa 1 e o mesmo delimitador de campo, e armazene dados Hive no formato ORC.

```hiveql
CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;
```

Selecione os dados da tabela externa da etapa 1 e insira-os na tabela ORC

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name>
    SELECT * FROM <database name>.<external textfile table name>;
```

> [!NOTE]
> Se a tabela textfile *\<database name\> . \<external textfile table name\>* tiver partições, na ETAPA 3, o comando `SELECT * FROM <database name>.<external textfile table name>` selecionará a variável de partição como um campo no conjunto de dados retornado. Inserindo-o no *\<database name\> . \<ORC table name\>* falha desde *\<database name\> . \<ORC table name\>* não tem a variável de partição como um campo no esquema de tabela. Nesse caso, você precisa selecionar especificamente os campos a serem inseridos *\<database name\> . \<ORC table name\>* conforme a seguir:
>
>

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
    SELECT field1, field2, ..., fieldN
    FROM <database name>.<external textfile table name>
    WHERE <partition variable>=<partition value>;
```

É seguro descartar o *\<external text file table name\>* ao usar a consulta a seguir depois que todos os dados tiverem sido inseridos no *\<database name\> . \<ORC table name\>*:

```hiveql
    DROP TABLE IF EXISTS <database name>.<external textfile table name>;
```

Depois de seguir esse procedimento, você deve ter uma tabela com dados no formato ORC pronta para uso.  

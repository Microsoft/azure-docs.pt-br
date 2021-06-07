---
title: Backup & replicação para Apache HBase, Phoenix – Azure HDInsight
description: Configurar o backup e a replicação para o Apache HBase e o Apache Phoenix no Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 1d5bcf9c04ad02eaf297f8971aa0f4ff599888c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943002"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Configurar backup e replicação para Apache HBase e Apache Phoenix no HDInsight

Apache HBase oferece suporte a várias abordagens para proteger contra perda de dados:

* Copie a pasta `hbase`
* Exportar e importar
* Copiar tabelas
* Instantâneos
* Replicação

> [!NOTE]  
> O Apache Phoenix armazena seus metadados nas tabelas do HBase, para que seja executado o backup dos metadados quando você fizer o backup de tabelas de catálogo do sistema HBase.

As seções a seguir descrevem o cenário de uso para cada uma dessas abordagens.

## <a name="copy-the-hbase-folder"></a>Copie a pasta hbase

Com essa abordagem, você copia todos os dados do HBase sem ser capaz de selecionar um subconjunto de tabelas ou famílias de colunas. Abordagens subsequentes fornecem maior controle.

O HBase no HDInsight usa o armazenamento padrão selecionado ao criar o cluster, blobs de Armazenamento do Microsoft Azure ou Azure Data Lake Storage. Em ambos os casos, o HBase armazena seus arquivos de metadados e dados no seguinte caminho:

`/hbase`

* Em uma conta de Armazenamento do Microsoft Azure, a pasta `hbase` reside na raiz do contêiner de blob:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* No Azure Data Lake Storage, a `hbase` pasta reside no caminho raiz que você especificou ao provisionar um cluster. Normalmente, esse caminho raiz tem uma pasta `clusters`, com uma subpasta nomeada de acordo com o seu cluster do HDInsight:

  `/clusters/<clusterName>/hbase`

Em ambos os casos, a pasta `hbase` contém todos os dados que o HBase liberou para o disco, mas pode não conter os dados dentro da memória. Antes de você poder confiar nessa pasta como uma representação precisa dos dados do HBase, você deve desligar o cluster.

Depois de excluir o cluster, você pode deixar os dados no local ou copiar os dados para um novo local:

* Crie uma nova instância do HDInsight apontando para o local de armazenamento atual. A nova instância é criada com todos os dados existentes.

* Copie a pasta `hbase` para um contêiner de blob de Armazenamento do Azure diferente ou local do Data Lake Storage e, em seguida, inicie um novo cluster com esses dados. Para o Armazenamento do Azure, use [AzCopy](../../storage/common/storage-use-azcopy-v10.md), e para o Data Lake Storage, use [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportar e importar

No cluster HDInsight de origem, use o [Utilitário de exportação](https://hbase.apache.org/book.html#export) (incluído com o HBase) para exportar dados de uma tabela de origem para o armazenamento anexado padrão. Em seguida, você pode copiar a pasta exportada para o local de armazenamento de destino e executar o [Utilitário de importação](https://hbase.apache.org/book.html#import) no cluster de destino do HDInsight.

Para exportar dados de tabela, primeiro SSH no nó principal do seu cluster HDInsight de origem e, em seguida, execute o seguinte `hbase` comando:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

O diretório de exportação ainda não deve existir. O nome da tabela diferencia maiúsculas de minúsculas.

Para importar dados de tabela, use o SSH no nó principal do cluster HDInsight de destino e, em seguida, execute o seguinte `hbase` comando:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

A tabela já deve existir.

Especifique o caminho completo de exportação para o armazenamento padrão ou para qualquer uma das opções de armazenamento anexadas. Por exemplo, no Armazenamento do Microsoft Azure:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

No Azure Data Lake Storage Gen2, a sintaxe é:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

No Azure Data Lake Storage Gen1, a sintaxe é:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Essa abordagem oferece granularidade em nível de tabela. Você também pode especificar um intervalo de datas para as linhas a serem incluídas, o que permite que você execute o processo de forma incremental. Cada data é em milissegundos desde a época do Unix.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Observe que você precisa especificar o número de versões de cada linha para exportar. Para incluir todas as versões no intervalo de datas, defina `<numberOfVersions>` como um valor maior do que as versões de linhas máximas possível, como 100000.

## <a name="copy-tables"></a>Copiar tabelas

O [utilitário copyTable](https://hbase.apache.org/book.html#copy.table) copia dados de uma tabela de origem, linha por linha, para uma tabela de destino existente com o mesmo esquema que a origem. A tabela de destino pode estar no mesmo cluster ou em um cluster do HBase diferente. Os nomes de tabela diferenciam maiúsculas de minúsculas.

Para usar CopyTable dentro de um cluster, execute o SSH no nó principal do seu cluster do HDInsight de origem e, em seguida, execute este comando `hbase`:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Para usar CopyTable para copiar para uma tabela em um cluster diferente, adicione o interruptor `peer` com o endereço do cluster de destino:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

O endereço de destino é composto pelas três partes a seguir:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` é uma lista separada por vírgulas de nós de Apache ZooKeeper, por exemplo:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` em padrões do HDInsight para 2181, e `<ZnodeParent>` é `/hbase-unsecure`, de modo que o `<destinationAddress>` completo seria:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Consulte [Coletar manualmente a lista de quorum Apache ZooKeeper](#manually-collect-the-apache-zookeeper-quorum-list) neste artigo para obter detalhes sobre como recuperar esses valores para o seu cluster do HDInsight.

O utilitário CopyTable também oferece suporte a parâmetros para especificar o intervalo de tempo de linhas para copiar, e para especificar o subconjunto de famílias de colunas em uma tabela para copiar. Para ver a lista completa dos parâmetros com suporte pelo CopyTable, execute CopyTable sem nenhum parâmetro:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

OmCopyTable examina o conteúdo da tabela de origem inteiro que será copiado na tabela de destino. Isso pode reduzir o desempenho do seu cluster do HBase enquanto o CopyTable está sendo executado.

> [!NOTE]  
> Para automatizar a cópia de dados entre tabelas, consulte o script `hdi_copy_table.sh` no repositórios dos [utilitários do HBase do Azure](https://github.com/Azure/hbase-utils/tree/master/replication) no GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Coletar manualmente a lista do quorum do Apache ZooKeeper

Quando ambos os clusters do HDInsight estão na mesma rede virtual, conforme descrito anteriormente, a resolução de nomes de host internos é automática. Para usar Copiartable para clusters HDInsight em duas redes virtuais separadas conectadas por um gateway de VPN, você precisará fornecer os endereços IP do host dos nós Zookeeper no quorum.

Para obter os nomes do host do quorum, execute o comando de ondulação a seguir:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

O comando de ondulação recupera um documento JSON com informações de configuração do HBase e o comando grep retorna apenas a entrada "hbase.zookeeper.quorum", por exemplo:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

O valor dos nomes do host do quorum é a cadeia de caracteres inteira à direita dos dois pontos.

Para recuperar os endereços IP para esses hosts, use o comando de ondulação a seguir para cada host na lista anterior:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

Neste comando de ondulação, `<zookeeperHostFullName>` é o nome DNS completo de um host do ZooKeeper, como o exemplo `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. A saída do comando contém o endereço IP para o host especificado, por exemplo:

`100    "ip" : "10.0.0.9",`

Depois de coletar os endereços IP para todos os nós do ZooKeeper no seu quorum, recompile o endereço de destino:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

Em nosso exemplo:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Instantâneos

Os [instantâneos](https://hbase.apache.org/book.html#ops.snapshots) permitem que você faça um backup pontual dos dados em seu armazenamento do HBase. Instantâneos têm sobrecarga mínima e conclusão dentro de segundos, porque uma operação de instantâneo é efetivamente uma operação de metadados capturando os nomes de todos os arquivos no armazenamento nesse momento. No momento de um instantâneo, nenhum dado real é copiado. Instantâneos dependem da natureza imutável dos dados armazenados em HDFS, onde as atualizações, inserções e exclusões são todas representadas como novos dados. Você pode restaurar (*clonar*) um instantâneo no mesmo cluster ou exportar um instantâneo para outro cluster.

Para criar um instantâneo, execute o SSH no nó principal do seu cluster do HBase no HDInsight e inicie o shell `hbase`:

```console
hbase shell
```

No shell do hbase, use o comando de instantâneo com os nomes da tabela e desse instantâneo:

```console
snapshot '<tableName>', '<snapshotName>'
```

Para restaurar um instantâneo pelo nome dentro do shell `hbase`, primeiro desabilite a tabela e, em seguida, restaure o instantâneo e habilite novamente a tabela:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Para restaurar um instantâneo para uma nova tabela, use clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Para exportar um instantâneo para o HDFS para uso por outro cluster, primeiro crie o instantâneo, conforme descrito anteriormente e, em seguida, use o utilitário ExportSnapshot. Execute esse utilitário de dentro da sessão SSH para o nó principal, não dentro do shell `hbase`:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

O `<hdfsHBaseLocation>` pode ser qualquer um dos locais de armazenamento acessíveis ao seu cluster de origem, e deve apontar para a pasta do hbase usada pelo seu cluster de destino. Por exemplo, se você tiver uma conta de Armazenamento do Microsoft Azure secundária anexada ao seu cluster de origem, e essa conta fornecer acesso ao contêiner usado pelo armazenamento padrão do cluster de destino, você pode usar este comando:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Se você não tiver uma conta de armazenamento do Azure secundária anexada ao seu cluster de origem ou se o seu cluster de origem for um cluster local (ou cluster não HDI), você poderá enfrentar problemas de autorização ao tentar acessar a conta de armazenamento do cluster do HDI. Para resolver isso, especifique a chave para sua conta de armazenamento como um parâmetro de linha de comando, conforme mostrado no exemplo a seguir. Você pode obter a chave para sua conta de armazenamento no portal do Azure.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Se o cluster de destino for um cluster ADLS Gen 2, altere o comando anterior para ajustar as configurações usadas pelo ADLS Gen 2:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.<account_name>.dfs.core.windows.net=<key> -Dfs.azure.account.auth.type.<account_name>.dfs.core.windows.net=SharedKey -Dfs.azure.always.use.https.<account_name>.dfs.core.windows.net=false -Dfs.azure.account.keyprovider.<account_name>.dfs.core.windows.net=org.apache.hadoop.fs.azurebfs.services.SimpleKeyProvider -snapshot 'Snapshot1' -copy-to 'abfs://<container>@<account_name>.dfs.core.windows.net/hbase'
```

Depois que o instantâneo for exportado, use o SSH no nó principal do cluster de destino e restaure o instantâneo usando o `restore_snapshot` comando, conforme descrito anteriormente.

Os instantâneos fornecem um backup completo de uma tabela no momento do comando `snapshot`. Os instantâneos não fornecem a capacidade de executar instantâneos incrementais por janelas de tempo, nem para especificar subconjuntos de famílias de colunas a serem incluídas no instantâneo.

## <a name="replication"></a>Replicação

A [replicação do HBase](https://hbase.apache.org/book.html#_cluster_replication) envia automaticamente as transações de um cluster de origem para um cluster de destino, usando um mecanismo assíncrono com sobrecarga mínima no cluster de origem. No HDInsight, você pode configurar a replicação entre os clusters, onde:

* Os clusters de origem e destino estão na mesma rede virtual.
* Os clusters de origem e destinos estão em diferentes redes virtuais conectadas por um gateway de VPN, mas ambos os clusters existem no mesmo local geográfico.
* O cluster de origem e os clusters destinos estão em diferentes redes virtuais conectadas por um gateway de VPN, e cada cluster existe em um local geográfico diferente.

As etapas gerais para configurar a replicação são:

1. No cluster de origem, crie as tabelas e popule os dados.
2. No cluster de destino, crie tabelas de destino vazias com o esquema da tabela de origem.
3. Registre o cluster de destino como um par de cluster de origem.
4. Habilite a replicação nas tabelas de origem desejadas.
5. Copie os dados existentes das tabelas de origem para as tabelas de destino.
6. A replicação copia automaticamente as novas modificações de dados às tabelas de origem nas tabelas de destino.

Para habilitar a replicação no HDInsight, aplique uma Ação de Script ao seu cluster do HDInsight de origem em execução. Para obter uma explicação da habilitação da replicação no seu cluster, ou para fazer experiências com replicação nos clusters de exemplo criados em redes virtuais usando modelos de gerenciamento de recursos do Azure, consulte [Configurar replicação do Apache HBase](apache-hbase-replication.md). Esse artigo também inclui instruções para habilitar a replicação de metadados de Phoenix.

## <a name="next-steps"></a>Próximas etapas

* [Configure a replicação do Apache HBase](apache-hbase-replication.md)
* [Trabalhando com o utilitário de importação e exportação do HBase](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)

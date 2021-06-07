---
title: Migrar um cluster HBase para uma nova versão – Azure HDInsight
description: Como migrar clusters do Apache HBase para uma versão mais recente no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 43b46d19503856f5eae38272299f73d9c80055b8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868878"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrar um cluster Apache HBase para uma nova versão

Este artigo discute as etapas necessárias para atualizar o cluster do Apache HBase no Azure HDInsight para uma versão mais recente.

O tempo de inatividade durante a atualização deve ser mínimo, na ordem de minutos. Esse tempo de inatividade é causado pelas etapas para liberar todos os dados na memória, e então, pelo tempo para configurar e reiniciar os serviços no novo cluster. Os resultados vão variar, dependendo do número de nós, da quantidade de dados e de outras variáveis.

## <a name="review-apache-hbase-compatibility"></a>Revisar a compatibilidade do Apache HBase

Antes de atualizar o Apache HBase, verifique se as versões do HBase nos clusters de origem e destino são compatíveis. Para obter mais informações, consulte [Componentes e versões do Apache Hadoop disponíveis com o HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> É altamente recomendável que você examine a matriz de compatibilidade de versão no [Catálogo do HBase](https://hbase.apache.org/book.html#upgrading). Qualquer incompatibilidade de interrupção deve ser descrita nas notas de versão da versão do HBase.

Aqui está um exemplo de matriz de compatibilidade de versão. Y indica compatibilidade e N indica uma possível incompatibilidade:

| Tipo de compatibilidade | Versão principal| Versão secundária | Patch |
| --- | --- | --- | --- |
| Compatibilidade de transferência cliente-servidor | N | Y | Y |
| Compatibilidade servidor-servidor | N | Y | Y |
| Compatibilidade de formato de arquivo | N | Y | Y |
| Compatibilidade de API do cliente | N | Y | Y |
| Compatibilidade de binário do cliente | N | N | Y |
| **Compatibilidade de API limitada ao lado do servidor** |  |  |  |
| Estável | N | Y | Y |
| Evoluindo | N | N | Y |
| Instável | N | N | N |
| Compatibilidade de dependência | N | Y | Y |
| Compatibilidade operacional | N | N | Y |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Atualizar com a mesma versão principal do Apache HBase

Para atualizar o cluster do Apache HBase no Azure HDInsight, conclua as seguintes etapas:

1. Certifique-se de que seu aplicativo é compatível com a nova versão, conforme mostrado na matriz de compatibilidade e nas notas de versão do HBase. Teste seu aplicativo em um cluster que esteja executando a versão de destino do HDInsight e do HBase.

1. [Configure um novo cluster do HDInsight de destino](../hdinsight-hadoop-provision-linux-clusters.md) usando a mesma conta de armazenamento, mas com um nome de contêiner diferente:

   :::image type="content" source="./media/apache-hbase-migrate-new-version/same-storage-different-container.png" alt-text="Usar a mesma Conta de armazenamento, mas criar um Contêiner diferente" border="true":::

1. Libere o cluster HBase de origem, que é o cluster que você está atualizando. O HBase grava os dados de entrada em um repositório na memória, chamado _memstore_. Depois que o memstore atingir um determinado tamanho, o HBase o liberará para o disco para armazenamento de longo prazo na conta de armazenamento do cluster. Ao excluir o cluster antigo, os memstores são reciclados, possivelmente perdendo dados. Para liberar manualmente o memstore para cada tabela para o disco, execute o script a seguir. A versão mais recente do script está no [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) do Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Interrompa a ingestão para o cluster antigo do HBase.

1. Para garantir que nenhum dado recente do memstore seja liberado, execute novamente o script anterior.

1. Entre no [Apache Ambari](https://ambari.apache.org/) no cluster antigo ( `https://OLDCLUSTERNAME.azurehdidnsight.net` ) e interrompa os serviços do HBase. Quando você receber uma solicitação para confirmar que deseja parar os serviços, marque a caixa para ativar o modo de manutenção do HBase. Para saber mais sobre como usar e conectar-se ao Ambari, confira [Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web](../hdinsight-hadoop-manage-ambari.md).

    :::image type="content" source="./media/apache-hbase-migrate-new-version/stop-hbase-services1.png" alt-text="No Ambari, clique em serviços > HBase > parar em ações de serviço" border="true":::

    :::image type="content" source="./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png" alt-text="Marque a caixa de seleção Ativar no modo de manutenção para o HBase e confirme." border="true":::

1. Se você não estiver usando clusters HBase com o recurso de gravações avançadas, ignore esta etapa. Ele é necessário apenas para clusters do HBase com o recurso de gravações avançadas.

   Faça backup do diretório WAL em HDFS executando os comandos abaixo de uma sessão SSH em qualquer um dos nós do Zookeeper ou nós de trabalho do cluster original.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup**
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup**
   ```
    
1. Entre no Ambari no novo cluster HDInsight. Altere a configuração `fs.defaultFS` HDFS para apontar para o nome do contêiner usado pelo cluster original. Essa configuração está em **HDFS > Configurações > Avançado > Site principal avançado**.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="No Ambari, clique em serviços > HDFS > configurações > avançado" border="true":::

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name.png" alt-text="Em Ambari, altere o nome do contêiner." border="true":::

1. Se você não estiver usando clusters HBase com o recurso de gravações avançadas, ignore esta etapa. Ele é necessário apenas para clusters do HBase com o recurso de gravações avançadas.

   Altere o `hbase.rootdir` caminho para apontar para o contêiner do cluster original.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="Em Ambari, altere o nome do contêiner para o HBase rootdir" border="true":::
    
1. Se você não estiver usando clusters HBase com o recurso de gravações avançadas, ignore esta etapa. Ele é necessário apenas para clusters HBase com recurso de gravações aprimoradas e somente em casos em que o cluster original era um cluster HBase com recurso de gravações aprimoradas.

   Limpe os dados Zookeeper e WAL FS para esse novo cluster. Emita os seguintes comandos em qualquer um dos nós Zookeeper ou nós de trabalho:

   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit

   hdfs dfs -rm -r hdfs://mycluster/hbasewal**
   ```

1. Se você não estiver usando clusters HBase com o recurso de gravações avançadas, ignore esta etapa. Ele é necessário apenas para clusters do HBase com o recurso de gravações avançadas.
   
   Restaure o diretório WAL para o HDFS do novo cluster de uma sessão SSH em qualquer um dos nós do Zookeeper ou nós de trabalho do novo cluster.
   
   ```bash
   hdfs dfs -cp /hbase-wal-backup/hbasewal hdfs://mycluster/**
   ```
   
1. Se você estiver atualizando o HDInsight 3,6 para 4,0, siga as etapas abaixo, caso contrário, pule para a etapa 13:

    1. Reinicie todos os serviços necessários no Ambari selecionando **Serviços**  >  **reiniciar todos os necessários**.
    1. Interrompa o serviço HBase.
    1. Use o SSH para o nó Zookeeper e execute o comando [zkCli](https://github.com/go-zkcli/zkcli) `rmr /hbase-unsecure` para remover o Znode raiz do HBase de Zookeeper.
    1. Reinicie o HBase.

1. Se você estiver atualizando para qualquer outra versão do HDInsight além de 4,0, siga estas etapas:
    1. Salve suas alterações.
    1. Reinicie todos os serviços necessários conforme indicado pelo Ambari.

1. Aponte seu aplicativo para o novo cluster.

    > [!NOTE]  
    > O DNS estático para que as alterações de aplicativo durante a atualização. Em vez de fazer hard-coding deste DNS, você pode configurar um CNAME nas configurações de DNS do nome de domínio que aponta para o nome do cluster. Outra opção é usar um arquivo de configuração para o aplicativo que você pode atualizar sem reimplantação.

1. Inicie a ingestão para verificar se tudo está funcionando conforme o esperado.

1. Se o novo cluster for satisfatório, exclua o cluster original.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o [Apache HBase](https://hbase.apache.org/) e como atualizar clusters do HDInsight, consulte os artigos a seguir:

* [Atualizar um cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Monitorar e gerenciar o Azure HDInsight usando a IU da Web do Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Componentes e versões do Apache Hadoop](../hdinsight-component-versioning.md)
* [Otimizar o Apache HBase](../optimize-hbase-ambari.md)

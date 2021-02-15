---
title: Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight
description: Saiba como usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: 2bbfbd2d953ea663453f0092ff366e95f6dd5ea7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945381"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight

[Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) é um serviço de armazenamento em nuvem dedicado a Big data Analytics, criado no [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 combina os recursos do armazenamento de BLOBs do Azure e Azure Data Lake Storage Gen1. O serviço resultante oferece recursos de Azure Data Lake Storage Gen1 incluindo: semântica do sistema de arquivos, segurança em nível de diretório e de arquivo e adaptabilidade. Juntamente com os recursos de baixo custo, armazenamento em camadas, alta disponibilidade e recuperação de desastre do armazenamento de BLOBs do Azure.

Para obter uma comparação completa das opções de criação de cluster usando Data Lake Storage Gen2, consulte [comparar as opções de armazenamento para uso com clusters do Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade o Data Lake Storage Gen2

Data Lake Storage Gen2 está disponível como uma opção de armazenamento para quase todos os tipos de cluster do Azure HDInsight como um padrão e uma conta de armazenamento adicional. No entanto, o HBase pode ter apenas uma conta com Data Lake Storage Gen2.

> [!Note]  
> Depois de selecionar Data Lake Storage Gen2 como seu **tipo de armazenamento primário**, você não poderá selecionar um data Lake Storage Gen1 como armazenamento adicional.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Criar clusters HDInsight usando Data Lake Storage Gen2

Use os links a seguir para obter instruções detalhadas sobre como criar clusters HDInsight com acesso ao Data Lake Storage Gen2.

* [Usando o Portal](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Usando a CLI do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* Atualmente, o PowerShell não tem suporte para a criação de um cluster HDInsight com Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controle de acesso para Data Lake Storage Gen2 no HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>O Data Lake Storage Gen2 dá suporte a quais tipos de permissões?

Data Lake Storage Gen2 usa um modelo de controle de acesso que dá suporte ao controle de acesso baseado em função do Azure (RBAC do Azure) e às ACLs (listas de controle de acesso) semelhantes a POSIX. Data Lake Storage Gen1 dá suporte a listas de controle de acesso somente para controlar o acesso aos dados.

O RBAC do Azure usa atribuições de função para aplicar efetivamente conjuntos de permissões a usuários, grupos e entidades de serviço para recursos do Azure. Normalmente, esses recursos do Azure são restritos a recursos de nível superior (por exemplo, contas de armazenamento de BLOBs do Azure). Para o armazenamento de BLOBs do Azure, e também Data Lake Storage Gen2, esse mecanismo foi estendido para o recurso do sistema de arquivos.

Para obter mais informações sobre permissões de arquivo com o RBAC do Azure, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Para obter mais informações sobre permissões de arquivo com ACLs, consulte [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como fazer controlar o acesso aos meus dados no Data Lake Storage Gen2?

A capacidade do cluster HDInsight de acessar arquivos no Data Lake Storage Gen2 é controlada por meio de identidades gerenciadas. Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (AD do Azure) cujas credenciais são gerenciadas pelo Azure. Com identidades gerenciadas, você não precisa registrar entidades de serviço no Azure AD. Ou manter credenciais como certificados.

Os serviços do Azure têm dois tipos de identidades gerenciadas: atribuído pelo sistema e atribuído pelo usuário. O HDInsight usa identidades gerenciadas atribuídas pelo usuário para acessar Data Lake Storage Gen2. Um `user-assigned managed identity` é criado como um recurso autônomo do Azure. Por meio de um processo de criação, o Microsoft Azure cria uma identidade no locatário do Azure AD confiado pela assinatura em uso. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure.

O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida das instâncias de serviço do Azure a que ela é atribuída. Para obter mais informações sobre identidades gerenciadas, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como fazer definir permissões para que os usuários do Azure AD consultem dados em Data Lake Storage Gen2 usando o hive ou outros serviços?

Para definir permissões para os usuários consultarem dados, use os grupos de segurança do Azure AD como a entidade atribuída em ACLs. Não atribua permissões de acesso de arquivo diretamente a usuários individuais ou a entidades de serviço. Com os grupos de segurança do Azure AD para controlar o fluxo de permissões, você pode adicionar e remover usuários ou entidades de serviço sem reaplicar ACLs a uma estrutura de diretório inteira. Você só precisa adicionar ou remover os usuários do grupo de segurança do Azure AD pertinente. As ACLs não são herdadas, portanto, reaplicar ACLs requer a atualização da ACL em todos os arquivos e subdiretórios.

## <a name="access-files-from-the-cluster"></a>Acessar arquivos do cluster

Há várias maneiras de acessar os arquivos em Data Lake Storage Gen2 de um cluster HDInsight.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Usando o formato de caminho encurtado**. Com essa abordagem, você substitui o caminho até a raiz do cluster com:

    ```
    abfs:///<file.path>/
    ```

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Os exemplos são baseados em uma [conexão SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) ao nó principal do cluster. Os exemplos usam todos os três esquemas de URI. Substitua `CONTAINERNAME` e `STORAGEACCOUNT` pelos valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos do HDFS

1. Criar um arquivo no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Criar diretórios no armazenamento de cluster.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiar dados do armazenamento local para o armazenamento de cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Listar o conteúdo do diretório no armazenamento de cluster.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Como criar uma tabela Hive

Três locais de arquivo são mostrados para fins ilustrativos. Para a execução real, use apenas uma das entradas `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Próximas etapas

* [Integração do Azure HDInsight com a versão prévia do Data Lake Storage Gen2 – ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutorial: Extrair, transformar e carregar dados usando a Interactive Query no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)

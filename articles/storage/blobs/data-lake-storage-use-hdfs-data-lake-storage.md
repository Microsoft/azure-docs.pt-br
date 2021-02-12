---
title: Usando a CLI do HDFS com o Azure Data Lake Storage Gen2
description: Use a CLI do Sistema de Arquivos Distribuído do Hadoop (HDFS) para Azure Data Lake Storage Gen2. Crie um contêiner, obtenha uma lista de arquivos ou diretórios e muito mais.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: d2b36dd600efa864913e0087c49bffd556e8330d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912392"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Usando a CLI do HDFS com o Data Lake Storage Gen2

Você pode acessar e gerenciar os dados em sua conta de armazenamento usando uma interface de linha de comando, assim como faria com um [sistema de arquivos distribuído do Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Este artigo fornece alguns exemplos que irão ajudá-lo a começar.

O HDInsight fornece acesso ao contêiner distribuído que está anexado localmente aos nós de computação. Você pode acessar esse contêiner usando o shell que interage diretamente com o HDFS e com os outros sistemas de arquivos aos quais o Hadoop dá suporte.

Para obter mais informações sobre a CLI do HDFS, consulte a [documentação oficial](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e o [Guia de permissões do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Se você estiver usando Azure Databricks em vez do HDInsight e desejar interagir com seus dados usando uma interface de linha de comando, poderá usar a CLI do databricks para interagir com o sistema de arquivos do databricks. Consulte a [CLI do databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Usar a CLI do HDFS com um cluster HDInsight Hadoop no Linux

Primeiro, estabeleça o [acesso remoto aos serviços](../../hdinsight/hdinsight-hadoop-linux-information.md#remote-access-to-services). Se você escolher [SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) o código de exemplo do PowerShell seria semelhante ao seguinte:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A cadeia de caracteres de conexão pode ser encontrada no "SSH + Cluster logon" seção da folha de cluster HDInsight no portal do Azure. As credenciais SSH foram especificadas no momento da criação do cluster.

>[!IMPORTANT]
>O faturamento de cluster do HDInsight é iniciado depois que um cluster é criado e pára quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado. Para saber como excluir um cluster, consulte o nosso artigo [sobre o tópico](../../hdinsight/hdinsight-delete-cluster.md). No entanto, os dados armazenados em uma conta de armazenamento com o Data Lake Storage Gen2 ativado persistem mesmo depois que um cluster do HDInsight é excluído.

## <a name="create-a-container"></a>Criar um contêiner

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* Substitua o espaço reservado `<container-name>` pelo nome que deseje fornecer ao contêiner.

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

## <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

`hdfs dfs -ls <path>`

Substitua o `<path>` espaço reservado pelo URI do contêiner ou da pasta do contêiner.

Por exemplo: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Criar um diretório

`hdfs dfs -mkdir [-p] <path>`

Substitua o `<path>` espaço reservado pelo nome do contêiner raiz ou por uma pasta dentro do contêiner.

Por exemplo: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Excluir um arquivo ou diretório

`hdfs dfs -rm <path>`

Substitua o espaço reservado `<path>` pelo URI do arquivo ou pasta que você quer excluir.

Por exemplo: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Exibir as ACLs (Lista de Controle de Acesso) de arquivos e diretórios

`hdfs dfs -getfacl [-R] <path>`

Exemplo:

`hdfs dfs -getfacl -R /dir`

Consulte [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Definir ACLs de arquivos e diretórios

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

Exemplo:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Consulte [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Alterar o proprietário dos arquivos

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

Consulte [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Alterar associação de grupos de arquivos

`hdfs dfs -chgrp [-R] <group> <URI>`

Consulte [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Alterar as permissões dos arquivos

`hdfs dfs -chmod [-R] <mode> <URI>`

Consulte [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

É possível exibir a lista completa de comandos no site [Guia do shell do sistema de arquivos do Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="next-steps"></a>Próximas etapas

* [ Use uma conta compatível com o Azure Data Lake Gen2 em bancos de dados do Azure ](./data-lake-storage-quickstart-create-databricks-account.md)

* [Saiba mais sobre as listas de controle de acesso em arquivos e diretórios](./data-lake-storage-access-control.md)
---
title: Usar o SCP com Apache Hadoop no Azure HDInsight
description: Este documento fornece informações sobre como se conectar ao HDInsight usando os comandos SSH e SCP.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 927b8c55008c3e01d8ff1dd09c46cfa3c6618026
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946276"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Usar o SCP com Apache Hadoop no Azure HDInsight

Este artigo fornece informações sobre como transferir arquivos com segurança com o cluster HDInsight.

## <a name="copy-files"></a>Copiar arquivos

O utilitário `scp` pode ser usado para copiar arquivos de e para os nós individuais no cluster. Por exemplo, o comando a seguir copia o diretório `test.txt` do sistema local para o nó principal primário:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Como nenhum caminho é especificado após `:`, o arquivo é colocado no diretório base `sshuser`.

O exemplo a seguir copia o arquivo `test.txt` do diretório base `sshuser` no nó principal primário para o sistema local:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` só pode acessar o sistema de arquivos de nós individuais dentro do cluster. Ele não pode ser usado para acessar dados no armazenamento compatível com HDFS para o cluster.

Use `scp` quando precisar carregar um recurso para usar em uma sessão SSH. Por exemplo, carregue um script Python, em seguida, execute o script em uma sessão SSH.

Para obter informações sobre como carregar diretamente os dados no armazenamento compatível com o HDFS, consulte os seguintes documentos:

* [HDInsight usando o armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight usando Azure data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar o SSH com o HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)

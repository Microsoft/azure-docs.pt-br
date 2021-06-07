---
title: Erro de permissão negada com a tabela Apache Hive no Azure HDInsight
description: Permissão negada ao tentar criar uma tabela de Apache Hive no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 6d91ca747bb264b4c7262eec2bb0745b8a40b7a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930918"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Cenário: erro de permissão negada ao tentar criar uma tabela de Apache Hive no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você verá o seguinte erro ao tentar criar uma tabela:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Você verá uma mensagem de erro semelhante se executar o seguinte comando de armazenamento HDFS:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Causa

A capacidade de criar uma tabela no Apache Hive é decidida pelas permissões aplicadas à conta de armazenamento do cluster. Se as permissões da conta de armazenamento de cluster estiverem incorretas, você não poderá criar tabelas. Isso significa que você pode ter as políticas corretas do Ranger para a criação da tabela e ainda ver erros de "permissão negada".

## <a name="resolution"></a>Resolução

Isso é causado por uma falta de permissões suficientes no contêiner de armazenamento que está sendo usado. O usuário que cria a tabela Hive precisa de permissões de leitura, gravação e execução no contêiner. Para obter mais informações, consulte [práticas recomendadas para a autorização do hive usando o Apache Ranger no HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
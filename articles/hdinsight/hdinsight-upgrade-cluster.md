---
title: Migrar cluster para uma versão mais recente
titleSuffix: Azure HDInsight
description: Aprenda as diretrizes para migrar seu cluster do Azure HDInsight para uma versão mais recente.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 04da5d668515fe96d50d4e6a7d0f5ff1c4c48c27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931390"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrar cluster do HDInsight para uma nova versão

Para aproveitar os recursos mais recentes do HDInsight, recomendamos que os clusters HDInsight sejam migrados regularmente para a versão mais recente. O HDInsight não oferece suporte a atualizações in-loco em que um cluster existente é atualizado para uma versão de componente mais recente. Você deve criar um novo cluster com o componente e a versão de plataforma desejados e, em seguida, migrar seus aplicativos para usar o novo cluster. Siga as diretrizes abaixo para migrar as versões do cluster HDInsight.

> [!NOTE]  
> Para obter mais informações sobre versões com suporte do HDInsight, consulte [Versões de componente do HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Tarefas de migração

O fluxo de trabalho para atualizar o cluster HDInsight serão apresentadas a seguir.
![Diagrama de fluxo de trabalho de atualização do HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Leia cada seção deste documento para entender as alterações que podem ser necessárias ao atualizar o cluster HDInsight.
2. Crie um cluster como um ambiente de teste/garantia de qualidade. Para saber mais sobre como criar um cluster, consulte [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Copie trabalhos, fontes de dados e coletores existentes para o novo ambiente.
4. Execute testes de validação para garantir que os trabalhos funcionem conforme o esperado no novo cluster.

Depois de verificar se tudo está funcionando conforme o esperado, agende o tempo de inatividade para a migração. Durante esse tempo de inatividade, execute as tarefas a seguir:

1. Faça backup de dados transitórios armazenados localmente em nós do cluster. Por exemplo, se você tiver dados armazenados diretamente em um nó principal.
1. [Exclua o cluster existente](./hdinsight-delete-cluster.md).
1. Crie um cluster na mesma sub-rede VNET com a versão do HDI mais recente (ou com suporte) que usa o mesmo armazenamento de dados padrão utilizado pelo cluster anterior. Isso permite que o novo cluster continue trabalhando nos dados de produção existentes.
1. Importe o backup de todos os dados transitórios.
1. Inicie os trabalhos/continue processando usando o novo cluster.

## <a name="workload-specific-guidance"></a>Diretrizes específicas da carga de trabalho

Os documentos a seguir fornecem orientação sobre como migrar cargas de trabalho específicas:

* [Migrar HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migrar Kafka](./kafka/migrate-versions.md)
* [Migrar Hive/consulta interativa](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Backup e restauração

Para obter mais informações sobre backup e restauração de banco de dados, consulte [recuperar um banco de dados no banco de dados SQL do Azure usando backups de banco de dados automático](../azure-sql/database/recovery-using-backups.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Conectar-se ao HDInsight usando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerenciar um cluster baseado em Linux usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Notas de versão do HDInsight](./hdinsight-version-release.md)

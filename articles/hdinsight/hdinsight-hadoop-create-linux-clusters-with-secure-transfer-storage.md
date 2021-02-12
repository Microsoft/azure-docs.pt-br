---
title: Apache Hadoop & armazenamento de transferência segura-Azure HDInsight
description: Saiba como criar clusters HDInsight com contas de armazenamento do Azure com transferência segura habilitada.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: a02da7237252811d89e2c19a29f49f0bf9bb3804
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945726"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop clusters com contas de armazenamento de transferência segura no Azure HDInsight

O recurso [Transferência segura exigida](../storage/common/storage-require-secure-transfer.md) aprimora a segurança de sua conta de Armazenamento do Azure aplicando todas as solicitações à sua conta por meio de uma conexão segura. Esse recurso e o esquema wasbs só têm suporte da versão 3.6 ou mais recente do cluster HDInsight.

> [!IMPORTANT]
> Habilitar a transferência de armazenamento seguro depois de criar um cluster pode resultar em erros ao usar sua conta de armazenamento e não é recomendado. É melhor criar um novo cluster usando uma conta de armazenamento com a transferência segura já habilitada.

## <a name="storage-accounts"></a>Contas de armazenamento

### <a name="azure-portal"></a>Portal do Azure

Por padrão, a propriedade de transferência segura necessária é habilitada quando você cria uma conta de armazenamento no portal do Azure.

Para atualizar uma conta de armazenamento existente com portal do Azure, consulte [exigir transferência segura com portal do Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Para o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)do PowerShell, verifique se o parâmetro `-EnableHttpsTrafficOnly` está definido como `1` .

Para atualizar uma conta de armazenamento existente com o PowerShell, consulte [exigir transferência segura com o PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>CLI do Azure

Para o comando CLI do Azure [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create), verifique se o parâmetro `--https-only` está definido como `true` .

Para atualizar uma conta de armazenamento existente com CLI do Azure, consulte [exigir transferência segura com CLI do Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Adicionar outras contas de armazenamento

Há várias opções para adicionar outras contas de armazenamento com transferência segura habilitada:

* Modifique o modelo do Azure Resource Manager na última seção.
* Crie um cluster usando o [Portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento vinculada.
* Use ações de script para adicionar outras contas de armazenamento com transferência segura habilitada a um cluster existente do HDInsight. Para saber mais, confira [Adicionar outras contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Próximas etapas

* O uso do Armazenamento do Azure (WASB) em vez de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como armazenamento de dados padrão
* Para saber mais sobre como o HDInsight usa o Armazenamento do Azure, veja [Usar Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight](hdinsight-upload-data.md).
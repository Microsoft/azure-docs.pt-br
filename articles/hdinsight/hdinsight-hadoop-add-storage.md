---
title: Adicionar outras contas de armazenamento do Azure ao HDInsight
description: Saiba como adicionar mais contas de armazenamento do Azure a um cluster HDInsight existente.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 11852046442901c70112b5e80fef371671546412
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945932"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicionar outras contas de armazenamento ao HDInsight

Saiba como usar ações de script para adicionar mais *contas* de armazenamento do Azure ao HDInsight. As etapas neste documento adicionam uma *conta* de armazenamento a um cluster HDInsight existente. Este artigo se aplica a *contas* de armazenamento (não a conta de armazenamento de cluster padrão) e não a armazenamento adicional, como [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) e [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md) .

> [!IMPORTANT]  
> As informações neste documento são sobre como adicionar mais contas de armazenamento a um cluster depois que ele tiver sido criado. Para saber mais sobre como adicionar contas de armazenamento durante a criação do cluster, veja [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e mais](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chave da conta de armazenamento. Consulte [gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md).
* Se estiver usando o PowerShell, você precisará do módulo AZ.  Consulte [visão geral do Azure PowerShell](/powershell/azure/).

## <a name="how-it-works"></a>Como ele funciona

Durante o processamento, o script executa as seguintes ações:

* Se a conta de armazenamento já existir na configuração de core-site.xml para o cluster, o script será encerrado e nenhuma ação adicional será feita.

* Verifica se a conta de armazenamento existe e se pode ser acessada usando a chave.

* Criptografa a chave usando a credencial do cluster.

* Adiciona a conta de armazenamento ao arquivo core-site.xml.

* Pare e reinicie o Apache Oozie, Apache Hadoop YARN, Apache Hadoop MapReduce2, e HDFS do Apache Hadoop serviços. Interromper e iniciar esses serviços permite que eles usem a nova conta de armazenamento.

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight.

## <a name="add-storage-account"></a>Nova conta de armazenamento

Use a [ação de script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) para aplicar as alterações com as seguintes considerações:

|Propriedade | Valor |
|---|---|
|URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Tipo(s) de nó|Head|
|Parâmetros|`ACCOUNTNAME``ACCOUNTKEY` `-p` (opcional)|

* `ACCOUNTNAME` é o nome da conta de armazenamento a ser adicionada ao cluster HDInsight.
* `ACCOUNTKEY` é a chave de acesso para `ACCOUNTNAME` .
* `-p` é opcional. Se especificado, a chave não é criptografada e armazenada no arquivo de core-site.xml como texto sem formatação.

## <a name="verification"></a>Verificação

Ao exibir o cluster HDInsight no portal do Azure, a seleção da entrada __contas de armazenamento__ em __Propriedades__ não exibe as contas de armazenamento adicionadas por meio dessa ação de script. Azure PowerShell e CLI do Azure não exibem também a conta de armazenamento adicional. As informações de armazenamento não são exibidas porque o script modifica apenas a `core-site.xml` configuração do cluster. Essas informações não são usadas ao recuperar as informações de cluster usando as APIs de gerenciamento do Azure.

Para verificar se o armazenamento adicional usa um dos métodos mostrados abaixo:

### <a name="powershell"></a>PowerShell

O script retornará os nomes de conta de armazenamento associados ao cluster especificado. Substitua `CLUSTERNAME` pelo nome real do cluster e, em seguida, execute o script.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Navegue até o **HDFS**  >  **configurações**  >  **avançado**  >  **personalizado Core-site**.

1. Observe as chaves que começam com `fs.azure.account.key` . O nome da conta fará parte da chave, como visto nesta imagem de exemplo:

   ![verificação por meio do Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Remover conta de armazenamento

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Navegue até o **HDFS**  >  **configurações**  >  **avançado**  >  **personalizado Core-site**.

1. Remova as seguintes chaves:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Depois de remover essas chaves e salvar a configuração, você precisa reiniciar Oozie, yarn, MapReduce2, HDFS e Hive um por um.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-firewall"></a>Firewall de armazenamento

Se você optar por proteger sua conta de armazenamento com as restrições de **redes virtuais e firewalls** em **redes selecionadas**, certifique-se de habilitar a exceção **permitir serviços confiáveis da Microsoft...** para que o HDInsight possa acessar sua conta de armazenamento`.`

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível acessar o armazenamento após a alteração da chave

Se você alterar a chave de uma conta de armazenamento, o HDInsight não poderá mais acessar a conta de armazenamento. O HDInsight usa uma cópia em cache da chave no core-site.xml do cluster. Essa cópia armazenada em cache deve ser atualizada para corresponder à nova chave.

Executar a ação de script novamente **não** atualiza a chave, pois o script verifica se já existe uma entrada para a conta de armazenamento. Se uma entrada já existir, ela não fará nenhuma alteração.

Para contornar esse problema:  
1. Remova a conta de armazenamento.
1. Adicione a conta de armazenamento.

> [!IMPORTANT]  
> Não há suporte para a rotação da chave de armazenamento para a conta de armazenamento primária anexada a um cluster.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a adicionar mais contas de armazenamento a um cluster HDInsight existente. Para saber mais sobre as ações de script, confira [Personalizar clusters HDInsight com base em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)
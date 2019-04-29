---
title: Usar o Azure Data Lake Storage Gen1 com o Hadoop no Azure HDInsight
description: Aprenda a consultar dados do Azure Data Lake Storage Gen1 e a armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 067bdcfc496fc986ae87620b7d57d7cad3a0f734
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766971"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Usar Data Lake Storage Gen1 com clusters Azure HDInsight

> [!Note] 
> Implante novos clusters HDInsight usando o [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) para ter um melhor desempenho e novos recursos.

Para analisar dados no cluster HDInsight, é possível armazenar os dados no [Armazenamento do Azure](../storage/common/storage-introduction.md), no [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) ou no [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md) ou em ambos. Todas as opções de armazenamento permitem que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

Neste artigo, você aprenderá como funciona o Data Lake Storage Gen1 com clusters HDInsight. Para saber como o Armazenamento do Azure funciona com clusters HDInsight, consulte [Usar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para saber mais sobre a criação de um cluster HDInsight, consulte [Criar clusters do Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> O Data Lake Storage Gen1 é sempre acessado por meio de um canal seguro, portanto não há nenhum nome do esquema de sistema de arquivos `adls`. Use sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilidade para clusters do HDInsight

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, é possível especificar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou, com o HDInsight 3.5 e versões posteriores, é possível selecionar o Armazenamento do Azure ou o Azure Data Lake Storage Gen1 como o sistema de arquivos padrão com algumas exceções. 

Os clusters HDInsight podem usar o Data Lake Storage Gen1 de duas maneiras:

* Como armazenamento padrão
* Como armazenamento adicional, com o Azure Storage Blob como o armazenamento padrão.

A partir de agora, somente alguns tipos/versões do cluster HDInsight dão suporte ao uso do Data Lake Storage Gen1 como armazenamento padrão e contas de armazenamento adicionais:

| Tipo de cluster HDInsight | Data Lake Storage Gen1 como armazenamento padrão | Data Lake Storage Gen1 como armazenamento adicional| Observações |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versão 4.0 | Não  | Não  |Não há suporte para o ADLS Gen1 com o HDInsight 4.0 |
| HDInsight versão 3.6 | sim | Sim | Com exceção do HBase|
| HDInsight versão 3.5 | sim | Sim | Com exceção do HBase|
| HDInsight versão 3.4 | Não | Sim | |
| HDInsight versão 3.3 | Não  | Não  | |
| HDInsight versão 3.2 | Não  | Sim | |
| Storm | | |É possível usar o Data Lake Storage Gen1 para gravar os dados de uma topologia do Storm. Também é possível usar o Data Lake Storage para dados de referência que, em seguida, podem ser lidos por uma topologia do Storm.|

> [!WARNING]  
> O HDInsight HBase não é compatível com o Azure Data Lake Storage Gen1

O uso do Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho nem a capacidade de ler ou gravar do cluster para o armazenamento do Azure.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Usar o Data Lake Storage Gen1 como armazenamento padrão

Quando o HDInsight é implantado com o Data Lake Storage Gen1 como armazenamento padrão, os arquivos relacionados ao cluster são armazenados no `adl://mydatalakestore/<cluster_root_path>/`, em que `<cluster_root_path>` é o nome de uma pasta que você cria no Data Lake Storage. Ao especificar um caminho raiz para cada cluster, você pode usar a mesma conta do Data Lake Storage para mais de um cluster. Portanto, você terá uma configuração em que:

* O Cluster1 pode usar o caminho `adl://mydatalakestore/cluster1storage`
* O Cluster2 pode usar o caminho `adl://mydatalakestore/cluster2storage`

Observe que os dois clusters usam a mesma conta **mydatalakestore** do Data Lake Storage Gen1. Cada cluster tem acesso ao seu próprio sistema de arquivos raiz no Data Lake Storage. A experiência de implantação do Portal do Azure em particular, solicita que você use um nome de pasta como **/clusters/\<clustername>** para o caminho raiz.

Para poder usar um Data Lake Storage Gen1 como armazenamento padrão, é necessário permitir o acesso à entidade de serviço para os seguintes caminhos:

- A raiz da conta do Data Lake Storage Gen1.  Por exemplo: adl://mydatalakestore/.
- A pasta para todas as pastas de cluster.  Por exemplo: adl://mydatalakestore/clusters.
- A pasta para o cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para saber mais sobre como criar a entidade de serviço e conceder acesso, confira Configurar acesso ao Data Lake Storage.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrair um certificado do Azure Key Vault para uso na criação do cluster

Se você quiser configurar o Azure Data Lake Storage Gen1 como seu armazenamento padrão para um novo cluster e o certificado da sua entidade de serviço estiver armazenado no Azure Key Vault, será necessário executar algumas etapas adicionais para converter o certificado no formato correto. Os trechos de código a seguir mostram como executar a conversão.

Primeiro, baixe o certificado do Key Vault e extraia o `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Em seguida, converta o `SecretValueText` em um certificado.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Depois, use o `$identityCertificate` para implantar um novo cluster, como no trecho a seguir:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Usar o Data Lake Storage Gen1 como armazenamento adicional

É possível usar o Data Lake Storage Gen1 como armazenamento adicional para o cluster também. Nesses casos, o armazenamento padrão do cluster pode ser um Azure Storage Blob ou uma conta do Data Lake Storage. Se você estiver executando trabalhos de HDInsight com os dados armazenados no Data Lake Storage como armazenamento adicional, use o caminho totalmente qualificado para os arquivos. Por exemplo: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Observe que agora não há **cluster_root_path** na URL. Isso ocorre porque o Data Lake Storage não é um armazenamento padrão nesse caso, portanto, tudo o que você precisa fazer é fornecer o caminho para os arquivos.

Para poder usar um Data Lake Storage Gen1 como armazenamento adicional, só é necessário permitir o acesso da entidade de serviço aos caminhos em que os arquivos são armazenados.  Por exemplo: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para saber mais sobre como criar a entidade de serviço e conceder acesso, confira Configurar acesso ao Data Lake Storage.


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Usar mais de uma conta do Data Lake Storage

Adicionar uma conta do Data Lake Storage como adicional e adicionar mais de uma conta do Data Lake Storage é feito dando ao cluster do HDInsight permissão sobre dados em uma ou mais contas do Data Lake Storage. Confira Configurar acesso ao Data Lake Storage.

## <a name="configure-data-lake-storage-access"></a>Configurar o acesso ao Data Lake Storage

Para configurar o acesso ao Data Lake Storage do seu cluster HDInsight, você precisa ter uma entidade de serviço do Azure AD (Azure Active Directory). Somente um administrador do Azure AD pode criar uma entidade de serviço. A entidade de serviço deve ser criada com um certificado. Para saber mais, consulte [Início Rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) e [Criar entidade de serviço com certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se você pretende usar o Azure Data Lake Storage Gen1 como armazenamento adicional para o cluster HDInsight, é altamente recomendável que faça isso ao criar o cluster, conforme descrito neste artigo. Não há suporte à adição do Azure Data Lake Storage Gen1 como armazenamento adicional a um cluster HDInsight.
>

## <a name="access-files-from-the-cluster"></a>Acessar arquivos do cluster

Há várias maneiras de acessar os arquivos no Data Lake Storage em um cluster HDInsight.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Usando o formato de caminho encurtado**. Com essa abordagem, você substitui o caminho até a raiz do cluster com adl:///. Portanto, no exemplo acima, é possível substituir `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` por `adl:///`.

        adl:///<file path>

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar. Por exemplo, se o caminho completo para o arquivo é:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Você pode acessar o mesmo arquivo sample.log usando esse caminho relativo em vez disso.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Criar clusters HDInsight com acesso ao Data Lake Storage Gen1

Use os links a seguir para obter instruções detalhadas sobre como criar clusters HDInsight com acesso ao Data Lake Storage Gen1.

* [Usando o Portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Usando o PowerShell (com o Data Lake Storage Gen1 como armazenamento padrão)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Usando o PowerShell (com o Data Lake Storage Gen1 como armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Usando modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Atualizar o certificado do HDInsight para acesso do Data Lake Storage Gen1

O código de exemplo do PowerShell a seguir lê um certificado de um arquivo local ou do Azure Key Vault e atualiza seu cluster HDInsight com o novo certificado para acessar o Azure Data Lake Storage Gen1. Forneça seu próprio nome do cluster HDInsight, nome do grupo de recursos, ID da assinatura, ID do aplicativo, caminho local para o certificado. Digite a senha quando solicitado.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Azure Data Lake Storage Gen1 compatível com HDFS com o HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo escalonáveis e use o HDInsight para desbloquear as informações nos dados armazenados estruturados e não estruturados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Início Rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Criar um cluster HDInsight para usar o Data Lake Storage Gen1 usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Apache Hive com HDInsight][hdinsight-use-hive]
* [Usar Apache Pig com o HDInsight][hdinsight-use-pig]
* [Usar Assinaturas de Acesso Compartilhado do Armazenamento do Azure para restringir o acesso a dados com o HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  

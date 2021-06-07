---
title: PowerShell-cluster HDInsight com Data Lake Storage Gen1-Azure
description: Use Azure PowerShell para criar e usar clusters do Azure HDInsight com Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5e899f28cf5b3c11ae5f935d7bc273c566214225
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606769"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Criar clusters do HDInsight com o Armazenamento de Data Lake do Azure Gen1 como armazenamento padrão usando o PowerShell

> [!div class="op_single_selector"]
> * [Use o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Aprenda a usar o Azure PowerShell para configurar clusters do Azure HDInsight com o Armazenamento de Data Lake do Azure Gen1, como armazenamento padrão. Para obter instruções sobre como criar um cluster do HDInsight com o Data Lake Storage Gen1 como armazenamento adicional, consulte [Crie um cluster do HDInsight com o Data Lake Storage Gen1 como armazenamento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

Aqui estão algumas considerações importantes para usar HDInsight com Data Lake Storage Gen1:

* A opção para criar clusters HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento padrão está disponível para as versões 3.5 e 3.6 do HDInsight.

* A opção para criar clusters HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento padrão é *não disponível* para clusters HDInsight Premium.

Para configurar o HDInsight para trabalhar com Data Lake armazenamento Gen1 usando o PowerShell, siga as instruções nas próximas cinco seções.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, verifique se você atende aos seguintes requisitos:

* **Uma assinatura do Azure**: acesse [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**: consulte [Como instalar e configurar o PowerShell](/powershell/azure/).
* **Software Development Kit do Windows (SDK do Windows)**: para instalar o SDK do Windows, acesse [Downloads e ferramentas para o Windows 10](https://dev.windows.com/downloads). O SDK é usado para criar um certificado de segurança.
* **Entidade de serviço do Azure Active Directory**: este tutorial descreve como criar uma entidade de serviço no Azure AD (Azure Active Directory). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você for um administrador, ignore esse pré-requisito e continue com o tutorial.

    >[!NOTE]
    >Você poderá criar uma entidade de serviço somente se for um administrador do Azure AD. O administrador do AD do Azure AD deve criar uma entidade de serviço antes de criar um cluster do HDInsight com o Data Lake Storage Gen1. A entidade de serviço deve ser criada com um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Criar uma conta do Azure Data Lake Storage Gen1

Para criar uma conta do Data Lake Storage Gen1, faça o seguinte:

1. Na área de trabalho, abra uma janela do PowerShell e insira os snippets abaixo. Quando receber uma solicitação para se conectar, conecte-se como um dos administradores ou proprietários da assinatura. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Se você registrar o provedor de recursos Data Lake Storage Gen1 e receber um erro semelhante a `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` , sua assinatura poderá não ser aprovada para data Lake Storage Gen1. Para habilitar sua assinatura do Azure para o Data Lake Storage Gen1, siga as instruções em [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md).
    >

2. Uma conta do Data Lake armazenamento Gen1 está associada um grupo de recursos do Azure. Comece criando um grupo de recursos.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Você deverá ver uma saída como esta:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Crie uma conta do Azure Data Lake Storage Gen1. O nome de conta especificado deve conter apenas letras minúsculas e números.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Você verá algo semelhante ao mostrado a seguir:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

4. Usar o Data Lake Storage Gen1 como armazenamento padrão requer que você especifique um caminho raiz no qual os arquivos específicos do cluster são copiados durante a criação do cluster. Para criar um caminho raiz, que é **/clusters/hdiadlcluster** no snippet, use os seguintes cmdlets:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurar a autenticação para acesso baseado em função para o Data Lake armazenamento Gen1
Cada assinatura do Azure está associada a uma entidade do Azure AD. Os usuários e serviços que acessam os recursos da assinatura usando o portal do Azure ou a API do Azure Resource Manager devem primeiro se autenticar no Azure AD. O acesso é concedido às assinaturas e serviços do Azure atribuindo a função apropriada para eles em um recurso do Azure. Para serviços, uma entidade de serviço identifica o serviço no Azure AD.

Esta seção ilustra como conceder a um serviço de aplicativo, como o HDInsight, acesso a um recurso do Azure (a conta Data Lake Storage Gen1 criada anteriormente). Isso é feito pela criação de uma entidade de serviço para o aplicativo e pela atribuição de funções a ela por meio do PowerShell.

Para configurar a autenticação do Active Directory para o Data Lake Storage Gen1, execute as tarefas nas duas seções a seguir.

### <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado
Verifique se o [SDK do Windows](https://dev.windows.com/en-us/downloads) está instalado antes de continuar com as etapas nesta seção. Também é necessário ter criado um diretório, como *C:\mycertdir*, no qual o certificado é criado.

1. Na janela do PowerShell, acesse a localização em que você instalou o SDK do Windows (normalmente, *C:\Program Files (x86)\Windows Kits\10\bin\x86*) e use o utilitário [MakeCert][makecert] para criar um certificado autoassinado e uma chave privada. Use os seguintes comandos:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Você receberá uma solicitação para inserir a senha da chave privada. Após a execução bem-sucedida do comando, você deverá ver **CertFile.cer** e **mykey.pvk** no diretório de certificado especificado.
2. Use o utilitário [Pvk2Pfx][pvk2pfx] para converter os arquivos .pvk e .cer criados pelo MakeCert em um arquivo .pfx. Execute o comando a seguir:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Quando receber a solicitação, insira a senha da chave privada especificada anteriormente. O valor especificado para o parâmetro **-po** é a senha associada ao arquivo .pfx. Depois que o comando for concluído com êxito, você também deverá ver um **CertFile. pfx** no diretório do certificado que você especificou.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Criar um Azure AD e uma entidade de serviço
Nesta seção, você cria uma entidade de serviço para um aplicativo do Azure AD, atribui uma função à entidade de serviço e se autentica como a entidade de serviço fornecendo um certificado. Para criar um aplicativo no Azure AD, execute os seguintes comandos:

1. Cole os seguintes cmdlets na janela do console do PowerShell. Verifique se o valor especificado para a propriedade **-DisplayName** é exclusivo. Os valores de **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Crie uma entidade de serviço usando a ID do aplicativo.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

    $objectId = $servicePrincipal.Id
    ```

3. Conceda ao serviço principal acesso à raiz do Data Lake Storage Gen1 e a todas as pastas no caminho raiz que você especificou anteriormente. Use os seguintes cmdlets:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Criar um cluster do HDInsight Linux com o Data Lake Storage Gen1 como armazenamento padrão

Nesta seção, você cria um cluster do HDInsight Hadoop Linux com o Data Lake Storage Gen1 como o armazenamento padrão. Para esta versão, o cluster HDInsight e o Data Lake Storage Gen1 devem estar no mesmo local.

1. Recupere a ID de locatário da assinatura e armazene-a para uso futuro.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Crie o cluster HDInsight usando os seguintes cmdlets:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name    # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>"     # e.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Após a conclusão bem-sucedida do cmdlet, você deverá ver uma saída que lista os detalhes do cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Executar trabalhos de teste no cluster HDInsight para usar Data Lake Storage Gen1
Depois de configurar um cluster do HDInsight, você poderá executar tarefas de teste nele para garantir que ele possa acessar o Data Lake Storage Gen1. Para fazer isso, execute um trabalho de hive de exemplo para criar uma tabela que usa os dados de exemplo que já estão disponíveis em Data Lake Storage Gen1 em *\<cluster root> /example/data/Sample.log*.

Nesta seção, você estabelece uma conexão SSH (Secure Shell) com o cluster Linux HDInsight criado e, em seguida, executa uma consulta do Hive de exemplo.

* Se estiver usando um cliente do Windows para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver usando um cliente do Linux para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Depois de estabelecer a conexão, inicie a CLI (interface de linha de comando) do Hive usando o seguinte comando:

    ```powershell
    hive
    ```

2. Use a CLI para inserir as instruções a seguir para criar uma nova tabela denominada **veículos** usando os dados de amostra no Data Lake Storage Gen1:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

Você deve ver a saída da consulta no console do SSH.

>[!NOTE]
>O caminho para os dados de exemplo no comando CREATE TABLE anterior é `adl:///example/data/`, em que `adl:///` é a raiz do cluster. Seguindo o exemplo da raiz do cluster especificada neste tutorial, o comando é `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. É possível usar a alternativa mais curta ou fornecer o caminho completo para a raiz do cluster.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Acesse o Data Lake Storage Gen1 usando os comandos do HDFS
Depois de configurar o cluster do HDInsight para usar o Data Lake Storage Gen1, você pode usar os comandos do shell do Hadoop Distributed File System (HDFS) para acessar o armazenamento.

Nesta seção, você estabelece uma conexão SSH com o cluster Linux HDInsight criado e, em seguida, executa os comandos do HDFS.

* Se estiver usando um cliente do Windows para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver usando um cliente do Linux para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de estabelecer a conexão, liste os arquivos no Data Lake Storage Gen1 usando o seguinte comando do sistema de arquivos HDFS.

```azurepowershell
hdfs dfs -ls adl:///
```

Também é possível usar o comando `hdfs dfs -put` para carregar alguns arquivos no Data Lake Storage Gen1 e, em seguida, usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## <a name="see-also"></a>Veja também
* [Usar Data Lake Storage Gen1 com clusters HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portal do Azure: criar um cluster de HDInsight para usar o Data Lake armazenamento Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
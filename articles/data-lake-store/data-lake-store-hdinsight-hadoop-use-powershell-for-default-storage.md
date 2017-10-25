---
title: "Criar clusters HDInsight com o Data Lake Store como armazenamento padrão usando o PowerShell | Microsoft Docs"
description: Usar o Azure PowerShell para criar e usar clusters HDInsight com o Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 64bd0a3ec0598fd7f78e93e510f0a6443f3edbd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Criar clusters HDInsight com o Data Lake Store como armazenamento padrão usando o PowerShell
> [!div class="op_single_selector"]
> * [Usar o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Saiba como usar o Azure PowerShell para configurar clusters Azure HDInsight com o Azure Data Lake Store como armazenamento padrão. Para obter instruções sobre como criar um cluster HDInsight com o Data Lake Store como armazenamento adicional, consulte [Criar um cluster HDInsight com o Data Lake Store como armazenamento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

Aqui estão algumas considerações importantes para usar o HDInsight com o Data Lake Store:

* A opção de criar clusters do HDInsight com acesso ao Data Lake Store como armazenamento padrão está disponível para o HDInsight versões 3.5 e 3.6.

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento padrão *não está disponível* para clusters HDInsight Premium.

Para configurar o HDInsight para trabalhar com o Data Lake Store usando o PowerShell, siga as instruções das próximas cinco seções.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, verifique se você atende aos seguintes requisitos:

* **Uma assinatura do Azure**: acesse [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**: consulte [Como instalar e configurar o PowerShell](/powershell/azure/overview).
* **Software Development Kit do Windows (SDK do Windows)**: para instalar o SDK do Windows, acesse [Downloads e ferramentas para o Windows 10](https://dev.windows.com/en-us/downloads). O SDK é usado para criar um certificado de segurança.
* **Entidade de serviço do Azure Active Directory**: este tutorial descreve como criar uma entidade de serviço no Azure AD (Azure Active Directory). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você for um administrador, ignore esse pré-requisito e continue com o tutorial.

    >[!NOTE]
    >Você poderá criar uma entidade de serviço somente se for um administrador do Azure AD. O administrador do Azure AD deverá criar uma entidade de serviço antes que você possa criar um cluster HDInsight com o Data Lake Store. A entidade de serviço deve ser criada com um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Criar uma conta do Repositório Data Lake
Para criar uma conta do Data Lake Store, faça o seguinte:

1. Na área de trabalho, abra uma janela do PowerShell e insira os trechos abaixo. Quando receber uma solicitação para se conectar, conecte-se como um dos administradores ou proprietários da assinatura. 

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Se você registrar o provedor de recursos do Data Lake Store e receber um erro semelhante a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, sua assinatura poderá não ter sido adicionada à lista de permissões do Data Lake Store. Para habilitar sua assinatura do Azure para a visualização pública do Data Lake Store, siga as instruções em [Introdução ao Azure Data Lake Store usando o portal do Azure](data-lake-store-get-started-portal.md).
    >

2. Uma conta do Data Lake Store está associada a um grupo de recursos do Azure. Comece criando um grupo de recursos.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Você verá uma saída semelhante à seguinte:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta do Data Lake Store. O nome de conta especificado deve conter apenas letras minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    Você verá algo semelhante ao mostrado a seguir:

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

4. Usar o Data Lake Store como armazenamento padrão exige a especificação de um caminho raiz para o qual os arquivos específicos ao cluster são copiados durante a criação do cluster. Para criar um caminho raiz, que é **/clusters/hdiadlcluster** no trecho, use os seguintes cmdlets:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurar a autenticação para acesso baseado em dados ao Repositório Data Lake
Cada assinatura do Azure está associada a uma entidade do Azure AD. Os usuários e serviços que acessam os recursos da assinatura usando o portal do Azure ou a API do Azure Resource Manager devem primeiro se autenticar no Azure AD. O acesso é concedido às assinaturas e serviços do Azure atribuindo a função apropriada para eles em um recurso do Azure. Para serviços, uma entidade de serviço identifica o serviço no Azure AD.

Esta seção ilustra como conceder a um serviço de aplicativo, como o HDInsight, o acesso a um recurso do Azure (a conta do Data Lake Store criada anteriormente). Isso é feito pela criação de uma entidade de serviço para o aplicativo e pela atribuição de funções a ela por meio do PowerShell.

Para configurar a autenticação do Active Directory para o Azure Data Lake, realize as tarefas das próximas duas seções.

### <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado
Verifique se o [SDK do Windows](https://dev.windows.com/en-us/downloads) está instalado antes de continuar com as etapas nesta seção. Também é necessário ter criado um diretório, como *C:\mycertdir*, no qual o certificado é criado.

1. Na janela do PowerShell, acesse a localização em que você instalou o SDK do Windows (normalmente, *C:\Program Files (x86)\Windows Kits\10\bin\x86*) e use o utilitário [MakeCert][makecert] para criar um certificado autoassinado e uma chave privada. Use os seguintes comandos:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Você receberá uma solicitação para inserir a senha da chave privada. Após a execução bem-sucedida do comando, você deverá ver **CertFile.cer** e **mykey.pvk** no diretório de certificado especificado.
2. Use o utilitário [Pvk2Pfx][pvk2pfx] para converter os arquivos .pvk e .cer criados pelo MakeCert em um arquivo .pfx. Execute o comando a seguir:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando receber a solicitação, insira a senha da chave privada especificada anteriormente. O valor especificado para o parâmetro **-po** é a senha associada ao arquivo .pfx. Após a conclusão bem-sucedida do comando, você também deverá ver um arquivo **CertFile.pfx** no diretório de certificado especificado.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Criar um Azure AD e uma entidade de serviço
Nesta seção, você cria uma entidade de serviço para um aplicativo do Azure AD, atribui uma função à entidade de serviço e se autentica como a entidade de serviço fornecendo um certificado. Para criar um aplicativo no Azure AD, execute os seguintes comandos:

1. Cole os seguintes cmdlets na janela do console do PowerShell. Verifique se o valor especificado para a propriedade **-DisplayName** é exclusivo. Os valores de **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Crie uma entidade de serviço usando a ID do aplicativo.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda acesso à raiz do Data Lake Store e a todas as pastas no caminho raiz especificado anteriormente à entidade de serviço. Use os seguintes cmdlets:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Criar um cluster Linux HDInsight com o Data Lake Store como o armazenamento padrão

Nesta seção, você cria um cluster Linux Hadoop HDInsight com o Data Lake Store como o armazenamento padrão. Para esta versão, o cluster HDInsight e o Data Lake Store devem estar na mesma localização.

1. Recupere a ID de locatário da assinatura e armazene-a para uso futuro.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Crie o cluster HDInsight usando os seguintes cmdlets:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
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

    Após a conclusão bem-sucedida do cmdlet, você deverá ver uma saída que lista os detalhes do cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Executar trabalhos de teste no cluster HDInsight para usar o Data Lake Store
Depois de configurar um cluster HDInsight, é possível executar trabalhos de teste nele para garantir que ele pode acessar o Data Lake Store. Para fazer isso, execute um trabalho do Hive de exemplo para criar uma tabela que usa os dados de exemplo que já estão disponíveis no Data Lake Store em *<cluster root>/example/data/sample.log*.

Nesta seção, você estabelece uma conexão SSH (Secure Shell) com o cluster Linux HDInsight criado e, em seguida, executa uma consulta do Hive de exemplo.

* Se estiver usando um cliente do Windows para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver usando um cliente do Linux para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Depois de estabelecer a conexão, inicie a CLI (interface de linha de comando) do Hive usando o seguinte comando:

        hive
2. Use a CLI para inserir as seguintes instruções para criar uma nova tabela chamada **vehicles** usando os dados de exemplo do Data Lake Store:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Você deve ver a saída da consulta no console do SSH.

    >[!NOTE]
    >O caminho para os dados de exemplo no comando CREATE TABLE anterior é `adl:///example/data/`, em que `adl:///` é a raiz do cluster. Seguindo o exemplo da raiz do cluster especificada neste tutorial, o comando é `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. É possível usar a alternativa mais curta ou fornecer o caminho completo para a raiz do cluster.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Acessar o Data Lake Store usando comandos do HDFS
Depois de configurar o cluster HDInsight para usar o Data Lake Store, você poderá usar os comandos do shell do HDFS (Sistema de Arquivos Distribuído Hadoop) para acessar o repositório.

Nesta seção, você estabelece uma conexão SSH com o cluster Linux HDInsight criado e, em seguida, executa os comandos do HDFS.

* Se estiver usando um cliente do Windows para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver usando um cliente do Linux para estabelecer uma conexão SSH com o cluster, consulte [Usar o SSH com o Hadoop baseado em Linux no HDInsight por meio do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de estabelecer a conexão, liste os arquivos do Data Lake Store usando o comando do sistema de arquivos HDFS a seguir.

    hdfs dfs -ls adl:///

Também é possível usar o comando `hdfs dfs -put` para carregar alguns arquivos no Data Lake Store e, em seguida, usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## <a name="see-also"></a>Consulte também
* [Portal do Azure: Criar um cluster HDInsight para usar o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

---
title: Introdução ao Azure Data Lake Storage Gen1-PowerShell | Microsoft Docs
description: Use Azure PowerShell para criar uma conta de Azure Data Lake Storage Gen1 e executar operações básicas.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485b14f85d68290c5447c885b9bc4974318f7952
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103723"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Introdução ao Azure Data Lake Store Gen1 usando Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como usar Azure PowerShell para criar uma conta de Azure Data Lake Storage Gen1 e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações sobre Data Lake Storage Gen1, consulte [visão geral do data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1,0 ou superior**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/).

## <a name="authentication"></a>Autenticação

Este artigo usa uma abordagem de autenticação mais simples com Data Lake Storage Gen1 em que você será solicitado a inserir suas credenciais de conta do Azure. O nível de acesso à conta do Data Lake Storage Gen1 e ao sistema de arquivos é controlado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens para autenticar com Data Lake Storage Gen1, que são a autenticação do usuário final ou a autenticação serviço a serviço. Para obter instruções e saber mais sobre como se autenticar, veja [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [Autenticação de serviço a serviço](./data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1

1. Na área de trabalho, abra uma nova janela do Windows PowerShell. Insira o snippet de código a seguir para entrar em sua conta do Azure, definir a assinatura e registrar o provedor do Data Lake Storage Gen1. Quando solicitado a fazer logon, certifique-se de fazer logon como um proprietário/administrador da assinatura:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Uma conta do Data Lake armazenamento Gen1 está associada um grupo de recursos do Azure. Comece criando um grupo de recursos.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Criar um grupo de recursos do Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um Grupo de Recursos do Azure")

1. Crie uma conta do Azure Data Lake Storage Gen1. O nome especificado deve conter apenas letras minúsculas e números.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Criar uma conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta do Data Lake Storage Gen1")

1. Verifique se a conta foi criada com êxito.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    A saída para o cmdlet deve ser **True**.

## <a name="create-directory-structures"></a>Criar estruturas de diretório

Você pode criar diretórios em sua conta do Data Lake Storage Gen1 para gerenciar e armazenar dados.

1. Especifique um diretório raiz.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Crie um novo diretório chamado **mynewdirectory** na raiz especificada.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Verifique se o novo diretório foi criado com êxito.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    Ele deverá exibir uma saída conforme mostrado na seguinte captura de tela:

    ![Verificar diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificar diretório")

## <a name="upload-data"></a>Carregar dados

É possível carregar seus dados no Data Lake Storage Gen1 diretamente no nível da raiz ou em um diretório que você criou na conta. Os snippets de código desta seção demonstram como carregar alguns dados de exemplo no diretório (**mynewdirectory**) criado na seção anterior.

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixe o arquivo e armazene-o em um diretório local no computador, como C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Renomear, baixar e excluir dados

Para renomear um arquivo, use o seguinte comando:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Para baixar um arquivo, use o seguinte comando:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Para excluir um arquivo, use o seguinte comando:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Quando solicitado, insira **Y** para excluir o item. Se você tiver mais de um arquivo para excluir, você pode fornecer todos os caminhos separados por vírgula.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Excluir sua conta

Use o comando a seguir para excluir sua conta do Data Lake Storage Gen1.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Quando solicitado, insira **Y** para excluir a conta.

## <a name="next-steps"></a>Próximas etapas

* [Orientação de ajuste de desempenho para o uso do PowerShell com o Armazenamento de Data Lake do Azure Gen1](data-lake-store-performance-tuning-powershell.md)
* [Use o Armazenamento de Data Lake do Azure Gen1 para requisitos de big data](data-lake-store-data-scenarios.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
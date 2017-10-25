---
title: "Use a interface de linha de comando do Azure 2.0 para começar a usar o Azure Data Lake Store | Microsoft Docs"
description: "Usar a linha de comando da plataforma cruzada do Azure 2.0 para criar uma conta do Data Lake Store e executar operações básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 431c974401c201a76b6d20de9837e44374716417
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Introdução ao Azure Data Lake Store usando a CLI do Azure 2.0
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Saiba como usar a CLI 2.0 para criar uma conta do Azure Data Lake Store e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para saber mais sobre o Data Lake Store, veja [Visão geral do Data Lake Store](data-lake-store-overview.md).

A CLI 2.0 do Azure é a nova experiência de linha de comando do Azure para gerenciar recursos do Azure. Ela pode ser usada em Windows, Linux e macOS. Para obter mais informações, consulte [Visão geral da CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/overview). Você também pode examinar a [referência da CLI 2.0 do Azure Data Lake Store](https://docs.microsoft.com/cli/azure/dls) para obter uma lista completa de comandos e sintaxes.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **CLI do Azure 2.0** -consulte [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções.

## <a name="authentication"></a>Autenticação

Este artigo usa uma abordagem de autenticação mais simples com o Data Lake Store, em que você faz logon como um usuário final. O nível de acesso à conta do Data Lake Store e ao sistema de arquivos é controlado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens para autenticar com o Data Lake Store, que são a **autenticação de usuário final** ou a **autenticação serviço a serviço**. Para obter instruções e saber mais sobre como se autenticar, veja [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [Autenticação de serviço a serviço](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Entre na sua assinatura do Azure

1. Faça logon em sua assinatura do Azure.

    ```azurecli
    az login
    ```

    Você recebe um código para usar na próxima etapa. Use um navegador da Web para abrir a página https://aka.ms/devicelogin e insira o código para autenticar. Você precisará fazer logon usando suas credenciais.

2. Depois de fazer logon, a janela listará todas as assinaturas do Azure que estão associadas à conta. Use o comando a seguir para usar uma assinatura específica.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Repositório Azure Data Lake

1. Crie um novo grupo de recursos. No comando a seguir fornecem os valores de parâmetro que você deseja usar. Se o nome do local contiver espaços, coloque-o entre aspas duplas. Por exemplo "Leste dos EUA 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Crie uma conta do Repositório Data Lake.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Criar pastas na conta do Repositório Data Lake

Você pode criar pastas em sua conta do Repositório Azure Data Lake para gerenciar e armazenar dados. Use o seguinte comando para criar uma pasta chamada **mynewfolder** na raiz do Data Lake Store.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> O parâmetro `--folder` faz com que o comando cria uma pasta. Se esse parâmetro não estiver presente, o comando criará um arquivo vazio chamado mynewfolder na raiz da conta do Data Lake Store.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Carregar dados na conta do Data Lake Store

É possível carregar dados no Data Lake Store diretamente no nível da raiz ou em uma pasta que você criou na conta. Os trechos de código abaixo demonstram como carregar alguns dados de exemplo no diretório (**mynewdirectory**) criado na seção anterior.

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixe o arquivo e armazene-o em um diretório local no computador, como C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Para o destino, você deve especificar o caminho completo, incluindo o nome do arquivo.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Listar arquivos em uma conta do Data Lake Store

Use o comando a seguir para listar os arquivos em uma conta do Repositório Data Lake.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

A saída desse comando deve ser:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Renomear, baixar e excluir dados de uma conta do Data Lake Store 

* **Para renomear um arquivo**, use o seguinte comando:
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Para baixar um arquivo**, use o comando a seguir. Verifique se o caminho de destino especificado já existe.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > O comando criará a pasta de destino se ela não existir.
    > 
    >

* **Para excluir um arquivo**, use o seguinte comando:
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Se você quiser excluir a pasta **mynewfolder** e o arquivo **vehicle1_09142014_copy.csv** juntos em um comando, use o parâmetro -- recurse

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Trabalhar com permissões e ACLs para uma conta do Data Lake Store

Nesta seção, saiba mais sobre como gerenciar ACLs e permissões usando a CLI do Azure 2.0. Para obter uma discussão detalhada sobre como as ACLs são implementadas no Azure Data Lake Store, confira [Controle de acesso no Azure Data Lake Store](data-lake-store-access-control.md).

* **Para atualizar o proprietário de um arquivo/pasta**, use o seguinte comando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Para atualizar as permissões para uma arquivo/pasta**, use o seguinte comando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Para obter as ACLs para um determinado caminho**, use o seguinte comando:

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    A saída deverá ser semelhante a esta:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Para definir uma entrada de uma ACL**, use o seguinte comando:

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Para remover uma entrada de uma ACL**, use o seguinte comando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Para remover uma ACL padrão inteira**, use o seguinte comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Para remover uma ACL não padrão inteira**, use o seguinte comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Excluir uma conta do Repositório do Data Lake
Use o comando a seguir para excluir sua conta do Repositório Data Lake.

```azurecli
az dls account delete --account mydatalakestore
```

Quando solicitado, insira **Y** para excluir a conta.

## <a name="next-steps"></a>Próximas etapas
* [Usar o Azure Data Lake Store para requisitos de big data](data-lake-store-data-scenarios.md) 
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

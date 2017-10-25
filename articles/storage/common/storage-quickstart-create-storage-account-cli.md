---
title: "Guia de início rápido do Azure – Criar uma conta de armazenamento usando a CLI do Azure | Microsoft Docs"
description: Aprenda rapidamente criar uma nova conta de armazenamento usando a CLI do Azure.
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: b1fb2da4acf6e06219d790f2354cada4f1e34285
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Criar uma conta de armazenamento usando a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia de início rápido detalha o uso da CLI do Azure para criar uma conta de armazenamento do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Este exemplo cria um grupo de recursos chamado *myResourceGroup* na região *eastus*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Se não tiver certeza de qual região especificar para o parâmetro `--location`, você poderá recuperar uma lista de regiões com suporte para a assinatura com o comando [az account list-locations](/cli/azure/account#list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Criar uma conta de armazenamento padrão de uso geral

Há vários tipos de contas de armazenamento apropriados para cenários de uso diferentes, cada um deles dá suporte a um ou mais dos serviços de armazenamento (blobs, arquivos, tabelas ou filas). A tabela a seguir fornece detalhes sobre os tipos de conta de armazenamento disponíveis.

|**Tipo de conta de armazenamento**|**Standard de uso geral**|**Premium de uso geral**|**Armazenamento de blobs, níveis de acesso quente e frio**|
|-----|-----|-----|-----|
|**Serviços compatíveis**| Serviços de Blob, Arquivo, Tabela e Fila | Serviço Blob | Serviço Blob|
|**Tipos de blobs compatíveis**|Blobs de blocos, blobs de páginas e blobs de acréscimo | Blobs de página | Blobs de blocos e blobs de acréscimo|

Crie uma conta de armazenamento padrão de uso geral com o comando [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos no seu grupo de recursos, incluindo a conta de armazenamento que você criou neste guia de início rápido, exclua o grupo de recursos com comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de recursos e uma conta de armazenamento padrão de uso geral. Para saber como transferir dados para e de sua conta de armazenamento, prossiga para o Guia de início rápido do Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Transferir objetos de e para o Armazenamento de Blobs do Azure usando a CLI do Azure](../blobs/storage-quickstart-blobs-cli.md)
---
title: Implantar o modelo do Azure com o token SAS e a CLI do Azure | Microsoft Docs
description: Use o Azure Resource Manager e a CLI do Azure para implantar recursos para o Azure de um modelo que é protegido com o token SAS.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: c869b76a0d1ba10bc27aefa60cbe4ed5b8d8201a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061349"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Implantar o modelo particular do Resource Manager com a CLI do Azure e o token SAS

Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso a ele e fornecer um token de SAS (Assinatura de Acesso Compartilhado) durante a implantação. Este tópico explica como usar o Azure PowerShell com modelos do Resource Manager para fornecer um token SAS durante a implantação. 

## <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado à conta de armazenamento

Você pode adicionar seus modelos a uma conta de armazenamento e vinculá-los durante a implantação com um token SAS.

> [!IMPORTANT]
> Seguindo as etapas abaixo, o blob que contém o modelo fica acessível somente para o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob fica acessível para qualquer pessoa com o URI. Se outro usuário interceptar o URI, esse usuário será capaz de acessar o modelo. Usar um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.
> 
> 

O seguinte exemplo configura um contêiner de conta de armazenamento privado e carrega um modelo:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Forneça um token SAS durante a implantação
Para implantar um modelo privado em uma conta de armazenamento, gere um token SAS e inclua-o no URI para o modelo. Defina a hora de vencimento de forma a permitir que haja tempo suficiente para concluir a implantação.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Para ver um exemplo de como usar um token SAS com modelos vinculados, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à implantação de modelos, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy-cli.md).
* Para um script de exemplo completo que implanta um modelo, veja [Implantar o script de modelo do Resource Manager](resource-manager-samples-cli-deploy.md)
* Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).

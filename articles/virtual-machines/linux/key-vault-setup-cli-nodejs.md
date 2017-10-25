---
title: Configurar o Key Vault para VMs do Linux com o CLI do Azure 1.0 | Microsoft Docs
description: "Como configurar o Key Vault para uso com uma máquina virtual do Azure Resource Manager com a CLI do Azure 1.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: fed612a354d45f34619f2a66bd40d78740c43ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Configurar o Key Vault para máquinas virtuais no Azure Resource Manager com a CLI do Azure 1.0
Na pilha do Azure Resource Manager, os certificados/segredos são modelados como recursos que são fornecidos pelo provedor de recursos do Key Vault. Para saber mais sobre o Cofre de Chaves do Azure, consulte [O que é o Cofre de Chaves do Azure?](../../key-vault/key-vault-whatis.md) Para que um Cofre de Chaves seja usado com máquinas virtuais do Azure Resource Manager, a propriedade *EnabledForDeployment* no Cofre de Chaves deverá ser definida como true. Você pode fazer isso em vários clientes. Este artigo mostra como configurar o Key Vault para uso com as máquinas virtuais do Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI do Azure 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa próxima geração de CLI para o modelo de implantação do resource manager

## <a name="use-cli-10-to-set-up-key-vault"></a>Usar a CLI 1.0 para configurar o Key Vault
Para criar um cofre de chaves usando a CLI (interface de linha de comando), consulte [Gerenciar Cofre da Chave usando a CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para a CLI 1.0, você precisa criar o Key Vault antes de atribuir a política de implantação. Você pode atribuir a diretiva usando o seguinte comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Usar modelos para configurar o Cofre de Chaves
Ao usar um modelo, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso de Cofre de Chaves.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para saber outras opções que você pode configurar ao criar um cofre de chaves usando modelos, consulte [Criar um cofre de chave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

---
title: Usar o Azure Key Vault com Aplicativos Gerenciados | Microsoft Docs
description: Mostra como usar segredos de acesso no Azure Key Vault ao implantar Aplicativos Gerenciados
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 55410250ccd4dfceac8ac9ae5b81d4736de0d91a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588191"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Acessar segredo do Azure Key Vault durante a implantação de Aplicativos Gerenciados do Azure

Quando você precisa passar um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível recuperar o valor de um [Azure Key Vault](../key-vault/key-vault-whatis.md). Para acessar o Key Vault ao implantar os Aplicativos Gerenciados, você precisa permitir acesso à entidade de serviço **Provedor de Recursos do Dispositivo**. O serviço de aplicativos gerenciados usa essa identidade para executar operações. Durante a implantação, a entidade de serviço deve ser capaz de acessar o Key Vault para recuperar um valor dele com êxito.

Este artigo descreve como configurar o Key Vault para trabalhar com os Aplicativos Gerenciados.

## <a name="enable-template-deployment"></a>Habilitar a implantação de modelo

1. No portal, selecione o Key Vault.

1. Selecione **Políticas de acesso**.   

   ![Selecionar as políticas de acesso](./media/key-vault-access/select-access-policies.png)

1. Selecione **Clique para exibir as políticas de acesso avançado**.

   ![Mostrar políticas de acesso avançado](./media/key-vault-access/advanced.png)

1. Selecione **Habilitar acesso ao Azure Resource Manager para implantação de modelos**. Em seguida, selecione **Salvar**.

   ![Habilitar a implantação de modelo](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Adicionar o serviço como colaborador

1. Selecione **IAM (Controle de acesso)**.

   ![Selecionar o controle de acesso](./media/key-vault-access/access-control.png)

1. Selecione **Adicionar atribuição de função**.

   ![Selecionar adicionar](./media/key-vault-access/add-access-control.png)

1. Selecione **Colaborador** para a função. Pesquise por **Provedor de Recursos do Dispositivo** e selecione-o entre as opções disponíveis.

   ![Pesquisar por provedor](./media/key-vault-access/search-provider.png)

1. Clique em **Salvar**.

## <a name="reference-key-vault-secret"></a>Referenciar segredo do Key Vault

Para transmitir um segredo de um Key Vault para um modelo em seu Aplicativo Gerenciado, você deve usar um [modelo vinculado](../azure-resource-manager/resource-group-linked-templates.md) e referenciar o Key Vault nos parâmetros para o modelo vinculado. Forneça a ID de recurso do Key Vault e o nome do segredo.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Próximas etapas

Você configurou seu Key Vault para ser acessível durante a implantação de um Aplicativo Gerenciado.

* Para obter informações sobre como passar um valor de um Key Vault como um parâmetro de modelo, veja [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Para obter exemplos de aplicativo gerenciado, veja [Projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para saber como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](create-uidefinition-overview.md).
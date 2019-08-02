---
title: Modos de implantação do Azure Resource Manager | Microsoft Docs
description: Descreve como especificar se um modo de implantação completo ou incremental deve ser usado com o Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: tomfitz
ms.openlocfilehash: 8a53ed1eea66c976c46a21378a9c48a1ad5ce902
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508211"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modos de implantação do Azure Resource Manager

Ao implantar os recursos, especifique que a implantação é uma atualização incremental ou uma atualização completa.  A principal diferença entre esses dois modos é como o Gerenciador de Recursos lida com recursos existentes no grupo de recursos que não estão no modelo. O modo padrão é incremental.

Para ambos os modos, o Resource Manager tenta criar todos os recursos especificados no modelo. Se o recurso já existe no grupo de recursos e suas configurações são as mesmas, nenhuma operação é realizada para esse recurso. Se você alterar os valores de propriedade de um recurso, o recurso será atualizado com os novos valores. Se você tentar atualizar o local ou o tipo de um recurso existente, a implantação falhará com um erro. Em vez disso, implante um novo recurso com o local ou o tipo de que você precisa.

## <a name="complete-mode"></a>Modo completo

No modo completo, o Gerenciador de recursos **exclui** recursos existentes no grupo de recursos, mas que não são especificados no modelo. Os recursos que estão especificados no modelo, mas que não foram implantados porque uma [condição](resource-group-authoring-templates.md#condition) foi avaliada como falsa, não são excluídos.

Tenha cuidado ao usar o modo completo com [copiar loops](resource-group-create-multiple.md). Todos os recursos que não são especificados no modelo depois de resolver o loop de cópia são excluídos.

Há algumas diferenças nos tipos de recurso lidarem com exclusões de modo completo. Os recursos pai serão excluídos automaticamente quando não estiverem em um modelo que é implantado no modo completo. Alguns recursos filho não são excluídos automaticamente quando não estão no modelo. No entanto, esses recursos filho serão excluídos se o recurso pai seja excluído. 

Por exemplo, se seu grupo de recursos contém uma zona DNS (tipo de recurso Microsoft.Network/dnsZones) e um registro CNAME (tipo de recurso Microsoft.Network/dnsZones/CNAME), a zona DNS é o recurso pai para o registro CNAME. Se você implantar com o modo completo e não incluir a zona DNS em seu modelo, a zona DNS e o registro CNAME são ambos excluídos. Se você incluir a zona DNS no seu modelo, mas não incluem o registro CNAME, o CNAME não é excluído. 

Para obter uma lista de como os tipos de recursos tratam a exclusão, confira [Exclusão de recursos do Azure para implantações no modo completo](complete-mode-deletion.md).

Se o grupo de recursos estiver [bloqueado](resource-group-lock-resources.md), modo completo não exclui os recursos.

> [!NOTE]
> Somente modelos de nível raiz suporte ao modo de implantação completa. Para ver os [modelos vinculados ou aninhados](resource-group-linked-templates.md), você deve usar o modo incremental. 
>
> [Implantações de nível de assinatura](deploy-to-subscription.md) não oferecem suporte para o modo completo.
>
> Atualmente, o portal não oferece suporte a modo completo.
>

## <a name="incremental-mode"></a>Modo incremental

No modo incremental, o Gerenciador de recursos **deixa inalterados** recursos existentes no grupo de recursos, mas que não são especificados no modelo.

No entanto, ao reimplantar um recurso existente no modo incremental, o resultado é uma opção diferente. Especifique todas as propriedades do recurso, não apenas aqueles que você está atualizando. Um erro comum é considerar as propriedades que não forem especificadas são permanecem inalteradas. Se você não especificar certas propriedades, o Resource Manager interpretará que a atualização está substituindo esses valores.

## <a name="example-result"></a>Resultados de exemplo

Para ilustrar a diferença entre os modos incrementais e completos, considere o cenário a seguir.

O **Grupo de recursos** contém:

* Recurso A
* Recurso B
* Recurso C

O **Modelo** contém:

* Recurso A
* Recurso B
* Recurso D

Quando implantada no **incremental** modo, o grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implantado no modo **completo**, o Recurso C é excluído. O grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso D

## <a name="set-deployment-mode"></a>Definir o modo de implantação

Para definir o modo de implantação ao implantar com o PowerShell, use o `Mode` parâmetro.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Para definir o modo de implantação ao implantar com a CLI do Azure, use o `mode` parâmetro.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

O exemplo a seguir mostra um conjunto de modelos vinculados para o modo de implantação incremental:

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar recursos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).
* Para exibir as operações para um provedor de recursos, consulte [API REST do Azure](/rest/api/).

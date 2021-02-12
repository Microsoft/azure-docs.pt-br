---
title: Modos de implantação
description: Descreve como especificar se um modo de implantação completo ou incremental deve ser usado com o Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 45eee255cec06925095ed0696c669b5c205f8b56
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724401"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modos de implantação do Azure Resource Manager

Ao implantar os recursos, especifique que a implantação é uma atualização incremental ou uma atualização completa. A diferença entre esses dois modos é como o Resource Manager lida com os recursos existentes no grupo de recursos que não estão no modelo.

Para ambos os modos, o Resource Manager tenta criar todos os recursos especificados no modelo. Se o recurso já existe no grupo de recursos e suas configurações são as mesmas, nenhuma operação é realizada para esse recurso. Se você alterar os valores de propriedade de um recurso, o recurso será atualizado com os novos valores. Se você tentar atualizar o local ou o tipo de um recurso existente, a implantação falhará com um erro. Em vez disso, implante um novo recurso com o local ou o tipo de que você precisa.

O modo padrão é incremental.

## <a name="complete-mode"></a>Modo completo

No modo completo, o Gerenciador de recursos **exclui** recursos existentes no grupo de recursos, mas que não são especificados no modelo.

> [!NOTE]
> Sempre use a [operação What-If](template-deploy-what-if.md) antes de implantar um modelo no modo completo. What-If mostra quais recursos serão criados, excluídos ou modificados. Use What-If para evitar a exclusão acidental de recursos.

Se o seu modelo inclui um recurso que não está implantado porque a [condição](conditional-resource-deployment.md) é avaliada como false, o resultado depende de qual versão da API REST você usa para implantar o modelo. Se você usar uma versão anterior à 2019-05-10, o recurso **não será excluído**. Com o 2019-05-10 ou posterior, o recurso **é excluído**. As versões mais recentes do Azure PowerShell e CLI do Azure excluir o recurso.

Tenha cuidado ao usar o modo completo com [loops de cópia](copy-resources.md). Todos os recursos que não são especificados no modelo após a resolução do loop de cópia são excluídos.

Se você implantar em [mais de um grupo de recursos em um modelo](./deploy-to-resource-group.md), os recursos no grupo de recursos especificado na operação de implantação poderão ser excluídos. Os recursos nos grupos de recursos secundários não são excluídos.

Há algumas diferenças em como os tipos de recursos lidam com exclusões de modo completo. Os recursos pai serão excluídos automaticamente quando não estiverem em um modelo que é implantado no modo completo. Alguns recursos filho não são excluídos automaticamente quando não estão no modelo. No entanto, esses recursos filho serão excluídos se o recurso pai for excluído.

Por exemplo, se o grupo de recursos contiver uma zona DNS ( `Microsoft.Network/dnsZones` tipo de recurso) e um registro CNAME ( `Microsoft.Network/dnsZones/CNAME` tipo de recurso), a zona DNS será o recurso pai do registro CNAME. Se você implantar com o modo completo e não incluir a zona DNS em seu modelo, a zona DNS e o registro CNAME são ambos excluídos. Se você incluir a zona DNS em seu modelo, mas não incluir o registro CNAME, o CNAME não será excluído.

Para obter uma lista de como os tipos de recursos tratam a exclusão, confira [Exclusão de recursos do Azure para implantações no modo completo](complete-mode-deletion.md).

Se o grupo de recursos estiver [bloqueado](../management/lock-resources.md), o modo completo não excluirá os recursos.

> [!NOTE]
> Somente modelos de nível raiz suporte ao modo de implantação completa. Para ver os [modelos vinculados ou aninhados](linked-templates.md), você deve usar o modo incremental.
>
> [Implantações de nível de assinatura](deploy-to-subscription.md) não dão suporte ao modo completo.
>
> Atualmente, o portal não dá suporte ao modo completo.
>

## <a name="incremental-mode"></a>Modo incremental

No modo incremental, o Gerenciador de recursos **deixa inalterados** recursos existentes no grupo de recursos, mas que não são especificados no modelo. Os recursos no modelo **são adicionados** ao grupo de recursos.

> [!NOTE]
> Ao reimplantar um recurso existente no modo incremental, todas as propriedades são reaplicadas. As **Propriedades não são adicionadas incrementalmente**. Um mal-entendido comum é considerar que as propriedades que não são especificadas no modelo permanecem inalteradas. Se você não especificar determinadas propriedades, o Resource Manager interpretará a implantação como substituindo esses valores. As propriedades que não estão incluídas no modelo são redefinidas para os valores padrão. Especifique todos os valores não padrão para o recurso, não apenas aqueles que você está atualizando. A definição de recurso no modelo sempre contém o estado final do recurso. Ele não pode representar uma atualização parcial para um recurso existente.
>
> Em casos raros, as propriedades que você especifica para um recurso são realmente implementadas como um recurso filho. Por exemplo, quando você fornece valores de configuração de site para um aplicativo Web, esses valores são implementados no tipo de recurso filho `Microsoft.Web/sites/config` . Se você reimplantar o aplicativo Web e especificar um objeto vazio para os valores de configuração do site, o recurso filho não será atualizado. No entanto, se você fornecer novos valores de configuração de site, o tipo de recurso filho será atualizado.

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
az deployment group create \
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
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2017-05-10",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
          <nested-template-or-external-template>
    }
  }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como criar modelos do Resource Manager, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para saber mais sobre a implantação de recursos, consulte [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
* Para exibir as operações para um provedor de recursos, consulte [API REST do Azure](/rest/api/).

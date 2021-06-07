---
title: Use parâmetros para criar blueprints dinâmicos
description: Saiba mais sobre os parâmetros estáticos e dinâmicos e como usá-los para criar plantas dinâmicas e seguras.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 5dbf7ec02e89eac791ec3e17202a5ab13a04b81d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918527"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Criando blueprints dinâmicos por meio de parâmetros

Um plano gráfico totalmente definido com vários artefatos, como grupos de recursos, modelos de Azure Resource Manager (modelos ARM), políticas ou atribuições de função, oferece a criação rápida e criação consistente de objetos no Azure. Para permitir o uso flexível desses contêineres e padrões de design reutilizáveis, o Azure Blueprints dá suporte a parâmetros. O parâmetro cria flexibilidade, tanto durante a definição quanto na atribuição, para alterar as propriedades nos artefatos implantados pelo blueprint.

Um exemplo simples é o artefato do grupo de recursos. Quando um grupo de recursos é criado, ele tem dois valores obrigatórios que devem ser fornecidos: nome e local. Ao adicionar um grupo de recursos ao seu plano gráfico, se não houver parâmetros, você definiria esse nome e o local para cada uso do plano gráfico. Essa repetição faria com que todo uso do blueprint criasse artefatos no mesmo grupo de recursos. Os recursos dentro desse grupo de recursos seriam duplicados e causariam um conflito.

> [!NOTE]
> Não é um problema o fato de dois blueprints diferentes incluírem um grupo de recursos com o mesmo nome.
> Se um grupo de recursos incluído em um blueprint já existir, o blueprint continuará criando os artefatos relacionados nesse grupo de recursos. Isso poderia causar um conflito, pois dois recursos com o mesmo nome e tipo de recurso não podem existir dentro de uma assinatura.

A solução para esse problema é parâmetros. Os planos gráficos do Azure permitem que você defina o valor de cada propriedade do artefato durante a atribuição de uma assinatura. O parâmetro possibilita a reutilização de um blueprint que cria um grupo de recursos e outros recursos em uma única assinatura sem conflito.

## <a name="blueprint-parameters"></a>Parâmetros de blueprint

Por meio da API REST, os parâmetros podem ser criados no próprio blueprint. Esses parâmetros são diferentes dos parâmetros em cada um dos artefatos suportados. Quando um parâmetro é criado no blueprint, ele pode ser usado pelos artefatos nesse blueprint. Um exemplo pode ser o prefixo para nomenclatura do grupo de recursos. O artefato pode usar o parâmetro blueprint para criar um parâmetro "principalmente dinâmico". Como o parâmetro também pode ser definido durante a atribuição, esse padrão permite uma consistência que pode aderir às regras de nomenclatura. Para obter as etapas, confira [definindo parâmetros estáticos – parâmetro do nível de blueprint](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Usando parâmetros secureString e secureObject

Embora um _artefato_ de modelo do ARM dê suporte a parâmetros dos tipos **secureString** e **Secureobject** , os planos gráficos do Azure exigem que cada um seja conectado a um Azure Key Vault. Essa medida de segurança impede a prática insegura de armazenar segredos junto com o Blueprint e incentiva o emprego de padrões seguros. Os planos gráficos do Azure dão suporte a essa medida de segurança, detectando a inclusão de um parâmetro seguro em um _artefato_ de modelo ARM. O serviço solicita, durante a atribuição, as seguintes propriedades do Key Vault por parâmetro seguro detectado:

- ID do recurso do Key Vault
- Nome do segredo do Key Vault
- Versão do segredo do Key Vault

Se a atribuição Blueprint usar uma **identidade gerenciada atribuída pelo sistema**, o Key Vault referenciado _deverá_ existir na mesma assinatura à qual a definição de Blueprint está atribuída.

Se a atribuição Blueprint usar uma **identidade gerenciada atribuída pelo usuário**, o Key Vault referenciado _poderá_ existir em uma assinatura centralizada. A identidade gerenciada deve receber os direitos apropriados no Key Vault antes da atribuição de Blueprint.

> [!IMPORTANT]
> Em ambos os casos, o Key Vault deve ter **habilitar acesso ao Azure Resource Manager para implantação de modelo** configurada na página **políticas de acesso** . Para obter instruções sobre como habilitar esse recurso, confira [Key Vault – Habilitar implantação de modelo](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Para obter mais informações sobre o Azure Key Vault, confira [Visão geral do Key Vault](../../../key-vault/general/overview.md).

## <a name="parameter-types"></a>Tipos de parâmetro

### <a name="static-parameters"></a>Parâmetros estáticos

Um valor de parâmetro definido na definição de um blueprint é chamado de **parâmetro estático**, porque todo uso do blueprint implantará o artefato usando esse valor estático. No exemplo do grupo de recursos, embora não faça sentido para o nome do grupo de recursos, pode fazer sentido para o local. Em seguida, todas as atribuições do blueprint criariam o grupo de recursos, o que quer que seja chamado durante a atribuição, no mesmo local. Essa flexibilidade permite que você seja seletivo em relação ao que você define como necessário em relação ao que pode ser alterado durante a atribuição.

#### <a name="setting-static-parameters-in-the-portal"></a>Parâmetros de configuração estáticos no portal

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Definições do blueprint** na página à esquerda.

1. Selecione um plano gráfico existente e, em seguida, selecione **Editar plano gráfico** ou selecione **+ criar plano gráfico** e preencha as informações na guia **noções básicas** .

1. Selecione **Avançar: artefatos** ou selecione a guia **artefatos** .

1. Os artefatos adicionados ao blueprint que têm opções de parâmetro exibem **X de Y parâmetros populados** na coluna **Parâmetros**. Selecione a linha de artefato para editar os parâmetros de artefato.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Captura de tela de uma definição de plano gráfico e o ' X dos parâmetros Y preenchidos ' realçado." border="false":::

1. A página **Editar artefato** exibe as opções de valor apropriadas para o artefato selecionado. Cada parâmetro no artefato tem um título, uma caixa de valor e uma caixa de seleção. Defina a caixa como desmarcada para torná-la um **parâmetro estático**. No exemplo abaixo, apenas _Local_ é um **parâmetro estático**, pois está desmarcado e _Nome do Grupo de Recursos_ está marcado.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Captura de tela de parâmetros estáticos em um artefato de Blueprint." border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Definindo parâmetros estáticos com base na API REST

Em cada URI da API REST, há variáveis usadas que precisam ser substituídas com seus próprios valores:

- `{YourMG}`: substitua pelo nome do seu grupo de gerenciamento
- `{subscriptionId}`: substitua por sua ID da assinatura

##### <a name="blueprint-level-parameter"></a>Parâmetro de nível do Blueprint

Ao criar um blueprint por meio da API REST, é possível criar [parâmetros de blueprint](#blueprint-parameters). Para fazer isso, use o seguinte URI da API REST e o formato do corpo:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Após a criação de um parâmetro de nível de blueprint, ela pode ser usado em artefatos adicionados a esse blueprint.
O exemplo de API REST a seguir cria um artefato de atribuição de função no blueprint e usa o parâmetro de nível de blueprint.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Corpo da solicitação

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

Neste exemplo, a propriedade **principalIds** usa o parâmetro de nível de blueprint **owners** usando um valor de `[parameters('owners')]`. Definir um parâmetro em um artefato usando um parâmetro de nível de blueprint ainda é um exemplo de um **parâmetro estático**. O parâmetro de nível de blueprint não pode ser definido durante a atribuição de blueprint e será o mesmo valor em cada atribuição.

##### <a name="artifact-level-parameter"></a>Parâmetro de nível do artefato

A criação de **parâmetros estáticos** em um artefato é semelhante, mas usa um valor direto em vez de usar a função `parameters()`. O exemplo a seguir cria dois parâmetros estáticos, **tagName** e **tagValue**. O valor de cada um é fornecido diretamente e não usa uma chamada de função.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Corpo da solicitação

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parâmetros dinâmicos

O oposto de um parâmetro estático é um **parâmetro dinâmico**. Esse parâmetro não está definido no blueprint, mas é definido durante cada atribuição do blueprint. No exemplo do grupo de recursos, o uso de um parâmetro **dinâmico** faz sentido para o nome do grupo de recursos. Ele fornece um nome diferente para cada designação do blueprint. Para obter uma lista de funções de plano gráfico, consulte a referência de [funções de Blueprint](../reference/blueprint-functions.md) .

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Definindo parâmetros dinâmicos no portal

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Definições do blueprint** na página à esquerda.

1. Clique com o botão direito do mouse no blueprint que você deseja atribuir. Selecione **atribuir plano gráfico** ou selecione o plano gráfico que você deseja atribuir e use o botão **atribuir Blueprint** .

1. Na página **atribuir Blueprint** , localize a seção **parâmetros de artefato** . Cada artefato com pelo menos um **parâmetro dinâmico** exibe o artefato e as opções de configuração. Forneça os valores necessários aos parâmetros antes de atribuir o blueprint. No exemplo abaixo, _Nome_ é um **parâmetro dinâmico** que deve ser definido para concluir a atribuição do blueprint.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Captura de tela da configuração de parâmetros dinâmicos durante a atribuição do Blueprint." border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Definindo parâmetros dinâmicos com base na API REST

Definir **parâmetros dinâmicos** durante a atribuição é feito inserindo o valor diretamente. Em vez de usar uma função, como [Parameters ()](../reference/blueprint-functions.md#parameters), o valor fornecido é uma cadeia de caracteres apropriada. Os artefatos de um grupo de recursos são definidos com as propriedades "nome do modelo", **nome** e **local**. Todos os outros parâmetros do artefato incluído são definidos em **parâmetros** com um **\<name\>** par de chaves e **valor** . Se o blueprint estiver configurado para um parâmetro dinâmico que não é fornecido durante a atribuição, a atribuição falhará.

- URI da API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Próximas etapas

- Consulte a lista de [funções de plantas](../reference/blueprint-functions.md).
- Saiba mais sobre o [ciclo de vida do blueprint](./lifecycle.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](./sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](./resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).
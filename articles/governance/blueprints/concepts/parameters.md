---
title: Use parâmetros para criar blueprints dinâmicos
description: Conheça parâmetros estáticos e dinâmicos e como sua utilização cria blueprints dinâmicos.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: ac7b662bc9ef4f3ae675c4cbde18e159383d3d8e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767039"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Criando blueprints dinâmicos por meio de parâmetros

Um blueprint totalmente definido com vários artefatos (como grupos de recursos, modelos, diretivas ou atribuições de funções do Resource Manager) oferece a criação rápida e a criação consistente de objetos no Azure. Para permitir o uso flexível desses contêineres e padrões de design reutilizáveis, o Azure Blueprints dá suporte a parâmetros. O parâmetro cria flexibilidade, tanto durante a definição quanto na atribuição, para alterar as propriedades nos artefatos implantados pelo blueprint.

Um exemplo simples é o artefato do grupo de recursos. Quando um grupo de recursos é criado, ele tem dois valores obrigatórios que devem ser fornecidos: nome e local. Ao adicionar um grupo de recursos ao seu blueprint, se os parâmetros não existissem, você definiria esse nome e local para todo uso do blueprint. Essa repetição faria com que todo uso do blueprint criasse artefatos no mesmo grupo de recursos. Os recursos dentro desse grupo de recursos seriam duplicados e causariam um conflito.

> [!NOTE]
> Não é um problema o fato de dois blueprints diferentes incluírem um grupo de recursos com o mesmo nome.
> Se um grupo de recursos incluído em um blueprint já existir, o blueprint continuará criando os artefatos relacionados nesse grupo de recursos. Isso poderia causar um conflito, pois dois recursos com o mesmo nome e tipo de recurso não podem existir dentro de uma assinatura.

A solução para esse problema é parâmetros. Blueprints permite que você defina o valor de cada propriedade do artefato durante a atribuição a uma assinatura. O parâmetro possibilita a reutilização de um blueprint que cria um grupo de recursos e outros recursos em uma única assinatura sem conflito.

## <a name="blueprint-parameters"></a>Parâmetros de blueprint

Por meio da API REST, os parâmetros podem ser criados no próprio blueprint. Esses parâmetros são diferentes dos parâmetros em cada um dos artefatos suportados. Quando um parâmetro é criado no blueprint, ele pode ser usado pelos artefatos nesse blueprint. Um exemplo pode ser o prefixo para nomenclatura do grupo de recursos. O artefato pode usar o parâmetro blueprint para criar um parâmetro "principalmente dinâmico". Como o parâmetro também pode ser definido durante a atribuição, esse padrão permite uma consistência que pode aderir às regras de nomenclatura. Para obter as etapas, confira [definindo parâmetros estáticos – parâmetro do nível de blueprint](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Usando parâmetros secureString e secureObject

Embora um _artefato_ do modelo do Resource Manager seja compatível com parâmetros dos tipos **secureString** e **secureObject**, o Azure Blueprints requer que cada um esteja conectado a um Azure Key Vault.
Essa medida de segurança impede a prática insegura de armazenar segredos junto com o Blueprint e incentiva o emprego de padrões seguros. O Blueprint do Azure suporta essa medida de segurança, detectando a inclusão de um parâmetro seguro em um modelo do Resource Manager _artefato_. O serviço solicita, durante a atribuição, as seguintes propriedades do Key Vault por parâmetro seguro detectado:

- ID do recurso do Key Vault
- Nome do segredo do Key Vault
- Versão do segredo do Key Vault

Se usar a atribuição de planta uma **atribuído pelo sistema de identidade gerenciada**, o referenciado Key Vault _deve_ existir na mesma assinatura que a definição de planta é atribuída a.

Se usa a atribuição de planta uma **usuário atribuído a identidade gerenciada**, o referenciado Key Vault _pode_ existe em uma assinatura centralizada. A identidade gerenciada deve receber direitos apropriados no cofre de chaves antes da atribuição de planta.

Em ambos os casos, o Cofre de chaves deve ter **habilitar o acesso ao Azure Resource Manager para implantação de modelo** configurado na **políticas de acesso** página. Para obter instruções sobre como habilitar esse recurso, confira [Key Vault – Habilitar implantação de modelo](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Para obter mais informações sobre o Azure Key Vault, confira [Visão geral do Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Tipos de parâmetro

### <a name="static-parameters"></a>Parâmetros estáticos

Um valor de parâmetro definido na definição de um blueprint é chamado de **parâmetro estático**, porque todo uso do blueprint implantará o artefato usando esse valor estático. No exemplo do grupo de recursos, embora não faça sentido para o nome do grupo de recursos, pode fazer sentido para o local. Em seguida, todas as atribuições do blueprint criariam o grupo de recursos, o que quer que seja chamado durante a atribuição, no mesmo local. Essa flexibilidade permite que você seja seletivo em relação ao que você define como necessário em relação ao que pode ser alterado durante a atribuição.

#### <a name="setting-static-parameters-in-the-portal"></a>Parâmetros de configuração estáticos no portal

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Definições do blueprint** na página à esquerda.

1. Clique em um projeto existente e, em seguida, clique em **blueprint de edição** ou clique em **+ criar blueprint** e preencha as informações sobre a **Noções básicas** guia.

1. Clique em **Avançar: Artefatos** OU clique na guia **Artefatos**.

1. Os artefatos adicionados ao blueprint que têm opções de parâmetro exibem **X de Y parâmetros populados** na coluna **Parâmetros**. Clique na linha do artefato para editar os seus parâmetros.

   ![Parâmetros de plano gráfico em uma definição de planta](../media/parameters/parameter-column.png)

1. A página **Editar artefato** exibe opções de valor apropriadas para o artefato clicado. Cada parâmetro no artefato tem um título, uma caixa de valor e uma caixa de seleção. Defina a caixa como desmarcada para torná-la um **parâmetro estático**. No exemplo abaixo, apenas _Local_ é um **parâmetro estático**, pois está desmarcado e _Nome do Grupo de Recursos_ está marcado.

   ![Parâmetros estáticos do plano gráfico em um artefato do blueprint](../media/parameters/static-parameter.png)

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

O oposto de um parâmetro estático é um **parâmetro dinâmico**. Esse parâmetro não está definido no blueprint, mas é definido durante cada atribuição do blueprint. No exemplo do grupo de recursos, o uso de um parâmetro **dinâmico** faz sentido para o nome do grupo de recursos. Ele fornece um nome diferente para cada designação do blueprint. Para obter uma lista de funções de especificação técnica, consulte a [blueprint funções](../reference/blueprint-functions.md) referência.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Definindo parâmetros dinâmicos no portal

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Definições do blueprint** na página à esquerda.

1. Clique com o botão direito do mouse no blueprint que você deseja atribuir. Selecione **Assign blueprint** ou clique no plano de gráfico que você deseja atribuir e clique no **atribuir blueprint** botão.

1. No **Assign blueprint** página, localize o **parâmetros de artefato** seção. Cada artefato com pelo menos um **parâmetro dinâmico** exibe o artefato e as opções de configuração. Forneça os valores necessários aos parâmetros antes de atribuir o blueprint. No exemplo abaixo, _Nome_ é um **parâmetro dinâmico** que deve ser definido para concluir a atribuição do blueprint.

   ![Parâmetro dinâmico do BluePrint durante a atribuição de planta](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Definindo parâmetros dinâmicos com base na API REST

Definir **parâmetros dinâmicos** durante a atribuição é feito inserindo o valor diretamente. Em vez de usar uma função, como [parameters()](../reference/blueprint-functions.md#parameters), o valor fornecido é uma cadeia de caracteres apropriada. Os artefatos de um grupo de recursos são definidos com as propriedades "nome do modelo", **nome** e **local**. Todos os outros parâmetros para o artefato incluído são definidos em **parâmetros** com um par de **\<nomes\>** e **valor**. Se o blueprint estiver configurado para um parâmetro dinâmico que não é fornecido durante a atribuição, a atribuição falhará.

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

- Consulte a lista de [blueprint funções](../reference/blueprint-functions.md).
- Saiba mais sobre o [ciclo de vida do blueprint](lifecycle.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).
---
title: Erros de recurso não encontrado
description: Descreve como resolver erros quando um recurso não pode ser encontrado. O erro pode ocorrer ao implantar um modelo de Azure Resource Manager ou ao tomar ações de gerenciamento.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b80c32683190167d5c0d6e0a7f75acce8bbdb833
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950868"
---
# <a name="resolve-resource-not-found-errors"></a>Resolver erros de recurso não encontrado

Este artigo descreve o erro que você vê quando um recurso não pode ser encontrado durante uma operação. Normalmente, você vê esse erro ao implantar recursos. Você também verá esse erro ao realizar tarefas de gerenciamento e Azure Resource Manager não conseguir localizar o recurso necessário. Por exemplo, se você tentar adicionar marcas a um recurso que não existe, você receberá esse erro.

## <a name="symptom"></a>Sintoma

Há dois códigos de erro que indicam que o recurso não foi encontrado. O erro não **encontrado** retorna um resultado semelhante a:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

O erro **ResourceNotFound** retorna um resultado semelhante a:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

O Resource Manager precisa recuperar as propriedades de um recurso, mas não consegue localizar o recurso em suas assinaturas.

## <a name="solution-1---check-resource-properties"></a>Solução 1-verificar as propriedades do recurso

Quando você receber esse erro durante uma tarefa de gerenciamento, verifique os valores fornecidos para o recurso. Os três valores a serem verificados são:

* Nome do recurso
* Nome do grupo de recursos
* Subscription

Se você estiver usando o PowerShell ou CLI do Azure, verifique se está executando o comando na assinatura que contém o recurso. Você pode alterar a assinatura com Set [-AzContext](/powershell/module/Az.Accounts/Set-AzContext) ou [AZ Account Set](/cli/azure/account#az-account-set). Muitos comandos também fornecem um parâmetro de assinatura que permite especificar uma assinatura diferente do contexto atual.

Se você estiver tendo problemas para verificar as propriedades, entre no [portal](https://portal.azure.com). Localize o recurso que você está tentando usar e examine o nome do recurso, o grupo de recursos e a assinatura.

## <a name="solution-2---set-dependencies"></a>Solução 2 – definir dependências

Se você receber esse erro ao implantar um modelo, talvez seja necessário adicionar uma dependência. O Gerenciador de Recursos otimiza a implantação criando recursos em paralelo, quando possível. Se um recurso precisar ser implantado após outro recurso, você precisará usar o elemento **dependsOn** em seu modelo. Por exemplo, ao implantar um aplicativo Web, o plano do Serviço de Aplicativo deve existir. Se você não tiver especificado que o aplicativo Web depende do plano do Serviço de Aplicativo, o Gerenciador de Recursos criará os dois recursos ao mesmo tempo. Você recebe um erro informando que o recurso do plano do Serviço de Aplicativo não pode ser encontrado porque ele ainda não existe ao tentar definir uma propriedade no aplicativo Web. Você evita esse erro configurando a dependência no aplicativo Web.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Mas, se você quiser evitar definir dependências que não são necessárias. Quando você tiver dependências desnecessárias, você prolongar a duração da implantação, impedindo que os recursos que não são dependentes entre si seja implantado em paralelo. Além disso, você pode criar dependências circulares que bloqueiam a implantação. A função [reference](template-functions-resource.md#reference) e as funções [list*](template-functions-resource.md#list) criam uma dependência implícita no recurso referenciado, quando esse recurso é implantado no mesmo modelo e é referenciado por seu nome (não pela ID de recurso). Portanto, você pode ter dependências mais que as dependências especificada no **dependsOn** propriedade. A função [resourceId](template-functions-resource.md#resourceid) não cria uma dependência implícita ou valida que o recurso existe. A função [reference](template-functions-resource.md#reference) e as funções [list*](template-functions-resource.md#list) não criam uma dependência implícita quando o recurso for referenciado por sua ID de recurso. Para criar uma dependência implícita, passe o nome do recurso que é implantado no mesmo modelo.

Quando você vir os problemas de dependência, você precisa obter informações sobre a ordem de implantação de recursos. Para exibir a ordem das operações de implantação:

1. Selecione o histórico de implantação do grupo de recursos.

   ![selecionar o histórico de implantação](./media/error-not-found/select-deployment.png)

2. Selecione uma implantação no histórico e selecione **Eventos**.

   ![selecionar os eventos de implantação](./media/error-not-found/select-deployment-events.png)

3. Examine a sequência de eventos de cada recurso. Preste atenção ao status de cada operação. Por exemplo, a imagem a seguir mostra três contas de armazenamento implantadas em paralelo. Observe que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implantação paralela](./media/error-not-found/deployment-events-parallel.png)

   A imagem a seguir mostra três contas de armazenamento que não são implantadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento e a terceira conta de armazenamento depende da segunda conta de armazenamento. A primeira conta de armazenamento é iniciada, aceita e concluída antes que a próxima seja iniciada.

   ![implantação sequencial](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Solução 3 – obter recurso externo

Ao implantar um modelo e você precisar obter um recurso que existe em uma assinatura ou grupo de recursos diferente, use a [função ResourceId](template-functions-resource.md#resourceid). Essa função retorna para obter o nome totalmente qualificado do recurso.

Os parâmetros de grupo de recursos e assinatura na função ResourceId são opcionais. Se você não fornecê-los, eles assumem como padrão a assinatura atual e o grupo de recursos. Ao trabalhar com um recurso em um grupo de recursos ou assinatura diferente, certifique-se de fornecer esses valores.

O exemplo a seguir obtém a ID de recurso de um recurso que existe em um grupo de recursos diferente.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Solução 4 – obter a identidade gerenciada do recurso

Se você estiver implantando um recurso que cria implicitamente uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md), deverá aguardar até que esse recurso seja implantado antes de recuperar os valores na identidade gerenciada. Se você passar o nome da identidade gerenciada para a função de [referência](template-functions-resource.md#reference) , o Gerenciador de recursos tentará resolver a referência antes que o recurso e a identidade sejam implantados. Em vez disso, passe o nome do recurso ao qual a identidade é aplicada. Essa abordagem garante que o recurso e a identidade gerenciada sejam implantados antes que o Resource Manager resolva a função de referência.

Na função de referência, use `Full` para obter todas as propriedades, incluindo a identidade gerenciada.

O padrão é:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Não use o padrão:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Seu modelo falhará.

Por exemplo, para obter a ID da entidade de segurança para uma identidade gerenciada que é aplicada a uma máquina virtual, use:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Ou, para obter a ID de locatário para uma identidade gerenciada que é aplicada a um conjunto de dimensionamento de máquinas virtuais, use:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Solução 5-verificar funções

Ao implantar um modelo, procure expressões que usam as funções [Reference](template-functions-resource.md#reference) ou [listKeys](template-functions-resource.md#listkeys) . Os valores que você fornecerá serão diferentes se o recurso estiver no mesmo modelo, grupo de recursos e assinatura. Verifique se você está fornecendo os valores de parâmetro necessários para seu cenário. Se o recurso estiver em um grupo de recursos diferente, forneça a ID do recurso completa. Por exemplo, para fazer referência a uma conta de armazenamento em outro grupo de recursos, use:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Solução 6-após a exclusão do recurso

Quando você exclui um recurso, pode haver um curto período de tempo em que o recurso ainda aparece no portal, mas que não está realmente disponível. Se você selecionar o recurso, receberá um erro informando que o recurso não foi encontrado. Atualize o portal para obter a exibição mais recente.

Se o problema continuar após uma breve espera, [contate o suporte](https://azure.microsoft.com/support/options/).

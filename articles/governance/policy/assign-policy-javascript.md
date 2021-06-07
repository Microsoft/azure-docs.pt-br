---
title: 'Início Rápido: nova atribuição de política com JavaScript'
description: Neste início rápido, use JavaScript para criar uma atribuição do Azure Policy a fim de identificar recursos sem conformidade.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: d42156e68747b66778ea1cf2001d2b4bfc901bb0
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090147"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Início Rápido: criar uma atribuição de política para identificar recurso sem conformidade usando JavaScript

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Neste guia de início rápido, você cria uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados. Ao concluir, você identificará máquinas virtuais que _não estão em conformidade_.

A biblioteca de JavaScript é usada para gerenciar recursos do Azure da linha de comando ou em scripts. Este guia explica como usar a biblioteca de JavaScript para criar uma atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- **Node.js**: o [Node.js](https://nodejs.org/) versão 12 ou superior é necessário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Adicionar as bibliotecas de política

Para habilitar o JavaScript a trabalhar com o Azure Policy, as bibliotecas devem ser adicionadas. Essas bibliotecas funcionarão sempre que o JavaScript puder ser usado, inclusive no [Bash no Windows 10](/windows/wsl/install-win10).

1. Configure um novo projeto Node.js executando o comando a seguir.

   ```bash
   npm init -y
   ```

1. Adicione uma referência à biblioteca yargs.

   ```bash
   npm install yargs
   ```

1. Adicione uma referência às bibliotecas do Azure Policy.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Adicione uma referência à biblioteca de autenticação do Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Em _package.json_, verifique se a versão de `@azure/arm-policy` é **3.1.0** ou superior e se a versão de `@azure/arm-policyinsights` é **3.2.0** ou superior e se a versão de `@azure/ms-rest-nodeauth` é **3.0.5** ou superior.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política e atribui a definição **Auditar VMs que não usam discos gerenciados** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Esta definição de política identifica recursos que não estão em conformidade com as condições configuradas na definição de política.

1. Crie um arquivo chamado _policyAssignment.js_ e insira o código a seguir.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Insira o seguinte comando no terminal:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

Os comandos anteriores usam as seguintes informações:

- **subID** – A ID da assinatura para o contexto de autenticação. Não se esqueça de substituir `{subscriptionId}` pela assinatura.
- **name** – o nome exclusivo para o objeto de atribuição de política. O exemplo acima usa _audit-vm-manageddisks_.
- **displayName** – o nome de exibição da atribuição de política. Nesse caso, você está usando _Auditar VMs sem atribuição de discos gerenciados_.
- **policyDefID** – o caminho da definição da política, com base no que você está usando para criar a atribuição. Nesse caso, é a ID da definição de política _Auditar VMs que não usam discos gerenciados_.
- **description** – uma explicação mais detalhada do que a política faz ou de por que ela está atribuída a esse escopo.
- **scope** – um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Ele pode variar de um grupo de gerenciamento a um recurso individual. Substitua `{scope}` por um dos seguintes padrões:
  - Grupo de gerenciamento: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Assinatura: `/subscriptions/{subscriptionId}`
  - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Agora você está pronto para identificar recursos fora de conformidade para entender o estado de conformidade do ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Agora que sua atribuição de política foi criada, você pode identificar os recursos que não estão em conformidade.

1. Crie um arquivo chamado _policyState.js_ e insira o código a seguir.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Insira o seguinte comando no terminal:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

Substitua `{subscriptionId}` pela assinatura cujos resultados de conformidade você deseja ver para a atribuição de política denominada 'audit-vm-manageddisks', que criamos nas etapas anteriores. Para ver uma lista de outros escopos e maneiras de resumir os dados, confira os métodos [PolicyStates*](/javascript/api/@azure/arm-policyinsights/).

Seus resultados devem se parecer com o exemplo a seguir:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Os resultados correspondem ao que você vê na **Conformidade do recurso** de uma atribuição de política na exibição do portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

- Exclua a atribuição de política _Auditar VMs sem a atribuição de discos gerenciados_ por meio do portal. A definição de política é um recurso interno, portanto, não há nenhuma definição a ser removida.

- Caso deseje remover as bibliotecas instaladas do aplicativo, execute o comando a seguir.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre definições de atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)
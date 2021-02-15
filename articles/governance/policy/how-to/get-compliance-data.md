---
title: Obter dados de conformidade da política
description: Efeitos e avaliações do Azure Policy determinam a conformidade. Saiba como obter os detalhes de conformidade dos seus recursos do Azure.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 112badce00ec56df0f80c7b51bb4789a414cdcbd
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920229"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obter dados de conformidade de recursos do Azure

Os maiores benefícios do Azure Policy são o insight e os controles que ele fornece sobre os recursos em uma assinatura ou [grupo de gerenciamento](../../management-groups/overview.md) de assinaturas. Este controle pode ser desempenhado de várias maneiras diferentes, como impedindo que os recursos sejam criados no local errado, imposição do uso comum e consistente de marcas ou auditoria de recursos existentes para configurações apropriadas. Em todos os casos, os dados são gerados pelo Azure Policy para que você entenda o estado de conformidade de seu ambiente.

Há várias maneiras de acessar as informações de conformidade geradas por sua política e iniciativas de atribuições:

- Usando o [Portal do Azure](#portal)
- Usando script de [linha de comando](#command-line)

Antes de examinar os métodos de relatório de conformidade, vamos ver quando as informações de conformidade são atualizadas, além da frequência e dos eventos que disparam um ciclo de avaliação.

> [!WARNING]
> Se o estado de conformidade estiver sendo relatado como **não registrado**, verifique se o provedor de recursos **Microsoft. PolicyInsights** está registrado e se o usuário tem as permissões apropriadas de controle de acesso baseado em função (RBAC do Azure), conforme descrito em [permissões de RBAC do Azure no Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Gatilhos de avaliação

Os resultados de um ciclo de avaliação concluído estão disponíveis no Provedor de Recursos `Microsoft.PolicyInsights` por meio das operações `PolicyStates` e `PolicyEvents`. Para saber mais sobre as operações da API REST de Insights do Azure Policy, confira [Insights do Azure Policy](/rest/api/policy-insights/).

As avaliações de políticas atribuídas e iniciativas ocorrem como resultado de diversos eventos:

- Uma política ou iniciativa foi recentemente atribuída a um escopo. Demora cerca de 30 minutos para que a atribuição seja aplicada ao escopo definido. Depois de aplicado, o ciclo de avaliação começa para os recursos dentro desse escopo em relação à política ou iniciativa atribuída recentemente e, dependendo dos efeitos usados pela política ou iniciativa, os recursos são marcados como em conformidade, não compatível ou isento. Uma política ou iniciativa grande avaliada em um grande escopo de recursos pode levar tempo. Como tal, não há nenhuma expectativa predefinida de quando o ciclo de avaliação é concluído. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no Portal e nos SDKs.

- Uma política ou iniciativa já atribuída a um escopo foi atualizada. O ciclo de avaliação e o tempo desse cenário são iguais aos de uma nova atribuição a um escopo.

- Um recurso é implantado ou atualizado dentro de um escopo com uma atribuição via Azure Resource Manager, API REST ou um SDK com suporte. Nesse cenário, o evento de efeito (anexar, auditar, negar, implantar) e as informações de status compatíveis com o recurso individual ficam disponíveis no portal e nos SDKs por volta de 15 minutos depois. Este evento não causa uma avaliação de outros recursos.

- Uma [isenção de política](../concepts/exemption-structure.md) é criada, atualizada ou excluída. Nesse cenário, a atribuição correspondente é avaliada para o escopo de isenção definido.

- Ciclo de avaliação de conformidade padrão. Uma vez a cada 24 horas, as atribuições são automaticamente reavaliadas. Uma política ou iniciativa de muitos recursos pode demorar, portanto, não há uma expectativa predefinida de quando o ciclo de avaliação é concluído. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no Portal e nos SDKs.

- O provedor de recursos [Configuração de Convidado](../concepts/guest-configuration.md) é atualizado com detalhes de conformidade por um recurso gerenciado.

- Exame sob demanda

### <a name="on-demand-evaluation-scan"></a>Exame de avaliação sob demanda

Uma verificação de avaliação para uma assinatura ou um grupo de recursos pode ser iniciada com CLI do Azure, Azure PowerShell, uma chamada para a API REST ou usando a [ação do GitHub de verificação de conformidade Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan).
Essa verificação é um processo assíncrono.

#### <a name="on-demand-evaluation-scan---github-action"></a>Verificação de avaliação sob demanda – ação do GitHub

Use a [ação de verificação de conformidade Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan) para disparar uma verificação de avaliação sob demanda de seu [fluxo de trabalho do GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) em um ou vários recursos, grupos de recursos ou assinaturas e portão o fluxo de trabalho com base no estado de conformidade dos recursos. Você também pode configurar o fluxo de trabalho para ser executado em um horário agendado para que você obtenha o status de conformidade mais recente em um momento conveniente. Opcionalmente, essa ação do GitHub pode gerar um relatório sobre o estado de conformidade dos recursos verificados para análise posterior ou arquivamento.

O exemplo a seguir executa uma verificação de conformidade para uma assinatura. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Para obter mais informações e exemplos de fluxo de trabalho, consulte a [ação do GitHub para Azure Policy repositório de verificação de conformidade](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Varredura de avaliação sob demanda-CLI do Azure

A verificação de conformidade é iniciada com o comando [AZ Policy estado Trigger-Scan](/cli/azure/policy/state#az_policy_state_trigger_scan) .

Por padrão, `az policy state trigger-scan` inicia uma avaliação para todos os recursos na assinatura atual. Para iniciar uma avaliação em um grupo de recursos específico, use o parâmetro **grupo de recursos** . O exemplo a seguir inicia uma verificação de conformidade na assinatura atual do grupo de recursos _MyRG_:

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Você pode optar por não aguardar a conclusão do processo assíncrono antes de continuar com o parâmetro **sem espera** .

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Varredura de avaliação sob demanda - Azure PowerShell

A verificação de conformidade é iniciada com o cmdlet [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan).

Por padrão, `Start-AzPolicyComplianceScan` inicia uma avaliação para todos os recursos na assinatura atual. Para iniciar uma avaliação em um grupo de recursos específico, use o parâmetro **ResourceGroupName**. O exemplo a seguir inicia uma verificação de conformidade na assinatura atual do grupo de recursos _MyRG_:

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Você pode fazer com que o PowerShell aguarde até que a chamada assíncrona seja concluída antes de fornecer a saída dos resultados ou que ela seja executada em segundo plano como um [trabalho](/powershell/module/microsoft.powershell.core/about/about_jobs). Para usar um trabalho do PowerShell para executar a verificação de conformidade em segundo plano, use o parâmetro **AsJob** e defina o valor para um objeto, como `$job` neste exemplo:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Você pode verificar o status do trabalho verificando o objeto `$job`. O trabalho é do tipo `Microsoft.Azure.Commands.Common.AzureLongRunningJob`. Use `Get-Member` no objeto `$job` para ver as propriedades e os métodos disponíveis.

Enquanto a verificação de conformidade está em execução, a verificação dos resultados de saída do objeto de `$job`, como:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Quando a verificação de conformidade é concluída, a propriedade **Estado** é alterada para _Concluída_.

#### <a name="on-demand-evaluation-scan---rest"></a>Exame de avaliação sob demanda - REST

Dessa forma, o ponto de extremidade REST para iniciar o exame não espera até que o exame seja concluído para responder. Em vez disso, ele fornece um URI para consultar o status da avaliação solicitada.

Em cada URI da API REST, há variáveis usadas que precisam ser substituídas com seus próprios valores:

- `{YourRG}`: substitua pelo nome do grupo de recursos
- `{subscriptionId}`: substitua por sua ID da assinatura

O exame dá suporte à avaliação de recursos em uma assinatura ou em um grupo de recursos. Inicie uma verificação por escopo com um comando **POST** da API REST usando as seguintes estruturas de URI:

- Subscription

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resource group

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

A chamada retorna um status **202 Aceito**. Uma propriedade **Location** com o seguinte formato é incluída no cabeçalho da resposta:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` é estaticamente gerado para o escopo solicitado. Se um escopo já estiver executando um exame sob demanda, um novo exame não será iniciado. Em vez disso, a nova solicitação receberá o mesmo URI de `{ResourceContainerGUID}`**localização** para o status. Um comando **GET** da API REST para o URI de **localização** retorna um **202 Aceito** enquanto a avaliação está em andamento. Quando o exame de avaliação for concluído, ela retornará um status **200 OK**. O corpo de um exame completo é uma resposta JSON com o status:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Varredura de avaliação sob demanda-Visual Studio Code

A extensão de Azure Policy para o Visual Studio Code é capaz de executar uma verificação de avaliação para um recurso específico. Essa verificação é um processo síncrono, ao contrário dos métodos Azure PowerShell e REST.
Para obter detalhes e etapas, consulte [avaliação sob demanda com a extensão vs Code](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Como funciona a conformidade

Em uma atribuição, um recurso não é **compatível** se não seguir as regras de política ou de iniciativa e não estiver _isento_. A tabela a seguir mostra como os diferentes efeitos da política funcionam com a avaliação da condição para o estado de conformidade resultante:

| Estado do Recurso | Efeito | Avaliação da política | Estado de conformidade |
| --- | --- | --- | --- |
| Novo ou atualizado | Audit, Modify, AuditIfNotExist | True | Sem conformidade |
| Novo ou atualizado | Audit, Modify, AuditIfNotExist | Falso | Em conformidade |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | True | Sem conformidade |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Falso | Em conformidade |

> [!NOTE]
> Os efeitos DeployIfNotExist e AuditIfNotExist exigem que a instrução IF seja TRUE e a condição de existência seja FALSE para não estar em conformidade. Quando TRUE, a condição IF dispara a avaliação da condição de existência para os recursos relacionados.

Por exemplo, suponha que você tenha um grupo de recursos – ContsoRG, com algumas contas de armazenamento (realçadas em vermelho) que são expostas para redes públicas.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos contoso R G." border="false":::
   Diagrama mostrando imagens para cinco contas de armazenamento no grupo de recursos contoso R G.  As contas de armazenamento um e três são azuis, enquanto as contas de armazenamento dois, quatro e cinco são vermelhas.
:::image-end:::

Neste exemplo, você precisa estar atento aos riscos de segurança. Agora que você criou uma atribuição de política, ela é avaliada para todas as contas de armazenamento incluídas e não isentas no grupo de recursos ContosoRG. Ele audita as três contas de armazenamento não compatíveis, consequentemente alterando seus estados para **Não compatíveis.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagrama de conformidade da conta de armazenamento no grupo de recursos contoso R G." border="false":::
   Diagrama mostrando imagens para cinco contas de armazenamento no grupo de recursos contoso R G. As contas de armazenamento um e três agora têm marcas de seleção verdes abaixo delas, enquanto as contas de armazenamento dois, quatro e cinco agora têm sinais de aviso vermelhos abaixo delas.
:::image-end:::

Além de serem **compatíveis** e **não compatíveis**, as políticas e os recursos têm quatro outros Estados:

- **Isentar**: o recurso está no escopo de uma atribuição, mas tem uma [isenção definida](../concepts/exemption-structure.md).
- **Conflito**: existem duas ou mais definições de política com regras conflitantes. Por exemplo, duas definições acrescentam a mesma marca com valores diferentes.
- **Não foi iniciado**: O ciclo de avaliação não foi iniciado para a política ou o recurso.
- **Não registrado**: o Provedor de Recursos do Azure Policy não foi registrado ou a conta conectada não tem permissão para ler dados de conformidade.

Azure Policy usa os campos **tipo**, **nome** ou **tipo** na definição para determinar se um recurso é uma correspondência. Quando o recurso corresponde, ele é considerado aplicável e tem um status de em **conformidade**, **não compatível** ou **isento**. Se o **tipo**, o **nome** ou o **tipo** for a única propriedade na definição, todos os recursos incluídos e não isentos serão considerados aplicáveis e serão avaliados.

A porcentagem de conformidade é determinada pela divisão de recursos em **conformidade** e **isentos** pelo _total de recursos_. _O total de recursos_ é definido como a soma dos recursos **compatíveis**, **não compatíveis**, **isentos** e **conflitantes** . Os números de conformidade geral são a soma de recursos distintos que são **compatíveis** ou **isentos** divididos pela soma de todos os recursos distintos. Na imagem abaixo, existem 20 recursos distintos que são aplicáveis e apenas um é **Não compatível**.
A conformidade geral do recurso é 95 (19 de 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Captura de tela de detalhes de conformidade de política da página conformidade." border="false":::

> [!NOTE]
> A conformidade regulatória no Azure Policy é um recurso de visualização. As propriedades de conformidade do SDK e das páginas no portal são diferentes para iniciativas habilitadas. Para obter mais informações, consulte [conformidade regulatória](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portal

O Portal do Azure apresenta uma experiência gráfica de visualização e compreensão do estado de conformidade em seu ambiente. Na página **Política**, a opção **Visão Geral** fornece detalhes dos escopos disponíveis sobre a conformidade das políticas e iniciativas. Junto com o estado de conformidade e com a contagem por atribuição, ela contém um gráfico mostrando a conformidade nos últimos sete dias. A página **Conformidade** contém muitas dessas mesmas informações (exceto o gráfico), mas fornece opções adicionais de filtragem e classificação.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Captura de tela da página conformidade, opções de filtragem e detalhes." border="false":::

Como uma política ou iniciativa pode ser atribuída a escopos diferentes, a tabela inclui o escopo de cada atribuição e o tipo de definição que foi atribuído. O número de recursos não compatíveis e políticas não compatíveis para cada atribuição também são fornecidos. A seleção de uma política ou iniciativa na tabela fornece uma análise mais profunda da conformidade para essa atribuição específica.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Captura de tela da página de detalhes de conformidade, incluindo contagens e detalhes de conformidade de recursos." border="false":::

A lista de recursos na guia **Conformidade de recursos** mostra o status de avaliação de recursos existentes para a atribuição atual. O padrão da guia é **Não compatível**, mas pode ser filtrado.
Os eventos (acrescentar, auditar, negar, implantar, modificar) disparados pela solicitação para criar um recurso são mostrados na guia **eventos** .

> [!NOTE]
> Para uma política de mecanismo AKS, o recurso mostrado é o grupo de recursos.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Captura de tela da guia eventos na página de detalhes de conformidade." border="false":::

<a name="component-compliance"></a> Para recursos do [modo provedor de recursos](../concepts/definition-structure.md#resource-provider-modes) , na guia **conformidade de recursos** , selecionar o recurso ou clicar com o botão direito do mouse na linha e selecionar **Exibir detalhes de conformidade** abre os detalhes de conformidade do componente. Esta página também oferece guias para ver as políticas que são atribuídas a esse recurso, eventos, eventos de componente e histórico de alterações.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Captura de tela da guia conformidade do componente e detalhes de conformidade para uma atribuição de modo do provedor de recursos." border="false":::

De volta à página de conformidade do recurso, clique com o botão direito na linha do evento para obter mais detalhes e selecione **Mostrar logs de atividade**. A página de registro de atividade é aberta e pré-filtrada na pesquisa, mostrando detalhes da atribuição e dos eventos. O log de atividades fornece um contexto adicional e informações sobre esses eventos.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Captura de tela do log de atividades para Azure Policy atividades e avaliações." border="false":::

### <a name="understand-non-compliance"></a>Entender a não conformidade

Quando determina-se que um recurso **não está em conformidade**, há muitos motivos possíveis. Para determinar o motivo pelo qual um recurso **não está em conformidade** ou para localizar a alteração responsável, consulte [Determinar a não conformidade](./determine-non-compliance.md).

## <a name="command-line"></a>Linha de comando

As mesmas informações disponíveis no portal podem ser recuperadas com a API REST (incluindo with [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell e CLI do Azure. Para obter detalhes completos sobre a API REST, consulte a referência [Insights do Azure Policy](/rest/api/policy-insights/). As páginas de referência da API REST têm um botão verde "Experimente" em cada operação, permitindo que você experimente diretamente no navegador.

Use o ARMClient ou uma ferramenta semelhante para lidar com a autenticação do Azure para os exemplos da API REST.

### <a name="summarize-results"></a>Resumir resultados

Com a API REST, o resumo pode ser executado por contêiner, por definição ou por atribuição. Veja um exemplo de resumo no nível de assinatura usando [Resumir para Assinatura](/rest/api/policy-insights/policystates/summarizeforsubscription) do Insight do Azure Policy:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

A saída resume a assinatura. No exemplo abaixo, a conformidade resumida está sob **value.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. Essa solicitação fornece mais detalhes, incluindo cada atribuição que compõem os números sem conformidade e as informações de definição de cada atribuição. Cada objeto de política na hierarquia fornece um **queryResultsUri** que pode ser usado para obter detalhes adicionais nesse nível.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Pesquisar recursos

No exemplo acima, **value.policyAssignments.policyDefinitions.results.queryResultsUri** fornece um URI de exemplo para obter todos os recursos sem conformidade de uma definição de política específica. Observando o valor de **$Filter** , compliancestate é igual (EQ) para ' não compatível ', PolicyAssignmentId é especificado para a definição de política e, em seguida, o próprio PolicyDefinitionId. O motivo para incluir o PolicyAssignmentId no filtro é porque PolicyDefinitionId pode existir em várias atribuições ou iniciativas de política com diferentes escopos. Ao especificar o PolicyAssignmentId e o PolicyDefinitionId, podemos ser explícitos com relação aos resultados que procuramos. Anteriormente, para PolicyStates usamos o **mais recente**, que define automaticamente uma janela de tempo **de** e **até** das últimas 24 horas.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

A resposta de exemplo abaixo foi reduzida para um único recurso não compatível para ter brevidade. A resposta detalhada tem várias partes de dados sobre o recurso, a política ou a iniciativa e a atribuição. Observe que você também pode ver quais parâmetros de atribuição foram passados para a definição de política.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Exibir eventos

Quando um recurso é criado ou atualizado, um resultado da avaliação da política é gerado. Os resultados são chamados _Eventos de Política_. Use o URI a seguir para exibir eventos recentes de política associados à assinatura.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

Seus resultados devem se parecer com o exemplo a seguir:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Para obter mais informações sobre como consultar os eventos de política, consulte o artigo de referência de [Eventos do Azure Policy](/rest/api/policy-insights/policyevents).

### <a name="azure-cli"></a>CLI do Azure

O grupo de comandos [CLI do Azure](/cli/azure/what-is-azure-cli) para Azure Policy abrange a maioria das operações que estão disponíveis em REST ou Azure PowerShell. Para obter a lista completa de comandos disponíveis, consulte [visão geral de CLI do Azure Azure Policy](/cli/azure/policy).

Exemplo: obter o estado de resumo da política superior atribuída com o maior número de recursos sem conformidade.

```azurecli-interactive
az policy state summarize --top 1
```

A parte superior da resposta é semelhante a este exemplo:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Exemplo: obter o registro de estado para o recurso avaliada mais recentemente (o padrão é pelo carimbo de data/hora em ordem decrescente).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Exemplo: obter os detalhes para todos os recursos de rede virtual sem conformidade.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Exemplo: obter eventos relacionados aos recursos de rede virtual sem conformidade que ocorreram após uma data específica.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

O módulo do Azure PowerShell para o Azure Policy está disponível na Galeria do PowerShell como [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Usando o PowerShellGet, você pode instalar o módulo usando `Install-Module -Name Az.PolicyInsights` (verifique se você tem a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) instalada):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

O módulo tem os seguintes cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exemplo: obter o estado de resumo da política superior atribuída com o maior número de recursos sem conformidade.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: obter o registro de estado para o recurso avaliada mais recentemente (o padrão é pelo carimbo de data/hora em ordem decrescente).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obter os detalhes para todos os recursos de rede virtual sem conformidade.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obtendo eventos relacionados a recursos de rede virtual sem conformidade que ocorreram após uma data específica, convertendo para um objeto CSV e exportando para um arquivo.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

A saída do `$policyEvents` objeto é semelhante ao seguinte:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

O campo **PrincipalOid** pode ser usado para obter um usuário específico com o cmdlet do Azure PowerShell `Get-AzADUser`. Substitua **{principalOid}** pela resposta obtida do exemplo anterior.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Logs do Azure Monitor

Se você tiver um [espaço de trabalho log Analytics](../../../azure-monitor/log-query/log-query-overview.md) com `AzureActivity` o da [solução análise do log de atividades](../../../azure-monitor/platform/activity-log.md) vinculada à sua assinatura, também poderá exibir os resultados de não conformidade da avaliação de recursos novos e atualizados usando consultas simples de Kusto e a `AzureActivity` tabela. Com os detalhes nos logs do Azure Monitor, os alertas poderão ser configurados para inspecionar a não conformidade.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Captura de tela de logs de Azure Monitor mostrando ações de Azure Policy na tabela AzureActivity." border="false":::

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de maneira programática](programmatically-create.md).
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).

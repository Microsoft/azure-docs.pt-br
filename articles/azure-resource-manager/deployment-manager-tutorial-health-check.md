---
title: Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager | Microsoft Docs
description: Use modelos do Resource Manager com o Gerenciador de Implantação do Azure para implantar recursos do Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3946690b24ec4123df1bb19deb05143189fd5aa5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596049"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Tutorial: Usar a verificação de integridade no Gerenciador de Implantação do Azure (versão prévia pública)

Saiba como integrar a verificação de integridade ao [Gerenciador de Implantação do Azure](./deployment-manager-overview.md). Este tutorial baseia-se no tutorial [Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md). Você precisa concluir esse tutorial antes de prosseguir com este.

No modelo de distribuição usado em [Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md), você usou uma etapa de espera. Neste tutorial, você substituirá a etapa de espera por uma etapa de verificação de integridade.

> [!IMPORTANT]
> Caso sua assinatura esteja marcada como Canário para testar novos recursos do Azure, você só poderá usar o Gerenciador de Implantação do Azure para implantação nas regiões do Canário. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar um simulador de serviço de verificação de integridade
> * Revisar o modelo de distribuição
> * Implantar a topologia
> * Implantar a distribuição com o status não íntegro
> * Verificar a implantação de distribuição
> * Implantar a distribuição com o status íntegro
> * Verificar a implantação de distribuição
> * Limpar recursos

Recursos adicionais:

- A [referência à API REST do Gerenciador de Implantação do Azure](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Um exemplo do Gerenciador de Implantação do Azure](https://github.com/Azure-Samples/adm-quickstart).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Conclua [Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md).
* Baixe [os modelos e os artefatos](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) usados por este tutorial.

## <a name="create-a-health-check-service-simulator"></a>Criar um simulador de serviço de verificação de integridade

Em produção, normalmente, são usados um ou mais provedores de monitoramento. Para facilitar ao máximo a integração de integridade, a Microsoft tem trabalhado com algumas das principais empresas de monitoramento de integridade de serviço para fornecer uma solução copiar/colar simples visando integrar as verificações de integridade às suas implantações. Para obter uma lista dessas empresas, confira [Provedores de monitoramento de integridade](./deployment-manager-health-check.md#health-monitoring-providers). Para fins deste tutorial, você criará uma [Função do Azure](/azure/azure-functions/) para simular um serviço de monitoramento de integridade. Essa função usa um código de status e retorna o mesmo código. O modelo do Gerenciador de Implantação do Azure usa o código de status para determinar como proceder com a implantação.

Os dois seguintes arquivos são usados para implantar a Função do Azure. Você não precisa baixar esses arquivos para acompanhar o tutorial.

* Um modelo do Resource Manager localizado em [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Você implantará esse modelo para criar uma Função do Azure.
* Um arquivo zip do código-fonte da Função do Azure, [https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). Esse zip chamado é chamado pelo modelo do Resource Manager.

Para implantar a função do Azure, selecione **Experimentar** para abrir o Azure Cloud Shell e, em seguida, cole o script a seguir na janela do shell.  Para colar o código, clique com o botão direito do mouse na janela do shell e, em seguida, selecione **Colar**.

> [!IMPORTANT]
> **projectName** no script do PowerShell é usado para gerar nomes para os serviços do Azure que são implantados neste tutorial. Diferentes serviços do Azure têm diferentes requisitos nos nomes. Para garantir que a implantação seja bem-sucedida, escolha um nome que tenha menos de 12 caracteres com apenas letras minúsculas e números.
> Salve uma cópia do nome do projeto. Você usará o mesmo projectName em todo o tutorial.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Para verificar e testar a função do Azure:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos.  O nome padrão é o nome do projeto com **rg** acrescentado.
1. Selecione o serviço de aplicativo no grupo de recursos.  O nome padrão do serviço de aplicativo é o nome do projeto com **webapp** acrescentado.
1. Expanda **Funções** e, em seguida, selecione **HttpTrigger1**.

    ![Função do Azure na verificação de integridade do Gerenciador de Implantação do Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Selecione **&lt;/> Obter URL da função**.
1. Selecione **Copiar** para copiar a URL para a área de transferência.  A URL é semelhante a:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Substitua `{healthStatus}` na URL por um código de status. Neste tutorial, use **não íntegro** para testar o cenário não íntegro e **íntegro** ou **aviso** para testar o cenário íntegro. Crie duas URLs, uma com o status não íntegro e a outra com o status íntegro. Por exemplo:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Você precisará de ambas as URLs para concluir este tutorial.

1. Para testar o simulador de monitoramento de integridade, abra as URLs que você criou na última etapa.  Os resultados para o status não íntegro deverão ser semelhantes a:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Revisar o modelo de distribuição

O objetivo desta seção é mostrar como incluir uma etapa de verificação de integridade no modelo de distribuição. Você não precisa criar seu próprio arquivo CreateADMRollout.json para concluir este tutorial. O modelo de distribuição revisado é compartilhado em uma conta de armazenamento que é usada nas seções seguintes.

1. Abra **CreateADMRollout.json**. Esse arquivo JSON é uma parte do download.  Consulte [Pré-requisitos](#prerequisites).
1. Adicione mais dois parâmetros:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Substitua a definição de recurso da etapa de espera por uma definição de recurso da etapa de verificação de integridade:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Com base na definição, a distribuição prossegue se o status da integridade é *íntegro* ou *aviso*.

1. Atualize o **dependsON** da definição de distribuição para incluir a etapa de verificação de integridade recém-definida:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Atualize **stepGroups** para incluir a etapa de verificação de integridade. A **healthCheckStep** é chamada em **postDeploymentSteps** do **stepGroup2**. **stepGroup3** e **stepGroup4** são implantadas somente se o status íntegro é *íntegro* ou *aviso*.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Se você comparar a seção **stepGroup3** antes e depois da revisão, essa seção agora dependerá de **stepGroup2**.  Isso é necessário quando **stepGroup3** e os grupos de etapas seguintes dependem dos resultados do monitoramento de integridade.

    A seguinte captura de tela ilustra as áreas modificadas e como a etapa de verificação de integridade é usada:

    ![Modelo de verificação de integridade do Gerenciador de Implantação do Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Implantar a topologia

Para simplificar o tutorial, o modelo de topologia e os artefatos são compartilhados nas localizações a seguir, de modo que você não precise preparar sua própria cópia. Caso deseje usar sua própria cópia, siga as instruções do [Tutorial: Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md).

* Modelo de topologia: \ https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Repositório de artefatos: \ https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Para implantar a topologia, selecione **Experimentar** para abrir o Cloud Shell e, em seguida, cole o script do PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Verifique se a topologia de serviço e os recursos de sublinhado foram criados com êxito usando o portal do Azure:

![Gerenciador de Implantação do Azure, tutorial para recursos de topologia de serviço implantados](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

**Mostrar tipos ocultos** deve ser selecionado para ver os recursos.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Implantar a distribuição com o status não íntegro

Para simplificar o tutorial, o modelo de distribuição revisado é compartilhado nas localizações a seguir, de modo que você não precise preparar sua própria cópia. Caso deseje usar sua própria cópia, siga as instruções do [Tutorial: Usar o Gerenciador de Implantação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md).

* Modelo de topologia: \ https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Repositório de artefatos: \ https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Use a URL de status não íntegro que você criou em [Criar um simulador de serviço de verificação de integridade](#create-a-health-check-service-simulator). Para **managedIdentityID**, confira [Criar a identidade gerenciada atribuída ao usuário](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` é uma chamada assíncrona. A mensagem de êxito apenas significa que a implantação foi iniciada com êxito. Para verificar a implantação, use `Get-AZDeploymentManagerRollout`.  Confira o próximo procedimento.

Para verificar o progresso da distribuição usando o seguinte script do PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

A seguinte saída de exemplo mostra que a implantação falhou devido ao status não íntegro:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Após a conclusão da distribuição, você deverá ver um grupo de recursos adicional criado para o Oeste dos EUA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Implantar a distribuição com o status íntegro

Repita esta seção para reimplantar a distribuição com a URL do status íntegro.  Após a conclusão da distribuição, você deverá ver mais um grupo de recursos criado para o Leste dos EUA.

## <a name="verify-the-deployment"></a>Verificar a implantação

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Navegue até os aplicativos Web criados recentemente nos novos grupos de recursos criados pela implantação de distribuição.
3. Abra o aplicativo Web em um navegador da Web. Verifique o local e a versão no arquivo index.html.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. Use o campo **Filtrar por nome** para restringir os grupos de recursos criados neste tutorial. Deve haver 3 a 4:

    * **&lt;namePrefix>rg**: contém os recursos do Gerenciador de Implantação.
    * **&lt;namePrefix>ServiceWUSrg**: contém os recursos definidos pelo ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: contém os recursos definidos pelo ServiceEUS.
    * O grupo de recursos para a identidade gerenciada definida pelo usuário.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.
5. Repita as duas últimas etapas para excluir outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o recurso de verificação de integridade do Gerenciador de Implantação do Azure. Para obter mais informações, veja [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).

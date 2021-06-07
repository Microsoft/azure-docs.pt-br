---
title: Usar identidade gerenciada para autenticar seu trabalho de Azure Stream Analytics para Power BI saída
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho de Azure Stream Analytics para Power BI saída.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 7c1ddbbbd8198cf769e89cfa824de370184a992c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589677"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Use a identidade gerenciada para autenticar seu trabalho de Azure Stream Analytics para Power BI

A [autenticação de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para saída para Power bi fornece aos trabalhos de Stream Analytics acesso direto a um espaço de trabalho dentro de sua conta de Power bi. Esse recurso permite que as implantações de Stream Analytics trabalhos sejam totalmente automatizadas, já que não é mais necessário que um usuário faça logon interativamente no Power BI por meio do portal do Azure. Além disso, trabalhos de longa execução que gravam em Power BI agora têm melhor suporte, já que você não precisará reautorizar o trabalho periodicamente.

Este artigo mostra como habilitar a identidade gerenciada para o Power BI saídas de um trabalho de Stream Analytics por meio do portal do Azure e por meio de uma implantação de Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para usar esse recurso:

- Uma conta de Power BI com uma [licença pro](/power-bi/service-admin-purchasing-power-bi-pro).

- Um espaço de trabalho atualizado dentro de sua conta de Power BI. Consulte o [comunicado do Power bi](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) sobre esse recurso para obter mais detalhes.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Criar um trabalho de Stream Analytics usando o portal do Azure

1. Crie um novo trabalho de Stream Analytics ou abra um trabalho existente no portal do Azure. Na barra de menus localizada no lado esquerdo da tela, selecione **identidade gerenciada** localizada em **Configurar**. Certifique-se de que "usar identidade gerenciada atribuída pelo sistema" esteja selecionado e, em seguida, selecione o botão **salvar** na parte inferior da tela.

   ![Configurar Stream Analytics identidade gerenciada](./media/common/stream-analytics-enable-managed-identity.png)

2. Antes de configurar a saída, conceda ao trabalho de Stream Analytics acesso ao seu espaço de trabalho do Power BI seguindo as instruções na seção [fornecer ao trabalho de Stream Analytics acesso ao seu espaço de trabalho do Power bi](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) deste artigo.

3. Navegue até a seção de **saídas** do trabalho do seu fluxo analítico, selecione **+ Adicionar** e, em seguida, escolha **Power bi**. Em seguida, selecione o botão **autorizar** e faça logon com sua conta de Power bi.

   ![Autorizar com Power BI conta](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Depois de autorizada, uma lista suspensa será preenchida com todos os espaços de trabalho aos quais você tem acesso. Selecione o espaço de trabalho que você autorizou na etapa anterior. Em seguida, selecione **identidade gerenciada** como o "modo de autenticação". Por fim, selecione o botão **salvar** .

   ![Configurar a saída de Power BI com identidade gerenciada](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Implantação do Azure Resource Manager

Azure Resource Manager permite automatizar totalmente a implantação de seu trabalho de Stream Analytics. Você pode implantar modelos do Resource Manager usando Azure PowerShell ou o [CLI do Azure](/cli/azure/). Os exemplos a seguir usam o CLI do Azure.


1. Você pode criar um recurso **Microsoft. StreamAnalytics/streamingjobs** com uma identidade gerenciada, incluindo a seguinte propriedade na seção de recursos do seu modelo do Resource Manager:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Essa propriedade informa Azure Resource Manager para criar e gerenciar a identidade de seu trabalho de Stream Analytics. Veja abaixo um exemplo de modelo do Resource Manager que implanta um trabalho Stream Analytics com identidade gerenciada habilitada e um coletor de saída Power BI que usa identidade gerenciada:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Implante o trabalho acima para o grupo de recursos de **exemplo** usando o comando a seguir CLI do Azure:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Depois que o trabalho for criado, use Azure Resource Manager para recuperar a definição completa do trabalho.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    O comando acima retornará uma resposta como a seguinte:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Se você planeja usar a API REST de Power BI para adicionar o trabalho de Stream Analytics ao seu espaço de trabalho do Power BI, anote o "PrincipalId" retornado.

3. Agora que o trabalho foi criado, vá para a seção [fornecer o acesso Stream Analytics trabalho ao espaço de trabalho do Power bi](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Dê ao Stream Analytics o acesso ao seu espaço de trabalho Power BI

Agora que o trabalho de Stream Analytics foi criado, ele pode receber acesso a um espaço de trabalho Power BI. Depois de ter dado o acesso ao trabalho, aguarde alguns minutos para que a identidade se propague.

### <a name="use-the-power-bi-ui"></a>Usar a interface do usuário do Power BI

   > [!Note]
   > Para adicionar o trabalho de Stream Analytics ao seu espaço de trabalho do Power BI usando a interface do usuário, você também precisa habilitar o acesso à entidade de serviço nas **configurações do desenvolvedor** no portal de administração do Power bi. Consulte [introdução a uma entidade de serviço](/power-bi/developer/embed-service-principal) para obter mais detalhes.

1. Navegue até as configurações de acesso do espaço de trabalho. Consulte este artigo para obter mais detalhes: [conceder acesso ao seu espaço de trabalho](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Digite o nome do seu trabalho de Stream Analytics na caixa de texto e selecione **colaborador** como o nível de acesso.

3. Selecione **Adicionar** e feche o painel.

   ![Adicionar Stream Analytics trabalho ao Power BI espaço de trabalho](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Usar os cmdlets do Power BI PowerShell

1. Instale os `MicrosoftPowerBIMgmt` cmdlets do Power bi PowerShell.

   > [!Important]
   > Verifique se você está usando a versão 1.0.821 ou posterior dos cmdlets.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Faça logon no Power BI.

```powershell
Login-PowerBI
```

3. Adicione seu trabalho de Stream Analytics como um colaborador ao espaço de trabalho.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Usar a API REST do Power BI

O trabalho de Stream Analytics também pode ser adicionado como um colaborador ao espaço de trabalho usando a API REST "Adicionar usuário de grupo" diretamente. A documentação completa para essa API pode ser encontrada aqui: [grupos-Adicionar usuário do grupo](/rest/api/power-bi/groups/addgroupuser).

**Solicitação de Exemplo**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Corpo da solicitação
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Remover identidade gerenciada

A identidade gerenciada criada para um trabalho de Stream Analytics é excluída somente quando o trabalho é excluído. Não é possível excluir a identidade gerenciada sem excluir o trabalho. Se você não quiser mais usar a identidade gerenciada, poderá alterar o método de autenticação para a saída. A identidade gerenciada continuará existindo até que o trabalho seja excluído e será usado se você decidir usar a autenticação de identidade gerenciada novamente.

## <a name="limitations"></a>Limitações
Abaixo estão as limitações desse recurso:

- Não há suporte para espaços de trabalho do Power BI clássico.

- Contas do Azure sem Azure Active Directory.

- Não há suporte para acesso multilocatário. A entidade de serviço criada para um determinado trabalho de Stream Analytics deve residir no mesmo locatário de Azure Active Directory no qual o trabalho foi criado e não pode ser usada com um recurso que reside em um locatário de Azure Active Directory diferente.

- Não há suporte para a [identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/overview.md) . Isso significa que você não pode inserir sua própria entidade de serviço a ser usada por seus Stream Analytics trabalho. A entidade de serviço deve ser gerada pelo Azure Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Integração do painel de controle do Power BI com o Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Entender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)

---
title: Usar identidade gerenciada para autenticar seu trabalho de Azure Stream Analytics para a saída do armazenamento de BLOBs do Azure (versão prévia)
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho de Azure Stream Analytics para a saída do armazenamento de BLOBs do Azure.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 2bde1e8556fb1255e27595630e061f6b80870ce1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278688"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output-preview"></a>Usar identidade gerenciada para autenticar seu trabalho de Azure Stream Analytics para a saída do armazenamento de BLOBs do Azure (versão prévia)

A [autenticação de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para saída para o armazenamento de BLOBs do Azure está disponível para Azure Stream Analytics como uma visualização. Isso dá aos trabalhos de Stream Analytics acesso direto a uma conta de armazenamento em vez de usar uma cadeia de conexão. Além da segurança aprimorada, esse recurso também permite que você grave dados em uma conta de armazenamento em uma rede virtual (VNET) no Azure.

Este artigo mostra como habilitar a identidade gerenciada para as saídas de blob de um trabalho de Stream Analytics por meio do portal do Azure e de uma implantação de Azure Resource Manager.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Criar o trabalho de Stream Analytics usando o portal do Azure

1. Crie um novo trabalho de Stream Analytics ou abra um trabalho existente no portal do Azure. Na barra de menus localizada no lado esquerdo da tela, selecione **identidade gerenciada** localizada em **Configurar**. Certifique-se de que "usar identidade gerenciada atribuída pelo sistema" esteja selecionado e, em seguida, clique no botão **salvar** na parte inferior da tela.

   ![Configurar Stream Analytics identidade gerenciada](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-enable-managed-identity.png)

2. Na janela Propriedades de saída do coletor de saída do armazenamento de BLOBs do Azure, selecione a lista suspensa modo de autenticação e escolha **identidade gerenciada**. Para obter informações sobre outras propriedades de saída, consulte [entender as saídas de Azure Stream Analytics](./stream-analytics-define-outputs.md). Quando terminar, clique em **Salvar**.

   ![Configurar a saída do armazenamento de BLOBs do Azure](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Agora que o trabalho foi criado, consulte a seção [fornecer a Stream Analytics o acesso ao trabalho para sua conta de armazenamento](#give-the-stream-analytics-job-access-to-your-storage-account) deste artigo.

## <a name="azure-resource-manager-deployment"></a>Implantação do Azure Resource Manager

O uso de Azure Resource Manager permite automatizar totalmente a implantação de seu trabalho de Stream Analytics. Você pode implantar modelos do Resource Manager usando Azure PowerShell ou o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Os exemplos a seguir usam o CLI do Azure.


1. Você pode criar um recurso **Microsoft. StreamAnalytics/streamingjobs** com uma identidade gerenciada, incluindo a seguinte propriedade na seção de recursos do seu modelo do Resource Manager:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Essa propriedade informa Azure Resource Manager para criar e gerenciar a identidade de seu trabalho de Stream Analytics. Veja abaixo um exemplo de modelo do Resource Manager que implanta um trabalho de Stream Analytics com identidade gerenciada habilitada e um coletor de saída de BLOB que usa identidade gerenciada:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    O trabalho acima pode ser implantado para o grupo de recursos de **exemplo** usando o comando a seguir CLI do Azure:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Depois que o trabalho for criado, você poderá usar Azure Resource Manager para recuperar a definição completa do trabalho.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    O comando acima retornará uma resposta como a seguinte:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Anote a entidade de **segurança** da definição do trabalho, que identifica a identidade gerenciada do trabalho dentro do Azure Active Directory e será usada na próxima etapa para conceder ao Stream Analytics acesso ao trabalho para a conta de armazenamento.

3. Agora que o trabalho foi criado, consulte a seção [fornecer a Stream Analytics o acesso ao trabalho para sua conta de armazenamento](#give-the-stream-analytics-job-access-to-your-storage-account) deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Dê ao Stream Analytics acesso ao trabalho para sua conta de armazenamento

Há dois níveis de acesso que você pode escolher para dar seu Stream Analytics trabalho:

1. **Acesso em nível de contêiner:** essa opção fornece o acesso de trabalho a um contêiner existente específico.
2. **Acesso no nível da conta:** essa opção fornece ao trabalho acesso geral à conta de armazenamento, incluindo a capacidade de criar novos contêineres.

A menos que você precise do trabalho para criar contêineres em seu nome, escolha o **acesso em nível de contêiner** , pois essa opção concederá ao trabalho o nível mínimo de acesso necessário. As duas opções são explicadas abaixo para o portal do Azure e a linha de comando.

### <a name="grant-access-via-the-azure-portal"></a>Conceder acesso por meio do portal do Azure

#### <a name="container-level-access"></a>Acesso em nível de contêiner

1. Navegue até o painel de configuração do contêiner em sua conta de armazenamento.

2. Selecione **controle de acesso (iam)** no lado esquerdo.

3. Na seção "adicionar uma atribuição de função", clique em **Adicionar**.

4. No painel atribuição de função:

    1. Defina a **função** como "colaborador de dados de blob de armazenamento"
    2. Certifique-se de que a lista suspensa **atribuir acesso ao** está definida como "usuário, grupo ou entidade de serviço do Azure AD".
    3. Digite o nome do seu trabalho de Stream Analytics no campo de pesquisa.
    4. Selecione seu trabalho de Stream Analytics e clique em **salvar**.

   ![Conceder acesso ao contêiner](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Acesso no nível da conta

1. Navegue até sua conta de armazenamento.

2. Selecione **controle de acesso (iam)** no lado esquerdo.

3. Na seção "adicionar uma atribuição de função", clique em **Adicionar**.

4. No painel atribuição de função:

    1. Defina a **função** como "colaborador de dados de blob de armazenamento"
    2. Certifique-se de que a lista suspensa **atribuir acesso ao** está definida como "usuário, grupo ou entidade de serviço do Azure AD".
    3. Digite o nome do seu trabalho de Stream Analytics no campo de pesquisa.
    4. Selecione seu trabalho de Stream Analytics e clique em **salvar**.

   ![Conceder acesso à conta](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Conceder acesso por meio da linha de comando

#### <a name="container-level-access"></a>Acesso em nível de contêiner

Para conceder acesso a um contêiner específico, execute o seguinte comando usando o CLI do Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Acesso no nível da conta

Para conceder acesso à conta inteira, execute o seguinte comando usando o CLI do Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Habilitar o acesso à VNET

Ao configurar os **firewalls e as redes virtuais**da sua conta de armazenamento, você pode opcionalmente permitir o tráfego de rede de outros serviços confiáveis da Microsoft. Quando Stream Analytics autentica usando identidade gerenciada, ele fornece uma prova de que a solicitação é originada de um serviço confiável. Abaixo estão as instruções para habilitar essa exceção de acesso VNET.

1.  Navegue até o painel "firewalls e redes virtuais" no painel de configuração da conta de armazenamento.
2.  Verifique se a opção "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento" está habilitada.
3.  Se você o tiver habilitado, clique em **salvar**.

   ![Habilitar o acesso à VNET](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Limitações
Abaixo estão as limitações atuais deste recurso:

1. Contas de armazenamento do Azure clássicas.

2. Contas do Azure sem Azure Active Directory.

3. Não há suporte para acesso multilocatário. A entidade de serviço criada para um determinado trabalho de Stream Analytics deve residir no mesmo locatário de Azure Active Directory no qual o trabalho foi criado e não pode ser usada com um recurso que reside em um locatário de Azure Active Directory diferente.

4. Não há suporte para a [identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/overview.md) . Isso significa que o usuário não é capaz de inserir sua própria entidade de serviço a ser usada por seus Stream Analytics trabalho. A entidade de serviço deve ser gerada pelo Azure Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Entender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics o particionamento de saída de blob personalizado](./stream-analytics-custom-path-patterns-blob-storage-output.md)

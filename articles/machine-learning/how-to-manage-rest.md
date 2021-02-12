---
title: Usar REST para gerenciar recursos do ML
titleSuffix: Azure Machine Learning
description: Como usar as APIs REST para criar, executar e excluir Azure Machine Learning recursos, como um espaço de trabalho ou registrar modelos.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 09a0580adbe6d51e4de811a57ee17203d65a2435
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316900"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Criar, executar e excluir recursos do Azure ML usando REST



Há várias maneiras de gerenciar seus recursos do Azure ML. Você pode usar o [portal](https://portal.azure.com/), a [interface de linha de comando](/cli/azure/?preserve-view=true&view=azure-cli-latest)ou o SDK do [python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Ou, você pode escolher a API REST. A API REST usa verbos HTTP de uma maneira padrão para criar, recuperar, atualizar e excluir recursos. A API REST funciona com qualquer linguagem ou ferramenta que possa fazer solicitações HTTP. A estrutura direta do REST geralmente o torna uma boa opção nos ambientes de script e na automação de MLOps. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Recuperar um token de autorização
> * Criar uma solicitação REST formatada corretamente usando a autenticação de entidade de serviço
> * Usar solicitações GET para recuperar informações sobre os recursos hierárquicos do Azure ML
> * Usar solicitações PUT e POST para criar e modificar recursos
> * Usar solicitações de exclusão para limpar recursos 
> * Usar a autorização baseada em chave para pontuar modelos implantados

## <a name="prerequisites"></a>Pré-requisitos

- Uma **assinatura do Azure** para a qual você tem direitos administrativos. Se você não tiver essa assinatura, experimente a [assinatura pessoal gratuita ou paga](https://aka.ms/AMLFree)
- Um [Workspace do Azure Machine Learning](./how-to-manage-workspace.md)
- As solicitações administrativas REST usam a autenticação de entidade de serviço. Siga as etapas em [Configurar a autenticação para Azure Machine Learning recursos e fluxos](./how-to-setup-authentication.md#service-principal-authentication) de trabalho para criar uma entidade de serviço em seu espaço de trabalho
- O utilitário de **rotação** . O programa de **ondulação** está disponível no [subsistema do Windows para Linux](/windows/wsl/install-win10) ou em qualquer distribuição do UNIX. No PowerShell, a **ondulação** é um alias para **Invoke-WebRequest** e `curl -d "key=val" -X POST uri` se torna `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Recuperar um token de autenticação de entidade de serviço

As solicitações de REST administrativas são autenticadas com um fluxo implícito OAuth2. Esse fluxo de autenticação usa um token fornecido pela entidade de serviço da sua assinatura. Para recuperar esse token, você precisará de:

- Sua ID de locatário (identificando a organização à qual sua assinatura pertence)
- Sua ID do cliente (que será associada ao token criado)
- Seu segredo do cliente (que você deve proteger)

Você deve ter esses valores da resposta à criação de sua entidade de serviço. Obter esses valores é discutido em [Configurar a autenticação para recursos de Azure Machine Learning e fluxos de trabalho](./how-to-setup-authentication.md#service-principal-authentication). Se você estiver usando a assinatura de sua empresa, talvez não tenha permissão para criar uma entidade de serviço. Nesse caso, você deve usar uma [assinatura pessoal gratuita ou paga](https://aka.ms/AMLFree).

Para recuperar um token:

1. Abrir uma janela de terminal
1. Insira o código a seguir na linha de comando
1. Substitua seus próprios valores para `{your-tenant-id}` , `{your-client-id}` e `{your-client-secret}` . Ao longo deste artigo, as cadeias de caracteres entre chaves são variáveis que você terá que substituir pelos seus próprios valores apropriados.
1. Executar o comando

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

A resposta deve fornecer um token de acesso válido para uma hora:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Anote o token, pois você o usará para autenticar todas as solicitações administrativas subsequentes. Você fará isso definindo um cabeçalho de autorização em todas as solicitações:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Observe que o valor começa com a cadeia de caracteres "portador", incluindo um único espaço antes de adicionar o token.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Obter uma lista de grupos de recursos associados à sua assinatura

Para recuperar a lista de grupos de recursos associados à sua assinatura, execute:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

No Azure, muitas APIs REST são publicadas. Cada provedor de serviços atualiza sua API em sua própria cadência, mas faz isso sem interromper os programas existentes. O provedor de serviços usa o `api-version` argumento para garantir a compatibilidade. O `api-version` argumento varia de serviço para serviço. Para o serviço de Machine Learning, por exemplo, a versão da API atual é `2019-11-01` . Para contas de armazenamento, é `2019-06-01` . Para cofres de chaves, é `2019-09-01` . Todas as chamadas REST devem definir o `api-version` argumento para o valor esperado. Você pode contar com a sintaxe e a semântica da versão especificada, mesmo que a API continue a evoluir. Se você enviar uma solicitação a um provedor sem o `api-version` argumento, a resposta conterá uma lista legível de valores com suporte. 

A chamada acima resultará em uma resposta JSON compactada do formulário: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Faça uma busca detalhada em espaços de trabalho e seus recursos

Para recuperar o conjunto de espaços de trabalho em um grupo de recursos, execute o seguinte, substituindo `{your-subscription-id}` , `{your-resource-group}` e `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Novamente, você receberá uma lista JSON, desta vez contendo uma lista, cada item do qual detalha um espaço de trabalho:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Para trabalhar com recursos em um espaço de trabalho, você alternará do servidor **Management.Azure.com** geral para um servidor de API REST específico para o local do espaço de trabalho. Observe o valor da `discoveryUrl` chave na resposta JSON acima. Se você OBTIVEr essa URL, receberá uma resposta como:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

O valor da `api` resposta é a URL do servidor que você usará para solicitações adicionais. Para listar experimentos, por exemplo, envie o comando a seguir. Substituir `regional-api-server` pelo valor da `api` resposta (por exemplo, `centralus.api.azureml.ms` ). Substitua também `your-subscription-id` , `your-resource-group` , `your-workspace-name` e `your-access-token` como de costume:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Da mesma forma, para recuperar modelos registrados em seu espaço de trabalho, envie:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Observe que, para listar os experimentos que o caminho começa com `history/v1.0` while para listar modelos, o caminho começa com `modelmanagement/v1.0` . A API REST é dividida em vários grupos operacionais, cada um com um caminho distinto. 

|Área|Caminho|
|-|-|
|Artifacts|/rest/api/azureml|
|Armazenamentos de dados|/azure/machine-learning/how-to-access-data|
|Ajuste de hiperparâmetro|hyperdrive/v 1.0/|
|Modelos|modelmanagement/v 1.0/|
|Histórico da execução|execução/v 1.0/e History/v 1.0/|

Você pode explorar a API REST usando o padrão geral de:

|Componente de URL|Exemplo|
|-|-|
| https://| |
| regional-API-Server/ | centralus.api.azureml.ms/ |
| operações-caminho/ | History/v 1.0/ |
| assinaturas/{sua-Subscription-ID}/ | assinaturas/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{Your-Resource-Group}/ | resourceGroups/MyResource/ |
| provedores/operação-provedor/ | Providers/Microsoft. MachineLearningServices/ |
| provedor-recurso-caminho/ | espaços de trabalho/MLWorkspace/MyWorkspace/FirstExperiment/execuções/1/ |
| operações-ponto de extremidade/ | artefatos/metadados/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Criar e modificar recursos usando solicitações PUT e POST

Além da recuperação de recursos com o verbo GET, a API REST dá suporte à criação de todos os recursos necessários para treinar, implantar e monitorar soluções de ML. 

Os modelos de ML de treinamento e execução exigem recursos de computação. Você pode listar os recursos de computação de um espaço de trabalho com: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Para criar ou substituir um recurso de computação nomeado, você usará uma solicitação PUT. A seguir, além das substituições agora conhecidas de `your-subscription-id` , `your-resource-group` , `your-workspace-name` , e `your-access-token` , substituir e `your-compute-name` valores para `location` , `vmSize` , `vmPriority` ,, `scaleSettings` `adminUserName` e `adminUserPassword` . Conforme especificado na referência em [computação do Machine Learning-Create ou Update SDK Reference](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), o comando a seguir cria um Standard_D1 de nó único dedicado (um recurso de computação de CPU básico) que será reduzido após 30 minutos:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Em terminais do Windows, você pode precisar escapar dos símbolos de aspas duplas ao enviar dados JSON. Ou seja, o texto como `"location"` se torna `\"location\"` . 

Uma solicitação bem-sucedida receberá uma `201 Created` resposta, mas observe que essa resposta simplesmente significa que o processo de provisionamento foi iniciado. Você precisará sondar (ou usar o Portal) para confirmar sua conclusão bem-sucedida.

### <a name="create-an-experimental-run"></a>Criar uma execução experimental

Para iniciar uma execução em um experimento, você precisa de uma pasta zip que contém o script de treinamento e os arquivos relacionados e um arquivo JSON de definição de execução. A pasta zip deve ter o arquivo de entrada Python em seu diretório raiz. Por exemplo, compactar um programa de Python trivial, como o seguinte, em uma pasta chamada **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Salve este próximo trecho de código como **definition.jsem**. Confirme se o valor de "script" corresponde ao nome do arquivo Python que você acabou de compactar. Confirme se o valor de "destino" corresponde ao nome de um recurso de computação disponível. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Poste esses arquivos no servidor usando o `multipart/form-data` conteúdo:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Uma solicitação POST bem-sucedida gerará um `200 OK` status, com um corpo de resposta que contém o identificador da execução criada:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Você pode monitorar uma execução usando o padrão REST-realizando que agora deve ser familiar:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Exclua os recursos que você não precisa mais

Alguns, mas não todos, os recursos dão suporte ao verbo DELETE. Verifique a [referência da API](/rest/api/azureml/) antes de confirmar a API REST para casos de uso de exclusão. Para excluir um modelo, por exemplo, você pode usar:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Use REST para pontuar um modelo implantado

Embora seja possível implantar um modelo para que ele se autentique com uma entidade de serviço, a maioria das implantações voltadas para o cliente usa a autenticação baseada em chave. Você pode encontrar a chave apropriada na página da sua implantação na guia **pontos de extremidade** do estúdio. O mesmo local mostrará o URI de Pontuação do ponto de extremidade. As entradas do modelo devem ser modeladas como uma matriz JSON chamada `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Criar um espaço de trabalho usando REST 

Cada espaço de trabalho do Azure ML tem uma dependência de quatro outros recursos do Azure: um registro de contêiner com administração habilitada, um cofre de chaves, um recurso de Application Insights e uma conta de armazenamento. Você não pode criar um espaço de trabalho até que esses recursos existam. Consulte a referência da API REST para obter os detalhes da criação de cada um desses recursos.

Para criar um espaço de trabalho, coloque uma chamada semelhante à seguinte para `management.azure.com` . Embora essa chamada exija que você defina um grande número de variáveis, ela é estruturalmente idêntica a outras chamadas discutidas neste artigo. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Você deve receber uma `202 Accepted` resposta e, nos cabeçalhos retornados, um `Location` URI. Você pode obter esse URI para obter informações sobre a implantação, incluindo informações úteis de depuração se houver um problema com um dos seus recursos dependentes (por exemplo, se você se esqueceu de habilitar o acesso de administrador no registro de contêiner). 

## <a name="troubleshooting"></a>Solução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Como mover o workspace

> [!WARNING]
> Não há suporte para a movimentação do workspace do Azure Machine Learning para outra assinatura nem para a movimentação da assinatura proprietária para um novo locatário. Se você fizer isso, poderá causar erros.

### <a name="deleting-the-azure-container-registry"></a>Como excluir o Registro de Contêiner do Azure

O workspace do Azure Machine Learning usa o ACR (Registro de Contêiner do Azure) para algumas operações. Ele criará automaticamente uma instância do ACR quando precisar de uma pela primeira vez.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Próximas etapas

- Explore a [referência da API REST do AzureML](/rest/api/azureml/)completa.
- Saiba como usar o designer para [prever o preço do automóvel com o designer](./tutorial-designer-automobile-price-train-score.md).
- Explore [Azure Machine Learning com notebooks Jupyter](..//machine-learning/samples-notebooks.md).
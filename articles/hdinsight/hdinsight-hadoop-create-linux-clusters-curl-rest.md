---
title: Criar clusters do Apache Hadoop usando a API de REST do Azure - Azure
description: Saiba como criar clusters do HDInsight enviando os modelos do Azure Resource Manager para a API REST do Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 12/10/2019
ms.openlocfilehash: fa5675104d9614e1bd917585ea537c92dddd88cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945783"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Criar clusters do Apache Hadoop usando a API REST do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como criar um cluster HDInsight usando um modelo do Azure Resource Manager e a API REST do Azure.

A API REST do Azure permite executar operações de gerenciamento de serviços hospedados na plataforma Azure, incluindo a criação de novos recursos, como clusters HDInsight.

> [!NOTE]  
> As etapas neste documento usam o utilitário [curl (https://curl.haxx.se/)](https://curl.haxx.se/) para comunicar-se com a API REST do Azure.

## <a name="create-a-template"></a>Criar um modelo

Os modelos de Azure Resource Manager são documentos JSON que descrevem um **grupo de recursos** e todos os recursos nele (como o HDInsight). Essa abordagem baseada em modelo permite que você defina os recursos necessários para o HDInsight em um modelo.

O documento JSON a seguir é uma fusão do modelo e dos arquivos de parâmetros do [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password) , que cria um cluster baseado em Linux usando uma senha para proteger a conta de usuário SSH.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Esse exemplo será usado nas etapas presentes neste documento. Substitua os *valores* de exemplo na seção **Parâmetros** pelos valores para o cluster.

> [!IMPORTANT]  
> O modelo usa o número padrão de nós de trabalho (4) para um cluster HDInsight. Se você planeja ter mais de 32 nós de trabalho, será necessário selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

Siga as etapas documentadas em [Introdução à CLI do Azure](/cli/azure/get-started-with-az-cli2) e conecte-se à sua assinatura usando o `az login` comando.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

> [!NOTE]  
> Essas etapas são uma versão resumida da seção *Criar a entidade de serviço com uma senha* do documento [Usar a CLI do Azure para criar uma entidade de serviço para acessar recursos](/cli/azure/create-an-azure-service-principal-azure-cli). Estas etapas criam uma entidade de serviço que é usada para autenticar a API REST do Azure.

1. Em uma linha de comando, use o seguinte comando para listar as assinaturas do Azure.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na lista, selecione a assinatura que você deseja usar e observe as colunas **Subscription_ID** e __Tenant_ID__. Salve esses valores.

2. Use o comando a seguir para criar um aplicativo no Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Substitua os valores de `--display-name`, `--homepage` e `--identifier-uris` pelos seus próprios valores. Forneça uma senha para a nova entrada do Active Directory.

   > [!NOTE]  
   > Os valores `--home-page` e `--identifier-uris` não precisam fazer referência a uma página da Web real hospedada na Internet. Eles devem ser URIs exclusivos.

   O valor retornado deste comando é a __ID do aplicativo__ do novo aplicativo. Salve esse valor.

3. Use o comando a seguir para criar uma entidade de serviço usando a **ID do aplicativo**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     O valor retornado deste comando é a __ID de Objeto__. Salve esse valor.

4. Atribua a função **Proprietário** à entidade de serviço usando o valor da **ID de Objeto**. Use o valor **ID da assinatura** obtido anteriormente.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Obtenha um token de autenticação

Use o seguinte comando para recuperar um token de autenticação:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Defina `$TENANTID`, `$APPID` e `$PASSWORD` para os valores obtidos ou usado anteriormente.

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series, e o corpo da resposta conterá um documento JSON.

O documento JSON retornado por esta solicitação contém um elemento denominado **access_token**. O valor de **access_token** é usado para solicitações de autenticação para a API REST.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Use o que é descrito a seguir para criar um grupo de recursos.

* Substitua `$SUBSCRIPTIONID` pela ID da assinatura recebida ao criar a entidade de serviço.
* Substitua `$ACCESSTOKEN` pelo token de acesso recebido na etapa anterior.
* Substitua `DATACENTERLOCATION` pelo data center no qual você quer criar o grupo de recursos e os recursos. Por exemplo, "Centro-Sul dos EUA".
* Defina `$RESOURCEGROUPNAME` como o nome que deseja usar para este grupo:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series e o corpo da resposta conterá um documento JSON com informações sobre o grupo. O elemento `"provisioningState"` contém um valor de `"Succeeded"`.

## <a name="create-a-deployment"></a>Criar uma implantação

Execute o comando a seguir para implantar o modelo no grupo de recursos.

* Defina `$DEPLOYMENTNAME` como o nome que deseja usar para esta implantação.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Se você salvou o modelo em um arquivo, pode usar o seguinte comando em vez de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series e o corpo da resposta conterá um documento JSON com informações sobre a operação de implantação.

> [!IMPORTANT]  
> A implantação foi enviada, mas não foi concluída. Pode levar vários minutos, normalmente em torno de 15, para concluir a implantação.

## <a name="check-the-status-of-a-deployment"></a>Verificar o status de uma implantação

Para verificar o status da implantação, use o comando a seguir:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Esse comando retorna informações de um documento JSON que contém informações sobre a operação de implantação. O elemento `"provisioningState"` contém o status da implantação. Se esse elemento contiver um valor de `"Succeeded"`, a implantação foi concluída com êxito.

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um cluster HDInsight com êxito, use o seguinte para aprender a trabalhar com o cluster.

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters do Apache Storm

* [Desenvolver topologias em Java para o Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes de Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

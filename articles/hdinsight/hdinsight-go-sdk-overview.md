---
title: SDK do Azure HDInsight para Go
description: Material de referência para usar o SDK do Azure HDInsight para clusters do Go e do Apache Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 09564bd149488d956586c8a0e349cd79eec358a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582757"
---
# <a name="hdinsight-sdk-for-go-preview"></a>SDK do HDInsight para Go (versão prévia)

## <a name="overview"></a>Visão geral
O SDK do HDInsight para Go oferece classes e funções que permitem gerenciar seus clusters do HDInsight. Inclui operações para criar, excluir, atualizar, listar, redimensionar, executar ações de script, monitorar, obter propriedades dos clusters HDInsight e muito mais.

> [!NOTE]  
>O material de referência do GoDoc para esse SDK também está [disponível aqui](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [ferramenta `go get`](https://github.com/golang/go/wiki/GoGetTools).
* [Go](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalação do SDK

Na sua localização do GOPATH, execute `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Autenticação

O SDK precisa primeiro ser autenticado com a assinatura do Azure.  Siga o exemplo abaixo para criar uma entidade de serviço e use-a para a autenticação. Após isso ser feito, você terá uma instância de um `ClustersClient`, que contém muitas funções (descritas nas seções abaixo) que podem ser usadas para realizar operações de gerenciamento.

> [!NOTE]  
> Existem outras formas de autenticar além do exemplo abaixo que talvez sejam mais adequadas às suas necessidades. Todas as funções são descritas aqui: [Funções de autenticação no SDK do Azure para linguagem Go](/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Exemplo de autenticação usando uma entidade de serviço

Primeiro, faça logon no [Azure Cloud Shell](https://shell.azure.com/bash). Verifique se você está usando atualmente a assinatura na qual deseja a entidade de serviço criada.

```azurecli-interactive
az account show
```

As informações da assinatura são exibidas como JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Se você não estiver conectado à assinatura correta, selecione a correta executando: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Se ainda não tiver registrado o Provedor de Recursos do HDInsight por meio de outra função (tal como criando um cluster do HDInsight pelo portal do Azure), você precisará fazer isso uma vez para poder autenticar. Isso também pode ser feito no [Azure Cloud Shell](https://shell.azure.com/bash) executando o seguinte comando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Em seguida, escolha um nome para a sua entidade de serviço e crie-a com o seguinte comando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

As informações da entidade de serviço são exibidas como JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Copie o snippet abaixo e preencha `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET` e `SUBSCRIPTION_ID` com as cadeias de caracteres do JSON que foi retornado após a execução do comando para criar a entidade de serviço.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Gerenciamento de clusters

> [!NOTE]  
> Essa seção pressupõe que você já autenticou e criou uma instância `ClusterClient` e a armazenou em uma variável chamada `client`. As instruções para autenticar e obter um `ClusterClient` podem ser encontradas na seção Autenticação acima.

### <a name="create-a-cluster"></a>Criar um cluster

Um novo cluster pode ser criado chamando `client.Create()`. 

#### <a name="example"></a>Exemplo

Este exemplo demonstra como criar um cluster do [Apache Spark](https://spark.apache.org/) com dois nós de cabeçalho e um nó de trabalho.

> [!NOTE]  
> Primeiro você precisa criar um grupo de recursos e uma conta de armazenamento, conforme explicado abaixo. Se você já os tiver criado, ignore as próximas etapas.

##### <a name="creating-a-resource-group"></a>Criar um grupo de recursos

É possível criar um grupo de recursos usando o [Azure Cloud Shell](https://shell.azure.com/bash) executando:

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Criando uma conta de armazenamento

É possível criar uma conta de armazenamento usando o [Azure Cloud Shell](https://shell.azure.com/bash) executando:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Agora execute o seguinte comando para obter a chave para a sua conta de armazenamento (você precisará dela para criar um cluster):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
O snippet do Go abaixo cria um cluster do Spark com dois nós principais e um nó de trabalho. Preencha as variáveis em branco conforme explicado nos comentários e fique à vontade para alterar outros parâmetros conforme as suas necessidades.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Obter detalhes do cluster

Para obter propriedades para um determinado cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Você pode usar `get` para confirmar que criou com êxito o seu cluster.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

A saída deve ser assim:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Listar clusters

#### <a name="list-clusters-under-the-subscription"></a>Listar os clusters em uma assinatura

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Listar os clusters por grupo de recursos

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Tanto `List()` quanto `ListByResourceGroup()` retornam um struct `ClusterListResultPage`. Para obter a página seguinte, você pode chamar `Next()`. Isso pode ser repetido até que `ClusterListResultPage.NotDone()` retorne `false`, conforme mostrado no exemplo abaixo.

#### <a name="example"></a>Exemplo

O exemplo a seguir imprime as propriedades de todos os clusters da assinatura atual:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Excluir um cluster

Para excluir um cluster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Atualizar as marcas de cluster

É possível atualizar as marcas de um determinado cluster da seguinte forma:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Exemplo

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Redimensionar o cluster

É possível redimensionar o número de nós de trabalho de determinado cluster especificando um novo tamanho, assim:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitoramento do cluster

O SDK de gerenciamento do HDInsight também pode ser usado para gerenciar o monitoramento dos seus clusters através do Operations Management Suite (OMS).

Da mesma forma que você criou `ClusterClient` para ser usado para as operações de gerenciamento, é necessário criar um `ExtensionClient` para ser usado para as operações de monitoramento. Depois de concluir a seção Autenticação acima, você poderá criar um `ExtensionClient` da seguinte forma:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Os exemplos de monitoramento abaixo pressupõem que você já inicializou um `ExtensionClient` chamado `extClient` e definiu seus `Authorizer` como mostrado acima.

### <a name="enable-oms-monitoring"></a>Habilitar o monitoramento de OMS

> [!NOTE]  
> Para habilitar o Monitoramento de OMS, você deve ter um espaço de trabalho do Log Analytics existente. Se você já não tiver criado um, você pode aprender como fazer isso aqui: [Criar um espaço de trabalho do Log Analytics no portal do Azure](../azure-monitor/logs/quick-create-workspace.md).

Para habilitar o Monitoramento de OMS no seu cluster:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Exibir o status do monitoramento de OMS

Para obter o status do OMS no seu cluster:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Desabilitar monitoramento de OMS

Para desabilitar o OMS no seu cluster:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Ações de script

O HDInsight fornece uma função de configuração chamada ações de script que invoca os scripts personalizados para personalizar o cluster.

> [!NOTE]  
> Mais informações sobre como usar as ações de script podem ser encontradas aqui: [Personalizar clusters HDInsight com base em Linux usando ações de script](./hdinsight-hadoop-customize-cluster-linux.md)

### <a name="execute-script-actions"></a>Executar ações de script

É possível executar ações de script em um determinado cluster da seguinte forma:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Para as operações “Excluir a ação de script” e “Listar ações de script persistentes”, você precisa criar um `ScriptActionsClient`, da mesma forma que criou `ClusterClient` para ser usado para operações de gerenciamento. Depois de concluir a seção Autenticação acima, você poderá criar um `ScriptActionsClient` da seguinte forma:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Os exemplos de ações de script abaixo pressupõem que você já inicializou um `ScriptActionsClient` chamado `scriptActionsClient` e definiu seus `Authorizer` como mostrado acima.

### <a name="delete-script-action"></a>Excluir a ação de script

Para excluir uma ação de script persistente específica em um determinado cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Listar ações de script persistentes

> [!NOTE]  
> Os dois `ListByCluster()` retornam um struct `ScriptActionsListPage`. Para obter a página seguinte, você pode chamar `Next()`. Isso pode ser repetido até que `ClusterListResultPage.NotDone()` retorne `false`, conforme mostrado no exemplo abaixo.

Para listar todas as ações de script persistentes para o cluster especificado:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Listar o histórico de execução de todos os scripts

Para esta operação, é necessário criar um `ScriptExecutionHistoryClient`, da mesma forma que criou `ClusterClient` para ser usado nas operações de gerenciamento. Depois de concluir a seção Autenticação acima, você poderá criar um `ScriptActionsClient` da seguinte forma:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> O exemplo abaixo pressupõem que você já inicializou um `ScriptExecutionHistoryClient` chamado `scriptExecutionHistoryClient` e definiu seus `Authorizer` como mostrado acima.

Para listar o histórico de execução de todos os scripts para o cluster especificado:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Este exemplo imprime todos os detalhes para todas as execuções de script anteriores.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Explore o [material de referência do GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight). Os GoDocs fornecem a documentação de referência para todas as funções no SDK.

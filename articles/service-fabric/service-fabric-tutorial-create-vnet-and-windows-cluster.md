---
title: Criar um cluster do Service Fabric executando Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a implantar um cluster do Service Fabric do Windows em uma rede virtual do Azure e em um grupo de segurança de rede usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 12e886c107249c338dc27aefcd2e1a32eba13d3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598882"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Tutorial: Implantar um cluster do Service Fabric executando o Windows em uma rede virtual do Azure

Este tutorial é a primeira parte de uma série. Você aprenderá como implantar um cluster do Microsoft Azure Service Fabric executando o Windows em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) e em [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) usando o PowerShell e um modelo. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos. Para criar um cluster do Linux usando a CLI do Azure, consulte [Criar um cluster seguro do Linux no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Este tutorial descreve um cenário de produção. Se você quiser criar um cluster pequeno para fins de teste, consulte [Criar um cluster de teste](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configurar a autenticação do Azure Active Directory
> * Configurar a coleção de diagnóstico
> * Configurar o serviço EventStore
> * Configurar os logs do Azure Monitor
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Monitorar um cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Reduzir ou escalar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Excluir um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale o [SDK do Service Fabric e o módulo do PowerShell](service-fabric-get-started.md).
* Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Examine os conceitos principais dos [clusters do Azure](service-fabric-azure-clusters-overview.md).
* [Planejar e preparar](service-fabric-cluster-azure-deployment-preparation.md) para uma implantação de cluster de produção.

Os procedimentos a seguir criam um cluster de sete nós do Service Fabric. Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Baixar e explorar o modelo

Baixe os seguintes arquivos do modelo do Azure Resource Manager:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Esse modelo implanta um cluster seguro de sete máquinas virtuais e três tipos de nó em uma rede virtual e um Grupo de Segurança de Rede.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [azuredeploy.json][template] implanta uma série de recursos, incluindo os seguintes.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, um cluster do Windows é configurado com as seguintes características:

* Três tipos de nó.
* Cinco nós no tipo de nó primário (configurável nos parâmetros de modelo), um nó em cada um dos outros dois tipos de nó.
* sistema operacional: Windows Server 2016 Datacenter com Contêineres (configuráveis nos parâmetros de modelo).
* Certificado protegido (configurável nos parâmetros de modelo).
* [Proxy reverso](service-fabric-reverseproxy.md) está habilitado.
* [Serviço DNS](service-fabric-dnsservice.md) está habilitado.
* [Nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Bronze (configurável nos parâmetros de modelo).
* [Nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de Prata (configurável nos parâmetros de modelo).
* Ponto de extremidade de conexão do cliente: 19000 (configurável nos parâmetros de modelo).
* ponto de extremidade de gateway HTTP: 19080 (configurável nos parâmetros de modelo).

### <a name="azure-load-balancer"></a>Azure Load Balancer

No recurso **Microsoft.Network/loadBalancers**, um balanceador de carga é configurado. Investigações e regras estão configuradas para as seguintes portas:

* Ponto de extremidade de conexão do cliente: 19000
* ponto de extremidade de gateway HTTP: 19080
* Porta do aplicativo: 80
* Porta do aplicativo: 443
* Proxy inverso do Service Fabric: 19081

Se forem necessárias outras portas de aplicativo, você precisará ajustar o recurso **Microsoft.Network/loadBalancers** e o recurso **Microsoft.Network/networkSecurityGroups** para permitir o tráfego de entrada.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rede virtual, sub-rede e grupo de segurança de rede

Os nomes de rede virtual, sub-rede e grupo de segurança de rede são declarados nos parâmetros de modelo. Espaços de endereço de rede virtual e sub-rede também são declarados nos parâmetros de modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* Espaço de endereço da rede virtual: 172.16.0.0/20
* espaço de endereço de sub-rede do Service Fabric: 172.16.2.0/23

As regras de tráfego de entrada a seguir estão habilitadas no recurso **Microsoft.Network/networkSecurityGroups**. Você pode alterar os valores de porta, alterando as variáveis de modelo.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Intervalo de porta efêmera: 49152 a 65534 (necessário um mínimo de 256 portas).
* Portas para o uso do aplicativo: 80 e 443
* Intervalo de portas de destino: 49152 a 65534 (usado para comunicação serviço a serviço. Outras portas não estão abertas no balanceador de carga).
* Bloquear todas as outras portas

Se forem necessárias outras portas de aplicativo, você precisará ajustar o recurso **Microsoft.Network/loadBalancers** e o recurso **Microsoft.Network/networkSecurityGroups** para permitir o tráfego de entrada.

### <a name="windows-defender"></a>Windows Defender
Por padrão, o [Windows Defender Antivírus](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) está instalado e funcional no Windows Server 2016. A interface do usuário é instalada por padrão em alguns SKUs, mas não é necessária. Para cada tipo de nó/conjunto de dimensionamento de VMs declarado no modelo, a [extensão Antimalware de VM do Azure](/azure/virtual-machines/extensions/iaas-antimalware-windows) é usada para excluir os diretórios e os processos do Service Fabric:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Definir os parâmetros do modelo

O arquivo de parâmetros [azuredeploy.parameters.json][parameters] declara muitos valores usados para implantar o cluster e recursos associados. Estes são os parâmetros para modificar para sua implantação:

**Parâmetro** | **Valor de exemplo** | **Observações** 
|---|---|---|
|adminUserName|vmadmin| Um nome de usuário de administrador para as VMs do cluster. [Requisitos de nome de usuário para máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Uma senha de administrador para as VMs do cluster. [Requisitos de senha para máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Nome do cluster. Pode conter apenas letras e números. O comprimento deve ter entre 3 e 23 caracteres.|
|location|southcentralus| Localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que já foi carregado em um cofre de chaves, preencha o valor da impressão digital SHA1 do certificado. Por exemplo, "6190390162C988701DB5676EB81083EA608DCCF3".</p> |
|certificateUrlValue|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado. </p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha a URL do certificado. Por exemplo, "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha o valor do cofre de origem. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Configurar a autenticação de cliente do Azure Active Directory
Para clusters do Service Fabric implantados em uma rede pública hospedada no Azure, a recomendação para a autenticação mútua do cliente para nó é:
* Usar o Azure Active Directory para identidade de cliente.
* Use um certificado para identidade do servidor e criptografia SSL da comunicação HTTP.

A configuração do Azure Active Directory para autenticação de clientes de um cluster do Service Fabric precisa ser feita antes da [criação do cluster](#createvaultandcert). O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos. 

Um cluster do Service Fabric oferece vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e o [Visual Studio](service-fabric-manage-application-in-visual-studio.md) baseados na Web. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.  Depois que os aplicativos forem criados, você atribuirá usuários a funções de administrador e somente leitura.

> [!NOTE]
> Você deve concluir as etapas a seguir para poder criar o cluster. Como os scripts esperam pontos de extremidade e nomes de cluster, os valores devem ser planejados, não os valores que você já tinha criado.

Neste artigo, partimos do pressuposto que você já tenha criado um locatário. Se não for o caso, comece lendo [Como obter um locatário do Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Para simplificar as etapas envolvidas na configuração do Microsoft Azure Active Directory com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell. [Faça download dos scripts](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) em seu computador.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicativos do Azure AD e atribuir usuários a funções
Crie dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo. Depois de criar os aplicativos para representar seu cluster, atribua os usuários às [funções compatíveis com o Service Fabric](service-fabric-cluster-security-roles.md): somente leitura e administrador.

Execute `SetupApplications.ps1` e forneça a ID de locatário, o nome do cluster e a URL de resposta do aplicativo Web como parâmetros. Especifique também nomes de usuário e senhas para os usuários. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para nuvens nacionais (por exemplo, Azure Governamental, Azure China e Azure Alemanha), especifique o parâmetro `-Location`.

Encontre a *TenantId* ou a ID de diretório no [portal do Azure](https://portal.azure.com). Selecione **Azure Active Directory** > **Propriedades** e copie o valor **Directory ID**.

O *ClusterName* é usado para prefixar os aplicativos do Azure AD criados pelo script. Ele não precisa corresponder exatamente ao nome real do cluster. Ele só torna mais fácil mapear artefatos do Azure Active Directory ao cluster do Service Fabric em uso.

*WebApplicationReplyUrl* é o ponto de extremidade padrão retornado pelo Azure AD para os usuários depois que eles concluem a entrada. Defina esse ponto de extremidade como o ponto de extremidade do Service Fabric Explorer para o seu cluster, que por padrão é:

https://&lt;cluster_domain&gt;:19080/Explorer

Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do Azure Active Directory. Depois de entrar, o script criará aplicativos Web e nativos para representar seu cluster do Service Fabric. Nos aplicativos do locatário no [portal do Azure](https://portal.azure.com), você deverá ver duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Cliente

O script imprime o JSON necessário para o modelo do Resource Manager quando você cria o cluster, portanto, é uma boa ideia manter a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicionar a configuração do Azure AD para usar o Azure AD para acesso de cliente
Em [azuredeploy.json][template], configure o Azure AD na seção **Microsoft.ServiceFabric/clusters**. Adicione parâmetros para a ID de locatário, a ID do aplicativo do cluster e a ID do aplicativo cliente.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Adicione os valores de parâmetro ao arquivo de parâmetros [azuredeploy.parameters.json][parameters]. Por exemplo:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Configurar a coleção de diagnóstico no cluster
Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda a analisar e solucionar problemas no cluster ou nos aplicativos e serviços em execução nesse cluster.

Uma maneira de fazer upload e coletar logs é utilizar a extensão de Diagnóstico do Microsoft Azure (WAD) que faz upload dos logs no Armazenamento do Microsoft Azure e, além disso, possui a opção de enviar os logs para o Azure Application Insights ou Hubs de Evento. Também é possível utilizar um processo externo para ler os eventos do armazenamento e colocá-los em um produto da plataforma de análise, como os logs do Azure Monitor ou outra solução de análise de log.

Se você estiver seguindo este tutorial, a coleta de diagnóstico já estará configurada no [modelo][template].

Se você tiver um cluster existente que não tenha o Diagnóstico implantado, poderá adicioná-lo ou atualizá-lo por meio do modelo de cluster. Modifique o modelo do Resource Manager usado para criar o cluster existente ou baixe o modelo do portal. Modifique o arquivo template.json executando as tarefas a seguir:

Adicione um novo recurso de armazenamento à seção de recursos no modelo:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Em seguida, adicione parâmetros para o tipo e o nome da conta de armazenamento para a seção de parâmetros do modelo. Substitua o texto do espaço reservado nome da conta de armazenamento aqui pelo nome da conta de armazenamento que você deseja.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Em seguida, adicione a extensão **IaaSDiagnostics** à matriz de extensões da propriedade **VirtualMachineProfile** de cada recurso **Microsoft.Compute/virtualMachineScaleSets** no cluster.  Se você está usando o [modelo de exemplo][template], há três conjuntos de dimensionamento de máquinas virtuais (um para cada tipo de nó no cluster).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Configurar o serviço EventStore
O serviço EventStore introduzido é uma opção de monitoramento no Service Fabric. O EventStore fornece uma maneira de entender o estado do cluster ou cargas de trabalho em um determinado ponto no tempo. O EventStore é um serviço do Service Fabric com monitoramento de estado que mantém os eventos do cluster. Os eventos são expostos por meio do Service Fabric Explorer, REST e APIs. As consultas do EventStore permitem que você consulte o cluster diretamente para obter dados de diagnóstico em qualquer entidade no cluster e devem ser usadas para ajudar a:

* Diagnosticar problemas em desenvolvimento ou teste, ou em que talvez você possa estar usando um pipeline de monitoramento
* Confirmar que as ações de gerenciamento que você está realizando no cluster estão sendo processadas corretamente por ele
* Obter um "instantâneo" de como o Service Fabric está interagindo com uma entidade específica



Para habilitar o serviço do EventStore no seu cluster, adicione o seguinte para a propriedade **fabricSettings** do recurso **Microsoft.ServiceFabric/clusters**:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Configurar logs do Azure Monitor para o cluster

Os logs do Azure Monitor é nossa recomendação para monitorar eventos de nível de cluster. Para configurar os logs do Azure Monitor para monitorar seu cluster, você precisa ter o [diagnóstico habilitado para exibir eventos no nível do cluster ou da plataforma](#configure-diagnostics-collection-on-the-cluster).  

O workspace precisa estar conectado aos dados de diagnóstico provenientes do seu cluster.  Esses dados de log são armazenados na conta de armazenamento *applicationDiagnosticsStorageAccountName*, na tabelas WADServiceFabric*EventTable, WADWindowsEventLogsTable, e WADETWEventTable.

Adicione o espaço de trabalho do Log Analytics do Azure e adicione a solução ao espaço de trabalho:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Em seguida, adicione os parâmetros
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Em seguida, adicione as variáveis:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Adicione a extensão do agente do Log Analytics a cada conjunto de dimensionamento de máquinas virtuais no cluster e conecte o agente ao workspace do Log Analytics. Isso permite coletar dados de diagnóstico sobre contêineres, aplicativos e monitoramento do desempenho. Ao adicioná-lo como uma extensão para o recurso de conjunto de dimensionamento de máquinas virtuais, o Azure Resource Manager garante que ele seja instalado em cada nó, mesmo ao dimensionar o cluster.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implantar a rede virtual e o cluster

Em seguida, configure a topologia de rede e implante o cluster do Service Fabric. O modelo do Resource Manager [azuredeploy.json][template] cria uma rede virtual, uma sub-rede e também um grupo de segurança de rede para o Service Fabric. O modelo também implanta um cluster com a segurança de certificado habilitada. Para clusters de produção, use um certificado de uma autoridade de certificação como o certificado de cluster. Um certificado autoassinado pode ser usado para proteger clusters de teste.

O modelo deste artigo implanta um cluster que usa a impressão digital do certificado para identificar o certificado de cluster. Dois certificados não podem ter a mesma impressão digital, o que dificulta o gerenciamento de certificados. Alternar um cluster implantado do uso de impressões digitais de certificado para certificar os nomes comuns simplifica o gerenciamento de certificado. Para saber como atualizar o cluster para que ele use nomes comuns de certificado para o gerenciamento de certificados, leia [Alterar o cluster para o gerenciamento de nomes comuns de certificado](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Criar um cluster usando um certificado existente

O script a seguir usa o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster no Azure. O cmdlet cria um novo cofre de chaves no Azure e carrega o certificado.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Criar um cluster usando um novo certificado autoassinado

O script a seguir usa o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster no Azure. O cmdlet cria um cofre de chaves no Azure, adiciona um certificado autoassinado a ele e baixa o arquivo de certificado localmente.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Conectar-se ao cluster seguro

Conecte-se ao cluster usando o módulo do PowerShell do Service Fabric instalado com o SDK do Service Fabric.  Primeiro, instale o certificado no repositório pessoal (Meu) do usuário atual no seu computador. Execute o seguinte comando do PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Agora você já está pronto para se conectar ao seu cluster seguro.

O módulo do PowerShell do **Service Fabric** fornece vários cmdlets para gerenciar clusters, aplicativos e serviços do Service Fabric. Use o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para se conectar ao cluster seguro. Os detalhes do ponto de extremidade de conexão e de impressão digital do SHA1 do certificado podem ser encontrados na saída da etapa anterior.

Se você já tiver configurado a autenticação de cliente do Azure Active Directory anteriormente, execute o seguinte: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Se você já não tiver configurado a autenticação de cliente do Azure Active Directory anteriormente, execute o seguinte:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique se você está conectado e se o cluster está íntegro usando o cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpar recursos

Os outros artigos nesta série de tutoriais usam o cluster que você criou. Se você não for imediatamente para o próximo artigo, [exclua o cluster](service-fabric-cluster-delete.md) para evitar a cobrança de encargos.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo tutorial para saber como dimensionar o cluster.

> [!div class="checklist"]
> * Criar uma VNET no Azure usando o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configurar a autenticação do Azure Active Directory
> * Configurar a coleção de diagnóstico
> * Configurar o serviço EventStore
> * Configurar os logs do Azure Monitor
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

Em seguida, avance para o próximo tutorial para saber como monitorar o cluster.
> [!div class="nextstepaction"]
> [Monitorar um cluster](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

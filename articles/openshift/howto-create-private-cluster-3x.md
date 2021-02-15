---
title: Criar um cluster privado com o Red Hat OpenShift no Azure 3.11 | Microsoft Docs
description: Criar um cluster privado com o Red Hat OpenShift no Azure 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, private cluster, red hat
ms.openlocfilehash: 37e9dc996fddf2b592ea6bf7fff1e1f4825f3ca8
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220621"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Criar um cluster privado com o Red Hat OpenShift no Azure 3.11

> [!IMPORTANT]
> O Azure Red Hat OpenShift 3,11 será desativado em 30 de junho de 2022. O suporte para a criação de novos clusters do Azure Red Hat OpenShift 3,11 continua até 30 de novembro de 2020. Após a aposentadoria, os clusters restantes do Azure Red Hat OpenShift 3,11 serão desligados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se você tiver dúvidas específicas, [entre em contato conosco](mailto:arofeedback@microsoft.com).

Os clusters privados oferecem os seguintes benefícios:

* Os clusters privados não expõem componentes de painel de controle de cluster (como os servidores de API) em um endereço IP público.
* A rede virtual de um cluster privado é configurável por clientes, permitindo que você configure a rede para permitir o emparelhamento com outras redes virtuais, incluindo ambientes de ExpressRoute. Você também pode configurar o DNS personalizado na rede virtual para integração aos serviços internos.

## <a name="before-you-begin"></a>Antes de começar

Os campos no snippet de configuração a seguir são novos e devem ser incluídos na configuração do cluster. O `managementSubnetCidr` deve estar dentro da rede virtual de cluster e ser usado pelo Azure para gerenciar o cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Um cluster privado pode ser implantado usando os scripts de exemplo fornecidos abaixo. Depois que o cluster for implantado, execute o comando `cluster get` e veja a propriedade `properties.FQDN` para determinar o endereço IP privado do servidor de API do OpenShift.

A rede virtual do cluster terá sido criada com permissões para que você possa modificá-la. Em seguida, você pode configurar a rede para acessar a rede virtual (ExpressRoute, VPN, emparelhamento de rede virtual) conforme necessário para suas necessidades.

Se você alterar os nameservers DNS na rede virtual do cluster, será necessário emitir uma atualização no cluster com a propriedade `properties.RefreshCluster` definida como `true` para que as VMs possam ter a imagem recriada. Essa atualização permitirá que eles escolham os novos nameservers.

## <a name="sample-configuration-scripts"></a>Scripts de configuração de exemplo

Use os scripts de exemplo nesta seção para configurar e implantar seu cluster privado.

### <a name="environment"></a>Ambiente

Preencha as variáveis de ambiente abaixo usando seus valores.

> [!NOTE]
> A localização deve ser definida como `eastus2`, pois essa atualmente é a única localização com suporte para clusters privados.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.json

Usando as variáveis de ambiente definidas acima, aqui está um exemplo de configuração de cluster com o cluster privado habilitado.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Implantar um cluster privado

Depois de configurar seu cluster privado com os scripts de exemplo acima, execute o comando a seguir para implantar o cluster privado.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como acessar o console do OpenShift, confira o [Passo a passo do console Web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).

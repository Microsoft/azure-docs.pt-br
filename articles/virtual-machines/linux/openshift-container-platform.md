---
title: Implantar o OpenShift Container Platform no Azure | Microsoft Docs
description: Implante o OpenShift Container Platform no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 296bc42313ef80425004d3c9b43c6792cbaf97f4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126994"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implantar o OpenShift Container Platform no Azure

Você pode usar um dos vários métodos para implantar a plataforma de contêiner OpenShift no Azure:

- Você pode implantar manualmente todos os componentes de infraestrutura necessários do Azure e, em seguida, seguir a [documentação](https://docs.openshift.com/container-platform) do OpenShift Container Platform.
- Você também pode usar um [modelo do Gerenciador de Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implantação do cluster do OpenShift Container Platform.
- Outra opção é usar a [Oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para ambas as opções, é necessária uma assinatura do Red Hat. Durante a implantação, a instância Red Hat Enterprise Linux é registrada na Assinatura do Red Hat e anexada à ID do Pool que contém os direitos para o OpenShift Container Platform.
Certifique-se de que você tenha uma senha, uma ID do Pool e um Nome de usuário de gerente de Assinatura do Red Hat (RHSM) válidos. Você pode usar uma Chave de Ativação, ID da Organização e ID do Pool. Você pode verificar essas informações entrando em https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Implantar usando o modelo do Resource Manager de OpenShift Container Platform

### <a name="private-clusters"></a>Clusters particulares

A implantação de clusters particulares de OpenShift requer mais do que simplesmente não ter um IP público associado ao balanceador de carga mestre (console web) ou como a infraestrutura (roteador) do balanceador de carga.  Um cluster privado geralmente usa um servidor DNS personalizado (não o padrão DNS do Azure), um nome de domínio personalizado (por exemplo, contoso.com) e redes virtuais predefinidos.  Para clusters particulares, você precisa configurar sua rede virtual com todas as sub-redes apropriadas e as configurações do servidor DNS com antecedência.  Em seguida, use **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, e  **existingNodeSubnetReference** para especificar a sub-rede existente para uso pelo cluster.

Se privada mestre está selecionado (**masterClusterType**= privada), um endereço IP privado estático deve ser especificado para **masterPrivateClusterIp**.  Esse IP será atribuído para o front-end do balanceador de carga mestre.  O IP deve estar dentro de CIDR para a sub-rede mestre e não está em uso.  **masterClusterDnsType** deve ser definido como "custom" e o mestre de nome DNS deve ser fornecido para **masterClusterDns**.  O nome DNS deve ser mapeado para o IP privado estático e será usado para acessar o console em nós mestres.

Se o roteador privado é selecionado (**routerClusterType**= privada), um endereço IP privado estático deve ser especificado para **routerPrivateClusterIp**.  Esse IP será atribuído para o front-end do balanceador de carga de infraestrutura.  O IP deve estar dentro de CIDR para a infraestrutura sub-rede e não em uso.  **routingSubDomainType** deve ser definido como "custom" e o nome DNS curinga para o roteamento deve ser fornecido por **routingSubDomain**.  

Se forem selecionados privadas mestres e roteador privado, o nome de domínio personalizado também deve ser inserido para **domainName**

Após a implantação bem-sucedida, o nó de bastião é o único nó com um IP público que é possível executar ssh no.  Mesmo se os nós mestres estiverem configurados para acesso público, eles não são expostos para o ssh acesso.

Para implantar usando o modelo do Resource Manager, você usa um arquivo de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implantação, bifurque o repositório do GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não estão limitadas a:

- Tamanho de VM de bastião (variável em azuredeploy.json)
- Convenções de nomenclatura (variáveis em azuredeploy.json)
- Especificações de cluster do OpenShift – modificadas por meio do arquivo de hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurar o arquivo de parâmetros

O [modelo do OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) tem várias ramificações disponíveis para diferentes versões do OpenShift Container Platform.  Com base em suas necessidades, você pode implantar diretamente do repositório ou você pode criar fork do repositório e fazer alterações personalizadas para os modelos ou scripts antes de implantar.

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

O exemplo a seguir mostra um arquivo de parâmetros chamado azuredeploy.parameters.json com todas as entradas necessárias.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Substitua os parâmetros com as informações específicas.

Versões diferentes podem ter parâmetros diferentes. Portanto, verifique os parâmetros necessários para a ramificação que você usar.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Arquivo Parameters. JSON explicado

| Propriedade | DESCRIÇÃO | Opções válidas | Valor Padrão |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL para artefatos (json, scripts, etc.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Região do Azure para implantar recursos para |  |  |
| `masterVmSize` | Tamanho da VM mestra. Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_E2s_v3 |
| `infraVmSize` | Tamanho da infraestrutura VM. Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_D4s_v3 |
| `nodeVmSize` | Tamanho da VM do nó de aplicativo. Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_D4s_v3 |
| `cnsVmSize` | Tamanho da VM do nó de armazenamento nativo (CNS) recipiente. Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_E4s_v3 |
| `osImageType` | A imagem do RHEL para usar. defaultgallery: Sob demanda; Marketplace: imagem de terceiros | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Se `osImageType` é marketplace, em seguida, insira os valores apropriados para 'publisher', 'oferecem', 'sku', 'version' da oferta do marketplace. Esse parâmetro é um tipo de objeto |  |  |
| `storageKind` | O tipo de armazenamento a ser usado  | gerenciado<br> não gerenciado | gerenciado |
| `openshiftClusterPrefix` | Prefixo usado para configurar nomes de host para todos os nós do cluster.  Entre 1 e 20 caracteres |  | MyCluster |
| `minoVersion` | A versão secundária do OpenShift Container Platform 3.11 para implantar |  | 69 |
| `masterInstanceCount` | Número de nós mestres para implantar | 1, 3, 5 | 3 |
| `infraInstanceCount` | Número de infraestrutura nós a serem implantados | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Número de nós a serem implantados | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Número de nós CNS para implantar | 3, 4 | 3 |
| `osDiskSize` | Tamanho do disco do SO da VM (em GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Tamanho do disco de dados a ser anexado a nós para o volume do Docker (em GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Tamanho do disco de dados a ser anexado a nós CNS para uso por glusterfs (em GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome de usuário de administrador para logon de sistema operacional (VM) e o usuário inicial do OpenShift |  | ocpadmin |
| `enableMetrics` | Habilite as métricas. As métricas que exigem que mais recursos portanto, selecione o tamanho adequado de infraestrutura de VM | verdadeiro <br> falso | falso |
| `enableLogging` | Habilite registro em log. requer o pod de elasticsearch 8 GB de RAM portanto, selecione o tamanho adequado de infraestrutura de VM | verdadeiro <br> falso | falso |
| `enableCNS` | Habilitar o contêiner de armazenamento nativo | verdadeiro <br> falso | falso |
| `rhsmUsernameOrOrgId` | ID de nome de usuário do Gerenciador de assinaturas Hat ou organização vermelho |  |  |
| `rhsmPoolId` | A ID do Pool Red Hat assinatura Manager que contém seus direitos de OpenShift para nós de computação |  |  |
| `rhsmBrokerPoolId` | ID do Pool de Gerenciador de assinaturas Hat vermelho que contém seus direitos de OpenShift para mestres e infraestrutura de nós. Se você não tiver um pool diferente IDs, insira a mesma ID de pool de 'rhsmPoolId' |  |
| `sshPublicKey` | Copie a chave pública SSH aqui |  |  |
| `keyVaultSubscriptionId` | A ID da assinatura da assinatura que contém o Cofre de chaves |  |  |
| `keyVaultResourceGroup` | O nome do grupo de recursos que contém o Cofre de chaves |  |  |
| `keyVaultName` | O nome do Cofre de chaves que você criou |  |  |
| `enableAzure` | Habilitar provedor de nuvem do Azure | verdadeiro <br> falso | verdadeiro |
| `aadClientId` | Azure ID de cliente do Active Directory também conhecida como ID do aplicativo para entidade de serviço |  |  |
| `domainName` | Nome do nome de domínio personalizado para usar (se aplicável). Definido como "none" se não for implantar totalmente privada de cluster |  | Nenhum |
| `masterClusterDnsType` | Tipo de domínio para o console de web do OpenShift. 'default' usará o rótulo DNS do mestre de infraestrutura IP público. 'custom' permite que você defina seu próprio nome | padrão <br> personalizado | padrão |
| `masterClusterDns` | O nome DNS personalizado para usar para acessar o console do OpenShift web se você selecionou 'custom' para `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se definido como 'nipio' `routingSubDomain` usará nip.io.  Use 'custom' se você tiver seu próprio domínio que você deseja usar para roteamento | nipio <br> personalizado | nipio |
| `routingSubDomain` | O nome DNS de curinga que você deseja usar para se você selecionou 'custom' para o roteamento `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecione se deseja usar uma rede Virtual existente ou criar uma nova rede Virtual | Existente <br> novo | novo |
| `virtualNetworkResourceGroupName` | Nome do grupo de recursos para a nova rede Virtual se você selecionou 'new' para `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | O nome da nova rede Virtual para criar, se você selecionou 'new' para `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefixo de endereço da nova rede virtual |  | 10.0.0.0/14 |
| `masterSubnetName` | O nome da sub-rede mestre |  | mastersubnet |
| `masterSubnetPrefix` | CIDR usado para a sub-rede mestre - precisa ser um subconjunto do addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | O nome da infraestrutura subrede |  | infrasubnet |
| `infraSubnetPrefix` | CIDR usado para a infraestrutura subnet - precisa ser um subconjunto do addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | O nome da sub-rede de nó |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR usado para a sub-rede de nó - precisa ser um subconjunto do addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Referência completa a sub-rede existente para nós mestres. Não é necessária se a criação de nova rede virtual / sub-rede |  |  |
| `existingInfraSubnetReference` | Referência completa a sub-rede existente de infraestrutura nós. Não é necessária se a criação de nova rede virtual / sub-rede |  |  |
| `existingCnsSubnetReference` | Referência completa a sub-rede existente para nós CNS. Não é necessária se a criação de nova rede virtual / sub-rede |  |  |
| `existingNodeSubnetReference` | Referência completa a sub-rede existente para nós de computação. Não é necessária se a criação de nova rede virtual / sub-rede |  |  |
| `masterClusterType` | Especifique se o cluster usa nós mestres públicos ou privados. Se for escolhida a privada, os nós mestres não sejam expostos à Internet através de um IP público. Em vez disso, ele usará IP privado, especificado no `masterPrivateClusterIp` | público <br> privado | público |
| `masterPrivateClusterIp` | Se nós mestres privadas forem selecionadas, em seguida, um endereço IP privado deve ser especificado para uso pelo Balanceador de carga interno para nós mestres. Esse endereço IP estático deve ser dentro do bloco CIDR da sub-rede mestre e ainda não estiver em uso. Se nós mestres públicos são selecionados, esse valor não será usado, mas ainda deve ser especificado |  | 10.1.0.200 |
| `routerClusterType` | Especifique se o cluster usa privado ou público infraestrutura nós. Se private for escolhido, a infraestrutura, nós não sejam expostos à Internet através de um IP público. Em vez disso, ele usará IP privado, especificado no `routerPrivateClusterIp` | público <br> privado | público |
| `routerPrivateClusterIp` | Se privada infraestrutura nós forem selecionados, em seguida, um endereço IP privado deve ser especificado para usar, o balanceador de carga interno para infraestrutura nós. Esse endereço IP estático deve ser dentro do bloco CIDR da sub-rede mestre e ainda não estiver em uso. Se público infraestrutura nós forem selecionados, esse valor não será usado, mas ainda deve ser especificado |  | 10.2.0.200 |
| `routingCertType` | Usar o certificado personalizado para o certificado autoassinado do padrão ou de domínio de roteamento – siga as instruções em **certificados personalizados** seção | selfsigned <br> personalizado | selfsigned |
| `masterCertType` | Usar o certificado personalizado para o domínio do mestre ou o certificado autoassinado do padrão – siga as instruções em **certificados personalizados** seção | selfsigned <br> personalizado | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Implantar usando a CLI do Azure

> [!NOTE] 
> O comando a seguir requer a CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão CLI com o comando `az --version`. Para atualizar a versão da CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo a seguir implanta o cluster do OpenShift e todos os recursos relacionados em um grupo de recursos denominado openshiftrg, com um nome de implantação de myOpenShiftCluster. O modelo é referenciado diretamente do repositório GitHub e um arquivo de parâmetros local chamado azuredeploy.parameters.json é usado.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 60 minutos para ser concluída, com base no número total de nós implantados e as opções configuradas. O FQDN do DNS de bastiões e a URL do console do OpenShift imprime no terminal quando a implantação for concluída.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se você não deseja obstruir a linha de comando, aguardando a implantação seja concluída, adicione `--no-wait` como uma das opções para a implantação de grupo. A saída da implantação pode ser recuperada do portal do Azure na seção de implantação do grupo de recursos.

## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conectar-se ao console do OpenShift com o navegador usando o **URL do Console OpenShift**. Você também pode SSH para o host de bastiões. A seguir está um exemplo em que o nome de usuário do administrador é clusteradmin e o IP público de bastiões FQDN do DNS é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift no Azure](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform)

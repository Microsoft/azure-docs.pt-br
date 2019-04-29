---
title: Criar um cluster do Service Fabric do Linux no Azure | Microsoft Docs
description: Saiba como implantar um cluster do Service Fabric do Linux em uma rede virtual do Azure existente usando o CLI do Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 00d7e510fa43865f1427092f2f20b9847f1afa9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863777"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Implantar um cluster do Service Fabric do Linux em uma rede virtual do Azure

Neste artigo você aprenderá como implantar um cluster Linux do Service Fabric em uma [VNET (rede virtual) do Azure](../virtual-network/virtual-networks-overview.md) usando a CLI do Azure e um modelo. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos. Para criar um cluster do Windows usando o PowerShell, consulte [Create a secure Windows cluster on Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) (Criar um cluster seguro do Windows no Azure).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instalar a [CLI do Service Fabric](service-fabric-cli.md)
* Instale a [CLI do Azure](/cli/azure/install-azure-cli)
* Para saber os principais conceitos sobre clusters, leia [Visão geral dos clusters do Azure](service-fabric-azure-clusters-overview.md)
* [Planejar e preparar](service-fabric-cluster-azure-deployment-preparation.md) para uma implantação de cluster de produção.

Os procedimentos a seguir criam um cluster de sete nós do Service Fabric. Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Baixar e explorar o modelo

Baixe os seguintes arquivos do modelo do Resource Manager:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Esse modelo implanta um cluster seguro de sete máquinas virtuais e três tipos de nó em uma rede virtual.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [AzureDeploy.json][template] implanta diversos recursos, incluindo o seguinte.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, um cluster do Linux é implantado com as seguintes características:

* três tipos de nó
* cinco nós no tipo de nó primário (configurável nos parâmetros de modelo), um nó em cada um dos outros tipos de nó
* sistema operacional: Ubuntu 16.04 LTS (configurável nos parâmetros de modelo)
* certificado protegidos (configurável nos parâmetros de modelo)
* [Serviço DNS](service-fabric-dnsservice.md) está habilitado
* [Nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Bronze (configurável nos parâmetros de modelo)
* [Nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de Prata (configurável nos parâmetros de modelo)
* ponto de extremidade de conexão do cliente: 19000 (configurável nos parâmetros de modelo)
* ponto de extremidade de gateway HTTP: 19080 (configurável nos parâmetros de modelo)

### <a name="azure-load-balancer"></a>Azure Load Balancer

No recurso **Microsoft.Network/loadBalancers**, um balanceador de carga é configurado e as investigações e as regras são configuradas para as seguintes portas:

* ponto de extremidade de conexão do cliente: 19000
* ponto de extremidade de gateway HTTP: 19080
* porta do aplicativo: 80
* porta do aplicativo: 443

### <a name="virtual-network-and-subnet"></a>Rede virtual e sub-rede

Os nomes da rede virtual e da sub-rede são declarados nos parâmetros de modelo.  Espaços de endereço de rede virtual e sub-rede também são declarados nos parâmetros de modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* espaço de endereço da rede virtual: 10.0.0.0/16
* espaço de endereço de sub-rede do Service Fabric: 10.0.2.0/24

Se forem necessárias outras portas de aplicativo, você precisará ajustar o recurso Microsoft.Network/loadBalancers para permitir o tráfego de entrada.

## <a name="set-template-parameters"></a>Definir os parâmetros do modelo

O arquivo de parâmetros [AzureDeploy.Parameters][parameters] declara muitos valores usados para implantar o cluster e os recursos associados. Alguns dos parâmetros que você talvez precise modificar para sua implantação:

|Parâmetro|Valor de exemplo|Observações|
|---|---||
|adminUserName|vmadmin| Um nome de usuário de administrador para as VMs do cluster. |
|adminPassword|Password#1234| Uma senha de administrador para as VMs do cluster.|
|clusterName|mysfcluster123| Nome do cluster. |
|location|southcentralus| Localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que já foi carregado em um cofre de chaves, preencha o valor da impressão digital SHA1 do certificado. Por exemplo, "6190390162C988701DB5676EB81083EA608DCCF3". </p>|
|certificateUrlValue|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha a URL do certificado. Por exemplo, "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha o valor do cofre de origem. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implantar a rede virtual e o cluster

Em seguida, configure a topologia de rede e implante o cluster do Service Fabric. O modelo do Resource Manager [AzureDeploy.json][template] cria uma VNET (rede virtual) e uma sub-rede para o Service Fabric. O modelo também implanta um cluster com a segurança de certificado habilitada.  Para clusters de produção, use um certificado de uma autoridade de certificação (CA) como o certificado de cluster. Um certificado autoassinado pode ser usado para proteger clusters de teste.

O modelo deste artigo implanta um cluster que usa a impressão digital do certificado para identificar o certificado de cluster.  Dois certificados não podem ter a mesma impressão digital, o que dificulta o gerenciamento de certificados. Alternar um cluster implantado do uso de impressões digitais de certificado para o uso de nomes comuns do certificado simplifica muito o gerenciamento de certificados.  Para saber como atualizar o cluster para que ele use nomes comuns de certificado para o gerenciamento de certificados, leia [Alterar o cluster para o gerenciamento de nomes comuns de certificado](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Criar um cluster usando um certificado existente

O script a seguir usa o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) e o modelo para implantar um novo cluster protegido por um certificado existente. O comando também cria um novo cofre de chaves no Azure e carrega o certificado.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Criar um cluster usando um novo certificado autoassinado

O script a seguir usa o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) e um modelo para implantar um novo cluster no Azure. O comando também cria um cofre de chaves no Azure, adiciona um certificado autoassinado a ele e baixa o arquivo de certificado localmente.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Conectar-se ao cluster seguro

Conecte-se ao cluster usando o comando `sfctl cluster select` da CLI do Service Fabric com a sua chave.  Observação: use apenas a opção **--no-verify** para um certificado autoassinado.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Verifique se você está conectado e se o cluster está íntegro usando o comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for imediatamente para o próximo artigo, [exclua o cluster](service-fabric-cluster-delete.md) para evitar a cobrança de encargos.

## <a name="next-steps"></a>Próximas etapas

Saiba como [dimensionar um cluster.](service-fabric-tutorial-scale-cluster.md)

O modelo deste artigo implanta um cluster que usa a impressão digital do certificado para identificar o certificado de cluster.  Dois certificados não podem ter a mesma impressão digital, o que dificulta o gerenciamento de certificados. Alternar um cluster implantado do uso de impressões digitais de certificado para o uso de nomes comuns do certificado simplifica muito o gerenciamento de certificados.  Para saber como atualizar o cluster para que ele use nomes comuns de certificado para o gerenciamento de certificados, leia [Alterar o cluster para o gerenciamento de nomes comuns de certificado](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json

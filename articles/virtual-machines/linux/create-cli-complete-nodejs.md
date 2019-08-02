---
title: Crie um ambiente Linux completo com o CLI do Azure Classic | Microsoft Docs
description: Crie armazenamento, uma VM Linux, uma rede virtual e sub-rede, um balanceador de carga, uma NIC, um IP público e um grupo de segurança de rede, tudo a partir do zero usando a CLI Clássica do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 5fbcbc63b3038151a7d45a70ce88eb7ca9829fe5
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668022"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Crie um ambiente Linux completo com a CLI Clássica do Azure
Neste artigo, vamos criar uma rede simples com um balanceador de carga e com um par de VMs úteis para desenvolvimento e computação simples. Percorreremos o processo, comando por comando, até que você tenha duas VMs do Linux funcionais e seguras às quais possa se conectar de qualquer lugar na Internet. Em seguida, você poderá passar para redes e ambientes mais complexos.

Durante o processo, você entenderá sobre a hierarquia de dependência que o modelo de implantação do Gerenciador de Recursos lhe oferece e todos os recursos que proporciona. Após ver como o sistema é criado, você poderá recriá-lo muito mais rapidamente usando [modelos do Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Além disso, após aprender como as partes de seu ambiente se encaixam, fica mais fácil criar modelos para automatizá-las.

O ambiente contém:

* Duas VMs dentro de um conjunto de disponibilidade.
* Um balanceador de carga com uma regra de balanceamento de carga na porta 80.
* Regras de NSG (grupo de segurança de rede) para proteger sua VM contra tráfego indesejado.

Para criar esse ambiente personalizado, você precisa da [Azure Classic CLI ](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mais recente no modo Gerenciador de recursos (`azure config mode arm`). Você também precisará de uma ferramenta de análise de JSON. Este exemplo usa [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure Classic](#quick-commands) - nossa CLI para os modelos de implantação de gerenciamento clássico e de recursos (este artigo)
- [CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha a base de dados de comandos para carregar uma VM no Azure. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, começando [aqui](#detailed-walkthrough).

Certifique-se de ter o [CLI Clássico do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) conectado e usando o modo Gerenciador de Recursos:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount` e `myVM`.

Crie o grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Verificar o grupo de recursos usando o analisador JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Crie a conta de armazenamento. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`. (O nome da conta de armazenamento deve ser exclusivo; portanto, forneça seu próprio nome exclusivo.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Verificar a conta de armazenamento usando o analisador JSON:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Crie a rede virtual. O exemplo a seguir cria uma rede virtual chamada `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Crie uma sub-rede. O seguinte exemplo cria uma sub-rede chamada `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verificar a rede virtual e a sub-rede usando o analisador JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Crie um IP público. O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. (O nome DNS deve ser exclusivo; portanto, forneça seu próprio nome exclusivo.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Crie o balanceador de carga. O exemplo a seguir cria um balanceador de carga chamado `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Crie um pool de IPs de front-end para o balanceador de carga e associe o IP público. O exemplo a seguir cria um pool de IPs de front-end chamado `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Crie o pool de IPs de back-end para o balanceador de carga. O exemplo a seguir cria um pool de IPs de back-end chamado `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Crie as regras de conversão de endereço de rede (NAT) de entrada de SSH para o balanceador de carga. O exemplo a seguir cria duas regras de balanceador de carga, `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Crie as regras de NAT de entrada da Web para o balanceador de carga. O seguinte exemplo cria uma regra do balanceador de carga chamada `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Crie a investigação de integridade do balanceador de carga. O exemplo a seguir cria uma investigação de TCP chamada `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verificar o balanceador de carga, os pools de IPs e as regras NAT usando o analisador JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Crie a primeira NIC (placa de adaptador de rede). Substitua as seções `#####-###-###` pela sua própria ID de assinatura do Azure. Sua ID de assinatura é anotada na saída de **jq** quando você examina os recursos que estão sendo criados. Você também pode exibir sua ID de assinatura com `azure account list`.

O exemplo a seguir cria uma NIC chamada `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Crie a segunda NIC. O exemplo a seguir cria uma NIC chamada `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verifique as NICs usando o analisador JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Crie o grupo de segurança de rede. O seguinte exemplo cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Adicione duas regras de entrada para o grupo de segurança de rede. O exemplo a seguir cria duas regras, `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verifique o grupo de segurança de rede e as regras de entrada usando o analisador JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Associe o grupo de segurança de rede às duas NICs:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Crie o conjunto de disponibilidade. O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Crie a primeira VM do Linux. O exemplo a seguir cria uma VM chamada `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Crie a segunda VM do Linux. O exemplo a seguir cria uma VM chamada `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Usar o analisador JSON para verificar tudo o que foi criado:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporte seu novo ambiente para um modelo para recriar novas instâncias rapidamente:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado
As etapas detalhadas a seguir explicam o que cada comando faz enquanto você cria seu ambiente. Esses conceitos são úteis durante a criação de seus próprios ambientes personalizados para desenvolvimento ou produção.

Certifique-se de ter o [CLI Clássico do Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) conectado e usando o modo Gerenciador de Recursos:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount` e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Criar grupos de recursos e escolher locais de implantação
Os grupos de recursos do Azure são entidades de implantação lógica que contêm metadados e informações de configuração para habilitar o gerenciamento lógico de implantações de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Saída:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Você precisa de contas de armazenamento para seus discos de VM e quaisquer discos de dados adicionais que deseje adicionar. Você cria contas de armazenamento quase que imediatamente depois de criar grupos de recursos.

Aqui, usamos o comando `azure storage account create` , passando a localização da conta, o grupo de recursos que a controla e o tipo de suporte de armazenamento que você deseja. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Saída:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Para examinar nosso grupo de recursos usando o comando `azure group show`, vamos usar a ferramenta [jq](https://stedolan.github.io/jq/) junto com a opção `--json` da CLI do Azure. (Você pode usar **jsawk** ou qualquer biblioteca de linguagem que preferir para analisar o JSON.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Saída:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para investigar a conta de armazenamento usando a CLI, primeiro, você precisa definir os nomes e as chaves da conta. Substitua o nome da conta de armazenamento no exemplo a seguir pelo nome que você escolher:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Em seguida, você poderá exibir as informações de armazenamento facilmente:

```azurecli
azure storage container list
```

Saída:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Criar a rede virtual e a sub-rede
Em seguida, você precisará criar uma rede virtual em execução no Azure e uma sub-rede na qual possa criar suas VMs. O exemplo a seguir cria uma rede virtual chamada `myVnet` com o prefixo de endereço de `192.168.0.0/16`:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Saída:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Novamente, vamos usar a opção --json de `azure group show` e `jq` para ver como estamos criando nossos recursos. Agora, temos um recurso `storageAccounts` e um recurso `virtualNetworks`.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Saída:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Agora vamos criar uma sub-rede na rede virtual `myVnet` na qual as VMs são implantadas. Usamos o comando `azure network vnet subnet create`, em conjunto com os recursos que já criamos: o grupo de recursos `myResourceGroup` e a rede virtual `myVnet`. No exemplo a seguir, adicionamos a sub-rede chamada `mySubnet` com o prefixo de endereço de sub-rede `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Saída:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Como a sub-rede está logicamente dentro da rede virtual, vamos buscar as informações da sub-rede com um comando ligeiramente diferente. O comando que usaremos é `azure network vnet show`, mas continuaremos examinando a saída JSON usando `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Saída:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Agora, vamos criar o PIP (endereço IP público) que atribuiremos ao seu balanceador de carga. Ele permite que você conecte suas VMs da Internet usando o comando `azure network public-ip create` . Como o endereço padrão é dinâmico, criaremos uma entrada DNS nomeada no domínio **cloudapp.azure.com** usando a opção `--domain-name-label`. O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. Como o nome DNS deve ser exclusivo, forneça seu próprio nome DNS exclusivo:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Saída:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

O endereço IP público também é um recurso de nível superior, então você pode vê-lo com `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Saída:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Você pode investigar mais detalhes do recurso, incluindo o FQDN (nome de domínio totalmente qualificado) do subdomínio usando o comando completo `azure network public-ip show` . O recurso de endereço IP público foi alocado logicamente, mas ainda não há um endereço específico atribuído. Para obter um endereço IP, você precisará de um balanceador de carga, que nós ainda não criamos.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Saída:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Criar um balanceador de carga e pools de IPs
Quando você cria um balanceador de carga, ele permite que você distribua o tráfego entre várias VMs. Ele também fornece redundância para seu aplicativo ao executar várias VMs que respondem às solicitações dos usuários em caso de manutenção ou cargas pesadas. O exemplo a seguir cria um balanceador de carga chamado `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Saída:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Nosso balanceador de carga está bem vazio, então vamos criar alguns pools de IPs. Queremos criar dois pools de IPs para nosso balanceador de carga: um para o front-end e outro para o back-end. O pool de IPs de front-end fica visível publicamente. Também é o local ao qual atribuiremos o PIP que criamos anteriormente. Em seguida, usaremos o pool de back-end como uma localização à qual nossas VMs se conectarão. Dessa forma, o tráfego pode fluir por meio do balanceador de carga para as VMs.

Primeiro, vamos criar nosso pool de IPs de front-end. O exemplo a seguir cria um pool de front-end chamado `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Saída:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Observe como usamos a opção `--public-ip-name` para passar o `myPublicIP` que criamos anteriormente. A atribuição do endereço IP público ao balanceador de carga permite que você acesse suas VMs pela Internet.

Em seguida, vamos criar nosso segundo pool de IPs, desta vez, para o tráfego de back-end. O exemplo a seguir cria um pool de back-end chamado `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Saída:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos ver como está nosso balanceador de carga usando `azure network lb show` e examinando a saída JSON:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Saída:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Criar regras NAT do balanceador de carga
Para fazer com que o tráfego flua pelo balanceador de carga, precisamos criar regras de conversão de endereço de rede (NAT) que especifiquem as ações de entrada ou de saída. Você pode especificar o protocolo a ser usado e mapear portas externas para portas internas conforme desejado. Para nosso ambiente, vamos criar algumas regras que permitem que o SSH passe pelo balanceador de carga e chegue às VMs. Vamos configurar as portas TCP 4222 e 4223 para direcionar para a porta TCP 22 em nossas VMs (que criaremos posteriormente). O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH1` para mapear a porta TCP 4222 para a porta 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Saída:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita o procedimento para sua segunda regra de NAT para o SSH. O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH2` para mapear a porta TCP 4223 para a porta 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Também vamos aproveitar e criar uma regra de NAT para a porta TCP 80 para tráfego da Web, vinculando a regra aos nossos pools de IPs. Se vincularmos a regra a um pool de IPs, em vez de vincular a regra às nossas VMs individualmente, poderemos adicionar ou remover VMs do pool de IPs. O balanceador de carga ajusta automaticamente o fluxo de tráfego. O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleWeb` para mapear a porta TCP 80 para a porta 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Saída:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Criar uma investigação de integridade do balanceador de carga
Uma investigação de integridade verifica periodicamente as máquinas virtuais que estão por trás de nosso balanceador de carga para garantir que elas estejam em operação e estejam respondendo às solicitações da forma definida. Se não estiverem, elas serão removidas da operação para garantir que os usuários não sejam direcionados a elas. Você pode definir verificações personalizadas para a investigação de integridade, bem como valores de tempo limite e intervalos. Para obter mais informações sobre investigações de integridade, consulte [Investigações do Balanceador de Carga](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). O exemplo a seguir cria uma integridade de TCP investigada chamada `myHealthProbe`:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Saída:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Aqui, especificamos um intervalo de 15 segundos para nossas verificações de integridade. Podemos perder um máximo de quatro investigações (um minuto) antes que o balanceador de carga considere que o host não está mais funcionando.

## <a name="verify-the-load-balancer"></a>Verificar o balanceador de carga
Agora, a configuração do balanceador de carga está concluída. Estas são as etapas que você seguiu:

1. Você criou um balanceador de carga.
2. Você criou um pool de IPs de front-end e atribuiu um IP público a ele.
3. Você criou um pool de IPs de back-end ao qual as VMs podem se conectar.
4. Você criou regras NAT que permitem o SSH para as VMs para gerenciamento, junto com uma regra que permite a porta TCP 80 para nosso aplicativo Web.
5. Você adicionou uma investigação de integridade para verificar as VMs periodicamente. Essa investigação de integridade que os usuários não tentem acessar uma VM que não está mais funcionando ou fornecendo conteúdo.

Vamos ver como seu balanceador de carga está agora:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Saída:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Criar uma NIC para usar com a VM Linux
As NICs estão disponíveis por meio de programação porque você pode aplicar regras ao seu uso. Você também pode ter mais de uma. No comando `azure network nic create` a seguir, você vincula nossa NIC ao pool de IPs de back-end de carga e a associa a nossa regra de NAT para permitir o tráfego SSH.

Substitua as seções `#####-###-###` pela sua própria ID de assinatura do Azure. Sua ID de assinatura é anotada na saída de `jq` quando você examina os recursos que estão sendo criados. Você também pode exibir sua ID de assinatura com `azure account list`.

O exemplo a seguir cria uma NIC chamada `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Saída:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Você pode ver os detalhes examinando o recurso diretamente. Examine os recursos usando o comando `azure network nic show` :

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Saída:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Agora, vamos criar a segunda NIC, vinculando-a novamente ao nosso pool de IPs de back-end. Desta vez, a segunda regra NAT permite o tráfego SSH. O exemplo a seguir cria uma NIC chamada `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e suas regras
Agora, criamos um grupo de segurança de rede e as regras de entrada que regem o acesso à NIC. Um grupo de segurança de rede pode ser aplicado a uma NIC ou sub-rede. Defina regras para controlar o fluxo de tráfego dentro e fora de suas VMs. O seguinte exemplo cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos adicionar a regra de entrada para o NSG para permitir conexões de entrada na porta 22 (para dar suporte ao SSH). O seguinte exemplo cria uma regra chamada `myNetworkSecurityGroupRuleSSH` para permitir o TCP na porta 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Agora, vamos adicionar a regra de entrada para o NSG para permitir conexões de entrada na porta 80 (para dar suporte ao tráfego da Web). O seguinte exemplo cria uma regra chamada `myNetworkSecurityGroupRuleHTTP` para permitir o TCP na porta 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> A regra de entrada é um filtro para conexões de rede de entrada. Neste exemplo, associaremos o NSG à NIC virtual das VMs, o que significa que qualquer solicitação para a porta 22 será passada para a NIC na nossa VM. Essa regra de entrada é sobre uma conexão de rede e não sobre um ponto de extremidade, que seria o caso em implantações clássicas. Para abrir uma porta, você deve deixar `--source-port-range` definido como “\*” (o valor padrão) para aceitar solicitações de entrada de **qualquer** porta de solicitação. Normalmente, as portas são dinâmicas.
>
>

## <a name="bind-to-the-nic"></a>Associar à NIC
Associe o NSG às NICs. Precisamos conectar nossas NICs a nosso grupo de segurança de rede. Execute os dois comandos, para conectar os nossas duas NICs:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Conjuntos de disponibilidade ajudam a difundir suas VMs em domínios de falha e domínios de atualização. Vamos criar um conjunto de disponibilidade para suas VMs. O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Os domínios de falha definem um agrupamento de máquinas virtuais que compartilham uma mesma fonte de alimentação e um mesmo comutador de rede. Por padrão, as máquinas virtuais que são configuradas dentro do seu conjunto de disponibilidade são separadas entre até três domínios de falha. A ideia é que um problema de hardware em um desses domínios de falha não afete todas as VMs que estiverem executando seu aplicativo. O Azure distribui automaticamente as VMs entre os domínios de falha ao colocá-las em um conjunto de disponibilidade.

Domínios de atualização indicam grupos de máquinas virtuais e o hardware físico subjacente que podem ser reinicializados ao mesmo tempo. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas uma atualização será reinicializada por vez. Mais uma vez, o Azure distribui automaticamente as VMs entre os domínios de atualização ao colocá-las em um site de disponibilidade.

Leia mais sobre como [gerenciar a disponibilidade de VMs](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Criar as VMs Linux
Você criou os recursos de armazenamento e rede para dar suporte a VMs que podem ser acessadas pela Internet. Agora, vamos criar essas VMs e protegê-las com uma chave SSH que não tem senha. Nesse caso, vamos criar uma VM do Ubuntu com base no LTS mais recente. Localizaremos as informações dessa imagem usando `azure vm image list`, conforme descrito em [localização de imagens de VM do Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Selecionamos uma imagem usando o comando `azure vm image list westeurope canonical | grep LTS`. Neste caso, usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para o último campo, vamos passar `latest` para que no futuro sempre tenhamos o build mais recente. (A cadeia de caracteres usada é `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

A próxima etapa é familiar para qualquer pessoa que já tenha criado um par de chaves privada e pública rsa ssh no Linux ou Mac usando **ssh-keygen -t rsa -b 2048**. Se não tiver pares de chave de certificado no seu diretório `~/.ssh` , você poderá criá-los:

* Automaticamente, usando a opção `azure vm create --generate-ssh-keys` .
* Manualmente, usando [as instruções para criá-las você mesmo](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Como alternativa, você pode usar o método `--admin-password` para autenticar suas conexões SSH após a criação da VM. Esse método normalmente é menos seguro.

Criamos a VM reunindo todos os nossos recursos e informações com o comando `azure vm create` :

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Saída:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Você pode se conectar à sua VM imediatamente usando suas chaves SSH padrão. Certifique-se de especificar a porta apropriada, uma vez que estamos passando pelo balanceador de carga. (Para nossa primeira VM, configuramos a regra NAT para encaminhar a porta 4222 para nossa VM.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Saída:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Prossiga e crie sua segunda VM da mesma maneira:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

E agora você pode usar o comando `azure vm show myResourceGroup myVM1` para examinar o que criou. Neste ponto, você está executando suas VMs Ubuntu atrás de um balanceador de carga no Azure, em que só pode fazer logon com seu par de chaves SSH (porque as senhas estão desabilitadas). Você pode instalar nginx ou httpd e implantar um aplicativo Web para ver o fluxo do tráfego por meio do balanceador de carga para ambas as VMs.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Saída:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportar o ambiente como um modelo
Agora que criou esse ambiente, e se você quiser criar um ambiente de desenvolvimento adicional usando os mesmos parâmetros ou um ambiente de produção corresponde? O Gerenciador de Recursos usa modelos JSON que definem todos os parâmetros para seu ambiente. Crie ambientes inteiros fazendo referência a esse modelo JSON. Você pode [criar modelos JSON manualmente](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exportar um ambiente existente para criar o modelo JSON para você:

```azurecli
azure group export --name myResourceGroup
```

Esse comando cria o arquivo `myResourceGroup.json` no diretório de trabalho atual. Ao criar um ambiente com base nesse modelo, você receberá uma solicitação para fornecer todos os nomes de recursos, incluindo os nomes do balanceador de carga, das interfaces de rede ou VMs. Você pode preencher esses nomes em seu arquivo de modelo adicionando o parâmetro `-p` ou `--includeParameterDefaultValue` ao comando `azure group export` que foi mostrado anteriormente. Edite seu modelo JSON para especificar os nomes dos recursos, ou [crie um arquivo parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifica os nomes dos recursos.

Para criar um ambiente a partir de seu modelo:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Pode ser útil ler [mais detalhes sobre a implantações de modelos](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Saiba como atualizar ambientes gradativamente, usar o arquivo de parâmetros e acessar os modelos de uma única localização de armazenamento.

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para começar a trabalhar com vários componentes de rede e VMs. Você pode usar esse ambiente de exemplo para criar seu aplicativo usando os principais componentes introduzidos aqui.

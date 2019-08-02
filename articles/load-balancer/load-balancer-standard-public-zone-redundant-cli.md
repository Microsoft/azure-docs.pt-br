---
title: Balancear a carga de VMs com redundância de zona usando a CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer público com front-end com redundância de zona usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: 6a22ac9a2727c537d98e692e67076637fe8cc457
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274322"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Balancear carga de VMs em todas as zonas de disponibilidade usando a CLI do Azure

Este artigo orienta a criação de um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com um front-end com redundância de zona para obter redundância de zona sem depender de vários registros DNS. Um único endereço IP de front-end tem redundância de zona automaticamente.  Usando um front-end com redundância de zona para o balanceador de carga, com um único endereço IP, será possível acessar qualquer VM em uma rede virtual em uma região que esteja em todas as Zonas de Disponibilidade. Use zonas de disponibilidade para proteger seus aplicativos e dados de uma improvável falha ou perda de um datacenter inteiro.

Para obter mais informações sobre o uso de Zonas de Disponibilidade com o Load Balancer Standard, consulte [Zonas de disponibilidade e Load Balancer Standard](load-balancer-standard-availability-zones.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.17 ou superior.  Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> O suporte para Zonas de Disponibilidade está disponível para selecionar recursos e regiões do Azure e famílias de tamanho de VM. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroupSLB* no local *westeurope*:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Criar um Standard IP público com redundância de zona
Para acessar seu aplicativo na Internet, você precisará de um endereço IP público para o balanceador de carga. Um front-end com redundância de zona é atendido por todas as zonas de disponibilidade em uma região simultaneamente. Crie um endereço IP público com redundância de zona com [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). Quando você cria um endereço IP público Standard, ele é com redundância de zona por padrão.

O exemplo a seguir cria um endereço IP público com redundância de zona nomeado *myPublicIP* no grupo de recursos *myResourceGroupLoadBalancer*.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Criar o Azure Standard Load Balancer
Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:
- um pool de IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
- um pool de IP de back-end no qual o pool de front-end envia o tráfego de rede com a carga balanceada.
- uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
- uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga
Crie um balanceador de carga Standard com [az network lb create](/cli/azure/network/lb#az-network-lb-create). O exemplo a seguir cria um balanceador de carga nomeado *myLoadBalancer* e atribui o endereço *myPublicIP* para a configuração de IP de front-end.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Criar investigação de integridade na porta 80

Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode enviar o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação determinarem sua integridade. Crie uma investigação de integridade com az network lb probe create para monitorar a integridade das máquinas virtuais. Para criar uma investigação de integridade TCP, consulte [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). O exemplo a seguir cria uma investigação de integridade chamada *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Criar regra do balanceador de carga para a porta 80
Uma regra de balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myLoadBalancerRuleWeb* com [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) para escutar a porta 80 no pool de front-end *myFrontEndPool* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Configurar rede virtual
Antes de implantar algumas VMs e poder testar o balanceador de carga, crie os recursos de suporte de rede virtual.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual nomeada *myVnet* com uma sub-rede nomeada *mySubnet* no myResourceGroup usando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie o grupo de segurança de rede nomeado *myNetworkSecurityGroup* para definir conexões de entrada para a rede virtual com [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Crie uma regra de grupo de segurança de rede nomeada *myNetworkSecurityGroupRule* para a porta 80 com [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>Criar NICs
Crie três NICs virtuais com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe-as ao endereço IP Público e ao grupo de segurança de rede. O exemplo a seguir cria seis NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir). Você pode criar VMs e NICs virtuais adicionais a qualquer momento e adicioná-las ao balanceador de carga:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Criar servidores de back-end
Neste exemplo, você cria três máquinas virtuais localizadas na zona 1, na zona 2 e na zona 3 para serem utilizada como servidores de back-end para o balanceador de carga. Você também instala o NGINX nas máquinas virtuais para verificar se o balanceador de carga foi criado com êxito.

### <a name="create-cloud-init-config"></a>Criar configuração de inicialização de nuvem

Você pode usar um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js “Olá, Mundo” em uma máquina virtual Linux. No shell atual, crie um arquivo chamado cloud-init.txt e copie e cole a seguinte configuração no shell. Certifique-se de copiar todo o arquivo cloud-init corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>Criar as máquinas virtuais zonais
Crie as VMs com [az vm create](/cli/azure/vm#az-vm-create) na zona 1, zona 2 e zona 3. O exemplo a seguir cria uma VM em cada zona e gera chaves SSH, se ainda não houver:

Criar uma máquina virtual em cada zona (zona 1, zona 2 e zona 3) do local *westeurope*.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Obtenha o endereço IP público do balanceador de carga usando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Você pode inserir o endereço IP público em um navegador da Web. Lembre-se - leva alguns minutos para a VMs estar pronta antes do balanceador de carga distribuir tráfego a eles. O aplicativo é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego, como no exemplo a seguir:

![Executar o aplicativo Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Para ver o balanceador de carga distribuir o tráfego nas VMs em todas as três zonas de disponibilidade executando o aplicativo, você pode interromper uma VM em uma determinada zona e atualizar o navegador.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Load Balancer Standard](./load-balancer-standard-overview.md)




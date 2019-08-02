---
title: Criar um Standard Load Balancer com front-end de zona usando a CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer público com front-end de endereço IP público de zona usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 7da41456a4f4bb88d402d27b42b31f6d4adfa7f6
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274309"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Criar um Standard Load Balancer com front-end de zona usando a CLI do Azure

Este artigo segue as etapas para a criação de um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com um front-end de zona usando um endereço IP Público Standard. Nesse cenário, é possível especificar uma zona particular para suas instâncias de front-end e de back-end, a fim de alinhar o caminho de dados e os recursos com uma zona específica.

Para obter mais informações sobre o uso de Zonas de Disponibilidade com o Load Balancer Standard, consulte [Zonas de disponibilidade e Load Balancer Standard](load-balancer-standard-availability-zones.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, certifique-se de que você instalou a versão mais recente da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e entrou em uma conta do Azure com [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
>  O suporte para Zonas de Disponibilidade está disponível para selecionar recursos e regiões do Azure e famílias de tamanho de VM. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupLB* no local *westeurope*:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Criar um padrão de IP público zonal
Para acessar seu aplicativo na Internet, você precisará de um endereço IP público para o balanceador de carga. Um endereço IP Público criado em uma zona específica existe sempre apenas nessa zona. Não é possível alterar a zona de um endereço IP público.

Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). O exemplo a seguir cria um endereço IP público zonal chamado *myPublicIP* no grupo de recursos *myResourceGroupLoadBalancer* na zona 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-standard-load-balancer"></a>Criar o Azure Standard Load Balancer
Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:
- um pool de IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
- um pool de IP de back-end no qual o pool de front-end envia o tráfego de rede com a carga balanceada.
- uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
- uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga
Crie um Standard Load Balancer com [az network lb create](/cli/azure/network/lb#az-network-lb-create). O exemplo a seguir cria um balanceador de carga nomeado *myLoadBalancer* e atribui o endereço *myPublicIP* para a configuração de IP de front-end.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Criar investigação de integridade na porta 80

Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode enviar o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação determinarem sua integridade. Crie uma investigação de integridade com az network lb probe create para monitorar a integridade das máquinas virtuais. Para criar uma investigação de integridade TCP, consulte [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). O exemplo a seguir cria uma investigação de integridade chamada *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Criar regra do balanceador de carga para a porta 80
Uma regra de balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myLoadBalancerRuleWeb* com [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) para escutar a porta 80 no pool de front-end *myFrontEndPool* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Configurar rede virtual
Antes de implantar algumas VMs e poder testar o balanceador de carga, crie os recursos de suporte de rede virtual.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual nomeada *myVnet* com uma sub-rede nomeada *mySubnet* no myResourceGroup usando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie o grupo de segurança de rede nomeado *myNetworkSecurityGroup* para definir conexões de entrada para a rede virtual com [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Crie uma regra de grupo de segurança de rede nomeada *myNetworkSecurityGroupRule* para a porta 80 com [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
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
Crie três NICs virtuais com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe-as ao endereço IP Público e ao grupo de segurança de rede. O exemplo a seguir cria três NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir). Você pode criar VMs e NICs virtuais adicionais a qualquer momento e adicioná-las ao balanceador de carga:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Criar servidores de back-end
Neste exemplo, você cria três máquinas virtuais localizadas na zona 1 para serem utilizada como servidores de back-end para o balanceador de carga. Você também instala o NGINX nas máquinas virtuais para verificar se o balanceador de carga foi criado com êxito.

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
Crie as VM com [az vm create](/cli/azure/vm#az-vm-create). O exemplo a seguir cria três VMs na zona 1 e gera chaves SSH, se elas ainda não existirem:

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
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

![Executar o aplicativo Node.js](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Para ver o balanceador de carga distribuir tráfego para as VMs na zona 1 que executam seu aplicativo, você poderá forçar a atualização de seu navegador da Web.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Load Balancer Standard](./load-balancer-standard-overview.md).




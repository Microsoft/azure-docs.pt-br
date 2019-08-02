---
title: Criar um Basic Load Balancer interno – CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga interno usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2018
ms.author: allensu
ms.openlocfilehash: e38cc4e6da574e0c3be490cf5d9cd929624343e5
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275444"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Criar um balanceador de carga interno para balancear a carga de VMs usando a CLI do Azure

Este artigo mostra como criar um balanceador de carga interno para balancear cargas de VMs. Para testar o balanceador de carga, implante duas máquinas virtuais (VMs) executando o servidor do Ubuntu a fim de balancear a carga de um aplicativo Web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroupILB* na localização *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual chamada *myVnet* com uma sub-rede chamada *mySubnet* no *myResourceGroup* usando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:
  - uma configuração de IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  - um pool de IP de back-end no qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  - uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  - uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Crie um Load Balancer interno com [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) chamado **myLoadBalancer** , que inclui uma configuração de IP de front-end chamada **myfrontend**, um pool de back-end chamado **MYBACKENDPOOL** que está associado a um endereço IP privado * 10.0.0.7.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode receber o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação determinarem sua integridade. Crie uma investigação de integridade com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorar a integridade das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myHTTPRule* com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para escutar a porta 80 no pool de front-end *myFrontEnd* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>Criar servidores para o pool de endereços de back-end

Antes de implantar algumas VMs e poder testar o balanceador de carga, crie os recursos de suporte de rede virtual.

### <a name="create-nics"></a>Criar NICs

Crie duas interfaces de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe-as com o endereço IP privado. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você criará duas máquinas virtuais a serem usadas como servidores de back-end para o balanceador de carga. Para verificar se o balanceador de carga foi criado com êxito, instale também o NGINX nas máquinas virtuais.

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Crie um conjunto de disponibilidade com [az vm availabilityset create](/cli/azure/network/nic)

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

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
 
Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az-vm-create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
Pode demorar alguns minutos para as VMs serem implantadas.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Criar uma VM para testar o balanceador de carga

Para testar o balanceador de carga, crie uma máquina virtual, *myVMTest*e associe-a a *myNic3*.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>Testar o balanceador de carga interno

Para testar o balanceador de carga, você deve primeiro obter o endereço IP privado do balanceador de carga. Em seguida, entre na máquina virtual myVMTest e digite o endereço IP privado na barra de endereços do seu navegador da Web.

Para obter o endereço IP do balanceador de carga, use [az network lb show](/cli/azure/network/public-ip). Copie o endereço IP privado e cole-o na barra de endereço de um navegador da Web da máquina virtual - *myVMTest*.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
``` 
![Testar o balanceador de carga](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um Load Balancer Básico, anexou VMs a ele, configurou a regra de tráfego do balanceador de carga, a investigação de integridade e testou o balanceador de carga. Para saber mais sobre os balanceadores de carga e seus recursos associados, continue para os artigos de instrução.

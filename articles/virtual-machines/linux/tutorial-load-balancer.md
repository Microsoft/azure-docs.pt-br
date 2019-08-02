---
title: Tutorial – Balancear a carga de máquinas virtuais do Linux no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar a CLI do Azure para criar um balanceador de carga para um aplicativo altamente disponível e seguro em três Máquinas Virtuais do Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b813a197266db37bde961e079f5d5d5e92353db1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708523"
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli"></a>Tutorial: balancear carga de máquinas virtuais do Linux no Azure para criar um aplicativo altamente disponível com a CLI do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade, distribuindo as solicitações de entrada em várias máquinas virtuais. Neste tutorial, você aprenderá sobre os diferentes componentes do balanceador de carga do Azure que distribuem o tráfego e fornecem alta disponibilidade. Você aprenderá como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma investigação de integridade do balanceador de carga
> * Criar regras de tráfego para o balanceador de carga
> * Usar cloud-init para criar um aplicativo básico do Node.js
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Exibir um balanceador de carga em ação
> * Adicionar e remover as VMs de um balanceador de carga

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="azure-load-balancer-overview"></a>Visão geral do Balanceador de Carga do Azure
Um Azure Load Balancer é um balanceador de carga de Camada 4 (TCP, UDP) que fornece alta disponibilidade distribuindo o tráfego de entrada entre VMs íntegros. Uma investigação de integridade do balanceador de carga monitora uma determinada porta em cada VM e distribui o tráfego somente para uma VM operacional.

Você define uma configuração de IP de front-end que contém um ou mais endereços IP públicos. Essa configuração de IP de front-end permite que seu balanceador de carga e os aplicativos estejam acessíveis pela Internet. 

As máquinas virtuais conectam-se a um balanceador de carga usando a placa de interface de rede virtual (NIC). Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga.

Para controlar o fluxo do tráfego, você precisará definir regras do balanceador de carga para portas específicas e protocolos que mapeiam para suas VMs.

Se você seguiu o tutorial anterior para [criar um conjunto de escala de máquina virtual](tutorial-create-vmss.md), um balanceador de carga foi criado para você. Todos esses componentes foram configurados como parte do conjunto de dimensionamento.


## <a name="create-azure-load-balancer"></a>Criar o balanceador de carga do Azure
Esta seção fornece detalhes sobre como criar e configurar cada componente do balanceador de carga. Antes de criar seu balanceador de carga, crie um grupo de recursos com o [az group create](/cli/azure/group). O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupLoadBalancer* no local *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Para acessar seu aplicativo na Internet, você precisará de um endereço IP público para o balanceador de carga. Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo a seguir cria um endereço IP público chamado *myPublicIP* no grupo de recursos *myResourceGroupLoadBalancer*:

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie um balanceador de carga com [az network lb create](/cli/azure/network/lb). O exemplo a seguir cria um balanceador de carga chamado *myLoadBalancer* e atribui o endereço *myPublicIP* para a configuração de IP front-end:

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade
Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Por padrão, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Crie uma investigação de integridade com base em um protocolo ou página de verificação de integridade específica ao seu aplicativo. 

O exemplo a seguir cria uma investigação de TCP. Você também pode criar investigações de HTTP personalizadas para obter verificações de integridade mais refinadas. Ao usar uma investigação de HTTP personalizada, você deverá criar a página de verificação de integridade, como *healthcheck.js*. A investigação deve retornar a reposta **HTTP 200 OK** para o balanceador de carga a fim de manter o host em rotação.

Para criar uma investigação de integridade TCP, consulte [az network lb probe create](/cli/azure/network/lb/probe). O exemplo a seguir cria uma investigação de integridade chamada *myHealthProbe*:

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Para ter certeza de que apenas VMs íntegras recebem tráfego, defina também a investigação de integridade a ser usada.

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo a seguir cria uma regra chamada *myLoadBalancerRule*, usa a investigação de integridade *myHealthProbe* e equilibra o tráfego na porta *80*:

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Configurar rede virtual
Antes de implantar algumas VMs e poder testar o balanceador, crie os recursos de suporte de rede virtual. Para saber mais sobre redes virtuais, veja o tutorial [Gerenciar Redes Virtuais do Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Criar recursos da rede
Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede chamada *mySubnet*:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Para adicionar um grupo de segurança de rede, utilize [az network nsg create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede denominado *myNetworkSecurityGroup*:

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Crie uma regra de grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg/rule). O exemplo a seguir cria uma regra de grupo de segurança de rede chamada *myNetworkSecurityGroupRule*:

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

NICs virtuais são criadas com [az network nic create](/cli/azure/network/nic). O exemplo a seguir cria três NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir). Você pode criar VMs e NICs virtuais adicionais a qualquer momento e adicioná-las ao balanceador de carga:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Quando todos os três NICs virtuais forem criados, prossiga para a próxima etapa


## <a name="create-virtual-machines"></a>Criar máquinas virtuais

### <a name="create-cloud-init-config"></a>Criar configuração de inicialização de nuvem
Em um tutorial anterior sobre [Como personalizar uma máquina virtual do Linux na primeira inicialização](tutorial-automate-vm-deployment.md), você aprendeu a automatizar a personalização de VM com a inicialização de nuvem. Você pode utilizar o mesmo arquivo de configuração de inicialização de nuvem para instalar o NGINX e executar um aplicativo simples do Node. js 'Olá, Mundo' na próxima etapa. Para ver o balanceador de carga em ação, no final do tutorial, acesse este simples aplicativo em um navegador da web.

No shell atual, crie um arquivo chamado *cloud-init.txt* e cole a configuração a seguir. Por exemplo, crie o arquivo no Cloud Shell e não em seu computador local. Insira `sensible-editor cloud-init.txt` para criar o arquivo e ver uma lista de editores disponíveis. Certifique-se de que o arquivo de inicialização de nuvem inteiro seja copiado corretamente, especialmente a primeira linha:

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

### <a name="create-virtual-machines"></a>Criar máquinas virtuais
Para melhorar a alta disponibilidade do seu aplicativo, coloque suas VMs em um conjunto de disponibilidade. Para obter mais informações sobre conjuntos de disponibilidade, consulte o tutorial anterior[Como criar máquinas virtuais altamente disponíveis](tutorial-availability-sets.md).

Crie um conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set). O exemplo a seguir cria um conjunto de disponibilidade chamado *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Agora, você podecriar as VMs com [az vm create](/cli/azure/vm). O exemplo a seguir cria três VMs e gera chaves SSH, se elas ainda não existirem:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. O parâmetro `--no-wait` não espera até que todas as tarefas sejam concluídas. Pode demorar mais alguns minutos antes que você possa acessar o aplicativo. A investigação de integridade do balanceador de carga detecta automaticamente quando o aplicativo está em execução em cada VM. Quando o aplicativo estiver em execução, a regra do balanceador de carga começará a distribuir o tráfego.


## <a name="test-load-balancer"></a>Testar o balanceador de carga
Obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O exemplo a seguir obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Você pode inserir o endereço IP público em um navegador da Web. Lembre-se - leva alguns minutos para a VMs estar pronta antes do balanceador de carga distribuir tráfego a eles. O aplicativo é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego, como no exemplo a seguir:

![Executar o aplicativo Node.js](./media/tutorial-load-balancer/running-nodejs-app.png)

Para ver o balanceador de carga distribuir tráfego entre todas as três VMs que executam seu aplicativo, você poderá forçar a atualização de seu navegador da Web.


## <a name="add-and-remove-vms"></a>Adicionar e remover as VMs
Talvez seja necessário fazer a manutenção nas VMs que executam seu aplicativo, como a instalação de atualizações do sistema operacional. Para lidar com o aumento de tráfego em seu aplicativo, talvez seja necessário adicionar outras VMs. Esta seção mostra como remover ou adicionar uma VM do balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do balanceador de carga
Você pode remover uma VM do pool de endereços de back-end com [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool). O exemplo a seguir remove a NIC virtual para **myVM2** de *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Para ver o balanceador de carga distribuir tráfego entre as duas VMs restantes que executam seu aplicativo, você poderá forçar a atualização de seu navegador da Web. Agora você pode executar a manutenção na VM, como instalação de atualizações do sistema operacional ou execução de uma reinicialização da VM.

Para exibir uma lista de VMs com NICs virtuais conectadas ao balanceador de carga, use [az network lb address-pool show](/cli/azure/network/lb/address-pool). Consultar e filtrar a ID da NIC virtual da seguinte maneira:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

A saída é semelhante ao exemplo a seguir, que mostra que a NIC virtual para máquina virtual 2 não é parte do pool de endereços de back-end:

```bash
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM ao balanceador de carga
Após executar a manutenção da VM, ou se você precisar expandir a capacidade, você poderá adicionar uma VM ao pool de endereços de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool). O exemplo a seguir adiciona a NIC virtual para **myVM2** a *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Para verificar que a NIC virtual está conectada ao pool de endereços de back-end, use [az network lb address-pool show](/cli/azure/network/lb/address-pool) de novo da etapa anterior.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um balanceador de carga e anexou VMs. Você aprendeu como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma investigação de integridade do balanceador de carga
> * Criar regras de tráfego para o balanceador de carga
> * Usar cloud-init para criar um aplicativo básico do Node.js
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Exibir um balanceador de carga em ação
> * Adicionar e remover as VMs de um balanceador de carga

Avance para o próximo tutorial para aprender mais sobre os componentes de rede virtual do Azure.

> [!div class="nextstepaction"]
> [Gerencie VMs e redes virtuais](tutorial-virtual-network.md)

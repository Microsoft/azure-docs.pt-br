---
title: Tutorial – Criar um conjunto de dimensionamento de máquinas virtuais para o Linux no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar a CLI do Azure para criar e implantar um aplicativo altamente disponível em VMs Linux usando um conjunto de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: afbb3ed022f0a4d0e59e7c3eca4da24737c4d0a6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695404"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli"></a>Tutorial: Criar um conjunto de dimensionamento de máquinas virtuais e implantar um aplicativo altamente disponível no Linux com a CLI do Azure

Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Dimensione o número de VMs no conjunto de dimensionamento manualmente ou defina regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Neste tutorial, você implantará um conjunto de dimensionamento de máquinas virtuais no Azure. Você aprenderá como:

> [!div class="checklist"]
> * Usar cloud-init para criar um aplicativo para escala
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou diminuir o número de instâncias em um conjunto de dimensionamento
> * Criar regras de dimensionamento automático
> * Exibir informações de conexão para instâncias do conjunto de dimensionamento
> * Usar discos de dados com conjunto de dimensionamento

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="scale-set-overview"></a>Visão geral do conjunto de escala
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. As VMs em um conjunto de dimensionamento são distribuídas entre domínios de falha lógica e domínios de atualização em um ou mais *grupos de posicionamento*. Esses são grupos de VMs configuradas de maneira semelhante, da mesma forma que os [conjuntos de disponibilidade](tutorial-availability-sets.md).

VMs são criadas conforme necessário em um conjunto de escala. Você define regras de dimensionamento automático para controlar como e quando as VMs são adicionadas ou removidas do conjunto de dimensionamento. Essas regras podem ser disparados com base em métricas como carga de CPU, utilização de memória ou tráfego de rede.

Escala define suporte até 1.000 VMs quando você usar uma imagem da plataforma Windows Azure. Para cargas de trabalho com requisitos significativos de personalização de VM ou instalação, você pode querer [criar uma imagem de VM personalizada](tutorial-custom-images.md). Você pode criar até 300 VMs em um conjunto de dimensionamento ao usar uma imagem personalizada.


## <a name="create-an-app-to-scale"></a>Criar um aplicativo para escala
Para uso em produção, você poderá [criar uma imagem VM personalizada](tutorial-custom-images.md) que inclui o aplicativo instalado e configurado. Para este tutorial, permite personalizar as VMs na primeira inicialização para ver rapidamente uma escala definida em ação.

Um tutorial anterior, você aprendeu [como personalizar uma máquina virtual de Linux na primeira inicialização](tutorial-automate-vm-deployment.md) com cloud-init. Você pode utilizar o mesmo arquivo de configuração de inicialização de nuvem para instalar o NGINX e executar um aplicativo simples do Node. js 'Hello, World'.

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


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group#az-group-create). O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupScaleSet* no local *eastus*:

```azurecli-interactive
az group create --name myResourceGroupScaleSet --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss#az-vmss-create). O exemplo a seguir cria uma conjunto nomeada de escala *myScaleSet*, usa o arquivo de nuvem-int para personalizar a VM e gera chaves SSH, se não existirem:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento. Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. Pode demorar mais alguns minutos antes que você possa acessar o aplicativo.


## <a name="allow-web-traffic"></a>Permitir o tráfego da web
Um balanceador de carga foi criado automaticamente como parte do conjunto de dimensionamento de máquinas virtuais. O balanceador de carga distribui o tráfego entre um conjunto de VMs definidas usando regras do balanceador de carga. Você pode aprender mais sobre conceitos de Balanceador de carga e a configuração no próximo tutorial, [como carga de máquinas virtuais de saldo no Azure](tutorial-load-balancer.md).

Para permitir o tráfego acessar o aplicativo web, crie uma regra com [criar regra de balanceamento de carga de rede az](/cli/azure/network/lb/rule#az-network-lb-rule-create). O exemplo a seguir cria uma regra chamada *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Testar seu aplicativo
Para ver seu aplicativo Node. js na web, obter o endereço IP público de sua balanceador de carga com [az rede ip público exibir](/cli/azure/network/public-ip#az-network-public-ip-show). O seguinte exemplo obtém o endereço IP de *myScaleSetLBPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Insira o endereço IP público em um navegador da Web. O aplicativo é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego:

![Executar o aplicativo Node.js](./media/tutorial-create-vmss/running-nodejs-app.png)

Para ver a escala definida em ação, você pode forçar atualização seu navegador da web para ver o balanceador de carga distribuir tráfego entre todas as VMs que executam seu aplicativo.


## <a name="management-tasks"></a>Tarefas de gerenciamento
Durante todo o ciclo de vida do conjunto de dimensionamento, você poderá precisar executar uma ou mais tarefas de gerenciamento. Além disso, talvez você deseje criar scripts que automatizam várias tarefas do ciclo de vida. A CLI do Azure fornece uma maneira rápida de realizar essas tarefas. Estas são algumas tarefas comuns.

### <a name="view-vms-in-a-scale-set"></a>Exibição de VMs em um conjunto de escala
Para exibir uma lista de VMs em execução no seu conjunto de escala, use [instâncias de lista az vmss](/cli/azure/vmss#az-vmss-list-instances) da seguinte maneira:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="manually-increase-or-decrease-vm-instances"></a>Aumentar ou diminuir manualmente as instâncias de VM
Para ver o número de instâncias que você fez em um conjunto de escala, use [az vmss show](/cli/azure/vmss#az-vmss-show) e consulte *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Manualmente, é possível aumentar ou diminuir o número de máquinas virtuais no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss#az-vmss-scale). O seguinte exemplo aumenta o número de VMs no conjunto de dimensionamento para *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```

### <a name="get-connection-info"></a>Obter informações de conexão
Para obter informações de conexão sobre as VMs nos conjuntos de dimensionamento, use [az vmss list-instance-connection-info](/cli/azure/vmss#az-vmss-list-instance-connection-info). Esse comando gera o endereço IP público e as portas para cada VM que permite a conexão com o SSH:

```azurecli-interactive
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Usar discos de dados com conjuntos de dimensionamento
Você pode criar e usar discos de dados com conjuntos de dimensionamento. Em um tutorial anterior, você aprendeu como [Gerenciar discos do Azure](tutorial-manage-disks.md) que descreve as práticas recomendadas e as melhorias de desempenho para a criação de aplicativos em discos de dados, em vez do disco do sistema operacional.

### <a name="create-scale-set-with-data-disks"></a>Criar conjunto de dimensionamento com discos de dados
Para criar um conjunto de dimensionamento e anexar discos de dados, adicione o parâmetro `--data-disk-sizes-gb` ao comando [az vmss create](/cli/azure/vmss#az-vmss-create). O exemplo a seguir cria um conjunto de dimensionamento com discos de dados de *50*Gb anexados a cada instância:

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Quando as instâncias são removidas de um conjunto de dimensionamento, todos os discos de dados anexados também são removidos.

### <a name="add-data-disks"></a>Adicionar discos de dados
Para adicionar um disco de dados a instâncias no conjunto de dimensionamento, use [az vmss disk attach](/cli/azure/vmss/disk#az-vmss-disk-attach). O exemplo a seguir adiciona um disco de *50*Gb a cada instância:

```azurecli-interactive
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Desanexar discos de dados
Para remover um disco de dados para instâncias no conjunto de dimensionamento, use [az vmss disk detach](/cli/azure/vmss/disk#az-vmss-disk-detach). O exemplo a seguir remove o disco de dados no LUN *2* de cada instância:

```azurecli-interactive
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um conjunto de dimensionamento de máquinas virtuais. Você aprendeu como:

> [!div class="checklist"]
> * Usar cloud-init para criar um aplicativo para escala
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou diminuir o número de instâncias em um conjunto de dimensionamento
> * Criar regras de dimensionamento automático
> * Exibir informações de conexão para instâncias do conjunto de dimensionamento
> * Usar discos de dados com conjunto de dimensionamento

Avança para o próximo tutorial para saber mais sobre conceitos de máquinas virtuais de balanceamento de carga.

> [!div class="nextstepaction"]
> [Balancear carga de máquinas virtuais](tutorial-load-balancer.md)

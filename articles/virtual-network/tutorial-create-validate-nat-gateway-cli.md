---
title: Criar e testar um gateway da NAT – CLI do Azure
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um gateway da NAT usando a CLI do Azure e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 457749b353b9b7fabfb137ebe907463bb17158ba
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98926937"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Tutorial: Criar um gateway da NAT usando a CLI do Azure e testar o serviço NAT

Neste tutorial, você criará um gateway da NAT para fornecer conectividade de saída para máquinas virtuais no Azure. Para testar o gateway da NAT, implante uma máquina virtual de origem e de destino. Você testará o gateway da NAT realizando conexões de saída com um endereço IP público. Essas conexões serão provenientes da máquina virtual de origem para a de destino. Este tutorial implanta a origem e o destino em duas redes virtuais diferentes no mesmo grupo de recursos apenas para simplificar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.71 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>Criar o Gateway da NAT

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar a Internet pública, você precisa de um ou mais endereços IP públicos para o gateway da NAT. Use [az network public-ip create](/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsource** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Você pode usar um ou mais recursos de endereço IP público, prefixos IP público ou ambos com o gateway da NAT. Adicionaremos um recurso de prefixo IP público a esse cenário para demonstrar.   Use [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) para criar um recurso de prefixo IP público chamado **myPublicIPprefixsource** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway da NAT:
  - Um pool de IPs públicos e o prefixo IP público a serem usados para fluxos de saída convertidos pelo recurso de Gateway da NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

Crie um gateway da NAT global do Azure com [az network nat gateway create](/cli/azure/network/nat) chamado **myNATgateway**. O comando usa o endereço IP público **myPublicIP** e o prefixo IP público **myPublicIPprefix**. O comando também altera o tempo limite de ociosidade para 10 minutos.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

Neste momento, o gateway da NAT está funcional e tudo o que está faltando é configurar quais sub-redes de uma rede virtual devem usá-lo.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a origem para o tráfego de saída

Orientaremos você pela instalação de um ambiente de teste completo. Você configurará um teste usando ferramentas de software livre para verificar o gateway da NAT. Começaremos com a origem, que usará o gateway da NAT criado anteriormente.

### <a name="configure-virtual-network-for-source"></a>Configurar a rede virtual para a origem

Antes de implantar uma VM e poder testar o gateway da NAT, precisamos criar a rede virtual.

Crie uma rede virtual chamada **myVnetsource** com uma sub-rede chamada **mySubnetsource** no **myResourceGroupNAT** usando [az network Microsoft Azure Virtual Network create](/cli/azure/network/vnet).  O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurar o serviço NAT da sub-rede de origem

Configure a sub-rede de origem **mySubnetsource** na rede virtual **myVnetsource** para usar um recurso de gateway da NAT específico **myNATgateway** com [az network Microsoft Azure Virtual Network subnet update](/cli/azure/network/vnet/subnet). Esse comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

Todo o tráfego de saída para os destinos da Internet agora usa o serviço NAT.  Não é necessário configurar uma UDR.

Antes de podermos testar o gateway da NAT, precisamos criar uma VM de origem.  Atribuiremos um recurso de endereço IP público como um IP público em nível de instância para acessar essa VM de fora. Esse endereço é usado somente para acessá-la para o teste.  Demonstraremos como o serviço NAT tem precedência sobre outras opções de saída.

Você também pode criar essa VM sem um IP público e criar outra VM para usá-la como um jumpbox sem um IP público como um exercício.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para a VM de origem

Criamos um IP público para ser usado para acessar a VM de origem. Use [az network public-ip create](/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsourceVM** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Criar um NSG para a VM de origem

Como os endereços IP públicos Standard são "seguros por padrão", precisamos criar um NSG para permitir o acesso de entrada para acesso SSH.  O serviço NAT do Azure reconhece a direção do fluxo. Este NSG não será usado para saída depois que o gateway da NAT estiver configurado na mesma sub-rede. Use [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) para criar um recurso NSG chamado **myNSGsource** em **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor o ponto de extremidade SSH na VM de origem

Criamos uma regra no NSG para acesso SSH à VM de origem. Use [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh**. Essa regra será criada no NSG chamado **myNSGsource** no grupo de recursos **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Criar NIC para a VM de origem

Crie um adaptador de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe ao endereço IP público e o grupo de segurança de rede. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Criar uma VM de origem

Crie a máquina virtual com [az vm create](/cli/azure/vm#az-vm-create).  Geramos chaves SSH para essa VM e armazenamos a chave privada para uso posterior.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Embora o comando retorne imediatamente, pode levar alguns minutos para a VM ser implantada.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para o tráfego de saída

Agora criaremos um destino para o tráfego de saída convertido pelo serviço NAT para permitir que você o teste.

### <a name="configure-virtual-network-for-destination"></a>Configurar a rede virtual para o destino

 Precisamos criar uma rede virtual na qual a máquina virtual de destino estará.  Esses comandos são as mesmas etapas para a VM de origem com pequenas alterações para expor o ponto de extremidade de destino.

Crie uma rede virtual chamada **myVnetdestination** com uma sub-rede chamada **mySubnetdestination** no **myResourceGroupNAT** usando [az network Microsoft Azure Virtual Network create](/cli/azure/network/vnet).  O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Criar IP público para a VM de destino

Criamos um IP público para ser usado para acessar a VM de origem. Use [az network public-ip create](/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPdestinationVM** em **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Criar um NSG para a VM de destino

Os endereços IP Públicos Standard são "seguros por padrão". Você precisará criar um NSG para permitir o acesso de entrada para SSH. O serviço NAT do Azure reconhece a direção do fluxo. Este NSG não será usado para saída depois que o gateway da NAT estiver configurado na mesma sub-rede. Use [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) para criar um recurso NSG chamado **myNSGdestination** em **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Expor o ponto de extremidade SSH na VM de destino

Criamos uma regra no NSG para acesso SSH à VM de destino. Use [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh**. Essa regra será criada no NSG chamado **myNSGdestination** no grupo de recursos **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Expor ponto de extremidade HTTP na VM de destino

Criamos uma regra no NSG para acesso HTTP à VM de destino. Use [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) para criar uma regra NSG chamada **http** no NSG chamado **myNSGdestination** em **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Criar NIC para a VM de destino

Crie um adaptador de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe ao endereço IP público e **myPublicIPdestinationVM** e ao grupo de segurança de rede **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Criar uma VM de destino

Crie a máquina virtual com [az vm create](/cli/azure/vm#az-vm-create).  Geramos chaves SSH para essa VM e armazenamos a chave privada para uso posterior.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Embora o comando retorne imediatamente, pode levar alguns minutos para a VM ser implantada.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparar um servidor Web e testar o conteúdo na VM de destino

Primeiro precisamos descobrir o endereço IP da VM de destino.  Para obter o endereço IP público da VM de destino, use [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Entrar na VM de destino

As credenciais do SSH devem ser armazenadas no Cloud Shell da operação anterior.  Abra um [Azure Cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual. 

```bash
ssh <ip-address-destination>
```

Copie e cole os comandos a seguir após entrar.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Esses comandos atualizarão sua máquina virtual, instalarão o nginx e criarão um arquivo de 100 KB. Esse arquivo será recuperado da VM de origem usando o serviço NAT.

Feche a sessão SSH com a VM de destino.

## <a name="prepare-test-on-source-vm"></a>Preparar o teste na VM de origem

Primeiro precisamos descobrir o endereço IP da VM de origem.  Para obter o endereço IP público da VM de origem, use [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de origem.

### <a name="sign-in-to-source-vm"></a>Entrar na VM de origem

Novamente, as credenciais SSH são armazenadas no Cloud Shell. Abra uma nova guia para o [Azure Cloud Shell](https://shell.azure.com) no navegador.  Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual. 

```bash
ssh <ip-address-source>
```

Copie e cole os comandos a seguir para se preparar para testar o serviço NAT.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Esse comando atualizará sua máquina virtual, instalará o go, instalará o [hey](https://github.com/rakyll/hey) por meio do GitHub e atualizará o ambiente de shell.

Agora você está pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar o serviço NAT

Enquanto estiver conectado à VM de origem, você poderá usar o **curl** e o **hey** para gerar solicitações para o endereço IP de destino.

Use curl para recuperar o arquivo de 100 KB.  Substitua **\<ip-address-destination>** no exemplo abaixo pelo endereço IP de destino copiado anteriormente.  O parâmetro **--output** indica que o arquivo recuperado será descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Você também pode gerar uma série de solicitações usando o **hey**. Novamente, substitua **\<ip-address-destination>** pelo endereço IP de destino que você copiou anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Esse comando gerará 100 solicitações, 10 simultaneamente, com um tempo limite de 30 segundos. A conexão TCP não será reutilizada.  Cada solicitação recuperará 100 KB.  No fim da execução, o **hey** relatará algumas estatísticas sobre o desempenho do serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos nele.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um gateway da NAT, criou uma VM de origem e destino e, em seguida, testou o gateway da NAT.

Examine as métricas no Azure Monitor para verificar a operação do serviço NAT. Diagnostique problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento de recursos das portas SNAT é resolvido com facilidade adicionando mais recursos de endereço IP público, recursos de prefixo IP público ou ambos.

- Saiba mais sobre a [NAT de Rede Virtual](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando a CLI do Azure](./quickstart-create-nat-gateway-cli.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o portal do Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

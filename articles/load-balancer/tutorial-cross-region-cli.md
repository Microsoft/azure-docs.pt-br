---
title: 'Tutorial: criar um balanceador de carga entre regiões usando a CLI do Azure'
titleSuffix: Azure Load Balancer
description: Comece a usar este tutorial implantando um Azure Load Balancer entre regiões com a CLI do Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791905"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Tutorial: criar um Azure Load Balancer entre regiões usando a CLI do Azure

Um balanceador de carga entre regiões garante que um serviço esteja disponível globalmente em várias regiões do Azure. Se uma região falhar, o tráfego será roteado para o balanceador de carga regional íntegro mais próximo.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um balanceador de carga entre regiões.
> * Crie uma regra de balanceador de carga.
> * Criar um pool de back-end que contém dois balanceadores de carga regionais.
> * Testar o balanceador de carga.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Dois Azure Load Balancers de SKU **Standard** com pools de back-end implantados em duas regiões diferentes do Azure.
    - Para obter informações sobre como criar um balanceador de carga padrão regional e máquinas virtuais para pools de back-end, confira [Início Rápido: Criar um balanceador de carga público para balancear cargas de VMs usando a CLI do Azure](quickstart-load-balancer-standard-public-cli.md).
        - Acrescente o nome dos balanceadores de carga e das máquinas virtuais em cada região com **-R1** e **-R2**. 
- CLI do Azure instalada localmente ou Azure Cloud Shell.

Se você prefere instalar e usar a CLI localmente, este guia de início rápido exige a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure

Entre na CLI do Azure:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Criar um balanceador de carga entre regiões

Nesta seção, você criará um balanceador de carga entre regiões, um endereço IP público e uma regra de balanceamento de carga.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create):

* Chamado **myResourceGroupLB-CR**.
* No local **westus**.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga entre regiões com [az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Chamado **myLoadBalancer-CR**.
* Um pool de front-end chamado **myFrontEnd-CR**.
* Um pool de back-end chamado **myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra do balanceador de carga com [az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Chamada **myHTTPRule-CR**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd-CR**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool-CR** usando a **Porta 80**. 
* Protocolo **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Criar pool de back-end

Nesta seção, você adicionará dois balanceadores de carga Standard regionais ao pool de back-end do balanceador de carga entre regiões.

> [!IMPORTANT]
> Para concluir essas etapas, verifique se dois balanceadores de carga regionais com pools de back-end foram implantados na sua assinatura.  Para obter mais informações, confira **[Início Rápido: Criar um balanceador de carga público para balancear cargas de VMs usando a CLI do Azure](quickstart-load-balancer-standard-public-cli.md)** .

### <a name="add-the-regional-frontends-to-load-balancer"></a>Adicionar os front-ends regionais ao balanceador de carga

Nesta seção, você colocará as IDs de recurso de dois front-ends de balanceadores de carga regionais em variáveis.  Você usará as variáveis para adicionar os front-ends ao pool de endereços de back-end do balanceador de carga entre regiões.

Recupere as IDs de recurso com [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Use [az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) para adicionar os front-ends que você colocou em variáveis no pool de back-end do balanceador de carga entre regiões:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta seção, você testará o balanceador de carga entre regiões. Você se conectará ao endereço IP público em um navegador da Web.  Você interromperá as máquinas virtuais em um dos pools de back-end do balanceador de carga regional e observará o failover.

1. Para obter o endereço IP público do balanceador de carga, use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

3. Interrompa as máquinas virtuais no pool de back-end de um dos balanceadores de carga regionais.

4. Atualize o navegador da Web e observe o failover da conexão no outro balanceador de carga regional.

## <a name="clean-up-resources"></a>Limpar os recursos

Use o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados quando deixarem de ser necessários.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

* Criou um balanceador de carga entre regiões.
* Criou uma regra de balanceamento de carga.
* Adicionou balanceadores de carga regionais ao pool de back-end do balanceador de carga entre regiões.
* Testou o balanceador de carga.

Prossiga para o próximo artigo para saber como...
> [!div class="nextstepaction"]
> [VMs com balanceamento de carga entre zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

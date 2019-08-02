---
title: Usar o Filtro de Pacote do FreeBSD para criar um firewall no Azure | Microsoft Docs
description: Saiba como implantar um firewall NAT usando o PF do FreeBSD no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 03ef1ad3f81cfe7b11f74ace9ff2992535d5aad6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667632"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Como usar o Filtro de Pacote do FreeBSD para criar um firewall seguro no Azure
Este artigo apresenta como implantar um firewall NAT usando o Filtro de Pacote do FreeBSD por meio do modelo do Azure Resource Manager para um cenário comum de servidor Web.

## <a name="what-is-pf"></a>O que é o PF?
PF (Filtro de Pacote, também grafado pf) é um filtro de pacote com estado licenciado BSD, uma parte central do software de firewall. Desde então, o PF evoluiu rapidamente e agora apresenta várias vantagens sobre outros firewalls disponíveis. O NAT (Conversão de Endereços de Rede) está no PF desde o primeiro dia. Em seguida, o agendador de pacotes e o gerenciamento de filas ativas foram integrados ao PF, integrando o ALTQ e tornando-o configurável por meio da configuração do PF. Recursos como pfsync e CARP para failover e redundância, authpf para autenticação de sessão e ftp-proxy para facilitar o firewall do difícil protocolo FTP, também estenderam o PF. Em resumo, o PF é um firewall avançado e repleto de recursos. 

## <a name="get-started"></a>Introdução
Se estiver interessado em configurar um firewall seguro na nuvem para seus servidores Web, é hora de começar. Você também pode aplicar os scripts usados nesse modelo do Azure Resource Manager para configurar a topologia de rede.
O modelo do Azure Resource Manager configura uma máquina virtual FreeBSD que executa o NAT/redirecionamento usando o PF e duas máquinas virtuais FreeBSD com o servidor Web Nginx instalado e configurado. Além de executar o NAT para o tráfego de saída dos dois servidores Web, a máquina virtual do NAT/redirecionamento intercepta as solicitações HTTP e as redireciona para os dois servidores Web no estilo round robin. A VNet usa o intervalo de endereços IP privado 10.0.0.2/24 não roteável e é possível modificar os parâmetros do modelo. O modelo do Azure Resource Manager também define uma tabela de rotas para toda a VNet, que é uma coleção de rotas individuais usadas para substituir as rotas padrão do Azure de acordo com o endereço IP de destino. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implantar por meio da CLI do Azure
Você precisa da [Azure CLI](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index). Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado `myResourceGroup` na localização `West US`.

```azurecli
az group create --name myResourceGroup --location westus
```

Em seguida, implante o modelo [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) com [az group deployment create](/cli/azure/group/deployment). Baixe [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) no mesmo caminho e defina seus próprios valores de recursos, como `adminPassword`, `networkPrefix` e `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Após cerca de cinco minutos, você deverá obter as informações de `"provisioningState": "Succeeded"`. Em seguida, é possível executar SSH para a VM de front-end (NAT) ou acessar o servidor Web Nginx em um navegador usando o endereço IP público ou o FQDN da VM de front-end (NAT). O exemplo a seguir lista o FQDN e o endereço IP público atribuído à VM de front-end (NAT) no grupo de recursos `myResourceGroup`. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Próximas etapas
Você deseja configurar seu próprio NAT no Azure? Software Livre: gratuito mas eficiente? Portanto, o PF é uma boa opção. Ao usar o modelo [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), você só precisa de cinco minutos para configurar um firewall NAT com o balanceamento de carga round robin usando o PF do FreeBSD no Azure para um cenário comum de servidor Web. 

Se desejar saber mais sobre a oferta do FreeBSD no Azure, consulte [Introdução ao FreeBSD no Azure](freebsd-intro-on-azure.md).

Se desejar saber mais sobre o PF, consulte [Manual do FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) ou [Guia do Usuário do PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).

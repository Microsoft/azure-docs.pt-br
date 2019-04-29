---
title: Conectar-se ao Azure Data Lake Storage Gen1 por Redes Virtuais | Microsoft Docs
description: Conectar-se ao Azure Data Lake Storage Gen1 pelas Redes Virtuais do Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878861"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Acessar o Azure Data Lake Storage Gen1 por VMs em uma Rede Virtual do Azure
O Azure Data Lake Storage Gen1 é um serviço de PaaS executado em endereços de IP de Internet públicos. Em geral, qualquer servidor que pode se conectar à Internet pública também pode se conectar a pontos de extremidade do Azure Data Lake Storage Gen1. Por padrão, todas as VMs que estão em Redes Virtuais do Azure podem acessar a Internet e, portanto, podem acessar o Azure Data Lake Storage Gen1. No entanto, é possível configurar VMs em uma Rede Virtual para que ela não tenha acesso à Internet. Para essas VMs, o acesso ao Azure Data Lake Storage Gen1 também é restrito. O bloqueio do acesso à Internet pública para VMs em Redes Virtuais do Azure pode ser feito usando uma das abordagens a seguir:

* Configurando NSGs (Grupos de Segurança de Rede)
* Configurando UDRs (Rotas Definidas pelo Usuário)
* Trocando rotas pelo protocolo BGP (protocolo de roteamento dinâmico padrão do setor) quando o ExpressRoute é usado e que bloqueia o acesso à Internet

Neste artigo, você saberá como habilitar o acesso ao Azure Data Lake Storage Gen1 em VMs do Azure cujo acesso aos recursos foi restrito usando um dos três métodos listados anteriormente.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Habilitando a conectividade com o Azure Data Lake Storage Gen1 em VMs com conectividade restrita
Para acessar o Azure Data Lake Storage Gen1 de tais VMs, você deve configurá-las para acessar o endereço IP da região em que a conta do Armazenamento de Dados do Azure Data Lake Gen1 está disponível. Você pode identificar os endereços IP de suas regiões de conta do Data Lake Storage Gen1 resolvendo os nomes DNS de suas contas (`<account>.azuredatalakestore.net`). Para resolver nomes DNS de suas contas, você pode usar ferramentas como **nslookup**. Abra um prompt de comando no computador e execute o comando a seguir:

    nslookup mydatastore.azuredatalakestore.net

A saída é semelhante ao mostrado a seguir. O valor na propriedade **Address** é o endereço IP associado à sua conta do Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Habilitando a conectividade em VMs restritas usando o NSG
Quando uma regra do NSG é usada para bloquear o acesso à Internet, é possível criar outro NSG que permite o acesso ao Endereço IP do Data Lake Storage Gen1. Para saber mais sobre as regras de NSG, consulte [Visão geral dos grupos de segurança de rede](../virtual-network/security-overview.md). Para obter instruções sobre como criar NSGs, consulte [Como criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Habilitando a conectividade em VMs restritas usando a UDR ou o ExpressRoute
Quando rotas, sejam UDRs ou rotas trocadas pelo protocolo BGP, são usadas para bloquear o acesso à Internet, uma rota especial precisa ser configurada para que as VMs nessas sub-redes possam acessar os pontos de extremidade do Data Lake Storage Gen1. Para saber mais, consulte [Visão geral das rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md). Para obter instruções sobre como criar UDRs, consulte [Criar UDRs no Gerenciador de Recursos](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Habilitando a conectividade em VMs restritas usando o ExpressRoute
Quando um circuito do ExpressRoute é configurado, os servidores locais podem acessar o Data Lake Storage Gen1 por meio de emparelhamento público. Mais detalhes sobre como configurar o emparelhamento público no ExpressRoute está disponível em [Perguntas frequentes sobre o ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Consulte também
* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Protegendo dados armazenados no Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)


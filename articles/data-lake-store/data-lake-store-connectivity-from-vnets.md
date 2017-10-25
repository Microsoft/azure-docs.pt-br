---
title: Conectar-se ao Azure Data Lake Store por Redes Virtuais | Microsoft Docs
description: Conectar-se ao Azure Data Lake Store pelas Redes Virtuais do Azure
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 029939eabc7283d7591576fa1b14731a9e626a88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Acessar o Azure Data Lake Store por VMs em uma Rede Virtual do Azure
O Azure Data Lake Store é um serviço de PaaS executado em endereços de IP de Internet públicos. Em geral, qualquer servidor que pode se conectar à Internet pública também pode se conectar a pontos de extremidade do Azure Data Lake Store. Por padrão, todas as VMs que estão em Redes Virtuais do Azure podem acessar a Internet e, portanto, podem acessar o Azure Data Lake Store. No entanto, é possível configurar VMs em uma Rede Virtual para que ela não tenha acesso à Internet. Para essas VMs, o acesso ao Azure Data Lake Store também é restrito. O bloqueio do acesso à Internet pública para VMs em Redes Virtuais do Azure pode ser feito usando uma das abordagens a seguir.

* Configurando NSGs (Grupos de Segurança de Rede)
* Configurando UDRs (Rotas Definidas pelo Usuário)
* Trocando rotas pelo protocolo BGP (protocolo de roteamento dinâmico padrão do setor) quando o ExpressRoute é usado e que bloqueia o acesso à Internet

Neste artigo, você saberá como habilitar o acesso ao Azure Data Lake Store em VMs do Azure que foram restritas para acessar os recursos, usando um dos três métodos listados acima.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Habilitando a conectividade com o Azure Data Lake Store em VMs com conectividade restrita
Para acessar o Azure Data Lake Store nessas VMs, você deve configurá-las para acessar o endereço IP em que a conta do Azure Data Lake Store está disponível. Identifique os endereços IP das contas do Data Lake Store resolvendo os nomes DNS das contas (`<account>.azuredatalakestore.net`). Para isso, use ferramentas como **nslookup**. Abra um prompt de comando no computador e execute o comando a seguir.

    nslookup mydatastore.azuredatalakestore.net

A saída é semelhante ao mostrado a seguir. O valor na propriedade **Address** é o endereço IP associado à sua conta do Data Lake Store.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Habilitando a conectividade em VMs restritas usando o NSG
Quando uma regra do NSG é usada para bloquear o acesso à Internet, é possível criar outro NSG que permite o acesso ao Endereço IP do Data Lake Store. Mais informações sobre as regras do NSG estão disponíveis em [O que é um Grupo de Segurança de Rede?](../virtual-network/virtual-networks-nsg.md). Para obter instruções sobre como criar NSGs, consulte [Como gerenciar NSGs usando o portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Habilitando a conectividade em VMs restritas usando a UDR ou o ExpressRoute
Quando rotas, sejam UDRs ou rotas trocadas pelo protocolo BGP, são usadas para bloquear o acesso à Internet, uma rota especial precisa ser configurada para que as VMs nessas sub-redes possam acessar os pontos de extremidade do Data Lake Store. Para obter mais informações, consulte [O que são Rotas Definidas pelo Usuário?](../virtual-network/virtual-networks-udr-overview.md). Para obter instruções sobre como criar UDRs, consulte [Criar UDRs no Gerenciador de Recursos](../virtual-network/virtual-network-create-udr-arm-ps.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Habilitando a conectividade em VMs restritas usando o ExpressRoute
Quando um circuito do ExpressRoute está configurado, os servidores locais podem acessar o Data Lake Store por meio do emparelhamento público. Mais detalhes sobre como configurar o emparelhamento público no ExpressRoute está disponível em [Perguntas frequentes sobre o ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Confira também
* [Visão geral do Repositório Azure Data Lake](data-lake-store-overview.md)
* [Protegendo os dados armazenados no Azure Data Lake Store](data-lake-store-security-overview.md)


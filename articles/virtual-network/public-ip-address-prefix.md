---
title: Prefixo de endereço IP público do Azure | Microsoft Docs
description: Saiba mais sobre o que é um prefixo de endereço IP público do Azure e como ele pode ajudar a atribuir endereços IP públicos previsíveis aos seus recursos.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 3cc4933ae70ad1d661835749dd23e7e634ab54f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474429"
---
# <a name="public-ip-address-prefix"></a>Prefixo de endereço IP público

Um prefixo de endereço IP público é um intervalo reservado de endereços IP para seus pontos de extremidade públicos no Azure. O Azure aloca um intervalo contíguo de endereços à sua assinatura com base na quantidade que você especifica. Se você não estiver familiarizado com endereços públicos, confira [Endereços IP públicos.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Os endereços IP públicos são atribuídos de um pool de endereços em cada região do Azure. Você pode [baixar](https://www.microsoft.com/download/details.aspx?id=56519) a lista de intervalos que o Azure usa para cada região. Por exemplo, 40.121.0.0/16 é um dos mais de 100 intervalos que usa o Azure na região Leste dos EUA. O intervalo inclui os endereços utilizáveis de 40.121.0.1 a 40.121.255.254.

É possível criar um prefixo de endereço IP público em uma região e em uma assinatura do Azure especificando um nome e quantos endereços deseja que o prefixo inclua. Por exemplo, se você criar o prefixo de endereço IP público /28, Azure alocará 16 endereços de um dos seus intervalos para você. Somente depois de criar o intervalo você saberá qual intervalo o Azure atribuirá, mas os endereços são contíguos. Os prefixos de endereço IP públicos têm um valor. Para obter detalhes, confira [preços de endereço IP público](https://azure.microsoft.com/pricing/details/ip-addresses).

> [!IMPORTANT]
> O prefixo de IP público está na versão prévia pública em regiões limitadas. [Saiba o que significa estar na versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). O prefixo do IP público está disponível atualmente em: Centro-Oeste dos EUA, Oeste dos EUA, Oeste dos EUA 2, Centro dos EUA, Europa Setentrional, Europa Ocidental e Sudeste Asiático. Para obter uma lista de regiões atualizada, visite [Atualizações do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="why-create-a-public-ip-address-prefix"></a>Por que criar um prefixo de endereço IP público?

Quando você cria recursos de endereço IP público, o Azure atribui um endereço IP público disponível de qualquer um dos intervalos usados em uma região. Depois que o Azure atribui o endereço, você sabe qual é o endereço, mas até que o Azure atribua o endereço, você não sabe qual endereço poderá ser atribuído. Isso pode ser um problema quando, por exemplo, você ou seus parceiros comerciais, configuram regras de firewall que permitem endereços IP específicos. Sempre que você atribuir um novo endereço IP público a um recurso, o endereço deverá ser adicionado à regra de firewall. Quando você atribui endereços aos recursos de um prefixo de endereço IP público, as regras de firewall não precisam ser atualizadas sempre que um dos endereços é atribuído, porque todo o intervalo poderia ser adicionado a uma regra.

## <a name="benefits"></a>Benefícios

- Você pode criar recursos de endereço IP público de um intervalo conhecido.
- Você ou seus parceiros comerciais podem criar regras de firewall com intervalos que incluem endereços IP públicos atribuídos no momento, bem como endereços que você ainda não atribuiu. Isso elimina a necessidade de alterar as regras de firewall à medida que você atribui endereços IP a novos recursos.
- O tamanho padrão de um intervalo que você pode criar é de /28 ou 16 endereços IP.
- Não há nenhum limite de quantos intervalos você pode criar, no entanto, há limites para o número máximo de endereços IP públicos estáticos que pode haver em uma assinatura do Azure. Como resultado, o número de intervalos que você cria não pode abranger mais endereços IP públicos estáticos do que o número que pode haver em sua assinatura. Para obter mais informações, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Os endereços que você cria usando o endereços do prefixo podem ser atribuídos a qualquer recurso do Azure ao qual você possa atribuir um endereço IP público.
- Você pode ver facilmente quais endereços IP estão alocados e quais ainda não estão alocados dentro do intervalo.

## <a name="scenarios"></a>Cenários
É possível associar os seguintes recursos a um endereço IP público estático de um prefixo:

|Resource|Cenário|Etapas|
|---|---|---|
|Máquinas Virtuais| A associação de IPs públicos de um prefixo às suas máquinas virtuais no Azure reduz a sobrecarga de gerenciamento em relação à lista de permissões de IPs em um firewall. Você pode simplesmente colocar um prefixo de inteiro na lista de permissões com uma única regra de firewall. Ao dimensionar com máquinas virtuais no Azure, você pode associar IPs do mesmo prefixo economizando custos, tempo e sobrecarga de gerenciamento.| Para associar os IPs de um prefixo à sua máquina virtual: 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP do prefixo.](manage-public-ip-address-prefix.md) 3. [Associe o IP ao adaptador de rede da máquina virtual.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Balanceadores de Carga | A associação de IPs públicos de um prefixo à configuração ou à regra de entrada de IP de front-end de um balanceador de carga garante a simplificação do espaço de endereços IP público do Azure. É possível simplificar o cenário limpando as conexões de saída a serem originadas de um intervalo de endereços IP contíguos definido pelo prefixo IP público. | Para associar os IPs de um prefixo ao balanceador de carga: 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP do prefixo.](manage-public-ip-address-prefix.md) 3. Ao criar o balanceador de carga, selecione ou atualize o IP criado na etapa 2 acima como o IP de front-end do balanceador de carga. |
| Firewall do Azure | Você pode usar um IP público de um prefixo do SNAT de saída. Isso significa que todo tráfego de rede virtual de saída é convertido no IP público do [Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Como esse IP vem de um prefixo predeterminado, é muito fácil saber antecipadamente qual será o volume de IP público no Azure. | 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP do prefixo.](manage-public-ip-address-prefix.md) 3. Ao [implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), selecione o IP já alocado do prefixo.|

## <a name="constraints"></a>Restrições

- Você não pode especificar os endereços IP do prefixo. O Azure aloca os endereços IP para o prefixo, com base no tamanho que você especifica.
- Você não poderá alterar o intervalo depois de criar o prefixo.
- O intervalo é de apenas endereços IPv4. O intervalo não contém endereços IPv6.
- Somente endereços IP públicos estáticos criados com o SKU Standard podem ser atribuídos do intervalo do prefixo. Para saber mais sobre os SKUs do endereço IP público, confira [endereço IP público](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Os endereços do intervalo só podem ser atribuídos aos recursos do Azure Resource Manager. Os endereços não podem ser atribuídos aos recursos no modelo de implantação clássico.
- Todos os endereços IP públicos criados do prefixo precisam existir nas mesmas região e assinatura do Azure que o prefixo e ser atribuídos aos recursos nas mesmas região e assinatura.
- Você não poderá excluir um prefixo se houver endereços dentro dele atribuídos a recursos de endereço IP público associados a um recurso. Primeiro, desassocie todos os recursos de endereço IP público atribuídos a endereços IP do prefixo.


## <a name="next-steps"></a>Próximas etapas

- [Criar](manage-public-ip-address-prefix.md) um prefixo de endereço IP público

---
title: Prefixo de endereço IP público do Azure
description: Saiba mais sobre o que é um prefixo de endereço IP público do Azure e como ele pode ajudar a atribuir endereços IP públicos previsíveis aos seus recursos.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 3d98107075c215c0a00bc2035325a6ad8bf5bfc5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222421"
---
# <a name="public-ip-address-prefix"></a>Prefixo de endereço IP público

Um prefixo de endereço IP público é um intervalo reservado de endereços IP no Azure. O Azure fornece um intervalo contíguo de endereços para sua assinatura com base na quantidade especificada. 

Se você não estiver familiarizado com endereços públicos, confira [Endereços IP públicos.](./public-ip-addresses.md#public-ip-addresses)

Os endereços IP públicos são atribuídos de um pool de endereços em cada região do Azure. Você pode [baixar](https://www.microsoft.com/download/details.aspx?id=56519) a lista de intervalos que o Azure usa para cada região. Por exemplo, 40.121.0.0/16 é um dos mais de 100 intervalos que usa o Azure na região Leste dos EUA. O intervalo inclui os endereços utilizáveis de 40.121.0.1 a 40.121.255.254.

É possível criar um prefixo de endereço IP público em uma região e em uma assinatura do Azure especificando um nome e quantos endereços deseja que o prefixo inclua. 

Os intervalos de endereços IP públicos são atribuídos com um prefixo de sua escolha. Se você criar um prefixo de/28, o Azure fornecerá 16 endereços IP de um de seus intervalos.

Somente depois de criar o intervalo você saberá qual intervalo o Azure atribuirá, mas os endereços são contíguos. 

Os prefixos de endereço IP público têm uma taxa, para obter mais informações, consulte [preços de endereço IP público](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Por que criar um prefixo de endereço IP público?

Quando você cria recursos de endereço IP público, o Azure atribui um endereço IP público disponível de qualquer um dos intervalos usados nessa região. 

Até que o Azure atribua o endereço IP, você não saberá o IP exato. Esse processo pode ser problemático quando você cria regras de firewall que permitem endereços IP específicos. Para cada endereço IP adicionado, uma regra de firewall correspondente deve ser adicionada.

Quando você atribui endereços a seus recursos de um prefixo de endereço IP público, as atualizações de regra de firewall não são necessárias. Todo o intervalo é adicionado à regra.

## <a name="benefits"></a>Benefícios

- Criação de recursos de endereço IP público a partir de um intervalo conhecido.
- Configuração de regra de firewall com intervalos que incluem endereços IP públicos que você atribuiu atualmente e endereços que ainda não foram atribuídos. Essa configuração elimina a necessidade de alterar as regras de firewall à medida que você atribui endereços IP a novos recursos.
- O tamanho padrão de um intervalo que você pode criar é de /28 ou 16 endereços IP.
- Não há limites quanto a quantos intervalos você pode criar. Há limites no número máximo de endereços IP públicos estáticos que você pode ter em uma assinatura do Azure. O número de intervalos que você cria não pode abranger endereços IP públicos mais estáticos do que você pode ter em sua assinatura. Para obter mais informações, confira [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Os endereços que você cria usando o endereços do prefixo podem ser atribuídos a qualquer recurso do Azure ao qual você possa atribuir um endereço IP público.
- Você pode ver facilmente quais endereços IP são fornecidos e não fornecidos dentro do intervalo.

## <a name="scenarios"></a>Cenários
É possível associar os seguintes recursos a um endereço IP público estático de um prefixo:

|Recurso|Cenário|Etapas|
|---|---|---|
|Máquinas virtuais| A associação de IPs públicos de um prefixo às suas máquinas virtuais no Azure reduz a sobrecarga de gerenciamento ao adicionar endereços IP a uma lista de permissões no firewall. Você pode adicionar um prefixo inteiro com uma única regra de firewall. Ao dimensionar com máquinas virtuais no Azure, você pode associar IPs do mesmo prefixo economizando custos, tempo e sobrecarga de gerenciamento.| Para associar os IPs de um prefixo à sua máquina virtual: </br> 1. [crie um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. [associe o IP à interface de rede da sua máquina virtual.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> Você também pode [associar os IPs a um conjunto de dimensionamento de máquinas virtuais](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Balanceadores de carga padrão | Associar IPs públicos de um prefixo à sua configuração de IP de front-end ou regra de saída de um balanceador de carga garante a simplificação do espaço de endereço IP público do Azure. Simplifique seu cenário ao realizar o grooming de conexões de saída de um intervalo de endereços IP contíguos. | Para associar IPs de um prefixo ao balanceador de carga: </br> 1. [crie um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. ao criar o balanceador de carga, selecione ou atualize o IP criado na etapa 2 acima como o IP de front-end do balanceador de carga. |
| Firewall do Azure | Você pode usar um IP público de um prefixo do SNAT de saída. Todo o tráfego de rede virtual de saída é convertido no IP público do [Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . | Para associar um IP de um prefixo ao seu firewall: </br> 1. [crie um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. ao [implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), certifique-se de selecionar o IP que você atribuiu anteriormente do prefixo.|
| Gateway de Aplicativo v2 | Você pode usar um IP público de um prefixo para o seu dimensionamento automático e o gateway de aplicativo com redundância de zona v2. | Para associar um IP de um prefixo ao seu gateway: </br> 1. [crie um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. ao [implantar o gateway de aplicativo](../application-gateway/quick-create-portal.md#create-an-application-gateway), certifique-se de selecionar o IP que você atribuiu anteriormente do prefixo.|

## <a name="constraints"></a>Restrições

- Você não pode especificar os endereços IP do prefixo. O Azure fornece os endereços IP para o prefixo, com base no tamanho que você especificar.
- Você pode criar um prefixo de até 16 endereços IP ou um/28 por padrão. Examine [os limites de rede aumente as solicitações](../azure-portal/supportability/networking-quota-requests.md) e [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter mais informações.
- Você não poderá alterar o intervalo depois de criar o prefixo.
- Somente endereços IP públicos estáticos criados com o SKU Standard podem ser atribuídos do intervalo do prefixo. Para saber mais sobre os SKUs do endereço IP público, confira [endereço IP público](./public-ip-addresses.md#public-ip-addresses).
- Os endereços do intervalo só podem ser atribuídos aos recursos do Azure Resource Manager. Os endereços não podem ser atribuídos a recursos no modelo de implantação clássico.
- Todos os endereços IP públicos criados a partir do prefixo devem existir na mesma região e assinatura do Azure que o prefixo. Os endereços devem ser atribuídos aos recursos na mesma região e assinatura.
- Você não poderá excluir um prefixo se houver endereços dentro dele atribuídos a recursos de endereço IP público associados a um recurso. Primeiro, desassocie todos os recursos de endereço IP público atribuídos a endereços IP do prefixo.


## <a name="next-steps"></a>Próximas etapas

- [Criar](manage-public-ip-address-prefix.md) um prefixo de endereço IP público
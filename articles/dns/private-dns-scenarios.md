---
title: Cenários de Zonas Privadas do DNS do Azure
description: Visão geral dos cenários comuns para usar Zonas Privadas do DNS do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: 409595febded7b242eae876ebb2cb35ae4999e5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686756"
---
# <a name="azure-dns-private-zones-scenarios"></a>Cenários de Zonas Privadas do DNS do Azure
As Zonas Privadas do DNS do Azure fornecem resolução de nomes em uma rede virtual, bem como entre redes virtuais. Neste artigo, vamos examinar alguns cenários comuns que podem ser obtidos usando esse recurso. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Cenário: Resolução de nomes com escopo para uma única rede virtual
Nesse cenário, você tem uma rede virtual no Azure que tem um número de recursos do Azure, incluindo máquinas virtuais (VMs). Você deseja resolver os recursos de dentro da rede virtual por meio de um nome de domínio específico (zona DNS) e você precisa que a resolução de nome seja privada e não acessível pela internet. Além disso, para as VMs na VNET, você precisa do Azure para registrá-los automaticamente na zona DNS. 

Este cenário é descrito abaixo. A Rede Virtual denominada "A" contém duas VMs (VNETA-VM1 e VNETA-VM2). Cada uma delas tem IPs privados associados. Depois de criar uma Zona Privada chamada contoso.com e vincular essa rede virtual como rede virtual do Registro, o DNS do Azure criará automaticamente dois registros A na zona conforme representado. Agora, as consultas DNS de VNETA-VM1 para resolver VNETA-VM2.contoso.com receberão uma resposta de DNS que contém o IP privado da VNETA-VM2. Além disso, uma consulta de DNS Reverso (PTR) para o IP privado de VNETA-VM1 (10.0.0.1) emitido de VNETA-VM2 receberá uma resposta DNS que contém o nome da VNETA-VM1, conforme esperado. 

![Resolução de rede virtual única](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Cenário: Resolução de nomes entre redes virtuais

Esse cenário é o caso mais comum em que você precisa associar uma Zona Privada a privada várias redes virtuais. Esse cenário pode ajustar arquiteturas, como o modelo Hub e Spoke em que há uma rede virtual Hub central a qual vários outras redes virtuais Spoke estão conectadas. A rede virtual Hub central pode ser vinculada como rede virtual do Registro a uma zona privada e as redes virtuais Spoke podem ser vinculadas como redes virtuais da Resolução. 

O diagrama a seguir mostra uma versão simples desse cenário em que há apenas duas redes virtuais - A e B. A é designada como rede virtual do Registro e B é designada como rede virtual de Resolução. A intenção é para ambas as redes virtuais compartilharem uma zona contoso.com comum. Quando a zona é criada e as redes virtuais de Resolução e Registro são vinculadas à zona, o Azure registra automaticamente os registros DNS para as VMs (VNETA-VM1 e VNETA-VM2) da rede virtual A. Você também pode adicionar manualmente os registros DNS na zona para VMs na rede virtual de Resolução B. Com essa configuração, você vai observar o seguinte comportamento para consultas DNS diretas e reversas:
* Uma consulta DNS da VNETB-VM1 na rede virtual de Resolução B, para VNETA-VM1.contoso.com, receberá uma resposta de DNS que contém o IP privado de VNETA-VM1.
* Uma consulta inversa de DNS (PTR) da VNETB-VM2 na rede virtual de Resolução B, para 10.1.0.1, receberá uma resposta de DNS que contém o FQDN VNETB-VM1.contoso.com. O motivo é que o escopo das consultas de DNS reverso é o mesmo da rede virtual. 
* Uma consulta de DNS reversa (PTR) de VNETB-VM3 na rede virtual de Resolução B, para 10.0.0.1, receberá NXDOMAIN. O motivo é que o escopo das consultas de DNS reverso é o mesmo da rede virtual. 


![Várias soluções de rede virtual](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Cenário: Funcionalidade de omissão de rotas

Nesse cenário, você tem um caso de uso em que você deseja obter o comportamento de resolução de DNS diferente dependendo de onde o cliente se encontra (dentro do Azure ou fora na internet), para a mesma zona DNS. Por exemplo, você pode ter uma versão pública e privada do seu aplicativo com diferente funcionalidade ou comportamento, mas você deseja usar o mesmo nome de domínio para ambas as versões. Esse cenário pode ser realizado com o DNS do Azure, criando uma Zona DNS Pública, bem como uma Zona Privada, com o mesmo nome.

O diagrama a seguir ilustra esse cenário. Você tem uma rede virtual A que tem duas VMs (VNETA-VM1 e VNETA-VM2) e ambas têm IPs privados e IPs público alocados. Crie uma zona DNS pública chamada contoso.com e registre os IPs públicos para essas VMs como registros de DNS na zona. Você também pode criar uma zona DNS privada também chamada contoso.com especificando A como a rede virtual do Registro. o Azure registra automaticamente as VMs como registros A na Zona Privada, apontando para os IPs privados.

Agora, quando um cliente da internet emitir uma consulta DNS para pesquisar VNETA-VM1.contoso.com, o Azure retornará o registro de IP público da zona pública. Se a mesma consulta DNS é emitida por outra VM (por exemplo: VNETA-VM2) na mesma rede virtual A, o Azure retornará o registro de IP privado da zona privada. 

![Resolução de dupla personalidade](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as zonas DNS privadas, consulte [Usar o Azure DNS para domínios privados](private-dns-overview.md).

Saiba como [criar uma zona DNS privada](./private-dns-getstarted-powershell.md) no DNS do Azure.

Saiba mais sobre zonas e registros DNS visitando: [Zonas e registros de visão geral do DNS](dns-zones-records.md).

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.


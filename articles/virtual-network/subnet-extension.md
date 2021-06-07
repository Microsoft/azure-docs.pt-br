---
title: Extensão de sub-rede no Azure | Microsoft Docs
description: Saiba mais sobre a extensão de sub-rede no Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: 555c903dc3375080a8b0d006d2aac7216854c939
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539291"
---
# <a name="subnet-extension"></a>Extensão de sub-rede
A migração de carga de trabalho para a nuvem pública requer planejamento e coordenação cuidadosos. Uma das principais considerações pode ser a capacidade de manter seus endereços IP. Isso pode ser importante especialmente se seus aplicativos tiverem dependência de endereço IP ou se você tiver requisitos de conformidade para usar endereços IP específicos. A rede virtual do Azure resolve esse problema para você, permitindo que você crie VNet e sub-redes usando um intervalo de endereços IP de sua escolha.

As migrações podem ser um pouco desafiadoras quando o requisito acima é combinado com um requisito adicional para manter alguns aplicativos locais. Nesse caso, você precisará dividir os aplicativos entre o Azure e o local, sem renumerar os endereços IP em ambos os lados. Além disso, você precisará permitir que os aplicativos se comuniquem como se estivessem na mesma rede.

Uma solução para o problema acima é a extensão de sub-rede. Estender uma rede permite que os aplicativos se comuniquem pelo mesmo domínio de transmissão quando existem em locais físicos diferentes, eliminando a necessidade de rearquitetar sua topologia de rede. 

Embora estender a rede não seja uma boa prática em geral, os casos de uso a seguir podem torná-lo necessário.

- **Migração em fases**: o cenário mais comum é que você deseja fazer a fase de migração. Você deseja colocar alguns aplicativos pela primeira vez e ao longo do tempo migrar o restante dos aplicativos para o Azure.
- **Latência**: os requisitos de baixa latência podem ser outro motivo para você manter alguns aplicativos locais para garantir que eles estejam o mais próximo possível do seu datacenter.
- **Conformidade**: outro caso de uso é que você pode ter requisitos de conformidade para manter alguns dos seus aplicativos locais.
 
> [!NOTE] 
> Você não deve estender suas sub-redes, a menos que seja necessário. Nos casos em que você estende suas sub-redes, você deve tentar torná-la uma etapa intermediária. Com o tempo, você deve tentar renumerar os aplicativos em sua rede local e migrá-los para o Azure.

Na próxima seção, discutiremos como você pode estender suas sub-redes para o Azure.


## <a name="extend-your-subnet-to-azure"></a>Estenda sua sub-rede para o Azure
 Você pode estender suas sub-redes locais para o Azure usando uma solução baseada em rede de sobreposição de camada 3. A maioria das soluções usa uma tecnologia de sobreposição, como VXLAN, para estender a rede de camada 2 usando uma rede de sobreposição de camada 3. O diagrama a seguir mostra uma solução generalizada. Nessa solução, a mesma sub-rede existe em ambos os lados, no Azure e no local. 

![Exemplo de extensão de sub-rede](./media/subnet-extension/subnet-extension.png)

Os endereços IP da sub-rede são atribuídos às VMs no Azure e no local. O Azure e o local têm um NVA inserido em suas redes. Quando uma VM no Azure tenta se comunicar com uma VM na rede local, o NVA do Azure captura o pacote, encapsula-o e o envia por meio de VPN/rota expressa para a rede local. O NVA local recebe o pacote, decapsulates-o e o encaminha para o destinatário pretendido em sua rede. O tráfego de retorno usa um caminho e uma lógica semelhantes.

No exemplo acima, o NVA do Azure e o NVA local se comunicam e aprendem os endereços IP por trás uns dos outros. Redes mais complexas também podem ter um serviço de mapeamento, que mantém o mapeamento entre o NVAs e os endereços IP por trás deles. Quando um NVA recebe um pacote, ele consulta o serviço de mapeamento para descobrir o endereço do NVA que tem o endereço IP de destino por trás dele.

Na próxima seção, você encontrará detalhes sobre as soluções de extensão de sub-rede que testamos no Azure.

## <a name="next-steps"></a>Próximas etapas 
[Estenda sua sub-rede para o Azure usando soluções de fornecedor.](https://github.com/microsoft/Azure-LISP)
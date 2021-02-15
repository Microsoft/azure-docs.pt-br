---
title: Solução de problemas de falhas de alocação de VM do Azure no modelo de implantação clássico | Microsoft Docs
description: Solução de problemas de falhas de alocação ao criar, reiniciar ou redimensionar uma VM clássica no Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77913371"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>As etapas de solução de problemas específicas de cenários de falha de alocação no modelo de implantação clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A seguir, os cenários de alocação comuns que fazem com que uma solicitação de alocação seja fixada. Vamos nos aprofundar em cada cenário posteriormente neste artigo.

- Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
- Reiniciar VMs parcialmente paradas (desalocadas)
- Reiniciar VMs totalmente paradas (desalocadas)
- Implantações de produção e de preparo (plataforma como serviço, somente)
- Grupo de afinidades (VM ou proximidade de serviço)
- Rede virtual baseada em grupo de afinidades

Ao receber um erro de alocação, verifique se algum dos cenários listados aplica-se ao seu erro. Use o erro de alocação que é retornado pela plataforma do Azure para identificar o cenário correspondente. Caso a solicitação seja fixada, remova algumas das restrições de fixação para abrir a solicitação para outros clusters e aumentar a probabilidade de êxito da alocação.
Em geral, se o erro não indicar que "não há suporte para o tamanho da VM solicitado", você poderá tentar novamente mais tarde. Isso ocorre porque recursos suficientes podem ter sido liberados no cluster para acomodar a solicitação. Se o problema for que o tamanho da VM solicitada não tem suporte, tente usar um tamanho de VM diferente. Caso contrário, a única opção é remover a restrição de fixação.

Dois cenários de falhas comuns estão relacionados aos grupos de afinidades. Anteriormente, o grupo de afinidades era usado para proporcionar uma estreita proximidade com as instâncias de serviço e VMs ou para permitir a criação de uma rede virtual. Com a introdução das redes virtuais regionais, não é mais necessário usar o grupo de afinidades para criar uma rede virtual. Com a redução da latência da rede na infraestrutura do Azure, a recomendação de uso de grupos de afinidades a proximidade de serviço e VM foi alterada.

O diagrama a seguir apresenta a taxonomia dos cenários de alocação (fixados). 

![Taxonomia de alocação fixada](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
**Erro**

Upgrade_VMSizeNotSupported ou GeneralError

**Causa de fixação de cluster**

A solicitação para redimensionar uma VM ou adicionar uma VM ou instância de função a um serviço de nuvem existente deve ser tentada no cluster original que hospeda o serviço de nuvem existente. A criação de um novo serviço de nuvem permite que a plataforma do Azure localize outro cluster com recursos livres ou que dê suporte para o tamanho de VM solicitado.

**Solução alternativa**

Se o erro for Upgrade_VMSizeNotSupported*, tente usar um tamanho de VM diferente. Quando não for possível usar um tamanho de VM diferente, mas for aceitável usar um endereço VIP (IP virtual) diferente, crie um novo serviço de nuvem para hospedar a nova VM e adicione-o à rede virtual regional na qual as VMs existentes estão em execução. Caso o serviço de nuvem existente não use a rede virtual regional, ainda é possível criar uma nova rede virtual para o novo serviço de nuvem e conectar sua [rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre as [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se o erro for GeneralError*, é provável que o cluster tenha suporte para o tipo de recurso (como um determinado tamanho de VM), embora não tenha recursos livres no momento. Semelhante ao cenário acima, adicione o recurso de computação desejado criando um novo serviço de nuvem (observe que o novo serviço de nuvem deve usar um VIP diferente) e use a rede virtual regional para conectar seus serviços de nuvem.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação parcial significa que você parou (desalocou) uma ou mais, mas não todas, VMs no serviço de nuvem. Quando você para (desaloca) uma VM, os recursos associados são liberados. A renicialização da VM parada (desalocada) é, portanto, uma nova solicitação de alocação. Reiniciar as VMs em um serviço de nuvem parcialmente desalocado é equivalente a adicionar VMs a um serviço de nuvem existente. A solicitação de alocação deve ser tentada no cluster original que hospeda o serviço de nuvem existente. A criação de um serviço de nuvem diferente permite que a plataforma do Azure localize outro cluster que tem recursos livres ou que dê suporte para o tamanho de VM solicitado.

**Solução alternativa**

Se for aceitável usar um VIP diferente, exclua as VMs paradas (desalocadas) (mas mantenha os discos associados) e adicione-as novamente através de um serviço de nuvem diferente. Use a rede virtual regional para se conectar aos serviços de nuvem:

* Caso o serviço de nuvem existente use uma rede virtual regional, basta adicionar o novo serviço de nuvem à mesma rede virtual.
* Caso o serviço de nuvem existente não use a rede virtual regional, crie uma nova rede virtual para o novo serviço de nuvem e [conecte sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre as [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs totalmente paradas (desalocadas)
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação total significa que você parou (desalocou) todas as VMs de um serviço de nuvem. As solicitações de alocação para reiniciar essas VMs devem ser tentadas no cluster original que hospeda o serviço de nuvem. A criação de um novo serviço de nuvem permite que a plataforma do Azure localize outro cluster com recursos livres ou que dê suporte para o tamanho de VM solicitado.

**Solução alternativa**

Se for aceitável usar um VIP diferente, exclua as VMs originais paradas (desalocadas) (mas mantenha os discos associados) e exclua o serviço de nuvem correspondente (os recursos de computação associados já foram liberados quando você parou ou desalocou as VMs). Crie um novo serviço de nuvem para adicionar novamente as VMs.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Implantações de preparo/produção (apenas plataforma como serviço)
**Erro**

New_General* ou New_VMSizeNotSupported\*

**Causa de fixação de cluster**

A implantação de preparo e a implantação de produção de um serviço de nuvem são hospedadas no mesmo cluster. Quando você adiciona a segunda implantação, a solicitação de alocação correspondente é tentada no mesmo cluster que hospeda a primeira implantação.

**Solução alternativa**

Exclua a primeira implantação e o serviço de nuvem original e reimplante o serviço de nuvem. Esta ação pode direcionar a primeira implantação para um cluster que dispõe de recursos livres adequados para as duas implantações ou que dê suporte para os tamanhos de VM solicitados.

## <a name="affinity-group-vmservice-proximity"></a>Grupo de afinidades (proximidade de serviço/VM)
**Erro**

New_General* ou New_VMSizeNotSupported\*

**Causa de fixação de cluster**

Os recursos de computação atribuídos a um grupo de afinidades estão vinculados a um único cluster. As novas solicitações de recursos de computação desse grupo de afinidades são tentadas no mesmo cluster que hospeda os recursos existentes. Isso ocorre se os novos recursos forem criados por meio de um novo serviço de nuvem ou um serviço de nuvem existente.

**Solução alternativa**

Se um grupo de afinidades não for necessário, não use o grupo de afinidades ou agrupe os recursos de computação em vários grupos de afinidades.

## <a name="affinity-group-based-virtual-network"></a>Rede virtual com base em grupo de afinidade
**Erro**

New_General* ou New_VMSizeNotSupported\*

**Causa de fixação de cluster**

Antes de as redes virtuais regionais serem introduzidas, era necessário associar uma rede virtual ao grupo de afinidades. Como resultado, os recursos de computação colocados em um grupo de afinidades são vinculados pelas mesmas restrições descritas na seção anterior, "Cenário de alocação: grupo de afinidades (proximidade de serviço/VM)". Os recursos de computação são vinculados a um cluster.

**Solução alternativa**

Caso você não precise de um grupo de afinidades, crie uma nova rede virtual regional para os novos recursos que está adicionando e [conecte sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre as [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Como alternativa, é possível [migrar a rede virtual baseada em grupo de afinidades para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e depois adicionar novamente os recursos desejados.



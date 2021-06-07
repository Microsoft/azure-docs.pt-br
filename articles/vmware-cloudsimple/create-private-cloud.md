---
title: Solução do Azure VMware por CloudSimple-criar nuvem privada do CloudSimple
description: Descreve como criar uma nuvem privada do CloudSimple para estender cargas de trabalho do VMware para a nuvem com flexibilidade operacional e continuidade
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2f4af4a36e719cbf15b3f0af77db81a32f2f2e42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896270"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Criar uma nuvem privada do CloudSimple

Uma nuvem privada é uma pilha VMware isolada que dá suporte a hosts ESXi, vCenter, vSAN e NSX. As nuvens privadas são gerenciadas por meio do portal do CloudSimple. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e nós de hardware bare-metal isolados.

A criação de uma nuvem privada ajuda a lidar com uma variedade de necessidades comuns de infraestrutura de rede:

* **Crescimento**. Se você atingiu um ponto de atualização de hardware para sua infraestrutura existente, uma nuvem privada permite que você se expanda sem nenhum novo investimento de hardware necessário.

* **Expansão rápida**. Se alguma necessidade de capacidade temporária ou não planejada surgir, uma nuvem privada permitirá que você crie a capacidade adicional sem atraso.

* **Maior proteção**. Com uma nuvem privada de três ou mais nós, você obtém redundância automática e proteção de alta disponibilidade.

* **Necessidades de infraestrutura de longo prazo**. Se os data centers estiverem na capacidade ou se você quiser reestruturar para reduzir os custos, uma nuvem privada permitirá que você desative os datacenters e migre para uma solução baseada em nuvem e, ao mesmo tempo, seja compatível com suas operações corporativas.

Ao criar uma nuvem privada, você obtém um único cluster vSphere e todas as VMs de gerenciamento criadas nesse cluster.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser provisionados antes que você possa criar sua nuvem privada. Para obter mais informações sobre os nós de provisionamento, consulte [provisionar nós para a solução do Azure VMware por CloudSimple](create-nodes.md).

Aloque um intervalo CIDR para sub-redes vSphere/vSAN para a nuvem privada. Uma nuvem privada é criada como um ambiente do VMware Stack isolado (com hosts ESXi, vCenter, vSAN e NSX) gerenciado por um servidor vCenter. Os componentes de gerenciamento são implantados na rede selecionada para o CIDR de sub-redes vSphere/vSAN. O intervalo de CIDR de rede é dividido em sub-redes diferentes durante a implantação. O espaço de endereço de sub-rede vSphere/vSAN deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais do Azure. Para obter mais informações sobre sub-redes vSphere/vSAN, consulte Visão geral de VLANs e sub-redes.

* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN mínima:/24
* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN máxima:/21


## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

Acesse o [portal da CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Criar uma nova nuvem privada

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Em **visão geral**, clique em **criar nuvem privada** para abrir uma nova guia do navegador para o portal do CloudSimple. Se solicitado, entre com suas credenciais de entrada do Azure.

    ![Criar nuvem privada do Azure](media/create-private-cloud-from-azure.png)

5. No portal do CloudSimple, forneça um nome para sua nuvem privada.
6. Selecione o **local** para sua nuvem privada.
7. Selecione o **tipo de nó**, consistente com o que você provisionou no Azure.
8. Especifique a **contagem de nós**.  Pelo menos três nós são necessários para criar uma nuvem privada.

    ![Criar nuvem privada-informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Avançar: opções avançadas**.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.

    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Um intervalo CIDR/24 dá suporte a até nove nós, um intervalo CIDR/23 dá suporte a até 41 nós, e um intervalo CIDR/22 e/21 dá suporte a até 64 nós (o número máximo de nós em uma nuvem privada).

    > [!IMPORTANT]
    > Os endereços IP no intervalo CIDR vSphere/vSAN são reservados para uso pela infraestrutura de nuvem privada.  Não use o endereço IP nesse intervalo em qualquer máquina virtual.

11. Clique em **Avançar: examinar e criar**.
12. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.
13. Clique em **Criar**.

O processo de provisionamento de nuvem particular é iniciado. Pode levar até duas horas para que a nuvem privada seja provisionada.

Para obter instruções sobre como expandir uma nuvem privada existente, consulte [expandir uma nuvem privada](expand-private-cloud.md).

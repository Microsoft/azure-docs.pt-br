---
title: Expanda a solução do Azure VMware da nuvem privada CloudSimple
description: Descreve como expandir uma nuvem privada CloudSimple existente para adicionar capacidade em um cluster novo ou existente
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c469ea3f104e71eb80010d4a5cd421eab6184fcf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895847"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expandir uma nuvem privada do CloudSimple

O CloudSimple fornece a flexibilidade para expandir dinamicamente uma nuvem privada. Você pode começar com uma configuração menor e, em seguida, expandir conforme precisar de maior capacidade. Ou você pode criar uma nuvem privada com base nas necessidades atuais e expandir conforme o consumo cresce.

Uma nuvem privada consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nós.  Ao expandir uma nuvem privada, você adiciona nós ao cluster existente ou cria um novo cluster. Para expandir um cluster existente, nós adicionais devem ser do mesmo tipo (SKU) que os nós existentes. Para criar um novo cluster, os nós podem ser de um tipo diferente. Para obter mais informações sobre limites de nuvem privada, consulte a seção limites no artigo [visão geral da nuvem privada do CloudSimple](cloudsimple-private-cloud.md) .

Uma nuvem privada é criada com um **datacenter** padrão no vCenter.  Cada datacenter serve como uma entidade de gerenciamento de nível superior.  Para um novo cluster, o CloudSimple fornece a opção de adicionar ao datacenter existente ou criar um novo datacenter.

Como parte da nova configuração de cluster, o CloudSimple configura a infra-estrutura do VMware.  As configurações incluem configurações de armazenamento para grupos de discos vSAN, alta disponibilidade do VMware e Distributed Resource Scheduler (DRS).

Uma nuvem privada pode ser expandida várias vezes. A expansão só pode ser feita quando você permanece dentro dos limites de nó gerais. Cada vez que você expande uma nuvem privada, você adiciona ao cluster existente ou cria um novo.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser provisionados antes que você possa expandir sua nuvem privada.  Para obter mais informações sobre os nós de provisionamento, consulte o artigo [provisionar nós para solução VMware por CloudSimple-Azure](create-nodes.md) .  Para criar um novo cluster, você deve ter pelo menos três nós disponíveis do mesmo SKU.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expandir uma nuvem privada

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos** e selecione a nuvem privada para a qual você deseja expandir.

3. Na seção Resumo, clique em **expandir**.

    ![Expandir nuvem privada](media/resources-expand-private-cloud.png)

4. Escolha se deseja expandir o cluster existente ou criar um novo cluster vSphere. Conforme você faz alterações, as informações resumidas na página são atualizadas.

    * Para expandir o cluster existente, clique em **expandir cluster existente**. Selecione o cluster que você deseja expandir e insira o número de nós a serem adicionados. Cada cluster pode ter um máximo de 16 nós.
    * Para adicionar um novo cluster, clique em **criar novo cluster**. Insira um nome para o cluster. Selecione um datacenter existente ou insira um nome para criar um novo datacenter. Escolha o tipo de nó. Você pode escolher um tipo de nó diferente ao criar um novo cluster vSphere, mas não ao expandir um cluster vSphere existente. Selecione o número de nós. Cada cluster novo deve ter pelo menos três nós.

    ![Expandir nuvem privada – adicionar nós](media/resources-expand-private-cloud-add-nodes.png)

5. Clique em **Enviar** para expandir a nuvem privada.

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)
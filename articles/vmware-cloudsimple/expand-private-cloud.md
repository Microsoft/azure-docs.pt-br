---
title: Expanda a solução VMware do Azure pela nuvem privada de CloudSimple
description: Descreve como expandir uma nuvem privada CloudSimple existente para adicionar capacidade em um cluster novo ou existente
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332636"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expanda uma nuvem privada de CloudSimple

CloudSimple fornece a flexibilidade para expandir dinamicamente de uma nuvem privada. Você pode começar com uma configuração de menor e, em seguida, expandir conforme você precisar de maior capacidade. Ou você pode criar uma nuvem privada com base em necessidades atuais e, em seguida, expanda à medida que aumenta de consumo.

Uma nuvem privada consiste em um ou mais clusters do vSphere. Cada cluster pode ter de 3 a 16 nós.  Ao expandir uma nuvem privada, você pode adicionar nós ao cluster existente ou cria um novo cluster. Para expandir um cluster existente, nós adicionais devem ser do mesmo tipo (SKU) que os nós existentes. Para criar um novo cluster, os nós podem ser de um tipo diferente. Para obter mais informações sobre os limites de nuvem privada, consulte limita a seção [visão geral de nuvem privada CloudSimple](cloudsimple-private-cloud.md) artigo.

Uma nuvem privada é criada com um padrão **Datacenter** no vCenter.  Cada datacenter serve como uma entidade de gerenciamento de nível superior.  Para um novo cluster, CloudSimple fornece a opção de adicionar o datacenter existente ou criar um novo data center.

Como parte da configuração do cluster novo, CloudSimple configura a infraestrutura do VMware.  As configurações incluem as configurações de armazenamento para grupos de discos vSAN, alta disponibilidade do VMware e Distributed Resource Scheduler (DRS).

Uma nuvem privada pode ser expandida várias vezes. Expansão pode ser feito somente quando você permanecer dentro dos limites do nó geral. Cada vez que você expandir uma nuvem privada que você adicionar ao cluster existente ou crie um novo.

## <a name="before-you-begin"></a>Antes de começar

Nós devem ser provisionados para expandir sua nuvem privada.  Para obter mais informações sobre o provisionamento de nós, consulte [provisionar nós para a solução VMware por CloudSimple - Azure](create-nodes.md) artigo.  Para criar um novo cluster, você deve ter pelo menos três nós disponíveis da mesma SKU.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expanda uma nuvem privada

1. [Acessar o portal CloudSimple](access-cloudsimple-portal.md).

2. Abra o **recursos** página e selecione a nuvem privada para o qual você deseja expandir.

3. Na seção de resumo, clique em **expandir**.

    ![Expanda a nuvem privada](media/resources-expand-private-cloud.png)

4. Escolha se deseja expandir o cluster existente ou criar um novo cluster do vSphere. Conforme você faz alterações, as informações na página de resumo são atualizadas.

    * Para expandir o cluster existente, clique em **expanda o cluster existente**. Selecione o cluster que você deseja expandir e insira o número de nós a serem adicionados. Cada cluster pode ter um máximo de 16 nós.
    * Para adicionar um novo cluster, clique em **criar novo cluster**. Insira um nome para o cluster. Selecione um datacenter existente ou digite um nome para criar um novo data center. Escolha o tipo de nó. Você pode escolher um tipo de nó diferente ao criar um novo cluster do vSphere, mas não ao expandir um cluster existente do vSphere. Selecione o número de nós. Cada novo cluster deve ter pelo menos três nós.

    ![Expanda a nuvem privada – adicionar nós](media/resources-expand-private-cloud-add-nodes.png)

5. Clique em **enviar** para expandir a nuvem privada.

## <a name="next-steps"></a>Próximas etapas

* [Consuma a VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
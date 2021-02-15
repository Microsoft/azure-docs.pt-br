---
title: Reduzir a solução VMware do Azure da nuvem privada CloudSimple
description: Saiba como reduzir dinamicamente uma nuvem privada no CloudSimple removendo um nó de um cluster existente do vSphere ou removendo um cluster inteiro.
author: Ajayan1008
ms.author: v-hborys
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a99b9b56f17b78a98f37d47dcefab26dd9c859de
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899126"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Reduzir uma nuvem privada CloudSimple

O CloudSimple fornece a flexibilidade para reduzir dinamicamente uma nuvem privada.  Uma nuvem privada consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nós. Ao reduzir uma nuvem privada, você remove um nó do cluster existente ou exclui um cluster inteiro. 

## <a name="before-you-begin"></a>Antes de começar

As condições a seguir devem ser atendidas para a redução de uma nuvem privada.  O cluster de gerenciamento (primeiro cluster) criado quando uma nuvem privada foi criada não pode ser excluído.

* Um cluster vSphere deve ter três nós.  Um cluster com apenas três nós não pode ser reduzido.
* O armazenamento total consumido não deve exceder a capacidade total após a redução do cluster.
* Verifique se alguma regra do Distributed Resource Scheduler (DRS) impede o vMotion de uma máquina virtual.  Se houver regras, desabilite ou exclua as regras.  As regras do DRS incluem a máquina virtual para hospedar regras de afinidade.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Reduzir uma nuvem privada

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos** .

3. Clique na nuvem privada que você deseja reduzir

4. Na página Resumo, clique em **reduzir**.

    ![Reduzir nuvem privada](media/shrink-private-cloud.png)

5. Selecione o cluster que você deseja reduzir ou excluir. 

    ![Reduzir nuvem privada-selecionar cluster](media/shrink-private-cloud-select-cluster.png)

6. Selecione **remover um nó** ou **excluir o cluster inteiro**. 

7. Verificar a capacidade do cluster

8. Clique em **Enviar** para reduzir a nuvem privada.

A redução da nuvem privada é iniciada.  Você pode monitorar o progresso em tarefas.  O processo de redução pode levar algumas horas, dependendo dos dados, que precisam ser ressincronizados no vSAN.

> [!NOTE]
> 1. Se você reduzir uma nuvem privada excluindo o último ou o único cluster no datacenter, o datacenter não será excluído.
> 2. Se ocorrer uma violação de regra de DRS, o nó não será removido do cluster e a descrição da tarefa mostrará que a remoção de um nó violará as regras do DRS no cluster.    


## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)

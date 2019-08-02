---
title: Reduzir a solução VMware do Azure pela nuvem privada de CloudSimple
description: Descreve como reduzir uma nuvem privada CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544511"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Reduzir uma nuvem privada de CloudSimple

CloudSimple fornece a flexibilidade para reduzir dinamicamente uma nuvem privada.  Uma nuvem privada consiste em um ou mais clusters do vSphere. Cada cluster pode ter de 3 a 16 nós. Ao reduzir uma nuvem privada, você pode remove um nó de cluster existente ou excluir um cluster inteiro. 

## <a name="before-you-begin"></a>Antes de começar

As condições a seguir deve ser atendida para redução de uma nuvem privada.  Gerenciamento de cluster (primeiro cluster) criado quando uma nuvem privada foi criada não pode ser excluída.

* Um cluster do vSphere deve ter três nós.  Não pode ser reduzido a um cluster com apenas três nós.
* Armazenamento total consumido não deve exceder a capacidade total após a redução do cluster. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Reduzir uma nuvem privada 

1. [Acessar o portal CloudSimple](access-cloudsimple-portal.md).

2. Abra o **recursos** página.

3. Clique na nuvem privada que deseja reduzir

4. Na página Resumo, clique em **reduzir**.

    ![Redução de nuvem privada](media/shrink-private-cloud.png)

5. Selecione o cluster que você deseja reduzir ou excluir. 

    ![Reduzir a nuvem privada - selecionar cluster](media/shrink-private-cloud-select-cluster.png)

6. Selecione **remover um nó** ou **excluir o cluster inteiro**. 

7. Verifique se a capacidade de cluster

8. Clique em **enviar** para reduzir a nuvem privada.

Inicia a redução da nuvem privada.  Você pode monitorar o progresso nas tarefas.  O processo de redução pode levar algumas horas dependendo dos dados, o que precisa ser resynced em vSAN.

## <a name="next-steps"></a>Próximas etapas

* [Consuma a VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
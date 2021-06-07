---
title: Solução do Azure VMware por CloudSimple-criar serviço CloudSimple
description: Saiba como criar o serviço CloudSimple no portal do Azure. Examine a configuração necessária antes de começar.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 53f2d0fc9f73985bd70792c8c3b7607eb4c560fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896287"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Criar a solução do Azure VMware pelo serviço CloudSimple

Para começar a usar a solução Azure VMware por CloudSimple, crie a solução Azure VMware pelo serviço CloudSimple no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR/28 para a sub-rede de gateway. Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada para serviços de rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
    ![Pesquisar serviço CloudSimple](media/create-cloudsimple-service-search.png)
3. Selecione **Serviços CloudSimples**.
4. Clique em **Adicionar** para criar um novo serviço.
    ![Adicionar serviço CloudSimple](media/create-cloudsimple-service-add.png)
5. Selecione a assinatura na qual você deseja criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o gateway de serviço. Especifique uma sub-rede/28 que não se sobreponha a nenhuma de suas sub-redes locais, sub-redes do Azure ou sub-redes CloudSimple planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço CloudSimple](media/create-cloudsimple-service.png)
9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [provisionar nós](create-nodes.md)
* Saiba como [criar uma nuvem privada](create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)

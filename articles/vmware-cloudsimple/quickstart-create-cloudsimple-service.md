---
title: 'Início rápido: criar serviço de CloudSimple do VMware'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como criar o serviço CloudSimple, nós de compra e nós de reserva
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507583"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Início rápido-criar solução VMware do Azure por serviço CloudSimple

Para começar, crie a solução Azure VMware de CloudSimple no portal do Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Visão geral da solução VMware por CloudSimple-Service

O serviço CloudSimple permite que você consuma a solução do Azure VMware pelo CloudSimple.  A criação do serviço permite provisionar nós, reservar nós e criar nuvens privadas.  Você adiciona o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível.  O serviço define a rede de borda da solução do Azure VMware por CloudSimple.  Essa rede de borda é usada para serviços que incluem conectividade VPN, ExpressRoute e Internet para suas nuvens privadas.

Para adicionar o serviço CloudSimple, você deve criar uma sub-rede de gateway. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não pode se sobrepor a nenhum dos espaços de endereço de rede local nem do espaço de endereço de rede virtual do Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR/28 para a sub-rede de gateway.  Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada para a solução do Azure VMware por serviços de rede do CloudSimple Edge e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple.  As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais do Azure.

Examine os [pré-requisitos de rede](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure o **serviço CloudSimple**.

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

## <a name="provision-nodes"></a>Provisionar nós

Para configurar a capacidade paga conforme o uso para um ambiente de nuvem privada do CloudSimple, primeiro provisionar nós na portal do Azure.

1. Selecione **Todos os serviços**.
2. Procure **nós CloudSimple**.

    ![Pesquisar nós do CloudSimple](media/create-cloudsimple-node-search.png)

3. Selecione **nós CloudSimple**.
4. Clique em **Adicionar** para criar nós.

    ![Adicionar nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a assinatura em que você deseja provisionar nós CloudSimple.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o prefixo para identificar os nós.
8. Selecione o local dos recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o [tipo de nó](cloudsimple-node.md).
11. Selecione o número de nós a serem provisionados.
12. Selecione **Examinar + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

* [Criar nuvem privada e configurar o ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre o [serviço CloudSimple](./cloudsimple-service.md)

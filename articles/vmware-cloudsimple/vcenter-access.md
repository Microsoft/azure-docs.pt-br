---
title: Solução do Azure VMware por cliente do vSphere CloudSimple-Access
description: Descreve como acessar o vCenter de sua nuvem privada.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899109"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Acessar seu portal particular do vCenter na nuvem

Você pode iniciar seu portal de vCenter de nuvem privada no portal do Azure ou no portal CloudSimple.  o portal do vCenter permite que você gerencie a infraestrutura do VMware em sua nuvem privada.

## <a name="before-you-begin"></a>Antes de começar

A conexão de rede deve ser estabelecida e a resolução de nomes DNS deve ser habilitada para acessar o portal do vCenter.  Você pode estabelecer a conexão de rede com sua nuvem privada usando qualquer uma das opções a seguir.

* [Conectar de local para CloudSimple usando o ExpressRoute](on-premises-connection.md)
* [Configurar uma conexão VPN para sua nuvem privada do CloudSimple](set-up-vpn.md)

Para configurar a resolução de nomes DNS de seus componentes de infraestrutura privada do VMware Infrastructure, consulte [Configurar o DNS para resolução de nomes para acesso de vCenter de nuvem privada de estações de trabalho locais](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Acesse o vCenter de portal do Azure

Você pode iniciar o portal do vCenter de sua nuvem privada de portal do Azure.

1. Selecione **Todos os serviços**.

2. Procure **Serviços CloudSimples**.

3. Selecione o serviço CloudSimple da sua nuvem privada à qual você deseja se conectar.

4. Na página **visão geral** , clique em **Exibir nuvens privadas do VMware**

    ![Visão geral do serviço CloudSimple](media/cloudsimple-service-overview.png)

5. Selecione a nuvem privada na lista de nuvens privadas e clique em **Iniciar vSphere cliente**.

    ![Iniciar cliente do vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Acesse o vCenter no portal do CloudSimple

Você pode iniciar o portal do vCenter de sua nuvem privada no portal do CloudSimple.

1. Acesse seu [portal do CloudSimple](access-cloudsimple-portal.md).

2. Nos **recursos** , selecione a nuvem privada que você deseja acessar e clique em **Iniciar cliente do vSphere**.

    ![Iniciar vSphere cliente-recursos](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Você também pode iniciar o portal do vCenter na tela de Resumo de sua nuvem privada.

    ![Iniciar vSphere cliente-Resumo](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar VLANs/sub-redes para suas nuvens privadas](create-vlan-subnet.md)
* [Modelo de permissão de nuvem privada do CloudSimple do VMware vCenter](learn-private-cloud-permissions.md)
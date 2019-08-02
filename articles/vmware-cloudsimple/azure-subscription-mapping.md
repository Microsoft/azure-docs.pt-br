---
title: Mapear a assinatura do Azure para pools de recursos na solução de VMware do Azure por CloudSimple
description: Descreve como mapear um pool de recursos na solução de VMware do Azure por CloudSimple para sua assinatura do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333094"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapear pools de recursos da nuvem privada para sua assinatura do Azure

Mapeamento de assinatura do Azure permite que você mapeie os pools de recursos do vCenter sua nuvem privada para sua assinatura do Azure. Você pode mapear somente a assinatura em que você criou o serviço CloudSimple.  Criar uma máquina virtual VMware no portal do Azure implanta a máquina virtual no pool de recursos mapeada.  No portal do CloudSimple, você pode exibir e gerenciar a assinatura do Azure para suas nuvens privadas.

Uma assinatura pode ser mapeada para vários pools de recursos do vCenter de uma nuvem privada.  É necessário mapear os pools de recursos de cada nuvem privada.  Apenas os pools de recursos mapeadas estarão disponíveis para a criação de uma máquina virtual VMware do portal do Azure.

> [!IMPORTANT]
> Também é um pool de recursos de mapeamento mapeia quaisquer pools de recursos filho. Um pool de recursos pai não pode ser mapeado se quaisquer pools de recursos filho já são mapeados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tiver um serviço de CloudSimple e a nuvem privada em sua assinatura.  Para criar um serviço CloudSimple, consulte [início rápido – criar serviço](quickstart-create-cloudsimple-service.md).  Se você precisar criar uma nuvem privada, consulte [início rápido - configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md).

Você pode mapear o cluster do vCenter (pool de recursos de raiz) para sua assinatura.  Se você quiser criar um novo pool de recursos, consulte [criar um Pool de recursos](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) artigo no site de documentação do VMware.

## <a name="default-resource-group"></a>Grupo de recursos padrão

Criar uma nova máquina virtual CloudSimple do portal do Azure permite que você selecione o grupo de recursos.  Uma máquina virtual criada no vCenter de nuvem privada em um pool de recursos mapeada poderão ser vista no portal do Azure.  A máquina virtual descoberta será colocada no grupo de recursos do Azure padrão.  Você pode alterar o nome do grupo de recursos padrão.

## <a name="map-azure-subscription"></a>Mapear a assinatura do Azure

1. Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

2. Abra o **recursos** página e selecione a nuvem privada que você deseja mapear.

3. Selecione **as assinaturas do Azure mapeando**.

4. Clique em **mapeamento de assinatura do Azure editar**.

5. Para mapear os pools de recursos disponíveis, selecione-os à esquerda e clique na seta para a direita.

6. Para remover os mapeamentos, selecioná-los à direita e clique na seta para a esquerda.

    ![Assinaturas do Azure](media/resources-azure-mapping.png)

7. Clique em **OK**.

## <a name="change-default-resource-group-name"></a>Nome do grupo de recursos de padrão de alteração

1. Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

2. Abra o **recursos** página e selecione a nuvem privada que você deseja mapear.

3. Selecione **as assinaturas do Azure mapeando**.

4. Clique em **editar** em nome do grupo de recursos do Azure.

    ![Editar o nome do grupo de recursos](media/resources-edit-resource-group-name.png)

5. Insira um novo nome para o grupo de recursos e clique em **enviar**.

    ![Insira o nome do novo grupo de recursos](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Próximas etapas

* [Consuma a VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [CloudSimple máquinas de virtuais](cloudsimple-virtual-machines.md)
---
title: Conectar-se a uma conta de armazenamento seguro do seu espaço de trabalho Synapse do Azure
description: Este artigo ensinará como se conectar a uma conta de armazenamento seguro de seu espaço de trabalho Synapse do Azure
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674312"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Conectar-se a uma conta de armazenamento do Azure segura do seu espaço de trabalho Synapse

Este artigo ensinará como se conectar a uma conta de armazenamento do Azure segura do seu espaço de trabalho Synapse do Azure. Você pode vincular uma conta de armazenamento do Azure ao seu espaço de trabalho do Synapse ao criar seu espaço de trabalho. Você pode vincular mais contas de armazenamento depois de criar seu espaço de trabalho.


## <a name="secured-azure-storage-accounts"></a>Contas de armazenamento do Azure protegidas
O armazenamento do Azure fornece um modelo de segurança em camadas que permite que você proteja e controle o acesso às suas contas de armazenamento. Você pode configurar regras de firewall IP para conceder ao tráfego de intervalos de endereços IP públicos selecionados acesso à sua conta de armazenamento. Você também pode configurar regras de rede para conceder ao tráfego de redes virtuais selecionadas acesso à sua conta de armazenamento. Você pode combinar regras de firewall IP que permitem o acesso de intervalos de endereços IP e regras de rede selecionados que concedem acesso de redes virtuais selecionadas na mesma conta de armazenamento. Essas regras se aplicam ao ponto de extremidade público de uma conta de armazenamento. Você não precisa de nenhuma regra de acesso para permitir o tráfego de pontos de extremidade privados gerenciados criados em seu espaço de trabalho para uma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas a contas de armazenamento existentes ou a novas contas de armazenamento ao criá-las. Você pode saber mais sobre como proteger sua conta de armazenamento [aqui](../../storage/common/storage-network-security.md).

## <a name="synapse-workspaces-and-virtual-networks"></a>Espaços de trabalho Synapse e redes virtuais
Ao criar um espaço de trabalho do Synapse, você pode optar por habilitar uma rede virtual gerenciada a ser associada a ela. Se você não habilitar a rede virtual gerenciada para seu espaço de trabalho ao criá-la, seu espaço de trabalho estará em uma rede virtual compartilhada junto com outros espaços de trabalho do Synapse que não têm uma rede virtual gerenciada associada a ela. Se você tiver habilitado a rede virtual gerenciada quando criou o espaço de trabalho, o espaço de trabalho será associado a uma rede virtual dedicada gerenciada pelo Azure Synapse. Essas redes virtuais não são criadas na sua assinatura de cliente. Portanto, você não poderá conceder ao tráfego dessas redes virtuais acesso à sua conta de armazenamento protegido usando as regras de rede descritas acima.  

## <a name="access-a-secured-storage-account"></a>Acessar uma conta de armazenamento protegida
O Synapse Opera de redes que não podem ser incluídas em suas regras de rede. É necessário fazer o seguinte para habilitar o acesso do seu espaço de trabalho à sua conta de armazenamento seguro.

* Criar um espaço de trabalho Synapse do Azure com uma rede virtual gerenciada associada a ele e criar pontos de extremidade privados gerenciados a partir dele para a conta de armazenamento seguro
* Conceda ao seu espaço de trabalho Synapse do Azure acesso à sua conta de armazenamento seguro como um serviço do Azure confiável. Como um serviço confiável, o Azure Synapse usará uma autenticação forte para se conectar com segurança à sua conta de armazenamento.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Criar um espaço de trabalho do Synapse com uma rede virtual gerenciada e criar pontos de extremidade privados gerenciados para sua conta de armazenamento
Você pode seguir [estas etapas](./synapse-workspace-managed-vnet.md) para criar um espaço de trabalho do Synapse que tenha uma rede virtual gerenciada associada a ele. Depois que o espaço de trabalho com uma rede virtual gerenciada associada é criado, você pode criar um ponto de extremidade privado gerenciado para sua conta de armazenamento seguro seguindo as etapas listadas [aqui](./how-to-create-managed-private-endpoints.md). 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Conceda ao seu espaço de trabalho Synapse do Azure acesso à sua conta de armazenamento seguro como um serviço do Azure confiável
Recursos analíticos como pool de SQL dedicado e pool SQL sem servidor usam a infraestrutura multilocatário que não é implantada na rede virtual gerenciada. Para o tráfego desses recursos para acessar a conta de armazenamento protegido, você deve configurar o acesso à sua conta de armazenamento com base na identidade gerenciada atribuída pelo sistema do espaço de trabalho seguindo as etapas abaixo.

Em portal do Azure, navegue até sua conta de armazenamento protegido. Selecione **rede** no painel de navegação à esquerda. Na seção **instâncias de recurso** , selecione *Microsoft. Synapse/Workspaces* como o **tipo de recurso** e insira o nome do espaço de trabalho para o **nome da instância**. Clique em **Salvar**.

![Configuração de rede da conta de armazenamento.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Agora você deve ser capaz de acessar sua conta de armazenamento protegido no espaço de trabalho.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [rede virtual de espaço de trabalho gerenciado](./synapse-workspace-managed-vnet.md).

Saiba mais sobre [pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md).
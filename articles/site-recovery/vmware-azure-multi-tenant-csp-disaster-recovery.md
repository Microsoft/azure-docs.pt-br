---
title: Configurar a recuperação de desastre do VMware no Azure em um ambiente multilocatário usando o Site Recovery e o programa CSP (Provedor de Solução de Nuvem) | Microsoft Docs
description: Descreve como configurar a recuperação de desastres do VMware em um ambiente multilocatário com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654942"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurar a recuperação de desastre do VMware em um ambiente multilocatário com o programa CSP (Provedor de Solução de Nuvem)

O [programa CSP](https://partner.microsoft.com/cloud-solution-provider) promove histórias melhores para os serviços em nuvem da Microsoft, incluindo Microsoft 365, Enterprise Mobility Suite e Microsoft Azure. Com o CSP, os parceiros controlam o relacionamento de ponta a ponta com os clientes e se tornam o principal ponto de contato desse relacionamento. Os parceiros podem implantar as assinaturas do Azure para os clientes e combiná-las com suas próprias ofertas personalizadas de valor agregado.

Com o [Azure Site Recovery](site-recovery-overview.md), como parceiros, você pode gerenciar a recuperação de desastre para os clientes diretamente por meio de do CSP. Como alternativa, você pode usar o CSP para configurar ambientes de Site Recovery e permitir que os clientes gerenciem suas próprias necessidades de recuperação de desastre como autoatendimento. Em ambos os cenários, os parceiros são o elo entre o Site Recovery e seus clientes. Os parceiros cuidam do relacionamento com o cliente e cobram pelo uso do Site Recovery.

Este artigo descreve como você como um parceiro pode criar e gerenciar assinaturas de locatários por meio do CSP, para um cenário de replicação do VMware multilocatário.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a replicação do VMware, você precisa fazer o seguinte:

- [Preparar](tutorial-prepare-azure.md) recursos do Azure, incluindo uma assinatura do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- [Preparar](vmware-azure-tutorial-prepare-on-premises.md) as VMs e os servidores VMware locais.
- Para cada locatário, crie um servidor de gerenciamento separado que possa se comunicar com as VMs do locatário e seus servidores vCenter. Somente você como um parceiro deve ter direitos de acesso ao servidor de gerenciamento. Saiba mais sobre [ambientes multilocatários](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Criar uma conta de locatário

1. Entre na sua conta do CSP por meio do [Microsoft Partner Center](https://partnercenter.microsoft.com/).
2. No menu **Painel**, selecione **Clientes**.
3. Na página que se abre, clique no botão **Adicionar cliente**.
4. Na página **Novo Cliente**, preencha todos os detalhes de informações da conta para o locatário.

    ![A página Informações da Conta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Em seguida, clique em **Próximo: assinaturas**.
6. Na página de seleção de assinaturas, marque a caixa de seleção **Microsoft Azure**. Você pode adicionar outras assinaturas agora ou a qualquer momento.
7. Na página **Revisão**, confirme os detalhes do locatário e clique em **Enviar**.
8. Depois de criar a conta do locatário, uma página de confirmação é mostrada exibindo os detalhes da conta e a senha padrão para essa assinatura. Salve as informações e altere a senha mais tarde, conforme necessário, na página de Logon do Portal do Azure.

Você pode compartilhar essas informações dessa forma com o locatário ou criar e compartilhar uma conta separada, se necessário.

## <a name="access-the-tenant-account"></a>Acessar a conta de locatário

Você pode acessar a assinatura do locatário por meio do Painel do Microsoft Partner Center.

1. Na página **Clientes**, clique no nome da conta de locatário.
2. Na página **Assinaturas** da conta do locatário, você pode monitorar as assinaturas existentes da conta e adicionar mais assinaturas, conforme necessário.
3. Para gerenciar operações de recuperação de desastre do locatário, selecione **Todos os recursos (Portal do Azure)**. Isso concede a você o acesso às assinaturas do Azure do locatário.

    ![O link Todos os Recursos](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Você pode verificar o acesso clicando no link do Azure Active Directory na parte superior direita do Portal do Azure.

    ![Link do Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Agora você pode executar e gerenciar todas as operações do Site Recovery do locatário no portal do Azure. Para acessar a assinatura do locatário por meio de CSP para recuperação de desastre gerenciada, siga o processo descrito anteriormente.

## <a name="assign-tenant-access-to-the-subscription"></a>Atribuir acesso do locatário à assinatura

1. Certifique-se de que a infra-estrutura de recuperação de desastres está configurada. Parceiros acessam as assinaturas de locatários por meio do portal CSP, independentemente de se a recuperação de desastres for gerenciada ou autoatendimento. Configurar seu cofre e registrar a infraestrutura para as assinaturas do locatário.
1. Forneça o locatário com a [conta que você criou](#create-a-tenant-account).
1. Você pode adicionar um novo usuário à assinatura de locatário por meio do portal do CSP da seguinte maneira:

    1. Acesse a página da assinatura do CSP do locatário e selecione a opção **Usuários e licenças**.

       ![A página da assinatura do CSP do locatário](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Agora, crie um novo usuário inserindo os detalhes relevantes e selecionando permissões ou carregando a lista de usuários em um arquivo CSV.

    1. Depois de criar um novo usuário, volte para a portal do Azure. Na página **Assinatura**, selecione a assinatura relevante.

    1. Selecione **controle de acesso (iam)** e clique em **atribuições de função**.

    1. Clique em **Adicionar atribuição de função** para adicionar um usuário com o nível de acesso relevante. Os usuários que foram criados por meio do portal do CSP são exibidos na guia Atribuições de função.

        ![Adicionar um usuário](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Para a maioria das operações de gerenciamento, a função de *Colaborador* é suficiente. Usuários com esse nível de acesso podem fazer tudo em uma assinatura, exceto alterar os níveis de acesso (para tal é necessário o nível de acesso *Proprietário*).
- O Site Recovery também tem três [funções de usuário predefinidas](site-recovery-role-based-linked-access-control.md) que podem ser usadas para restringir ainda mais os níveis de acesso conforme o necessário.

## <a name="multi-tenant-environments"></a>Ambientes multilocatário

Há três modelos principais de multilocatários:

* **HSP (Provedor de Serviços de Hospedagem Compartilhada)**: o parceiro é o proprietário da infraestrutura física e usa recursos compartilhados (vCenter, datacenters, armazenamento físico e assim por diante) para hospedar VMs de vários locatários na mesma infraestrutura. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode ter recuperação de desastre como uma solução de autoatendimento.

* **Provedor de Serviços de Hospedagem Dedicado**: o parceiro tem a infraestrutura física, mas usa recursos dedicados (vários vCenters, repositórios de dados físicos e assim por diante) para hospedar VMs de cada locatário em uma infraestrutura separada. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode já tê-la como uma solução de autoatendimento.

* **MSP (Provedor de Serviços Gerenciado)**: o cliente tem a infraestrutura física que hospeda as VMs e o parceiro fornece a habilitação e gerenciamento de recuperação de desastre.

Ao configurar assinaturas de locatários, conforme descrito neste artigo, você pode iniciar rapidamente permitindo os clientes em qualquer um dos modelos de multilocatários relevantes. Você pode aprender mais sobre os diferentes modelos de multilocatários e habilitar o controles de acesso no local [aqui](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o Azure [RBAC (controle de acesso baseado em função)](site-recovery-role-based-linked-access-control.md) para gerenciar implantações de Azure site Recovery.
- Saiba mais sobre a arquitetura de [replicação](vmware-azure-architecture.md)do VMware para o Azure.
- [Revise o tutorial](vmware-azure-tutorial.md) para replicar VMs VMware no Azure.
Saiba mais sobre [ambiantes multilocatários](vmware-azure-multi-tenant-overview.md) para replicar VMware VMs para Azure.

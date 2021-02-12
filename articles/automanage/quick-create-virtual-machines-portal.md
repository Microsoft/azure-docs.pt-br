---
title: Início rápido – Habilitar o Gerenciamento Automático do Azure para VMs no portal do Azure
description: Saiba como habilitar rapidamente o Gerenciamento Automático para máquinas virtuais em uma VM nova ou existente no portal do Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 69f43b626bb50171ceaca1b7a8761bec040d1dd6
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897221"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Início Rápido: Habilitar o gerenciamento do Azure para máquinas virtuais no portal do Azure

Introdução ao Gerenciamento Automático do Azure para máquinas virtuais usando o portal do Azure para habilitar o gerenciamento em uma máquina virtual nova ou existente.


## <a name="prerequisites"></a>Pré-requisitos

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de avaliação gratuita não têm acesso às máquinas virtuais usadas neste tutorial. Atualize para uma assinatura de Pagamento Conforme o Uso.

> [!IMPORTANT]
> Você precisa ter a função de **Colaborador** no grupo de recursos que contém suas VMs para habilitar o Gerenciamento Automatizado usando uma Conta de Gerenciamento Automatizado existente. Se você estiver habilitando o Gerenciamento Automático com uma nova Conta de Gerenciamento Automático, precisará das seguintes permissões: Função de **Proprietário** ou **Colaborador** junto com funções de **Administrador de Acesso do Usuário** em sua assinatura.


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Habilitar o Gerenciamento Automático para VMs em uma VM existente

1. Na barra de pesquisa, pesquise e selecione **o Gerenciamento Automático – melhores práticas de máquina virtual do Azure**.

2. Selecione **Habilitar em VM existente**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Habilitar na VM existente.":::

3. Na folha **Selecionar computadores** :
    1. Filtre a lista de VMs pela sua **Assinatura** e **Grupo de Recursos**.
    1. Marque a caixa de seleção de cada máquina virtual que você deseja integrar.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

4. Em **Perfil de configuração** , clique em **Procurar e alterar perfis e preferências**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Procurar e alterar perfis e preferências.":::

5. Na folha **Selecionar perfil de configuração + preferências** :
    1. Selecione um perfil à esquerda: *Desenvolvimento/Teste* para teste, *Prod* para produção.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Procurar perfil de configuração de produção.":::

6. Clique no botão **Habilitar**.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Habilitar o Gerenciamento Automático para VMs em uma nova VM

Entre no portal do Azure [aqui](https://aka.ms/automanageportalnextstep) para criar uma VM e habilitar o Gerenciamento Automático.

1. Siga as etapas de criação em [Início rápido – criar uma VM do Windows no portal do Azure](..\virtual-machines\windows\quick-create-portal.md).

2. Depois que a VM for implantada, você chegará à página status da implantação que tem as **próximas etapas** recomendadas na parte inferior.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Seção de próximas etapas localizada na parte inferior da página de implantação.":::

3. Em **Próximas etapas** , selecione **Habilitar Gerenciamento Automático – melhores práticas de máquina virtual**.

4. Na página **Gerenciamento Automático – melhores práticas de máquina virtual do Azure** , **Computadores** será preenchido automaticamente pela sua VM que acaba de ser criada.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="A VM que acaba de ser criada aparecerá como o computador selecionado.":::

5. Em **Perfil de configuração** , clique em **Procurar e alterar perfis e preferências**.

6. Na folha **Selecionar perfil de configuração + preferências** :
    1. Selecione um perfil à esquerda: *Desenvolvimento/Teste* para teste, *Prod* para produção.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Procurar perfil de configuração de produção.":::

7. Clique no botão **Habilitar**.

## <a name="disable-automanage-for-vms"></a>Desabilitar o Gerenciamento Automático para VMs

Pare rapidamente de usar o Gerenciamento Automático do Azure para máquinas virtuais desabilitando o gerenciamento automático.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Desabilitar o Gerenciamento Automático em uma máquina virtual.":::

1. Acesse a página **Gerenciamento Automático – melhores práticas de máquina virtual do Azure** , que lista todas as suas VMs gerenciadas automaticamente.
1. Marque a caixa de seleção ao lado da máquina virtual que você deseja desabilitar.
1. Clique no botão **Desabilitar gerenciamento automático**.
1. Leia atentamente as mensagens no pop-up resultante antes de concordar em **Desabilitar**.


## <a name="clean-up-resources"></a>Limpar os recursos

Caso você tenha criado um grupo de recursos para experimentar o Gerenciamento Automático do Azure para máquinas virtuais e não precise mais dele, pode excluí-lo. Excluir o grupo exclui também a VM e todos os recursos no grupo de recursos.

O Gerenciamento Automático do Azure cria grupos de recursos padrão nos quais para armazenar recursos. Verifique os grupos de recursos que têm a convenção de nomenclatura "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" para limpar todos os recursos.

1. Selecione o **Grupo de recursos**.
1. Na página do grupo de recursos, selecione **Excluir**.
1. Quando solicitado, confirme o nome do grupo de recursos e depois selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você habilitou o Gerenciamento Automático do Azure para VMs. 

Descubra como você pode criar e aplicar preferências personalizadas ao habilitar o Gerenciamento Automático em sua máquina virtual. 

> [!div class="nextstepaction"]
> [Gerenciamento Automático do Azure para VMS – perfil de configuração personalizada](virtual-machines-custom-preferences.md)

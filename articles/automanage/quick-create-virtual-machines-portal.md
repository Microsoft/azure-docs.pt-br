---
title: Início rápido – Habilitar o Gerenciamento Automático do Azure para VMs no portal do Azure
description: Saiba como habilitar rapidamente o Gerenciamento Automático para máquinas virtuais em uma VM nova ou existente no portal do Azure.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 7121d83f9401fe985966324afe6a61cf8396b2bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534076"
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

Entre no [portal do Azure](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Habilitar o Gerenciamento Automatizado em uma VM individual

1. Procure a Máquina Virtual que deseja habilitar.

2. Clique na entrada **Gerenciamento Automatizado (Versão prévia)** no Sumário em **Operações**.

3. Selecione **Introdução**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Introdução à VM individual.":::

4. Escolha suas configurações de Gerenciamento Automatizado (ambiente, preferências, conta do Gerenciamento Automatizado) e clique em **Habilitar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Habilitação em uma VM individual.":::

## <a name="enable-automanage-for-multiple-vms"></a>Habilitar o Gerenciamento Automatizado em várias VMs

1. Na barra de pesquisa, procure e selecione **Gerenciamento Automatizado – Melhores práticas para computadores do Azure**.

2. Selecione **Habilitar em VM existente**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Habilitar na VM existente.":::

3. Na folha **Selecionar computadores**:
    1. Filtre a lista de VMs pela sua **Assinatura** e **Grupo de Recursos**.
    1. Marque a caixa de seleção de cada máquina virtual que você deseja integrar.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

4. Em **Ambiente**, selecione o tipo de ambiente: **Desenvolvimento/Teste** ou **Produção**. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Selecione os ambientes.":::

   Clique em **Comparar Detalhes do Ambiente** para ver as diferenças entre os ambientes.
    1. Selecione um ambiente no menu suspenso: *Desenvolvimento/Teste* para teste, *Produção* para produção.
    1. Clique no botão **OK**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Procurar ambiente de produção.":::

5. Por padrão, a preferência **Práticas Recomendadas do Azure** é selecionada para as preferências de configuração. Para alterar isso, crie uma nova preferência ou selecione uma existente. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Criar preferência.":::

6. Clique no botão **Habilitar**.


## <a name="enable-automanage-for-a-new-vm"></a>Habilitar o Gerenciamento Automatizado em uma nova VM

Entre no portal do Azure [aqui](https://aka.ms/AzureAutomanagePreview) para criar uma VM e habilitar o Gerenciamento Automático.

1. Preencha a guia **Informações Básicas** com os detalhes da VM.

> [!NOTE]
> Verifique as [regiões com suporte](automanage-virtual-machines.md#supported-regions) do Gerenciamento Automatizado e as [distribuições Linux](automanage-linux.md#supported-linux-distributions-and-versions) e [versões do Windows Server](automanage-windows-server.md#supported-windows-server-versions) com suporte do Gerenciamento Automatizado.

2. Procure a guia **Gerenciamento** e escolha seu **Ambiente de Gerenciamento Automatizado**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Habilitar o Gerenciamento Automatizado na guia Gerenciamento.":::

3. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.

4. Quando você receber a mensagem informando que a validação foi aprovada, selecione **Criar**.

## <a name="disable-automanage-for-vms"></a>Desabilitar o Gerenciamento Automático para VMs

Pare rapidamente de usar o Gerenciamento Automático do Azure para máquinas virtuais desabilitando o gerenciamento automático.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Desabilitar o Gerenciamento Automático em uma máquina virtual.":::

1. Acesse a página **Gerenciamento Automático – melhores práticas de máquina virtual do Azure**, que lista todas as suas VMs gerenciadas automaticamente.
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
> [Gerenciamento Automatizado do Azure para VMs – Preferências personalizadas de configuração](virtual-machines-custom-preferences.md)

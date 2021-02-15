---
title: Criar uma preferência personalizada no autogerenciamento do Azure para VMs
description: Saiba como ajustar o perfil de configuração no Azure automanage para VMs e definir suas próprias preferências.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 377677c9e5e81487059241db68baff639a3de033
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715035"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Criar uma preferência personalizada no autogerenciamento do Azure para VMs

O autogerenci do Azure para práticas recomendadas de máquina virtual tem perfis de configuração padrão que podem ser ajustados, se necessário. Este artigo explicará como você pode definir suas próprias preferências de perfil de configuração ao habilitar o autogerenciamento em uma VM nova ou existente.

Atualmente, damos suporte a personalizações no [backup do Azure](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) e [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Você não pode alterar o perfil de configuração ou a preferência em sua VM enquanto o autogerenci está habilitado. Será necessário desabilitar o autogerenci para essa VM e reabilitar o autogerenci com o perfil de configuração desejado e as preferências.


## <a name="prerequisites"></a>Pré-requisitos

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de avaliação gratuita não têm acesso às máquinas virtuais usadas neste tutorial. Atualize para uma assinatura de Pagamento Conforme o Uso.

> [!IMPORTANT]
> A permissão do RBAC do Azure a seguir é necessária para habilitar o autogerenci: função de **proprietário** ou **colaborador** junto com as funções de **administrador de acesso do usuário** .


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Habilitar o Gerenciamento Automático para VMs em uma VM existente

1. Na barra de pesquisa, pesquise e selecione **o Gerenciamento Automático – melhores práticas de máquina virtual do Azure**.

2. Selecione **Habilitar em VM existente**.

3. Na folha **Selecionar computadores**:
    1. Filtre a lista de VMs pela sua **Assinatura** e **Grupo de Recursos**.
    1. Marque a caixa de seleção de cada máquina virtual que você deseja integrar.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

4. Em **Perfil de configuração**, clique em **Procurar e alterar perfis e preferências**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

5. Na folha **selecionar perfil de configuração + preferências** , selecione um perfil no lado esquerdo: *desenvolvimento/teste* para teste, *prod* para produção.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

6. No perfil escolhido, em **preferências de configuração** , há uma lista suspensa onde você pode ajustar para determinados serviços.
    1. Clique em **criar novas preferências**.
    1. Na folha **criar uma preferência de configuração** , preencha a guia noções básicas:
        1. Subscription
        1. Resource group
        1. Nome da preferência
        1. Região

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

7. Vá para a guia preferências e ajuste as preferências de configuração desejadas.
        
    > [!NOTE]
    > Somente os ajustes que ainda couberem em nossas práticas recomendadas limites superiores e inferiores serão permitidos durante a alteração das configurações de perfil.

8. Examine seu perfil de configuração.
9. Selecione o botão **Criar**.

10. Clique no botão **Habilitar**.


## <a name="disable-automanage-for-vms"></a>Desabilitar o Gerenciamento Automático para VMs

Pare rapidamente de usar o Gerenciamento Automático do Azure para máquinas virtuais desabilitando o gerenciamento automático.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

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

Obtenha as perguntas mais frequentes respondidas em nossas perguntas frequentes. 

> [!div class="nextstepaction"]
> [Perguntas frequentes](faq.md)
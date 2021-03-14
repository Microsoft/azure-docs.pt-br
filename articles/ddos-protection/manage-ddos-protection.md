---
title: Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure
description: Saiba como usar a proteção contra DDoS do Azure Standard para mitigar um ataque.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 336eeee7d522647d11f55a65dc9dc4e65e8b7d47
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535222"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Início rápido: criar e configurar a proteção contra DDoS do Azure Standard

Introdução ao padrão de proteção contra DDoS do Azure usando o portal do Azure. 

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm um padrão de proteção DDoS habilitada, entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas para o mesmo plano. 

Neste guia de início rápido, você criará um plano de proteção contra DDoS e o vinculará a uma rede virtual. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Entre no Portal do Azure em https://portal.azure.com. Verifique se sua conta está atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuída às ações apropriadas listadas no guia de instruções sobre [permissões](manage-permissions.md).

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

1. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
2. Pesquise o termo *DDoS*. Quando o **plano de proteção contra DDoS** aparecer nos resultados da pesquisa, selecione-o.
3. Selecione **Criar**.
4. Insira ou selecione os seguintes valores e, em seguida, selecione **criar**:

    |Configuração        |Valor                                              |
    |---------      |---------                                          |
    |Nome           | Insira _MyDdosProtectionPlan_.                     |
    |Subscription   | Selecione sua assinatura.                         |
    |Resource group | Selecione **criar novo** e insira _MyResource_.|
    |Localização       | Insira _leste dos EUA_.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Habilitar a proteção contra DDoS para uma rede virtual

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Habilitar a proteção contra DDoS para uma nova rede virtual

1. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Insira ou selecione os valores a seguir, aceite os padrões restantes e, em seguida, selecione **criar**:

    | Configuração         | Valor                                           |
    | ---------       | ---------                                       |
    | Nome            | Insira _MyVnet_.                                 |
    | Subscription    | Selecione sua assinatura.                                    |
    | Resource group  | Selecione **usar existente** e, em seguida, selecione **MyResource** |
    | Localização        | Insira _leste dos EUA_                                                    |
    | Proteção contra DDoS Standard | Selecione **Habilitar**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.|

Você não pode mover uma rede virtual para outro grupo de recursos ou assinatura quando o DDoS padrão está habilitado para a rede virtual. Se você precisar mover uma rede virtual com um padrão de DDoS habilitado, desabilite o padrão de DDoS primeiro, mova a rede virtual e, em seguida, habilite padrão de DDoS. Após a movimentação, os limites da política ajustados automaticamente para todos os endereços IP públicos protegidos na rede virtual são redefinidos.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Habilitar a proteção contra DDoS para uma rede virtual existente

1. Crie um plano de proteção contra DDoS completando as etapas em [Criar um plano de proteção contra DDoS](#create-a-ddos-protection-plan), se você não tiver um plano de proteção contra DDoS existente.
2. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
3. Insira o nome da rede virtual para a qual você deseja habilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
4. Selecione **Proteção contra DDoS**, em **Configurações**.
5. Selecione **Padrão**. Em **Plano de proteção contra DDoS**, selecione um plano de proteção contra DDoS existente ou o plano que você criou na etapa 1 e, em seguida, selecione **Salvar**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>Habilitar a proteção contra DDoS para todas as redes virtuais

Essa [política](https://aka.ms/ddosvnetpolicy) detectará todas as redes virtuais em um escopo definido que não tem a proteção contra DDoS padrão habilitada e, opcionalmente, criará uma tarefa de correção que criará a associação para proteger a VNet. Para obter instruções passo a passo detalhadas sobre como implantar essa política, consulte https://aka.ms/ddosvnetpolicy-techcommunity .

## <a name="validate-and-test"></a>Validar e testar

Primeiro, verifique os detalhes do seu plano de proteção contra DDoS:

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *DDoS* na caixa **Filtro**. Selecione **Plano de proteção contra DDoS** quando aparecer nos resultados.
3. Selecione o plano de proteção contra DDoS na lista.

A rede virtual _MyVnet_ deve ser listada. 

### <a name="view-protected-resources"></a>Exibir recursos protegidos
Em **recursos protegidos**, você pode exibir suas redes virtuais e endereços IP públicos protegidos ou adicionar mais redes virtuais ao seu plano de proteção contra DDoS:

![Exibir recursos protegidos](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos para o próximo tutorial. Se não for mais necessário, exclua o grupo de recursos _MyResource_ Group. Ao excluir o grupo de recursos, você também exclui o plano de proteção contra DDoS e todos os seus recursos relacionados. Se você não pretende usar esse plano de proteção contra DDoS, remova os recursos para evitar encargos desnecessários.

   >[!WARNING]
   >Esta ação é irreversível.

1. No portal do Azure, pesquise por **Grupos de recursos** e selecione essa opção. Alternativamente, selecione **Grupos de recursos** no menu do portal do Azure.

2. Filtre ou role para baixo para localizar o grupo de recursos _MyResource_ Group.

3. Selecione o grupo de recursos e selecione **Excluir grupo de recursos**.

4. Digite o nome do grupo de recursos para verificá-lo e selecione **Excluir**.

Para desabilitar a proteção contra DDoS para uma rede virtual: 

1. Insira o nome da rede virtual a qual você deseja desabilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
2. Selecione **em proteção contra DDoS Standard**, selecione **desabilitar**.

Se você quiser excluir um plano de proteção contra DDoS, deverá primeiro dissociar todas as redes virtuais dele. 

## <a name="next-steps"></a>Próximas etapas

Para saber como exibir e configurar a telemetria para seu plano de proteção contra DDoS, prossiga para os tutoriais.

> [!div class="nextstepaction"]
> [Exibir e configurar a telemetria da Proteção contra DDoS](telemetry.md)

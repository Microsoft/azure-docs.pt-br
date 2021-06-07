---
title: 'Tutorial: Criar política do WAF para Azure Front Door – portal do Azure'
description: Neste tutorial, você aprenderá a criar uma política de WAF (Firewall do Aplicativo Web) usando o portal do Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122213"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Tutorial: Criar uma política de Firewall do Aplicativo Web no Azure Front Door usando o portal do Azure

Este tutorial mostrará como criar uma política básica de WAF (Firewall de Aplicativo Web) do Azure e aplicá-la a um host de front-end no Azure Front Door.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma política de WAF
> * Associá-la a um host de front-end
> * Configurar regras de WAF

## <a name="prerequisites"></a>Pré-requisitos

Criar um perfil do [Front Door](../../frontdoor/quickstart-create-front-door.md) ou [Front Door Standard/Premium](../../frontdoor/standard-premium/create-front-door-portal.md). 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de Firewall do Aplicativo Web

Primeiro, crie uma política básica de WAF com DRS (Conjunto de Regras Padrão) no portal. 

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > pesquise **WAF** > selecione **WAF (Firewall de Aplicativo Web)** > selecione **Criar**.

1. Na guia **Noções básicas** da página **Criar uma política de WAF**, insira ou selecione as seguintes informações, aceite os padrões das configurações restantes e selecione **Examinar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Política para              | Selecione **WAF Global (Front Door)** . |
    | SKU do Front Door          | Selecione entre o SKU Básico, Standard e Premium. |
    | Subscription            | Selecione o nome da assinatura do Front Door.|
    | Resource group          | Selecione o nome do grupo de recursos do Front Door.|
    | Nome de política             | Insira um nome exclusivo para a política de WAF.|
    | Estado da política            | Defina como **Habilitado**. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Captura de tela da página Criar uma política do WAF, com um botão Examinar + criar e caixas de listagem para a assinatura, o grupo de recursos e o nome da política.":::

1. Na guia **Associação** da página **Criar uma política de WAF**, selecione **+ Associar um perfil do Front Door**, insira as seguintes configurações e clique em **Adicionar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Perfil do Front Door              | Selecione o nome de perfil do Front Door. |
    | Domínios          | Selecione os domínios aos quais deseja associar a política do WAF e clique em **Adicionar**. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Captura de tela da página Associar um perfil do Front Door.":::
    
    > [!NOTE]
    > Se o domínio estiver associado a uma política de WAF, aparecerá esmaecido. Primeiro, é preciso remover o domínio da política associada e, em seguida, associá-lo novamente à nova política de WAF.

1. Selecione **Examinar + criar** e **Criar**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configurar regras de Firewall do Aplicativo Web (opcional)

### <a name="change-mode"></a>Alterar o modo

Ao criar uma política de WAF, por padrão, ela fica em modo de **Detecção**. No modo de **Detecção**, o WAF não bloqueia as solicitações. Em vez disso, as solicitações que cumprem as regras de WAF são registradas em logs do WAF.
Para ver o WAF na prática, altere as configurações de modo de **Detecção** para **Prevenção**. No modo de **Prevenção**, as solicitações que cumprem as regras definidas no DRS (Conjunto de Regras Padrão) são bloqueadas e registradas em logs do WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Captura de tela da seção Configurações de política. A alternância de Modo está definida como Prevenção.":::

### <a name="custom-rules"></a>Regras personalizadas

É possível criar uma regra personalizada selecionando **Adicionar regra personalizada** na seção **Regras personalizadas**. Desse modo, você iniciará a página de configuração de regras personalizadas. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Captura de tela da página de regras personalizadas.":::

Veja abaixo um exemplo de como configurar uma regra personalizada para bloquear uma solicitação se a cadeia de caracteres de consulta contiver **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Captura de tela da página de configuração de regras personalizadas mostrando as configurações de uma regra que verifica se a variável QueryString contém o valor blockme.":::

### <a name="default-rule-set-drs"></a>DRS (Conjunto de Regras Padrão)

O Conjunto de Regras Padrão gerenciado pelo Azure é habilitado por padrão. A versão padrão atual é DefaultRuleSet_1.0. Em **Regras gerenciadas** do WAF, **Atribuir**, o conjunto de regras Microsoft_DefaultRuleSet_1.1 disponibilizado recentemente está disponível na lista suspensa.

Para desabilitar uma regra individual, marque a **caixa de seleção** na frente do número da regra e clique em **Desabilitar** na parte superior da página. Para alterar os tipos de ações para regras individuais no grupo de regras, marque a caixa de seleção ao lado do número da regra e clique em **Alterar ação** na parte superior da página.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Captura de tela da página Regras gerenciadas mostrando um conjunto de regras, os grupos de regras, as regras e os botões Habilitar, Desabilitar e Alterar Ação." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Limpar os recursos

Remova o grupo de recursos e todos os recursos relacionados quando eles não forem mais necessários.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure Front Door](../../frontdoor/front-door-overview.md)
> [Saiba mais sobre o Azure Front Door Standard/Premium](../../frontdoor/standard-premium/overview.md)

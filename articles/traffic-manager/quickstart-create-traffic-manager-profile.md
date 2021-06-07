---
title: 'Início Rápido: Criar um perfil para HA de aplicativos – portal do Azure – Gerenciador de Tráfego do Azure'
description: Este artigo de início rápido descreve como criar um perfil do Gerenciador de Tráfego para criar aplicativos Web altamente disponíveis usando o portal do Azure.
services: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 1a26becbc7ffaddc09dc06c5dff12f83deaa9ee2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067207"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Início Rápido: Criar um perfil do Gerenciador de Tráfego usando o portal do Azure

Este início rápido descreve como criar um perfil do Gerenciador de Tráfego que fornece alta disponibilidade para seu aplicativo Web.

Neste início rápido, você lerá sobre duas instâncias de um aplicativo Web. Cada uma delas está em execução em uma região diferente do Azure. Você criará um perfil do Gerenciador de Tráfego baseado na [prioridade de ponto de extremidade](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego de usuário para o site primário executando o aplicativo Web. O Gerenciador de Tráfego monitora o aplicativo Web continuamente. Se o site primário estiver indisponível, ele fornece failover automático para o site de backup.

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="prerequisites"></a>Pré-requisitos

Para esse início rápido, você precisará implantar duas instâncias de um aplicativo Web em duas regiões diferentes do Azure (*Leste dos EUA* e *Europa Ocidental*). Cada uma servirá como os pontos de extremidade primário e de failover do Gerenciador de Tráfego.

1. Entre no [portal do Azure](https://portal.azure.com).

1. No canto superior esquerdo da tela, selecione **Criar um recurso**. Procure **Aplicativo Web** e selecione **Criar**.

1. Em **Criar um Aplicativo Web**, insira ou selecione os seguintes valores na guia **Configurações básicas**:

    | Configuração                 | Valor |
    | ---                     | --- |
    | Subscription            | Selecione sua assinatura. |    
    | Resource group          | Selecione **Criar** e insira *myResourceGroupTM1* na caixa de texto.|
    | Nome                    | Insira um **Nome** exclusivo para o aplicativo Web. Este exemplo usa *myWebAppEastUS*. |
    | Publicar                 | Selecione **Código**. |
    | Pilha de runtime           | Selecione **ASP.NET V4.7**. |
    | Sistema operacional        | Selecione **Windows**. |
    | Região                  | Selecione **Leste dos EUA**. |
    | Plano do Windows            | Selecione **Criar** e insira *myAppServicePlanEastUS* na caixa de texto. |
    | SKU e tamanho            | Selecione **S1 Standard 100 ACU no total, 1,75 GB de memória**. |
   
1. Selecione a guia **Monitoramento** ou escolha **Avançar: Monitoramento**.  Em **Monitoramento**, defina **Application Insights** > **Habilitar Application Insights** para **Não**.

1. Selecione **Examinar e criar**.

1. Examine as configurações e selecione **Criar**.  Quando o aplicativo Web é implantado com êxito, ele cria um site da Web padrão.

1. Siga as etapas 1 a 6 para criar um segundo Aplicativo Web chamado *myWebAppWestEurope*. O nome do **Grupo de Recursos** é *myResourceGroupTM2*, com a **Região** *Oeste da Europa* e o nome **myAppServicePlanWestEurope** para o **Plano do Serviço de Aplicativo**. Todas as outras configurações são as mesmas que *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Crie um perfil do Gerenciador de Tráfego que direciona o tráfego de usuário com base na prioridade de ponto de extremidade.

1. No canto superior esquerdo da tela, selecione **Criar um recurso**. Em seguida, procure **perfil do Gerenciador de Tráfego** e selecione **Criar**.
1. Em **Criar perfil do Gerenciador de Tráfego**, insira ou selecione as informações a seguir:

    | Configuração | Valor |
    | --------| ----- |
    | Nome | Insira um nome exclusivo para seu perfil do Gerenciador de Tráfego.|
    | Método de roteamento | Selecione **Prioridade**.|
    | Subscription | Selecione a assinatura à qual você deseja que o perfil do Gerenciador de Tráfego seja aplicado. |
    | Resource group | Selecione *myResourceGroupTM1*.|
    | Location |Essa configuração se refere ao local do grupo de recursos. Ela não tem efeito sobre o perfil do Gerenciador de Tráfego que será implantado globalmente.|

1. Selecione **Criar**.

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

Adicione o site no *Leste dos EUA* como ponto de extremidade primário para encaminhar todo o tráfego de usuários. Adicione o site na *Europa Ocidental* como um ponto de extremidade de failover. Quando o ponto de extremidade primário estiver indisponível, o tráfego será encaminhado automaticamente para o ponto de extremidade de failover.

1. Na barra de pesquisa do portal, insira o nome do perfil do Gerenciador de Tráfego criado na seção anterior.
1. Selecione o perfil nos resultados da pesquisa.
1. Em **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, selecione **Pontos de Extremidade** e **Adicionar**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Configurações de ponto de extremidade no perfil do Gerenciador de Tráfego":::

1. Insira ou selecione estas configurações:

    | Configuração | Valor |
    | ------- | ------|
    | Type | Selecione **ponto de extremidade do Azure**. |
    | Nome | Insira *myPrimaryEndpoint*. |
    | Tipo de recurso de destino | Selecione **Serviço de Aplicativo**. |
    | Recurso de destino | Selecione **Escolher um serviço de aplicativo** > **Leste dos EUA**. |
    | Prioridade | Selecione **1**. Todo o tráfego vai para esse ponto de extremidade quando ele estiver íntegro. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Captura de tela em que você adiciona um ponto de extremidade ao seu perfil do Gerenciador de Tráfego":::
    
1. Selecione **OK**.
1. Para criar um ponto de extremidade de failover para sua segunda região do Azure, repita as etapas 3 e 4 com estas configurações:

    | Configuração | Valor |
    | ------- | ------|
    | Type | Selecione **ponto de extremidade do Azure**. |
    | Nome | Insira *myFailoverEndpoint*. |
    | Tipo de recurso de destino | Selecione **Serviço de Aplicativo**. |
    | Recurso de destino | Selecione **Escolher um serviço de aplicativo** > **Europa Ocidental**. |
    | Prioridade | Selecione **2**. Todo o tráfego vai para esse ponto de extremidade de failover se o ponto de extremidade primário não estiver íntegro. |

1. Selecione **OK**.

Ao terminar a adição de dois pontos de extremidade, eles são exibidos no **perfil do Gerenciador de Tráfego**. Observe que agora o status de monitoramento está como **Online**.

## <a name="test-traffic-manager-profile"></a>Testar perfil de Gerenciador de Tráfego

Nesta seção, você verificará o nome de domínio do seu perfil do Gerenciador de Tráfego. Você também configurará o ponto de extremidade primário para ficar indisponível. Por fim, você poderá ver que o aplicativo Web ainda está disponível. É porque o Gerenciador de Tráfego envia o tráfego para o ponto de extremidade de failover.

### <a name="check-the-dns-name"></a>Verificar o nome do DNS

1. Na barra de pesquisa do portal, procure o nome do **Perfil do Gerenciador de Tráfego** criado na seção anterior.
1. Selecione o perfil do Gerenciador de Tráfego. A **Visão geral** é exibida.
1. O **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Captura de tela do local do nome do DNS do Gerenciador de Tráfego":::

### <a name="view-traffic-manager-in-action"></a>Ver o Gerenciador de Tráfego em ação

1. Em um navegador da Web, insira o nome DNS do perfil do Gerenciador de Tráfego para exibir o site da Web padrão do aplicativo Web.

    > [!NOTE]
    > Nesse cenário de início rápido, todas as solicitações são encaminhadas para o ponto de extremidade primário. Ele é definido como **Prioridade 1**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Captura de tela da página da Web para confirmar a disponibilidade do perfil do Gerenciador de Tráfego":::

1. Para ver o failover do Gerenciador de Tráfego em ação, desabilite o site primário:
    1. Na página de perfil do Gerenciador de Tráfego, da seção **Visão geral**, selecione **myPrimaryEndpoint**.
    1. Em *myPrimaryEndpoint*, selecione **Desabilitado** > **Salvar**.
    1. Feche **myPrimaryEndpoint**. Observe que o status está como **Desabilitado** agora.
1. Copie o nome DNS do seu perfil do Gerenciador de Tráfego da etapa anterior para exibir o site em uma nova sessão do navegador da Web.
1. Verifique se o aplicativo Web ainda está disponível.

O ponto de extremidade primário não está disponível, portanto, você foi encaminhado para o ponto de extremidade de failover.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao terminar, exclua os grupos de recursos, os aplicativos Web e todos os recursos relacionados. Para fazer isso, selecione cada item individual no seu painel e selecione **Excluir** na parte superior de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um perfil do Gerenciador de Tráfego. Ele permite direcionar o tráfego de usuário para aplicativos Web de alta disponibilidade. Para saber mais sobre o encaminhamento de tráfego, prossiga para os tutoriais do Gerenciador de Tráfego.

> [!div class="nextstepaction"]
> [Tutoriais do Gerenciador de Tráfego](tutorial-traffic-manager-improve-website-response.md)

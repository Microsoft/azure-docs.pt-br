---
title: Criar uma porta frontal com o redirecionamento de HTTP para HTTPS usando o portal do Azure
description: Saiba como criar uma porta frontal com tráfego Redirecionado de HTTP para HTTPS usando o portal do Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91626591"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Criar uma porta frontal com o redirecionamento de HTTP para HTTPS usando o portal do Azure

Você pode usar o portal do Azure para [criar uma porta frontal](quickstart-create-front-door.md) com um certificado para terminação TLS. Uma regra de roteamento é usada para redirecionar o tráfego HTTP para HTTPS.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Criar uma porta frontal com um recurso de aplicativo Web existente

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

1. Selecione **criar um recurso** encontrado no canto superior esquerdo do portal do Azure.

1. Pesquise **porta frontal** usando a barra de pesquisa e, depois de localizar o tipo de recurso, selecione **criar**.

1. Escolha uma *assinatura* e, em seguida, use um grupo de recursos existente ou crie um novo. Selecione **Avançar** para inserir a guia configuração.

    > [!NOTE]
    > O local solicitado na interface do usuário é apenas para o grupo de recursos. A configuração da porta de front-end será implantada em todos os [locais pop da porta frontal do Azure](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Configurar noções básicas para uma nova porta de front-end":::

1. A configuração da porta frontal ocorre em três etapas-adicionando um host de front-end padrão, adicionando back-ends em um pool de back-end e criando regras de roteamento para mapear o comportamento de roteamento para o host de front-end. Selecione o **+** ícone ' ' nos _hosts de front-end_ para criar um host de front-end.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Designer de configuração de porta frontal":::

1. Insira um nome globalmente exclusivo para seu host de front-end padrão para sua porta frontal. Selecione **Adicionar** para continuar na próxima etapa.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Adicionar um host de front-end":::

### <a name="create-backend-pool"></a>Criar pool de back-end

1. Selecione o **+** ícone ' ' nos _pools de back-end_ para criar um pool de back-end. Forneça um nome para o pool de back-end e, em seguida, selecione **Adicionar um back-end**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Pool de back-end do designer de configuração de porta frontal":::

1. Selecione o tipo de host de back-end como _serviço de aplicativo_. Selecione a assinatura na qual seu aplicativo Web está hospedado e, em seguida, selecione o aplicativo Web específico no menu suspenso para **nome de host de back-end**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Adicionar um back-end em um pool de back-end":::

1. Selecione **Adicionar** para salvar o back-end e selecione **Adicionar** novamente para salvar a configuração do pool de back-end. 

## <a name="create-http-to-https-redirect-rule"></a>Criar regra de redirecionamento HTTP para HTTPS

1. Selecione o **+** ícone ' ' nas *regras de roteamento* para criar uma rota. Forneça um nome para a rota, por exemplo, ' HttpToHttpsRedirect ', e defina o campo de *protocolo aceito* como **' somente http '**. Verifique se o *front-end/domínios* apropriados está selecionado.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Regra de roteamento do designer de configuração de porta frontal":::

1. Na seção *detalhes da rota* , defina o *tipo de rota* como **redirecionar**. Certifique-se de que o *tipo de redirecionamento* Get definido como **encontrado (302)** e o *protocolo de redirecionamento* sejam definidos como **https somente**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Adicionar uma rota de redirecionamento de HTTP para HTTPS":::

1. Selecione **Adicionar** para salvar a regra de roteamento para redirecionamento de http para https.

## <a name="create-forwarding-rule"></a>Criar regra de encaminhamento

1. Adicione outra regra de roteamento para manipular o tráfego HTTPS. Selecione o **+** sinal "" nas *regras de roteamento* e forneça um nome para a rota, por exemplo, "DefaultForwardingRoute". Em seguida, defina o campo *protocolos aceitos* como **somente HTTPS**. Verifique se o *front-end/domínios* apropriados está selecionado.

1. Na seção detalhes da rota, defina o *tipo de rota* como **encaminhar**. Verifique se o pool de back-end correto é selecionado e se o *protocolo de encaminhamento* está definido como **somente HTTPS**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Adicionar uma rota de avanço para o tráfego HTTPS" border="false":::

1. Selecione **Adicionar** para salvar a regra de roteamento para encaminhamento de solicitação.

1. Selecione **revisar + criar** e **criar** para criar o perfil de porta frontal. Vá para o recurso uma vez criado.

## <a name="next-steps"></a>Próximas etapas

- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
- Saiba mais sobre o [redirecionamento de URL na porta frontal](front-door-url-redirect.md).

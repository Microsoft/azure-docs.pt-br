---
title: 'Início Rápido: Criar um perfil do Azure Front Door Standard/Premium – portal do Azure'
description: Este início rápido mostra como usar o Azure Front Door Standard/Premium Service para seu aplicativo Web global altamente disponível e de alto desempenho usando o portal do Azure.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 8c6fe355621b4f096814268dea8fd2fc8e1166a7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552862"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Início Rápido: Criar um perfil do Azure Front Door Standard/Premium – portal do Azure

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Em busca de informações sobre o Azure Front Door? Veja a [Documentação do Azure Front Door](../front-door-overview.md).

Neste início rápido, você aprende a criar um perfil do Azure Front Door Standard/Premium usando o Portal do Azure. Você pode criar o perfil do Azure Front Door Standard/Premium por meio da *Criação Rápida*, com configurações básicas, ou por meio da *Criação personalizada*, com configurações mais avançadas. Com a *Criação personalizada*, você implanta dois Aplicativos Web. Em seguida, você cria o perfil do Azure Front Door Standard/Premium usando os dois Aplicativos Web como origem. Depois, você verifica a conectividade com os Aplicativos Web usando o nome de host de front-end do Azure Front Door Standard/Premium.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Criar perfil do Front Door – Criação Rápida

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na home page ou no menu do Azure, selecione **+ Criar um recurso**. Pesquise por *Front Door Standard/Premium (Versão Prévia)* . Em seguida, selecione **Criar**.

1. Na página **Comparar ofertas**, selecione **Criação Rápida**. Selecione **Continuar para criar um Front Door**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Captura de tela da comparação de ofertas.":::

1. Na página **Criar um perfil de front door**, insira ou selecione as configurações a seguir.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Captura de tela da página de criação rápida do Front Door.":::    

    | Configurações | Valor |
    | --- | --- |
    | **Assinatura**  | Selecione sua assinatura. |
    | **Grupo de recursos**  | Selecione **Criar** e insira *contoso-appservice* na caixa de texto.|
    | **Nome** | Dê um nome ao perfil. Este exemplo usa **contoso-afd-quickcreate**. |
    | **Camada** | Selecione o SKU Standard ou o Premium. O SKU Standard é otimizado para entrega de conteúdo. O SKU Premium se baseia no SKU Standard com o foco voltado para a segurança. Confira [Comparação de camadas](tier-comparison.md).  |
    | **Nome do ponto de extremidade** | Insira um nome exclusivo globalmente para o ponto de extremidade. |
    | **Tipo de origem** | Selecione o tipo de recurso de origem. Neste exemplo, selecionamos como a origem um serviço de aplicativo que tem o Link Privado habilitado. |
    | **Nome do host de origem** | Insira o nome do host de sua origem. |
    | **Habilitar o Link Privado** | Se você quiser ter uma conexão privada entre o Azure Front Door e sua origem. Para obter mais detalhes, confira as [Diretrizes do link privado](concept-private-link.md) e [Habilitar o link privado](./how-to-enable-private-link-web-app.md).
    | **Cache** | Marque a caixa de seleção se quiser armazenar em cache o conteúdo mais próximo dos usuários globalmente usando os POPs de borda do Azure Front Door e a rede da Microsoft. |
    | **Política do WAF** | Selecione **Criar** ou selecione uma política do WAF existente no menu suspenso se quiser habilitar esse recurso. |

    > [!NOTE]
    > Ao criar um perfil do Azure Front Door Standard/Premium, você precisa selecionar uma origem na mesma assinatura em que o Front Door foi criado.

1. Selecione **Examinar + Criar** para colocar seu perfil do Front Door em funcionamento.

   > [!NOTE]
   > Pode levar alguns minutos para que as configurações sejam propagadas para todos os POPs de borda.

1. Em seguida, clique em **Criar** para que o perfil do Front Door seja implantado e comece a funcionar.

1. Se você habilitou o Link Privado, vá para sua origem (serviço de aplicativo neste exemplo). Selecione **Rede** > **Configurar Link Privado**. Em seguida, selecione a solicitação pendente do Azure Front Door e clique em Aprovar. Depois de alguns segundos, seu aplicativo poderá ser acessado por meio do Azure Front Door de maneira segura.

## <a name="create-front-door-profile---custom-create"></a>Criar perfil do Front Door – Criação personalizada

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Criar um aplicativo Web com duas instâncias como origem

Se você já tiver uma origem ou um grupo de origem configurado, vá diretamente para Criar um Front Door Standard/Premium (versão prévia) para seu aplicativo.

Neste exemplo, criamos um aplicativo Web com duas instâncias que são executadas em diferentes regiões do Azure. Ambas as instâncias do aplicativo Web são executadas no modo *Ativo/Ativo*, portanto, qualquer uma pode aceitar tráfego. Essa configuração difere de uma configuração *Ativa/Em Espera*, em que uma delas atua como um failover.

Se você ainda não tem um aplicativo Web, use as etapas a seguir para configurar um aplicativo Web de exemplo.

1. Entre no Portal do Azure em https://portal.azure.com.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Aplicativo Web**.

1. Na guia **Básico** da página **Criar Aplicativo Web**, insira ou selecione as informações a seguir.

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | **Assinatura**               | Selecione sua assinatura. |
    | **Grupo de recursos**       | Selecione **Criar** e insira *FrontDoorQS_rg1* na caixa de texto.|
    | **Nome**                   | Insira um **Nome** exclusivo para o aplicativo Web. Este exemplo usa *WebAppContoso-001*. |
    | **Publicar** | Selecione **Código**. |
    | **Pilha de runtime**         | Selecione **.NET Core 2.1 (LTS)** . |
    | **Sistema operacional**          | Selecione **Windows**. |
    | **Região**           | Selecione **EUA Central**. |
    | **Plano do Windows** | Selecione **Criar** e insira *myAppServicePlanCentralUS* na caixa de texto. |
    | **SKU e tamanho** | Selecione **S1 Standard 100 ACU no total, 1,75 GB de memória**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Criação rápida de um SKU Premium do Front Door no portal do Azure":::

1. Selecione **Examinar + criar**, examine o resumo e selecione **Criar**. Pode demorar alguns minutos para implantar em um

Depois que a implantação for concluída, crie um segundo aplicativo Web. Use as mesmas configurações acima, exceto pelo seguinte:

| Configuração          | Valor     |
| ---              | ---  |
| **Grupo de recursos**   | Selecione **Criar** e insira *FrontDoorQS_rg2*. |
| **Nome**             | Insira um nome exclusivo para seu aplicativo Web, neste exemplo, *WebAppContoso-002*.  |
| **Região**           | Uma região diferente, neste exemplo, *Centro-Sul dos EUA* |
| **Plano do Serviço de Aplicativo** > **Plano do Windows**         | Selecione **Novo** e insira *myAppServicePlanSouthCentralUS*, depois selecione **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Criar um Front Door Standard/Premium (versão prévia) para seu aplicativo

Configure o Azure Front Door Standard/Premium (versão prévia) para direcionar o tráfego do usuário com base na latência mais baixa entre dois servidores dos aplicativos Web. Proteja também seu Front Door com o Firewall de Aplicativo Web. 

1. Entre no [portal do Azure](https://portal.azure.com).
 
1. Na home page ou no menu do Azure, selecione **+ Criar um recurso**. Pesquise por *Front Door Standard/Premium (Versão Prévia)* . Em seguida, selecione **Criar**.

1. Na página **Comparar ofertas**, selecione **Criação personalizada**. Selecione **Continuar para criar um Front Door**.

1. Na guia **Básico** , insira ou selecione as informações a seguir e selecione **Avançar: Segredo**. 

    | Configuração | Valor |
    | --- | --- |
    | **Assinatura** | Selecione sua assinatura. |
    | **Grupo de recursos** | Selecione **Criar** e insira *FrontDoorQS_rg0* na caixa de texto. |
    | **Localização do grupo de recursos** | Selecione **Leste dos EUA** |
    | **Nome do Perfil** | Insira um nome exclusivo nesta assinatura **Webapp-Contoso-AFD** |
    | **Camada** | Selecione **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Criar um perfil do Front Door":::

1. *Opcional*: **Segredos**. Se você planeja usar certificados gerenciados, esta etapa é opcional. Se você tem um Key Vault existente no Azure que planeja usar para trazer seu próprio certificado para o domínio personalizado, selecione **Adicionar um certificado**. Você também pode adicionar o certificado na experiência de gerenciamento após a criação.

    > [!NOTE]
    > Você precisa ter a permissão certa para adicionar o certificado do Azure Key Vault como usuário. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Captura de tela da adição de segredo na criação personalizada.":::

1. Na guia **Ponto de extremidade**, selecione **Adicionar um ponto de extremidade** e dê a ele um nome globalmente exclusivo. Você pode criar vários pontos de extremidade em seu perfil do Azure Front Door Standard/Premium depois de concluir a experiência de criação. Este exemplo usa *contoso-frontend*. Deixe o Tempo de resposta de origem (em segundos) e o Status com os valores padrão. Selecione **Adicionar** para adicionar o ponto de extremidade.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Captura de tela da adição do ponto de extremidade.":::

1. Em seguida, adicione um Grupo de origem que contenha seus dois aplicativos Web. Selecione **+ Adicionar** para abrir a página **Adicionar um grupo de origem**. Em Nome, insira  *myOrignGroup* e selecione  **+ Adicionar uma origem**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Captura de tela da adição de um grupo de origem.":::

1. Na página **Adicionar uma origem** , insira ou selecione as informações abaixo. Em seguida, selecione **Adicionar**.

    | Configuração | Valor |
    | --- | --- |
    | **Nome** | Insira **webapp1** |
    | **Tipo de origem** | Selecionar **Serviços de aplicativos** |
    | **Nome do host** | Selecione `WebAppContoso-001.azurewebsites.net` |
    | **Cabeçalho de host de origem** | Selecione `WebAppContoso-001.azurewebsites.net` |
    | **Outros campos** | Deixe todos os outros campos com os valores padrão. |

    > [!NOTE]
    > Ao criar um perfil do Azure Front Door Standard/Premium, você precisa selecionar uma origem na mesma assinatura em que o Azure Front Door Standard/Premium foi criado.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Captura de tela da adição de mais origens.":::

1. Repita a etapa 8 para adicionar o webapp002 da segunda origem. Selecione `webappcontoso-002.azurewebsite.net` como o **Nome do host de origem** e o **Cabeçalho de host de origem**.

1. Na página **Adicionar um grupo de origem**, você verá duas origens adicionadas, deixe todos os outros campos com os valores padrão.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Captura de tela da adição da página de um grupo de origem.":::

1. Em seguida, adicione uma rota para mapear seu ponto de extremidade de front-end ao grupo de origem. Essa rota encaminha solicitações do ponto de extremidade para myOriginGroup. Selecione **+ Adicionar** em Rota para configurar uma Rota.  

1. Na página **Adicionar uma rota**, insira ou selecione as informações abaixo. Em seguida, selecione **Adicionar**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Adicionar rota sem cache":::

    | Configuração | Valor |
    | --- | --- |
    | **Nome** | Insira **MyRoute** |    
    | **Domínio** | Selecione `contoso-frontend.z01.azurefd.net` | 
    | **Nome do host** | Selecione `WebAppContoso-001.azurewebsites.net` |
    | **Padrões para correspondência** | Mantenha o padrão. |
    | **Protocolos aceitos** | Mantenha o padrão. | 
    | **Redirecionar** | Deixe o valor padrão para **Redirecionar todo o tráfego para usar HTTPS**. | 
    | **Grupo de origem** | Selecione **MyOriginGroup**. | 
    | **Caminho de origem** | Mantenha o padrão. | 
    | **Protocolo de encaminhamento** | Selecione **Corresponder à solicitação de entrada**. | 
    | **Cache** | Deixe desmarcado neste início rápido. Se quiser que seu conteúdo seja armazenado em cache nas bordas, marque a caixa de seleção para **Habilitar cache**. |
    | **Regras** | Mantenha o padrão. Depois de criar seu perfil do front door, você pode criar regras personalizadas e aplicá-las às rotas. |  
       
    >[!WARNING]
    > **Verifique** se há uma rota para cada ponto de extremidade. A ausência de uma rota pode fazer com que o ponto de extremidade falhe.

1. Em seguida, selecione **+ Adicionar** em Segurança para adicionar uma política do WAF. Selecione **Adicionar** Novo e dê à política um nome exclusivo. Marque a caixa de seleção para **Adicionar proteção contra bots**. Selecione o ponto de extremidade em **Domínios** e escolha **Adicionar**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="adicionar uma política do WAF":::

1. Selecione **Examinar + Criar** e escolha **Criar**. São necessários alguns minutos para que as configurações sejam propagadas para todos os POPs de borda. Agora, você tem seu primeiro perfil e ponto de extremidade do Front Door.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Examinar criação personalizada":::

## <a name="verify-azure-front-door"></a>Verificar o Azure Front Door

Quando você cria o perfil do Azure Front Door Standard/Premium, a configuração leva alguns minutos para ser implantada globalmente. Após a conclusão, você pode acessar o host de front-end que criou. Em um navegador, acesse `contoso-frontend.z01.azurefd.net`. Sua solicitação será roteada automaticamente para o servidor mais próximo dos servidores especificados no grupo de origem.

Se tiver criado criou esses aplicativos neste início rápido, você verá uma página de informações.

Para testar o failover global instantâneo, usaremos as seguintes etapas:

1. Abra um navegador conforme descrito acima e vá para o endereço de front-end: `contoso-frontend.azurefd.net`.

1. No portal do Azure, pesquise e selecione *Serviços de Aplicativos*. Role para baixo para encontrar um de seus aplicativos Web, **WebAppContoso-001** neste exemplo.

1. Selecione seu aplicativo Web e, em seguida, selecione **Parar** e **Sim** para verificar.

1. Atualize seu navegador. Você deverá ver a mesma página de informações.

   >[!TIP]
   >Há um pouco de atraso para essas ações. Talvez você precise atualizar novamente.

1. Localize o outro aplicativo Web e interrompa-o também.

1. Atualize seu navegador. Desta vez, você deverá ver uma mensagem de erro.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Ambas as instâncias do aplicativo Web foram interrompidas":::

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar, você poderá remover todos os itens que criou. Excluir um grupo de recursos também exclui o conteúdo dele. Se você não pretende usar esse Front Door, remova os recursos para evitar encargos desnecessários.

1. No portal do Azure, pesquise por **Grupos de recursos** e selecione essa opção. Alternativamente, selecione **Grupos de recursos** no menu do portal do Azure.

1. Filtre ou role para baixo para localizar um grupo de recursos, como **FrontDoorQS_rg0**.

1. Selecione o grupo de recursos e selecione **Excluir grupo de recursos**.

   >[!WARNING]
   >Esta ação é irreversível.

1. Digite o nome do grupo de recursos para verificá-lo e selecione **Excluir**.

Repita o procedimento para os outros dois grupos.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como adicionar um domínio personalizado ao Front Door.
> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](how-to-add-custom-domain.md)
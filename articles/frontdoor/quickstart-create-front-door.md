---
title: 'Início Rápido: Configurar a alta disponibilidade com o Azure Front Door Service – portal do Azure'
description: Este início rápido mostra como usar o Azure Front Door Service para seu aplicativo Web global altamente disponível e de alto desempenho usando o portal do Azure.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 1869098362e37ea18c7ca9a9f827b0e5ec98ea3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067564"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Início Rápido: Crie um Front Door para um aplicativo Web global altamente disponível

Comece a usar o Azure Front Door usando o portal do Azure para configurar a alta disponibilidade para um aplicativo Web.

Neste início rápido, o Azure Front Door coloca em pool duas instâncias de um aplicativo Web em execução em regiões do Azure diferentes. Você cria uma configuração de Front Door com base em back-ends com pesos e prioridades iguais. Essa configuração direciona o tráfego para o site mais próximo que executa o aplicativo. O Azure Front Door monitora o aplicativo Web continuamente. O serviço fornecerá failover automático para o próximo site disponível quando o site mais próximo estiver não disponível.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Criar duas instâncias de um aplicativo Web

Este início rápido requer duas instâncias de um aplicativo Web em execução em regiões do Azure diferentes. Ambas as instâncias do aplicativo Web são executadas no modo *Ativo/Ativo*, portanto, qualquer uma pode aceitar tráfego. Essa configuração difere de uma configuração *Ativa/Em Espera*, em que uma delas atua como um failover.

Se você ainda não tiver um aplicativo Web, use as etapas a seguir para configurar aplicativos Web de exemplo.

1. Entre no Portal do Azure em https://portal.azure.com.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** >  **Aplicativo Web**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Criar um aplicativo Web no portal do Azure":::

1. Na guia **Básico** da página **Criar Aplicativo Web**, insira ou selecione as informações a seguir.

    | Setting                 | Valor                                              |
    | ---                     | ---                                                |
    | **Assinatura**               | Selecione sua assinatura. |    
    | **Grupo de recursos**       | Selecione **Criar** e insira *FrontDoorQS_rg1* na caixa de texto.|
    | **Nome**                   | Insira um **Nome** exclusivo para o aplicativo Web. Este exemplo usa *WebAppContoso-1*. |
    | **Publicar** | Selecione **Código**. |
    | **Pilha de runtime**         | Selecione **.NET Core 2.1 (LTS)** . |
    | **Sistema operacional**          | Selecione **Windows**. |
    | **Região**           | Selecione **EUA Central**. |
    | **Plano do Windows** | Selecione **Criar** e insira *myAppServicePlanCentralUS* na caixa de texto. |
    | **SKU e tamanho** | Selecione **S1 Standard 100 ACU no total, 1,75 GB de memória**. |

1. Selecione **Examinar + criar**, examine o **Resumo** e, em seguida, selecione **Criar**. Pode levar vários minutos para que a implantação seja concluída.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Analisar resumo do aplicativo Web":::

Depois que a implantação for concluída, crie um segundo aplicativo Web. Use o mesmo procedimento com os mesmos valores, exceto pelos seguintes valores:

| Setting          | Valor     |
| ---              | ---  |
| **Grupo de recursos**   | Selecione **Criar** e insira *FrontDoorQS_rg2* |
| **Nome**             | Insira um nome exclusivo para seu aplicativo Web, neste exemplo, *WebAppContoso-2*  |
| **Região**           | Uma região diferente, neste exemplo, *Centro-Sul dos EUA* |
| **Plano do Serviço de Aplicativo** > **Plano do Windows**         | Selecione **Novo** e insira *myAppServicePlanSouthCentralUS*, depois selecione **OK** |

## <a name="create-a-front-door-for-your-application"></a>Criar um Front Door para seu aplicativo

Configure o Azure Front Door para direcionar o tráfego do usuário com base na latência mais baixa entre dois servidores dos aplicativos Web. Para começar, adicione um host de front-end para o Azure Front Door.

1. Na Página Inicial ou no menu do Azure, selecione **Criar um recurso**. Selecione **Rede** > **Ver Todos** > **Front Door**.

1. Na guia **Básico** da página **Criar um Front Door**, insira ou selecione as seguintes informações e selecione **Avançar: configuração**.

    | Setting | Valor |
    | --- | --- |
    | **Assinatura** | Selecione sua assinatura. |    
    | **Grupo de recursos** | Selecione **Criar** e insira *FrontDoorQS_rg0* na caixa de texto.|
    | **Localização do grupo de recursos** | Selecione **EUA Central**. |

1. Em **Front-ends/domínios**, selecione **+** para abrir **Adicionar um host de front-end**.

1. Para **Nome do host**, insira um nome do host globalmente exclusivo. Este exemplo usa *contoso-frontend*. Selecione **Adicionar**.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Adicionar um host de front-end ao Azure Front Door":::

Em seguida, crie um pool de back-end que contenha os dois aplicativos Web.

1. Ainda em **Criar um Front Door**, em **Pools de back-end**, selecione **+** para abrir **Adicionar um pool de back-end**.

1. Para **Nome**, insira *myBackendPool* e selecione **Adicionar um back-end**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Adicionar um pool de back-end":::

1. Na folha **Adicionar um back-end**, selecione as informações a seguir e selecione **Adicionar**.

    | Setting | Valor |
    | --- | --- |
    | **Tipo de host de back-end** | Selecione **Serviço de Aplicativo**. |   
    | **Assinatura** | Selecione sua assinatura. |    
    | **Nome do host de back-end** | Selecione o primeiro aplicativo Web criado. Neste exemplo, o aplicativo Web era *WebAppContoso-1*. |

    **Deixe todos os outros campos com os valores padrão.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Adicionar um host de back-end ao seu Front Door":::

1. Selecione **Adicionar um back-end** novamente. selecione as informações a seguir e selecione **Adicionar**.

    | Setting | Valor |
    | --- | --- |
    | **Tipo de host de back-end** | Selecione **Serviço de Aplicativo**. |   
    | **Assinatura** | Selecione sua assinatura. |    
    | **Nome do host de back-end** | Selecione o segundo aplicativo Web criado. Neste exemplo, o aplicativo Web era *WebAppContoso-2*. |

    **Deixe todos os outros campos com os valores padrão.*

1. Selecione **Adicionar** na folha **Adicionar um pool de back-end** para concluir a configuração do pool de back-end.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Adicionar um pool de back-end para o Azure Front Door":::

Por fim, adicione uma regra de roteamento. Uma regra de roteamento mapeia o host de front-end para o pool de back-end. A regra encaminha uma solicitação de `contoso-frontend.azurefd.net` para **myBackendPool**.

1. Ainda em **Criar um Front Door**, em **Regras de roteamento**, selecione **+** para configurar uma regra de roteamento.

1. Em **Adicionar uma regra**, para **Nome**, insira *LocationRule*. Aceite todos os valores padrão e, em seguida, selecione **Adicionar** para adicionar a regra de roteamento.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Adicionar uma regra ao seu Front Door":::

   >[!WARNING]
   > É **necessário** garantir que cada um dos hosts de front-end no seu Front Door tenha uma regra de roteamento com um caminho padrão (`\*`) associado a ela. Ou seja, entre todas as suas regras de roteamento, deve haver pelo menos uma regra de roteamento para cada um dos seus hosts de front-end definida no caminho padrão (`\*`). Não fazer isso poderá fazer o tráfego do seu usuário final não ser roteado corretamente.

1. Selecione **Examinar + Criar** e depois **Criar**.

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Azure Front Door configurado":::

## <a name="view-azure-front-door-in-action"></a>Ver o Azure Front Door em ação

Depois de você criar um Front Door, a configuração leva alguns minutos para ser implantada globalmente. Depois de concluído, acesse o host de front-end que você criou. Em um navegador, acesse `contoso-frontend.azurefd.net`. Sua solicitação será roteada automaticamente para o servidor mais próximo de você dos servidores especificados no pool de back-end.

Se tiver criado criou esses aplicativos neste início rápido, você verá uma página de informações.

Para testar o failover global instantâneo em ação, tente as seguintes etapas:

1. Abra um navegador conforme descrito acima e vá para o endereço de front-end: `contoso-frontend.azurefd.net`.

1. No portal do Azure, pesquise e selecione *Serviços de Aplicativos*. Role para baixo para encontrar um dos seus aplicativos Web, **WebAppContoso-1**, neste exemplo.

1. Selecione seu aplicativo Web e, em seguida, selecione **Parar** e **Sim** para verificar.

1. Atualize seu navegador. Você deverá ver a mesma página de informações.

   >[!TIP]
   >Há um pouco de atraso para essas ações. Talvez você precise atualizar novamente.

1. Localize o outro aplicativo Web e interrompa-o também.

1. Atualize seu navegador. Desta vez, você deverá ver uma mensagem de erro.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Ambas as instâncias do aplicativo Web foram interrompidas":::

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
> [Adicionar um domínio personalizado](front-door-custom-domain.md)

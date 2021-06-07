---
title: 'Tutorial: Configurar o roteamento de tráfego geográfico com o Gerenciador de Tráfego do Azure'
description: Este tutorial explica como configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego do Azure
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96188659"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Tutorial: Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego

O método de roteamento de tráfego geográfico permite direcionar tráfego para pontos de extremidade específicos com base na localização geográfica em que as solicitações são originadas. Este tutorial mostra como criar um perfil do Gerenciador de Tráfego com esse método de roteamento e configurar os pontos de extremidade para receber tráfego de regiões geográficas específicas.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar um perfil do Gerenciador de Tráfego com roteamento geográfico.
> - Configurar um ponto de extremidade aninhado.
> - Usar o perfil do Gerenciador de Tráfego.
> - Excluir o perfil do Gerenciador de Tráfego.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

1. Usando um navegador, entre no [portal do Azure](https://portal.azure.com).

1. Selecione **+ Criar um recurso** do lado esquerdo. Pesquise o **perfil do Gerenciador de Tráfego** e selecione **Criar**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Criar um perfil do Gerenciador de Tráfego":::

1. Na página *Criar perfil do Gerenciador de Tráfego*, defina as seguintes configurações:

    | Configuração         | Valor                                              |
    | ---             | ---                                                |
    | Nome            | Forneça um nome para seu perfil. Esse nome deve ser exclusivo dentro da zona trafficmanager.net. Para acessar o perfil do Gerenciador de Tráfego, você deve usar o nome DNS `<profilename>.trafficmanager.net`. |    
    | Método de roteamento  | Selecione **Geográfico**. |
    | Subscription    | Selecione sua assinatura. |
    | Resource group   | Use um grupo de recursos existente ou crie um no qual colocar esse perfil. Se você optar por criar um novo grupo de recursos, use a lista suspensa *Localização do Grupo de Recursos* para especificar a localização do grupo de recursos. Essa configuração refere-se ao local do grupo de recursos e não tem impacto no perfil do Gerenciador de Tráfego que é implantado globalmente. |

1. Selecione **Criar** para implantar seu perfil do Gerenciador de Tráfego.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Página de perfil Criar um Gerenciador de Tráfego":::

## <a name="add-endpoints"></a>Adicionar pontos de extremidade

1. Selecione o perfil do Gerenciador de Tráfego na lista.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Lista geográfica do Gerenciador de Tráfego":::

1. Selecione **Pontos de Extremidade** em *Configurações* e selecione **+ Adicionar** para adicionar um novo ponto de extremidade.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Adicionar pontos de extremidade":::

1. Selecione ou insira as seguintes configurações: 

    | Configuração                | Valor                                              |
    | ---                    | ---                                                |
    | Tipo                   | Selecionar o tipo de ponto de extremidade. Para perfis de roteamento geográfico usados na produção, é altamente recomendável usar tipos de ponto de extremidade aninhados que contenham um perfil filho com mais de um ponto de extremidade. Para obter mais informações, confira as [Perguntas frequentes sobre métodos de roteamento de tráfego geográfico](traffic-manager-FAQs.md). |    
    | Nome                   | Dar um nome para identificar esse ponto de extremidade. |
    | Tipo de recurso de destino   | Selecionar o tipo de recurso para o destino. |
    | Recurso de destino        | Selecionar o recurso na lista. |

    > [!Note]
    > Determinados campos nessa página dependem do tipo de ponto de extremidade que você está adicionando:
    > 1. Se estiver adicionando um ponto de extremidade do Azure, selecione o **Tipo de recurso de destino** e o **Destino** com base no recurso para o qual quer direcionar o tráfego
    > 1. Se estiver adicionando um ponto de extremidade **Externo**, forneça o **FQDN (nome de domínio totalmente qualificado)** para o ponto de extremidade.
    > 1. Se estiver adicionando um **ponto de extremidade Aninhado**, selecione o **Recurso de destino** que corresponda ao perfil filho que quer usar e especifique a **Contagem mínima de pontos de extremidade filho**.

1. Na seção *Mapeamento geográfico*, use a lista suspensa para adicionar as regiões de onde você quer que o tráfego seja enviado para esse ponto de extremidade. Pelo menos uma região precisa ser adicionada. Você pode ter várias regiões mapeadas.

1. Repita essa última etapa para todos os pontos de extremidade que queira adicionar nesse perfil e selecione **Salvar**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Adicionar um ponto de extremidade do Gerenciador de Tráfego":::

## <a name="use-the-traffic-manager-profile"></a>Usar o perfil do Gerenciador de Tráfego

1.  Na barra de pesquisa do portal, pesquise o nome do **Perfil do Gerenciador de Tráfego** que você criou na seção anterior e selecione o perfil do gerenciador de tráfego nos resultados exibidos.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Pesquisar perfil do Gerenciador de Tráfego":::

1. O **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente. Esse nome pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento. Com o roteamento geográfico, o Gerenciador de Tráfego examina o IP de origem da solicitação de entrada e determina a região da qual ela se origina. Se essa região estiver mapeada para um ponto de extremidade, o tráfego será roteado para lá. Se essa região não estiver mapeada para um ponto de extremidade, o Gerenciador de Tráfego retornará uma resposta de consulta NODATA.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Visão geral do Gerenciador de Tráfego geográfico":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais do perfil do Gerenciador de Tráfego, localize o perfil e selecione **Excluir perfil**.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Excluir o perfil do Gerenciador de Tráfego":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o método de roteamento geográfico, confira:

> [!div class="nextstepaction"]
> [Método de roteamento de tráfego geográfico](traffic-manager-routing-methods.md#geographic)

---
title: 'Tutorial: Configurar o roteamento de tráfego prioritário com o Gerenciador de Tráfego do Azure'
description: Este tutorial explica como configurar o método de roteamento de tráfego de prioridade no Gerenciador de Tráfego
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92208049"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configurar o método de roteamento de tráfego de prioridade no Gerenciador de Tráfego

Este tutorial descreve como usar o Gerenciador de Tráfego do Azure para rotear o tráfego do usuário para pontos de extremidade usando o método de roteamento prioritário. Nesse método de roteamento, você definirá a ordem de cada ponto de extremidade que entrar na configuração do perfil do Gerenciador de Tráfego. O tráfego de usuários será roteado para o ponto de extremidade na ordem em que são listados. Esse método de roteamento é útil quando você deseja fazer a configuração para o failover de serviço. O ponto de extremidade primário obtém um número de prioridade de '1' e atenderá a todas as solicitações de entrada. Enquanto isso, os pontos de extremidade de prioridade mais baixa funcionam como backups.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> - Criar um perfil do Gerenciador de Tráfego com roteamento prioritário.
> - Adicionar pontos de extremidade.
> - Configure a prioridade dos pontos de extremidade.
> - Usar o perfil do Gerenciador de Tráfego.
> - Excluir o perfil do Gerenciador de Tráfego.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método de roteamento de tráfego de prioridade
1. Usando um navegador, entre no [portal do Azure](https://portal.azure.com).

1. Selecione **+ Criar um recurso** do lado esquerdo. Pesquise o **perfil do Gerenciador de Tráfego** e selecione **Criar**.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Criar um perfil de prioridade do Gerenciador de Tráfego":::

1. Na página *Criar perfil do Gerenciador de Tráfego*, defina as seguintes configurações:

    | Configuração         | Valor                                              |
    | ---             | ---                                                |
    | Nome            | Forneça um nome para seu perfil. Esse nome deve ser exclusivo dentro da zona trafficmanager.net. Para acessar o perfil do Gerenciador de Tráfego, você deve usar o nome DNS `<profilename>.trafficmanager.net`. |    
    | Método de roteamento  | Selecione **Prioridade**. |
    | Subscription    | Selecione sua assinatura. |
    | Resource group   | Use um grupo de recursos existente ou crie um no qual colocar esse perfil. Se você optar por criar um novo grupo de recursos, use a lista suspensa *Localização do Grupo de Recursos* para especificar a localização do grupo de recursos. Essa configuração refere-se ao local do grupo de recursos e não tem impacto no perfil do Gerenciador de Tráfego que é implantado globalmente. |

1. Selecione **Criar** para implantar seu perfil do Gerenciador de Tráfego.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Criar uma prioridade de perfil do Gerenciador de Tráfego":::

## <a name="add-endpoints"></a>Adicionar pontos de extremidade

1. Selecione o perfil do Gerenciador de Tráfego na lista.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Lista de perfis do Gerenciador de Tráfego":::

1. Selecione **Pontos de Extremidade** em *Configurações* e selecione **+ Adicionar** para adicionar um novo ponto de extremidade.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Adicionar pontos de extremidade do Gerenciador de Tráfego":::

1. Selecione ou insira as seguintes configurações: 

    | Configuração                | Valor                                              |
    | ---                    | ---                                                |
    | Tipo                   | Selecionar o tipo de ponto de extremidade. |    
    | Nome                   | Dar um nome para identificar esse ponto de extremidade. |
    | Tipo de recurso de destino   | Selecionar o tipo de recurso para o destino. |
    | Recurso de destino        | Selecionar o recurso na lista. |
    | Prioridade               | Fornecer um número de prioridade para este ponto de extremidade. 1 é a prioridade mais alta. |


1. Selecione **Adicionar** para adicionar o ponto de extremidade. Repita as etapas 2 e 3 para adicionar mais pontos de extremidade. Lembre-se de definir o número de prioridade apropriado.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Adicionar ponto de extremidade prioritário 1":::

1. Na página **Pontos de Extremidade**, examine a ordem de prioridade dos pontos de extremidade. Ao selecionar o método de roteamento de tráfego de **Prioridade**, a ordem dos pontos de extremidade selecionados é importante. Verifique a ordem de prioridade dos pontos de extremidade.  O ponto de extremidade primário está no topo. Verifique novamente a ordem em que eles são exibidos. Todas as solicitações serão roteadas para o primeiro ponto de extremidade e, se o Gerenciador de Tráfego o detectar como não íntegro, o tráfego fará failover automaticamente para o próximo ponto de extremidade. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Lista de pontos de extremidade prioritários":::

1. Para alterar a ordem de prioridade do ponto de extremidade, selecione o ponto de extremidade, altere o valor de prioridade e selecione **Salvar** para salvar as configurações do ponto de extremidade.

## <a name="use-the-traffic-manager-profile"></a>Usar o perfil do Gerenciador de Tráfego

1.  Na barra de pesquisa do portal, pesquise o nome do **Perfil do Gerenciador de Tráfego** que você criou na seção anterior e selecione o perfil do gerenciador de tráfego nos resultados exibidos.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Pesquisar perfil do Gerenciador de Tráfego":::

1.  A página de visão geral do **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente. Isso pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para o primeiro ponto de extremidade e, se o Gerenciador de Tráfego o detectar como não íntegro, o tráfego fará failover automaticamente para o próximo ponto de extremidade.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Nome DNS do Gerenciador de Tráfego":::

1. Depois que o perfil do Gerenciador de Tráfego estiver funcionando, edite o registro DNS no servidor DNS autoritativo para que o nome de domínio de sua empresa aponte para o nome de domínio do Gerenciador de Tráfego.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais do perfil do Gerenciador de Tráfego, localize o perfil e selecione **Excluir perfil**.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Excluir o perfil prioritário do Gerenciador de Tráfego":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o método de roteamento prioritário, confira:

> [!div class="nextstepaction"]
> [Método de roteamento prioritário](traffic-manager-routing-methods.md#priority-traffic-routing-method)

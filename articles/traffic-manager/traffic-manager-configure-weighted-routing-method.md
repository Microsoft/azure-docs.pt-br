---
title: 'Tutorial: Configurar o roteamento de tráfego round robin ponderado com o Gerenciador de Tráfego do Azure'
description: Este tutorial explica como balancear a carga do tráfego usando um método round robin no Gerenciador de Tráfego
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207794"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configurar o método de roteamento de tráfego ponderado no Gerenciador de Tráfego

Um padrão comum de roteamento de tráfego é fornecer um conjunto de pontos de extremidade idênticos, que incluem serviços de nuvem e sites, e enviar tráfego a cada um deles com igualdade. As etapas a seguir descrevem como configurar esse tipo de método de roteamento de tráfego.

> [!NOTE]
> O Aplicativo Web do Azure já fornece a funcionalidade de balanceamento de carga round robin para sites em uma Região do Azure (que pode conter vários datacenters). O Gerenciador de Tráfego permite que você distribua o tráfego entre sites em datacenters diferentes.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar um perfil do Gerenciador de Tráfego com roteamento ponderado.
> - Usar o perfil do Gerenciador de Tráfego.
> - Excluir o perfil do Gerenciador de Tráfego.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>Configurar o método de roteamento de tráfego ponderado

1. Usando um navegador, entre no [portal do Azure](https://portal.azure.com).

1. Na barra de pesquisa do portal, pesquise o nome do **Perfil do Gerenciador de Tráfego** que você criou na seção anterior e selecione o perfil do gerenciador de tráfego nos resultados exibidos.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Pesquisar perfil do Gerenciador de Tráfego":::

1. Selecione **Configuração** ou insira as seguintes configurações:

    | Configuração         | Valor                                              |
    | ---             | ---                                                |
    | Método de roteamento            | Selecione **Ponderado**. |    
    | TTL (vida útil) do DNS | Esse valor controla com que frequência o servidor de nome do cache local do cliente consultará o sistema do Gerenciador de Tráfego para ver se há entradas DNS atualizadas. Qualquer alteração que ocorrer no Gerenciador de Tráfego, como alterações no método de roteamento de tráfego ou alterações na disponibilidade de pontos de extremidade adicionados, fará com que esse período de tempo seja atualizado em todo o sistema global dos servidores DNS. |
    | Protocolo    | Selecione um protocolo para monitoramento de ponto de extremidade. *Opções: HTTP, HTTPS e TCP* |
    | Porta | Especifique o número da porta. |
    | Caminho | Para monitorar os pontos de extremidade, especifique um caminho e um nome de arquivo. A barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão). |
    | Configurações personalizadas de cabeçalho | Configure os Cabeçalhos Personalizados no formato host:contoso.com,newheader:newvalue. O par máximo com suporte é 8. Aplicável para os protocolos Http e Https. Aplicável para todos os pontos de extremidade no perfil |
    | Intervalos de Códigos de Status Esperados (padrão: 200) | Configure os Intervalos de Códigos de Status no formato 200-299,301-301. O intervalo máximo com suporte é 8. Aplicável para os protocolos Http e Https. Aplicável para todos os pontos de extremidade no perfil |
    | Intervalo de sondagem | Configure o intervalo de tempo entre as investigações de integridade do ponto de extremidade. Você pode escolher 10 ou 30 segundos. |
    | Número de falhas tolerado | Configure o número de falhas da investigação de integridade toleradas antes que uma falha do ponto de extremidade seja disparada. É possível inserir um número entre 0 e 9. | 
    | Tempo limite da investigação | Configure o tempo necessário antes que uma investigação de integridade do ponto de extremidade atinja o tempo limite. Esse valor precisa ser pelo menos 5 e ser menor do que o valor do intervalo de investigação. |

1. Selecione **Salvar** para completar a configuração.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Configuração ponderada do Gerenciador de Tráfego"::: 

1. Selecione **Ponto de extremidade** e configure o peso de cada ponto de extremidade. O peso pode estar entre 1 e 1000. Quanto maior o peso, maior a prioridade.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Configuração de pontos de extremidade ponderados do Gerenciador de Tráfego"::: 

## <a name="use-the-traffic-manager-profile"></a>Usar o perfil do Gerenciador de Tráfego

O **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente. Esse nome pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para cada ponto de extremidade em round robin.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Visão geral ponderada do Gerenciador de Tráfego"::: 

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais do perfil do Gerenciador de Tráfego, localize o perfil e selecione **Excluir perfil**.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Excluir o perfil ponderado do Gerenciador de Tráfego":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o método de roteamento ponderado, confira:

> [!div class="nextstepaction"]
> [Método de roteamento de tráfego ponderado](traffic-manager-routing-methods.md#weighted)
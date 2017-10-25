---
title: "Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Este artigo explica como configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego do Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 13190189074b24b2d28cd3ce46cf8571f3e1e1d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego

O método de roteamento de tráfego geográfico permite direcionar tráfego para pontos de extremidade específicos com base na localização geográfica em que as solicitações são originadas. Este tutorial mostra como criar um perfil do Gerenciador de Tráfego com esse método de roteamento e configurar os pontos de extremidade para receber tráfego de regiões geográficas específicas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

1. Usando um navegador, entre no [portal do Azure](http://portal.azure.com). Caso ainda não tenha uma conta, você pode se inscrever para obter uma [avaliação gratuita por um mês](https://azure.microsoft.com/free/).
2. No menu Hub, clique em **Novo** > **Rede** > **Ver tudo** e no **perfil do Gerenciador de Tráfego** para abrir a folha **Criar perfil do Gerenciador de Tráfego**.
3. Na folha **Criar perfil do Gerenciador de Tráfego**:
    1. Forneça um nome para seu perfil. Esse nome deve ser exclusivo na zona trafficmanager.net e resultará no nome DNS <profilename>, trafficmanager.net, que será usado para acessar o seu perfil do Gerenciador de Tráfego.
    2. Selecione o método de roteamento **Geográfico**.
    3. Selecione a assinatura na qual deseja criar esse perfil.
    4. Use um grupo de recursos existente ou crie um no qual colocar esse perfil. Se você optar por criar um novo grupo de recursos, use a lista suspensa **Localização do Grupo de Recursos** para especificar a localização do grupo de recursos. Essa configuração refere-se ao local do grupo de recursos e não tem impacto no perfil do Gerenciador de Tráfego que será implantado globalmente.
    5. Depois de clicar em **Criar**, seu perfil do Gerenciador de Tráfego será criado e implantada globalmente.

![Criar um perfil do Gerenciador de Tráfego](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Adicionar pontos de extremidade

1. Procure na barra de pesquisa do portal o nome do perfil do Gerenciador de Tráfego que você acabou de criar e clique no resultado quando ele for mostrado.
2. Navegue até **Configurações** -> **Pontos de Extremidade** na folha Gerenciador de Tráfego.
3. Clique em **Adicionar** para exibir a folha **Adicionar Ponto de Extremidade**.
3. Na folha **Pontos de Extremidade**, clique em **Adicionar** na folha **Adicionar ponto de extremidade** que é exibida e preencha como se segue:
4. Selecione **Tipo**, dependendo do tipo de ponto de extremidade que você está adicionando. Para perfis de roteamento geográfico usados na produção, é altamente recomendável usar tipos de ponto de extremidade aninhados que contenham um perfil filho com mais de um ponto de extremidade. Para obter mais detalhes, confira as [perguntas frequentes sobre métodos de roteamento de tráfego geográfico](traffic-manager-FAQs.md).
5. Forneça um **Nome** pelo qual deseja reconhecer esse ponto de extremidade.
6. Determinados campos nessa folha dependem do tipo de ponto de extremidade que você está adicionando:
    1. Se estiver adicionando um ponto de extremidade do Azure, selecione o **Tipo de recurso de destino** e o **Destino** com base no recurso para o qual quer direcionar o tráfego
    2. Se estiver adicionando um ponto de extremidade **Externo**, forneça o **FQDN (nome de domínio totalmente qualificado)** para o ponto de extremidade.
    3. Se estiver adicionando um **ponto de extremidade Aninhado**, selecione o **Recurso de destino** que corresponda ao perfil filho que quer usar e especifique a **Contagem mínima de pontos de extremidade filho**.
7. Na seção de mapeamento de área geográfica, use a lista suspensa para adicionar as regiões de onde você quer que o tráfego seja enviado para esse ponto de extremidade. Você deve adicionar pelo menos uma região e pode ter várias regiões mapeadas.
8. Repita esse procedimento para todos os pontos de extremidade que queira adicionar nesse perfil

![Adicionar um ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Usar o perfil do Gerenciador de Tráfego
1.  Na barra de pesquisa do portal, pesquise o nome do **Perfil do Gerenciador de Tráfego** que você criou na seção anterior e clique no perfil do gerenciador de tráfego nos resultados que são exibidos.
2. Na folha **Perfil do Gerenciador de Tráfego**, clique em **Visão Geral**.
3. A folha **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego recém-criado. Isso pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento.  No caso de roteamento geográfico, o Gerenciador de Tráfego examina o IP de origem da solicitação de entrada e determina a região da qual ela se origina. Se essa região estiver mapeada para um ponto de extremidade, o tráfego será roteado para lá. Se essa região não estiver mapeada para um ponto de extremidade, o Gerenciador de Tráfego retornará uma resposta de consulta NODATA.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Método de roteamento de tráfego geográfico](traffic-manager-routing-methods.md#geographic).
- Saiba como [testar as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).

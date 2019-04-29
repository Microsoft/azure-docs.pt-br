---
title: Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego do Azure
description: Este artigo explica como configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego do Azure
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 47cc56aac7d3e0147ef8577aac19776c6cacf7a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884119"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego

O método de roteamento de tráfego geográfico permite direcionar tráfego para pontos de extremidade específicos com base na localização geográfica em que as solicitações são originadas. Este tutorial mostra como criar um perfil do Gerenciador de Tráfego com esse método de roteamento e configurar os pontos de extremidade para receber tráfego de regiões geográficas específicas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

1. Usando um navegador, entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para obter uma [avaliação gratuita de um mês](https://azure.microsoft.com/free/).
2. Clique em **Criar um recurso** > **Rede** > **Perfil do Gerenciador de Tráfego** > **Criar**.
4. Em **Criar perfil do Gerenciador de Tráfego**:
    1. Forneça um nome para seu perfil. Esse nome deve ser exclusivo dentro da zona trafficmanager.net. Para acessar seu perfil do Gerenciador de tráfego, você pode usar o nome DNS `<profilename>.trafficmanager.net`.
    2. Selecione o método de roteamento **Geográfico**.
    3. Selecione a assinatura na qual deseja criar esse perfil.
    4. Use um grupo de recursos existente ou crie um no qual colocar esse perfil. Se você optar por criar um novo grupo de recursos, use a lista suspensa **Localização do Grupo de Recursos** para especificar a localização do grupo de recursos. Essa configuração refere-se ao local do grupo de recursos e não tem impacto no perfil do Gerenciador de Tráfego que é implantado globalmente.
    5. Depois de clicar em **Criar**, seu perfil do Gerenciador de Tráfego será criado e implantada globalmente.

![Criar um perfil do Gerenciador de Tráfego](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Adicionar pontos de extremidade

1. Procure pelo nome do perfil do Gerenciador de Tráfego que você criou na barra de pesquisa do portal e clique no resultado quando ele for exibido.
2. Navegue até **Configurações** -> **Pontos de Extremidade** no Gerenciador de Tráfego.
3. Clique em **Adicionar** para exibir **Adicionar Ponto de Extremidade**.
3. Clique em **Adicionar**, e em **Adicionar ponto de extremidade** que é exibido, preencha como se segue:
4. Selecione **Tipo**, dependendo do tipo de ponto de extremidade que você está adicionando. Para perfis de roteamento geográfico usados na produção, é altamente recomendável usar tipos de ponto de extremidade aninhados que contenham um perfil filho com mais de um ponto de extremidade. Para obter mais detalhes, confira as [perguntas frequentes sobre métodos de roteamento de tráfego geográfico](traffic-manager-FAQs.md).
5. Forneça um **Nome** pelo qual deseja reconhecer esse ponto de extremidade.
6. Determinados campos nessa página dependem do tipo de ponto de extremidade que você está adicionando:
    1. Se estiver adicionando um ponto de extremidade do Azure, selecione o **Tipo de recurso de destino** e o **Destino** com base no recurso para o qual quer direcionar o tráfego
    2. Se estiver adicionando um ponto de extremidade **Externo**, forneça o **FQDN (nome de domínio totalmente qualificado)** para o ponto de extremidade.
    3. Se estiver adicionando um **ponto de extremidade Aninhado**, selecione o **Recurso de destino** que corresponda ao perfil filho que quer usar e especifique a **Contagem mínima de pontos de extremidade filho**.
7. Na seção de mapeamento de área geográfica, use a lista suspensa para adicionar as regiões de onde você quer que o tráfego seja enviado para esse ponto de extremidade. Você deve adicionar pelo menos uma região e pode ter várias regiões mapeadas.
8. Repita esse procedimento para todos os pontos de extremidade que queira adicionar nesse perfil

![Adicionar um ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Usar o perfil do Gerenciador de Tráfego
1.  Na barra de pesquisa do portal, pesquise o nome do **Perfil do Gerenciador de Tráfego** que você criou na seção anterior e clique no perfil do gerenciador de tráfego nos resultados que são exibidos.
2. Clique em **Visão Geral**.
3. O **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente. Isso pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento.  No caso de roteamento geográfico, o Gerenciador de Tráfego examina o IP de origem da solicitação de entrada e determina a região da qual ela se origina. Se essa região estiver mapeada para um ponto de extremidade, o tráfego será roteado para lá. Se essa região não estiver mapeada para um ponto de extremidade, o Gerenciador de Tráfego retornará uma resposta de consulta NODATA.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Método de roteamento de tráfego geográfico](traffic-manager-routing-methods.md#geographic).
- Saiba como [testar as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).

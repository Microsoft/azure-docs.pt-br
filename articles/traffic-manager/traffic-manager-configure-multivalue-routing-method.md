---
title: Configurar o roteamento de tráfego de vários valores-Gerenciador de tráfego do Azure
description: Este artigo explica como configurar o Gerenciador de Tráfego para rotear tráfego para pontos de extremidades A/AAAA.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 85e088dda767a6f6c80ac0a9f6eed84e8802e5ee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994920"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Configurar o método de roteamento de Múltiplos Valores no Gerenciador de Tráfego

Este artigo descreve como configurar o método de roteamento de tráfego de Múltiplos Valores. O método de roteamento de tráfego de **Múltiplos Valores** permite retornar vários pontos de extremidade íntegros e ajuda a aumentar a confiabilidade do aplicativo, já que os clientes têm mais opções para tentar novamente sem precisar fazer outra pesquisa de DNS. O roteamento de Múltiplos Valores é habilitado somente para perfis que tenham todos os seus pontos de extremidade especificados usando endereços IPv4 ou IPv6. Quando uma consulta é recebida para esse perfil, todos os pontos de extremidades íntegros são retornados com base na contagem máxima de retorno configurável especificada. 

>[!NOTE]
> Atualmente, adicionar pontos de extremidade usando endereços IPv4 ou IPv6 tem suporte somente para pontos de extremidade do tipo **Externo** e, portanto, o roteamento de Múltiplos Valores também tem suporte apenas para esses pontos de extremidades.

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos para o perfil do Gerenciador de Tráfego.
1. No painel esquerdo do portal do Azure, selecione **Grupos de recursos**.
2. Em **Grupos de recursos**, na parte superior da página, selecione **Adicionar**.
3. Em **Nome do grupo de recursos**, digite um nome *myResourceGroupTM1*. Para **Localização do grupo de recursos**, selecione **Leste dos EUAS** e, em seguida, selecione **OK**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego
Crie um perfil do Gerenciador de Tráfego que direciona o tráfego do usuário enviando-os para o ponto de extremidade com a menor latência.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Perfil do Gerenciador de Tráfego** > **Criar**.
2. Em **Criar perfil do Gerenciador de Tráfego**, insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:
    
    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Esse nome deve ser exclusivo na zona trafficmanager.net e resulta no nome DNS, trafficmanager.net, que é usado para acessar o seu perfil do Gerenciador de Tráfego.                                   |
    | Método de roteamento          | Selecione o método de roteamento de **Múltiplos valores**.                                       |
    | Subscription            | Selecione sua assinatura.                          |
    | Resource group          | Selecione *myResourceGroupTM1*. |
    | Location                | Essa configuração refere-se ao local do grupo de recursos e não tem impacto no perfil do Gerenciador de Tráfego que será implantado globalmente.                              |
   |        |           | 
  
   ![Criar um perfil do Gerenciador de Tráfego](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

Adicione dois endereços IP como pontos de extremidade externos ao perfil do Gerenciador de Tráfego de Múltiplos Valores que você criou na etapa anterior.

1. Na barra de pesquisa do portal, pesquise o nome do Perfil do Gerenciador de Tráfego que você criou na seção anterior e selecione o perfil do gerenciador de tráfego nos resultados que são exibidos.
2. Em **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade** e clique em **Adicionar**.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto de extremidade externo                                   |
    | Name           | myEndpoint1                                        |
    | FQDN (nome de domínio totalmente qualificado) ou IP           | Digite o endereço IP público do ponto de extremidade que você que adicionar a esse perfil do Gerenciador de Tráfego                         |
    |        |           |

4. Repita as etapas 2 e 3 para adicionar outro ponto de extremidade nomeado *myEndpoint2*, para **FQDN (nome de domínio totalmente qualificado) ou IP**, insira o endereço IP público do segundo ponto de extremidade.
5. Quando a adição de ambos os pontos de extremidade estiver concluída, eles serão exibidos em **Perfil do Gerenciador de Tráfego** com seu status de monitoramento como **Online**.

   ![Adicionar um ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [método de roteamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de roteamento de prioridade](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)
- Saiba mais sobre o [método de roteamento geográfico](traffic-manager-configure-geographic-routing-method.md).



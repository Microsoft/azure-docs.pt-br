---
title: "Criar um perfil do Gerenciador de Tráfego no Azure | Microsoft Docs"
description: "Este artigo descreve como criar um perfil do Gerenciador de Tráfego"
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
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: e9ff7947e7801a9f352a7a947b09893b8f615d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Este artigo descreve como um perfil com tipo de roteamento **Prioridade** pode ser criado para rotear usuários para dois pontos de extremidade de aplicativos Web do Azure. Usando o tipo de roteamento **Prioridade**, todo o tráfego é roteado para o primeiro ponto de extremidade, enquanto o segundo é mantido como um backup. Como resultado, os usuários podem ser roteados para o segundo ponto de extremidade, caso o primeiro ponto de extremidade se torne não íntegro.

Neste artigo, dois pontos de extremidade de aplicativo Web do Azure anteriormente criados são associados a esse perfil do Gerenciador de Tráfego recém-criado. Para saber mais sobre como criar pontos de extremidade de aplicativo Web do Azure, visite a [página de documentação de aplicativos Web do Azure](https://docs.microsoft.com/azure/app-service/). Você pode adicionar qualquer ponto de extremidade que tenha um nome DNS e possa ser acessado pela Internet pública. Assim, estamos usando pontos de extremidade de Aplicativos Web do Azure como um exemplo.

### <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego
1. Usando um navegador, entre no [portal do Azure](http://portal.azure.com). Caso ainda não tenha uma conta, você pode se inscrever para obter uma [avaliação gratuita por um mês](https://azure.microsoft.com/free/). 
2. No menu **Hub**, clique em **Novo** > **Rede** > **Ver tudo**, clique no perfil do **Gerenciador de Tráfego** para abrir a folha **Criar perfil do Gerenciador de Tráfego** e clique em **Criar**.
3. Na folha **Criar perfil do Gerenciador de Tráfego**, preencha os seguintes campos:
    1. Em **Nome**, forneça um nome para seu perfil. Esse nome deve ser exclusivo na zona trafficmanager.net e resulta no nome DNS <name>, trafficmanager.net, que é usado para acessar o seu perfil do Gerenciador de Tráfego.
    2. Em **Método de roteamento**, selecione o método de roteamento **Prioridade**.
    3. Em **Assinatura**, selecione a assinatura sob a qual deseja criar esse perfil
    4. Em **Grupo de Recursos**, crie um novo grupo de recursos no qual colocar esse perfil.
    5. Em **Local do grupo de recursos**, selecione o local do grupo de recursos. Essa configuração refere-se ao local do grupo de recursos e não tem impacto no perfil do Gerenciador de Tráfego que será implantado globalmente.
    6. Clique em **Criar**.
    7. Quando a implantação global do seu perfil do Gerenciador de Tráfego estiver concluída, ela será listada no respectivo grupo de recursos como um dos recursos.

    ![Criar um perfil do Gerenciador de Tráfego](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

1. Na barra de pesquisa do portal, pesquise o nome do **Perfil do Gerenciador de Tráfego** que você criou na seção anterior e clique no perfil do gerenciador de tráfego nos resultados que são exibidos.
2. Na folha **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, clique em **Pontos de Extremidade**.
3. Na folha **Pontos de Extremidade** que é exibida, clique em **Adicionar**.
4. Na folha **Adicionar ponto de extremidade**, preencha os seguintes campos:
    1. Para **Tipo**, clique em **Ponto de Extremidade do Azure**.
    2. Forneça um **Nome** pelo qual deseja reconhecer esse ponto de extremidade.
    3. Para **Tipo de recurso de destino**, clique em **Serviço de Aplicativo**.
    4. Para **Recurso de destino**, clique em **Escolher um serviço de aplicativo** para mostrar a lista dos Aplicativos Web sob a mesma assinatura. Na folha **Recursos** que é exibida, escolha o Serviço de aplicativo que deseja adicionar como o primeiro ponto de extremidade.
    5. Para **Prioridade**, selecione **1**. Isso faz com que todo o tráfego passe para esse ponto de extremidade, se ele estiver íntegro.
    6. Mantenha a opção **Adicionar como desabilitado** desmarcada.
    7. Clique em **OK**
5.  Repita as etapas 3 e 4 para o próximo ponto de extremidade de Aplicativos Web do Azure. Certifique-se de adicioná-lo com o valor de **Prioridade** definido como **2**.
6.  Quando a adição de ambos os pontos de extremidade estiver concluída, eles serão exibidos na folha **Perfil do Gerenciador de Tráfego** com seu status de monitoramento como **Online**.

    ![Adicionar um ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Usar o perfil do Gerenciador de Tráfego
1.  Na barra de pesquisa do portal, procure o nome do **Perfil do Gerenciador de Tráfego** criado na seção anterior. Nos resultados exibidos, clique no perfil do gerenciador de tráfego.
2. Na folha **Perfil do Gerenciador de Tráfego**, clique em **Visão Geral**.
3. A folha **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego recém-criado. Isso pode ser usado por todos os clientes (por exemplo, navegando até ele usando um navegador da Web) para ser roteado para o ponto de extremidade correto, conforme determinado pelo tipo de roteamento. Nesse caso, todas as solicitações são roteadas para o primeiro ponto de extremidade e, se o Gerenciador de Tráfego detectá-lo como não íntegro, o tráfego fará failover automaticamente para o próximo ponto de extremidade.

## <a name="delete-the-traffic-manager-profile"></a>Excluir o perfil do Gerenciador de Tráfego
Quando não for mais necessário, exclua o grupo de recursos e o perfil do Gerenciador de Tráfego que você criou. Para fazer isso, selecione o grupo de recursos na folha **Perfil do Gerenciador de Tráfego** e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [tipos de roteamento](traffic-manager-routing-methods.md).
- Saiba mais sobre os tipos de ponto de extremidade [tipos de ponto de extremidade](traffic-manager-endpoint-types.md).
- Saiba mais sobre [monitoramento do ponto de extremidade](traffic-manager-monitoring.md).




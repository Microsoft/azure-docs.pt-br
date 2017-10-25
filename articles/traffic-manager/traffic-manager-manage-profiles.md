---
title: "Gerenciar perfis do Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Este artigo o ajuda a criar, desabilitar, habilitar e excluir um perfil do Gerenciador de Tráfego do Azure."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: kumud
ms.openlocfilehash: a5164282264124835692bc72a4ab61891aa7af9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Gerenciar um perfil de Gerenciador de tráfego do Azure

Perfis do Gerenciador de Tráfego usam métodos de roteamento de tráfego para controlar a distribuição de tráfego para os serviços de nuvem ou pontos de extremidade de site. Este artigo explica como criar e gerenciar esses perfis.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Você pode criar um perfil do Gerenciador de Tráfego usando o portal do Azure. Depois de criar seu perfil, você pode definir pontos de extremidade, monitoramento e outras configurações no portal do Azure. O Gerenciador de Tráfego dá suporte a até 200 pontos de extremidade por perfil. No entanto, a maioria dos cenários de uso exigem apenas um pequeno número de pontos de extremidade.

### <a name="to-create-a-traffic-manager-profile"></a>Para criar um perfil do Gerenciador de Tráfego

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

## <a name="disable-enable-or-delete-a-profile"></a>Desabilitar, habilitar ou excluir um perfil

Você pode desabilitar um perfil existente para que o Gerenciador de Tráfego não referencie solicitações de usuários para os pontos de extremidade configurados. Quando você desabilita um perfil do Gerenciador de Tráfego, o próprio perfil e as informações contidas nele permanecem intactos e podem ser editados na interface do Gerenciador de Tráfego.  As referências retomam quando você reabilita o perfil. Quando você cria um perfil do Gerenciador de Tráfego no portal do Azure, ele é automaticamente habilitado. Se você decidir que um perfil não é mais necessário, poderá excluí-lo.

### <a name="to-disable-a-profile"></a>Para desabilitar um perfil

1. Se você estiver usando um nome de domínio personalizado, altere o registro CNAME em seu servidor DNS da Internet para que ele não aponte para o perfil do Gerenciador de Tráfego.
2. O tráfego deixará de ser direcionado para os pontos de extremidade por meio das configurações de perfil do Gerenciador de Tráfego.
3. Usando um navegador, entre no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, pesquise o nome do **perfil do Gerenciador de Tráfego** que você deseja modificar e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha **Perfil do Gerenciador de Tráfego**, clique em **Visão geral**, na folha Visão geral, clique em **Desabilitar** e confirme a desabilitação do perfil do Gerenciador de Tráfego.

### <a name="to-enable-a-profile"></a>Para habilitar um perfil

1. Usando um navegador, entre no [portal do Azure](http://portal.azure.com).
2. Na barra de pesquisa do portal, pesquise o nome do **perfil do Gerenciador de Tráfego** que você deseja modificar e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha **Perfil do Gerenciador de Tráfego**, clique em **Visão geral** e, na folha Visão geral, clique em **Habilitar**.
5. Se você estiver usando um nome de domínio personalizado, crie um registro de recurso CNAME em seu servidor DNS da Internet para apontar para o nome de domínio de seu perfil do Gerenciador de Tráfego.
6. O tráfego é novamente direcionado para os pontos de extremidade.

### <a name="to-delete-a-profile"></a>Para excluir um perfil

1. Verifique se o registro de recurso DNS no servidor DNS da Internet não usa mais um registro de recurso CNAME que aponta para o nome de domínio de seu perfil do Gerenciador de Tráfego.
2. Na barra de pesquisa do portal, pesquise o nome do **perfil do Gerenciador de Tráfego** que você deseja modificar e clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na folha **Perfil do Gerenciador de Tráfego**, clique em **Visão geral**, na folha Visão geral, clique em **Excluir** e confirme a exclusão do perfil do Gerenciador de Tráfego.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar um ponto de extremidade](traffic-manager-endpoints.md)
* [Configurar o método de roteamento de prioridades](traffic-manager-configure-priority-routing-method.md)
* [Configurar o método de roteamento geográfico](traffic-manager-configure-geographic-routing-method.md) 
* [Configurar o método de roteamento ponderado](traffic-manager-configure-weighted-routing-method.md)
* [Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)

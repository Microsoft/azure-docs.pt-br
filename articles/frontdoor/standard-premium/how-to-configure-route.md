---
title: Configurar rota de porta frontal do Azure
description: Este artigo mostra como configurar uma rota entre seus domínios e grupos de origem.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098343"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Configurar uma rota Premium/standard do Azure front door

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

Este artigo explica cada uma das configurações usadas na criação de uma rota do Azure front door (AFD) para um ponto de extremidade existente. Depois de adicionar um domínio personalizado e a origem ao ponto de extremidade existente do Azure front door, você precisará configurar a rota para definir a associação entre domínios e origens para rotear o tráfego entre eles.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma rota de porta frontal do Azure, você deve ter criado pelo menos um grupo de origem e um domínio personalizado dentro do ponto de extremidade atual. 

Para configurar um grupo de origem, consulte [criar um novo grupo de origem padrão/Premium do Azure front door](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Criar uma nova rota padrão/Premium da porta do Azure

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o perfil Standard/Premium da porta de front-end do Azure.

1. Selecione **Gerenciador de pontos de extremidade** em **configurações**.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Captura de tela das configurações do Gerenciador de pontos de extremidade da porta." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Em seguida, selecione **Editar ponto de extremidade** para o ponto de extremidade que você deseja configurar a rota.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Captura de tela da seleção de Editar ponto de extremidade.":::

1. A página **Editar ponto de extremidade** será exibida. Selecione **+ Adicionar** para rotas.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Captura de tela de adicionar uma rota na página Editar ponto de extremidade.":::    
    
1. Na página **Adicionar rota** , insira ou selecione as informações a seguir.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Captura de tela da página Adicionar uma rota." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Configuração | Valor |
    | --- | --- |
    | Nome | Insira um nome exclusivo para a nova rota. |   
    | Domain| Selecionar um ou mais domínios que foram validados e que não estão associados a outra rota |
    | Padrões para correspondência  | Configure todos os padrões de caminho de URL que serão aceitos por essa rota. Por exemplo, você pode definir isso como `/images/*` para aceitar todas as solicitações na URL `www.contoso.com/images/*` . O AFD tentará determinar o tráfego com base na correspondência exata primeiro, se não houver nenhum caminho de correspondência exato e, em seguida, procurar por um Path curinga que corresponda. Se nenhuma regra de roteamento for encontrada com um Caminho correspondente, rejeite a solicitação e retorne uma resposta HTTP 400: Erro de solicitação inválida. |
    | Protocolos aceitos | Especifique os protocolos que você deseja que o Azure front door aceite quando o cliente estiver fazendo a solicitação. |
    | Redirecionar | Especificar se o HTTPS é imposto para a solicitação de entrada com a solicitação HTTP |
    | Grupo de origem | Selecione a qual grupo de origem deve ser encaminhado quando ocorrer a solicitação de volta à origem. |
    | Caminho de origem | Insira o caminho para os recursos que você deseja armazenar em cache. Para permitir o armazenamento em cache de qualquer recurso no domínio, deixe essa configuração em branco. |
    | Protocolo de encaminhamento | Selecione o protocolo usado para encaminhar a solicitação. |
    | Cache | Selecione esta opção para habilitar o cache de conteúdo estático com a porta frontal do Azure. |
    | Regra | Selecione conjuntos de regras que serão aplicados a esta rota. Para obter mais informações sobre como configurar regras, consulte [configurar um conjunto de regras para a porta frontal do Azure](how-to-configure-rule-set.md) | 

1. Selecione **Adicionar** para criar a nova rota. A rota aparecerá na lista de rotas para o ponto de extremidade.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Captura de tela da lista de rotas.":::  
    
## <a name="clean-up-resources"></a>Limpar recursos

Para excluir uma rota quando ela não for mais necessária, selecione a rota e, em seguida, selecione **excluir**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Captura de tela de como excluir uma rota.":::  

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre domínios personalizados, prossiga para o tutorial para adicionar um domínio personalizado ao ponto de extremidade de porta de frente do Azure.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado]()

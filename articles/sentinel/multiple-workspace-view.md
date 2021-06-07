---
title: Trabalhe com incidentes do Azure Sentinel em muitos espaços de trabalho ao mesmo tempo | Microsoft Docs
description: Como exibir incidentes em vários espaços de trabalho simultaneamente no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "83124154"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Trabalhe com incidentes em muitos espaços de trabalho ao mesmo tempo 

 Para aproveitar ao máximo os recursos do Azure Sentinel, a Microsoft recomenda usar um ambiente de espaço de trabalho único. No entanto, há alguns casos de uso que exigem ter vários espaços de trabalho, em alguns casos – por exemplo, de um [MSSP (provedor de serviços de segurança gerenciado)](./multiple-tenants-service-providers.md) e de seus clientes – em vários locatários. A **exibição de vários espaços de trabalho** permite ver e trabalhar com incidentes de segurança em vários espaços de trabalho ao mesmo tempo, mesmo entre locatários, permitindo que você mantenha a visibilidade e o controle totais da capacidade de resposta de segurança de sua organização.

## <a name="entering-multiple-workspace-view"></a>Inserindo exibição de vários espaços de trabalho

Ao abrir o Azure Sentinel, você verá uma lista de todos os espaços de trabalho aos quais você tem direitos de acesso, em todos os locatários e assinaturas selecionados. À esquerda de cada nome de espaço de trabalho está uma caixa de seleção. Clicar no nome de um único espaço de trabalho irá levá-lo para esse espaço de trabalho. Para escolher vários espaços de trabalho, clique em todas as caixas de seleção correspondentes e, em seguida, clique no botão **exibir vários espaços de trabalho** na parte superior da página.

> [!IMPORTANT]
> A exibição de vários espaços de trabalho atualmente dá suporte a um máximo de 10 espaços de trabalho exibidos simultaneamente. 
> 
> Se você marcar mais de 10 espaços de trabalho, uma mensagem de aviso será exibida.

Observe que na lista de espaços de trabalho, você pode ver o diretório, a assinatura, o local e o grupo de recursos associados a cada espaço de trabalho. O diretório corresponde ao locatário.

   ![Escolher vários espaços de trabalho](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Trabalhando com incidentes

No **modo de exibição de vários espaços de trabalho**, somente a tela **incidentes** está disponível por enquanto. Ele parece e funciona da maioria das formas como a tela **incidentes** regulares. No entanto, há algumas diferenças importantes:

   ![Exibir incidentes em vários espaços de trabalho](./media/multiple-workspace-view/incidents.png)

- Os contadores na parte superior da página- *incidentes abertos*, *novos incidentes*, *em andamento*, etc. – mostram os números de todos os espaços de trabalho selecionados coletivamente.

- Você verá incidentes de todos os espaços de trabalho e diretórios (locatários) selecionados em uma única lista unificada. Você pode filtrar a lista por espaço de trabalho e diretório, além dos filtros da tela **incidentes** regulares.

- Você precisará ter permissões de leitura e gravação em todos os espaços de trabalho dos quais você selecionou incidentes. Se você tiver apenas permissões de leitura em alguns espaços de trabalho, você verá mensagens de aviso se selecionar incidentes nesses espaços de trabalho. Você não poderá modificar esses incidentes ou quaisquer outros que tenha selecionado junto com eles (mesmo que você tenha permissões para os outros).

- Se você escolher um único incidente e clicar em **Exibir detalhes completos** ou **investigar**, você estará no contexto de dados do espaço de trabalho desse incidente e nenhum outro.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a exibir e trabalhar com incidentes em vários espaços de trabalho do Azure Sentinel simultaneamente. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).


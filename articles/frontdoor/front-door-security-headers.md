---
title: 'Tutorial: Adicionar cabeçalhos de segurança com o Mecanismo de Regras – Azure Front Door'
description: Este tutorial ensina como configurar um cabeçalho de segurança por meio do Mecanismo de Regras no Azure Front Door
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: e8462b98aee1d34b6c7a2ed671667223eca45a25
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551910"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Tutorial: Adicionar cabeçalhos de segurança com o Mecanismo de Regras

Este tutorial mostra como implementar cabeçalhos de segurança para impedir vulnerabilidades baseadas em navegador como HSTS (HTTP Strict-Transport-Security), X-XSS-Protection, Content-Security-Policy ou X-Frame-Options. Os atributos baseados em segurança também podem ser definidos com cookies.

O exemplo a seguir mostra como adicionar um cabeçalho Content-Security-Policy a todas as solicitações de entrada que correspondem ao caminho definido na rota à qual a configuração do mecanismo de regras está associada. Aqui, só permitiremos a execução de scripts do nosso site confiável, **https://apiphany.portal.azure-api.net** , no aplicativo.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Configure um Content-Security-Policy no Mecanismo de Regras.

## <a name="prerequisites"></a>Pré-requisitos

* Antes de poder concluir as etapas neste tutorial, é necessário criar primeiro um Front Door. Para saber mais, confira [Início Rápido: Criar um Front Door](quickstart-create-front-door.md).
* Se esta for a primeira vez que você usa o recurso Mecanismo de Regras, confira como [Configurar um Mecanismo de Regras](front-door-tutorial-rules-engine.md).

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Adicionar um cabeçalho Content-Security-Policy no portal do Azure

1. Clique em **Adicionar** para adicionar uma nova regra. Forneça um nome para a regra e clique em **Adicionar uma Ação** > **Cabeçalho de Resposta**.

1. Defina o Operador como **Acréscimo** para adicionar esse cabeçalho como uma resposta a todas as solicitações de entrada para essa rota.

1. Adicione o nome do cabeçalho: **Content-Security-Policy** e defina os valores que esse cabeçalho deverá aceitar. Nesse cenário, escolhemos *"script-src 'self' https://apiphany.portal.azure-api.net".*

   > [!NOTE]
   > Os valores de cabeçalho estão limitados a 128 caracteres.

1. Depois de adicionar todas as regras desejadas à configuração, não se esqueça de acessar sua rota preferencial e associar a configuração do mecanismo de regras à regra de rota. Essa etapa é necessária para permitir que a regra funcione. 

![amostra do portal](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> Neste cenário, não adicionamos [condições de correspondência](front-door-rules-engine-match-conditions.md) à regra. Todas as solicitações de entrada que corresponderem ao caminho definido na regra de rota terão essa regra aplicada. Caso deseje que ela só se aplique a um subconjunto dessas solicitações, lembre-se de adicionar as **condições de correspondência** específicas a essa regra.

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você configurou Cabeçalhos de segurança com o Mecanismo de Regras. Se você não quiser mais a regra, poderá removê-la clicando em Excluir regra.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Excluir regra":::

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar um Firewall de Aplicativo Web para seu Front Door, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Firewall do Aplicativo Web e Front Door](front-door-waf.md)

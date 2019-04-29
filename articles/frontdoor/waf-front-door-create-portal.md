---
title: Criar uma política de firewall do aplicativo web para frente do Azure usando o portal do Azure
titlesuffix: Azure web application firewall
description: Saiba como criar uma política de WAF (firewall) do aplicativo web usando o portal do Azure.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/8/2019
ms.author: kumud;tyao
ms.openlocfilehash: 26db3a67c3efbd0ba2a5c58facd0c07175f7ed12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460197"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Criar uma política de WAF para frente do Azure usando o portal do Azure

Este artigo descreve como criar uma política de WAF (firewall) do aplicativo web básico do Azure e aplicá-la a um host de front-end na frente do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Crie um perfil de Front Door](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Criar uma política de WAF

Primeiro, crie uma diretiva básica de WAF com gerenciado padrão regra definida (DRS) por meio do portal. 

1. No canto superior esquerdo da tela, selecione **criar um recurso**> pesquise **WAF**> selecione **firewall do aplicativo Web (visualização)** > selecione  **Criar**.
2. No **Noções básicas** guia da **criar uma política de WAF** página, insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Assinatura            |Selecione o nome da sua assinatura de porta da frente.|
    | Grupo de recursos          |Selecione o nome do grupo de recursos da frente.|
    | Nome da política             |Insira um nome exclusivo para sua política de WAF.|

   ![Criar uma política de WAF](./media/waf-front-door-create-portal/basic.png)

3. No **associação** guia da **criar uma política de WAF** página, selecione **Adicionar host de front-end**, insira as seguintes configurações e, em seguida, selecione **Add**:

    | Configuração                 | Value                                              |
    | ---                     | ---                                                |
    | Porta da frente              | Selecione seu nome de perfil de porta da frente.|
    | Host de front-end           | Selecione o nome do seu host de porta da frente e selecione **adicionar**.|
    
    > [!NOTE]
    > Se o host de front-end estiver associado a uma política de WAF, ele é mostrado como esmaecido. Você deve primeiro remover o host de front-end da política associada e, em seguida, reassociar o host de front-end para uma nova política de WAF.
1. Selecione **revisar + criar**, em seguida, selecione **criar**.

## <a name="configure-waf-rules-optional"></a>Configurar regras de WAF (opcionais)

### <a name="change-mode"></a>Alterar modo

Quando você cria uma política de WAF, o padrão do waf política está em **detecção** modo. Na **detecção** modo, o WAF não bloqueia todas as solicitações, em vez disso, as solicitações de correspondência com as regras de WAF são registradas nos logs de WAF.
Para ver o WAF em ação, você pode alterar as configurações do modo de **detecção** à **prevenção**. Na **prevenção** modo, as solicitações que regras de correspondência que são definidas no padrão regra definida (DRS) são bloqueadas e registradas em logs de WAF.

 ![Modo de WAF de alteração de diretiva](./media/waf-front-door-create-portal/policy.png)

### <a name="default-rule-set-drs"></a>Conjunto de regras padrão (DRS)

Conjunto de regras de padrão gerenciados do Azure é habilitado por padrão. Para desabilitar uma regra individual dentro de um grupo de regras, expandir as regras dentro desse grupo de regra, selecione a **caixa de seleção** na frente de número da regra e selecione **desabilitar** na guia acima. Para alterar os tipos de ações para regras individuais dentro a regra definida, marque a caixa de seleção na frente do número de regra e, em seguida, selecione a **alterar a ação** guia acima.

 ![Alterar o conjunto de regras de WAF](./media/waf-front-door-create-portal/managed.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [firewall do aplicativo web do Azure](waf-overview.md).
- Saiba mais sobre [do Azure da frente](front-door-overview.md).





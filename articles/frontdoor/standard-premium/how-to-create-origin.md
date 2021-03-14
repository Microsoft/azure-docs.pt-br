---
title: Configurar uma origem do Azure front door Standard/Premium (visualização)
description: Este artigo mostra como configurar uma origem com o Gerenciador de ponto de extremidade.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741881"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Configurar uma origem do Azure front door Standard/Premium (visualização)

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

Este artigo mostrará como criar uma origem padrão/Premium da porta do Azure em um grupo de origem existente. 

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma origem padrão/Premium da porta frontal do Azure, você deve ter criado pelo menos um grupo de origem.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Criar uma nova origem padrão/Premium da porta do Azure

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o perfil Standard/Premium da porta de front-end do Azure.

1. Selecione o **grupo de origem**. Em seguida, selecione **+ Adicionar** para criar um novo grupo de origem.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Captura de tela da página de aterrissagem do grupo de origem.":::

1. Na página **Adicionar um grupo de origem** , insira um **nome** exclusivo para o novo grupo de origem.

1. Em seguida, selecione **+ Adicionar uma origem** para adicionar uma nova origem a esse grupo de origem. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Captura de tela da página Adicionar uma origem.":::
  
    | Configuração | Valor |
    | --- | --- |
    | Nome | Insira um nome exclusivo para a nova origem de porta de entrada do Azure. |   
    | Tipo de origem | O tipo de recurso que você deseja adicionar. O Standard/Premium da porta do Azure dá suporte à descoberta automática de sua origem de aplicativo do serviço de aplicativo, serviço de nuvem ou armazenamento. Se você quiser um recurso diferente no Azure ou um back-end não Azure, selecione **host personalizado**. |
    | Nome de host  | Se você não selecionou **host personalizado** para tipo de host de origem, selecione o back-end escolhendo o nome do host de origem na lista suspensa. |
    | Cabeçalho de host de origem | Insira o valor do cabeçalho de host que está sendo enviado para o back-end para cada solicitação. Para obter mais informações, consulte [cabeçalho de host de origem](concept-origin.md#hostheader). |
    | Porta HTTP | Insira o valor para a porta à qual a origem dá suporte para o protocolo HTTP. |
    | Porta HTTPS | Insira o valor para a porta à qual a origem dá suporte para o protocolo HTTPS. |
    | Prioridade | Atribua prioridades à sua origem diferente quando desejar usar uma origem de serviço primária para todo o tráfego. Além disso, forneça backups se a origem primária ou de backup não estiver disponível. Para obter mais informações, consulte [Priority](concept-origin.md#priority). |
    | Peso | Atribua pesos a suas origens diferentes para distribuir o tráfego entre um conjunto de origens, seja uniformemente ou de acordo com os coeficientes de peso. Para obter mais informações, consulte [pesos](concept-origin.md#weighted). |
    | Status | Selecione esta opção para habilitar a origem. |
    | Regra | Selecione conjuntos de regras que serão aplicados a esta rota. Para obter mais informações sobre como configurar regras, consulte [configurar um conjunto de regras para a porta frontal do Azure](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > Durante a configuração, as APIs não validam se a origem é inacessível de ambientes de porta frontal. Verifique se a porta frontal pode alcançar sua origem.

1. Selecione **Adicionar** para criar a nova origem. A origem criada deve aparecer na lista de origem com o grupo.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Captura de tela de origem na exibição de lista.":::

1. Selecione **Adicionar** para adicionar o grupo de origem ao ponto de extremidade atual. O grupo de origem deve aparecer dentro do painel de grupo de origem.

## <a name="clean-up-resources"></a>Limpar os recursos
Para excluir um grupo de origem quando ele não for mais necessário, clique em **...** e, em seguida, selecione **excluir** na lista suspensa.

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="Captura de tela de como excluir um grupo de origem.":::

Para excluir uma origem quando você não precisar mais dela, clique em **...** e, em seguida, selecione **excluir** na lista suspensa. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Captura de tela de como excluir uma origem.":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre domínios personalizados, confira [Adicionar um domínio personalizado](how-to-add-custom-domain.md) ao ponto de extremidade padrão/Premium da porta de front-end do Azure.

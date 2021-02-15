---
title: Configurar notificações e modelos de email
titleSuffix: Azure API Management
description: Saiba como configurar notificações e modelos de email no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84690295"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Como configurar notificações e modelos de email no Gerenciamento de API do Azure

O Gerenciamento de API possibilita configurar notificações de eventos específicos e modelos dos emails que são usados para se comunicar com os administradores e desenvolvedores de uma instância do Gerenciamento de API. Este artigo mostra como configurar notificações de eventos disponíveis e fornece uma visão geral da configuração dos modelos dos emails usados desses eventos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma instância do serviço de Gerenciamento de API, conclua o seguinte guia de início rápido: [Criar uma instância de Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Configurar notificações

1.  Selecione sua instância de **Gerenciamento de API** .
2.  Clique em **Notificações** para exibir as notificações disponíveis.

    ![Notificações do editor][api-management-publisher-notifications]

    Os eventos na lista a seguir podem ser configurados para enviar notificações.

    -   **Solicitações de assinatura (que precisam de aprovação)** - Os usuários e destinatários de email especificados receberão notificações por email sobre solicitações de assinaturas de produtos de API que precisarem de aprovação.
    -   **Novas assinaturas** - Os usuários e destinatários de email especificados receberão notificações por email sobre novas assinaturas de produtos de API.
    -   **Solicitações da galeria de aplicativos** - Os usuários e destinatários de email especificados receberão notificações por email quando novos aplicativos forem enviados à galeria de aplicativos.
    -   **CCO** - Os usuários e destinatários de email especificados receberão cópias ocultas de todos os emails enviados a desenvolvedores.
    -   **Novo problema ou comentário** - Os usuários e destinatários de email especificados receberão notificações por email quando um novo problema ou comentário for enviado no portal do desenvolvedor.
    -   **Mensagem de conta encerrada** - Os usuários e destinatários de email especificados receberão notificações por email quando uma conta for encerrada.
    -   **Limite de cota de assinatura se aproximando** - Os usuários e destinatários de email a seguir receberão notificações por email quando o uso de assinaturas estiver se aproximando da cota.

        > [!NOTE]
        > As notificações são disparadas pela política de [cota por assinatura](api-management-access-restriction-policies.md#SetUsageQuota) apenas. [A política de cota por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) não gera notificações.

    Para cada evento, você pode especificar destinatários de email usando a caixa de texto de endereço de email ou selecionando os usuários em uma lista.

3.  Para especificar os endereços de email a serem notificados, insira-os na caixa de texto de endereço de email. Se tiver diversos endereços de email, separe-os por vírgula.

    ![Destinatários da notificação][api-management-email-addresses]

4.  Pressione **Adicionar**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Configurar modelos de notificação

O Gerenciamento de API fornece modelos de notificação para mensagens de email que são enviadas no decorrer da administração e da utilização do serviço. Os seguintes modelos de email são fornecidos.

-   Envio à galeria de aplicativos aprovado
-   Carta de despedida do desenvolvedor
-   Notificação de limite de cota se aproximando
-   Convidar usuário
-   Novo comentário adicionado a um problema
-   Novo problema recebido
-   Nova assinatura ativada
-   Confirmação de renovação de assinatura
-   Solicitação de assinatura negada
-   Solicitação de assinatura recebida

Esses modelos podem ser modificados da forma desejada.

Para exibir e configurar os modelos de email para sua instância de Gerenciamento de API, clique em **Modelos de notificações**.

![Modelos de email][api-management-email-templates]

Cada modelo de email tem um assunto em texto sem formatação e uma definição de corpo em formato HTML. Cada item pode ser personalizado da forma desejada.

![Editor de modelos de email][api-management-email-template]

A lista **Parâmetros** contém parâmetros que, quando inseridos no assunto ou corpo, serão substituídos pelo valor designado quando o email for enviado. Para inserir um parâmetro, posicione o cursor onde quiser que ele seja colocado e clique na seta à esquerda do nome do parâmetro.

> [!NOTE]
> Os parâmetros não são substituídos por valores reais ao visualizar ou enviar um teste.

Para salvar as alterações feitas no modelo de email, clique em **Salvar** ou, para cancelar as alterações, clique em **Descartar**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md

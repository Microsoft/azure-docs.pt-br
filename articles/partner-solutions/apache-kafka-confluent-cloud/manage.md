---
title: Gerenciar uma nuvem confluente-soluções de parceiros do Azure
description: Este artigo descreve o gerenciamento de uma nuvem confluente no portal do Azure. Como configurar o logon único, excluir uma organização confluente e obter suporte.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99989119"
---
# <a name="manage-the-confluent-cloud-resource"></a>Gerenciar o recurso da Confluent Cloud

Este artigo descreve como gerenciar sua instância do Apache Kafka para nuvem confluente no Azure. Ele mostra como configurar o SSO (logon único), excluir uma organização confluente e criar uma solicitação de suporte.

## <a name="single-sign-on"></a>Logon único

Para implementar o SSO para sua organização, o administrador de locatários pode importar o aplicativo da galeria. Esta etapa é opcional. Para obter informações sobre como importar um aplicativo, consulte [início rápido: adicionar um aplicativo ao seu locatário do Azure Active Directory (Azure AD)](../../active-directory/manage-apps/add-application-portal.md). Quando o administrador de locatários importa o aplicativo, os usuários não precisam consentir explicitamente para permitir o acesso ao portal de nuvem confluente.

Para habilitar o SSO, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até a **visão geral** da sua instância do recurso de nuvem confluente.
1. Selecione o link para **gerenciar na nuvem confluente**.

   :::image type="content" source="media/sso-link.png" alt-text="Logon único do portal do influente.":::

1. Se o administrador de locatários não importar o aplicativo de galeria para consentimento de SSO, conceda permissões e consentimento. Essa etapa só é necessária na primeira vez que você acessar o link para **gerenciar na nuvem confluente**.
1. Escolha uma conta do Azure AD para logon único no portal de nuvem confluente.
1. Após o consentimento ser fornecido, você será redirecionado para o portal de nuvem confluente.

## <a name="set-up-cluster"></a>Configurar o cluster

Para obter informações sobre como configurar seu cluster, consulte [criar um cluster em documentação de nuvem de forma confluente](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Excluir organização confluente

Quando você não precisar mais do recurso de nuvem do Fluent, exclua o recurso no Azure e na nuvem confluente.

Para excluir os recursos no Azure:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **todos os recursos** e **filtre pelo nome** do recurso de nuvem ou da _organização confluente_ do **tipo de recurso** . Ou, na portal do Azure, procure o nome do recurso.
1. Na **visão geral** do recurso, selecione **excluir**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Ícone de exclusão de recurso.":::

1. Para confirmar a exclusão, digite o nome do recurso e selecione **excluir**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Avisar para confirmar a exclusão de recursos.":::

Para excluir o recurso na nuvem confluente, confira a documentação para [ambientes de nuvem confluentes-documentação](https://docs.confluent.io/current/cloud/using/environments.html) e [noções básicas de nuvem Antifluent-documentação](https://docs.confluent.io/current/cloud/using/cloud-basics.html)de informações

O cluster e todos os dados no cluster são excluídos permanentemente. Se seu contrato incluir uma cláusula de retenção de dados, o de Fluent manterá seus dados para o período de tempo especificado na [documentação de termos de serviço-Fluent](https://www.confluent.io/confluent-cloud-tos).

Você é cobrado pelo uso rateado até o momento da exclusão do cluster. Depois que o cluster é excluído permanentemente, o de forma fluente envia uma confirmação por email.

## <a name="get-support"></a>Obtenha suporte

Para enviar uma solicitação de suporte para o de forma fluente, entre em contato com o [suporte do Fluent](https://support.confluent.io) ou envie uma solicitação por meio do portal, conforme mostrado abaixo.

> [!NOTE]
> Para os usuários da primeira vez, redefina sua senha antes de entrar no portal de suporte do Fluent. Se você não tiver uma conta com nuvem confluente, envie um email para `cloud-support@confluent.io` para obter assistência adicional.

No portal, você pode enviar uma solicitação por meio da ajuda e do suporte do Azure ou diretamente da instância do Apache Kafka para nuvem confluente no Azure.

Para enviar uma solicitação por meio da ajuda e do suporte do Azure:

1. Selecione **Ajuda + suporte**.
1. Selecione **criar uma solicitação de suporte**.
1. No formulário, selecione **técnico** para o **tipo de problema**. Selecione sua assinatura. Na lista de serviços, selecione **confluente no Azure**.

    :::image type="content" source="media/support-request-help.png" alt-text="Crie uma solicitação de suporte da ajuda.":::

Para enviar uma solicitação de seu recurso, siga estas etapas:

1. Na portal do Azure, selecione sua organização confluente.
1. No menu do lado esquerdo da tela, selecione **nova solicitação de suporte**.
1. Para criar uma solicitação de suporte, selecione o link para o **portal do Fluent**.

    :::image type="content" source="media/support-request.png" alt-text="Crie uma solicitação de suporte da instância.":::

## <a name="next-steps"></a>Próximas etapas

Para obter ajuda com a solução de problemas, consulte [Apache Kafka de solução de problemas para soluções de nuvem confluentes](troubleshoot.md).

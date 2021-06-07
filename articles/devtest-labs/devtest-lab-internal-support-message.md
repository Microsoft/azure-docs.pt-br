---
title: Adicionar uma instrução de suporte interno em um laboratório no Azure DevTest Labs
description: Saiba como publicar uma instrução de suporte interno em um laboratório no Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c9920e6fe7fbfe2a8d0aeacb896150b342981b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480466"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma instrução de suporte interno em um laboratório no Azure DevTest Labs

O Azure DevTest Labs permite que você personalize seu laboratório com uma instrução de suporte interno que fornece aos usuários informações de suporte sobre o laboratório. Por exemplo, você pode fornecer informações de contato para que um usuário saiba como contatar o suporte interno quando precisar de ajuda com solução de problemas ou para acessar recursos no ambiente do laboratório. Você também pode fornecer links para sites internos ou perguntas frequentes que sua equipe pode acessar antes de entrar em contato com o suporte.

Uma instrução de suporte interno tem a finalidade de permitir que você publique informações de laboratório que normalmente não são alteradas com muita frequência. Para notificar os usuários sobre informações de laboratório que são mais temporárias – como atualizações recentes em políticas do laboratório –, consulte [Postar um comunicado em um laboratório](devtest-lab-announcements.md).

Você pode facilmente desabilitar ou editar uma instrução de suporte depois que ela não for mais aplicável.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Etapas para adicionar uma instrução de suporte a um laboratório existente

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços** e selecione **DevTest Labs** na lista. (O laboratório já pode ser exibido no painel, em **Todos os Recursos**).
1. Na lista de laboratórios, selecione o laboratório no qual você deseja adicionar uma instrução de suporte.  
1. Na área **Visão geral** do laboratório, selecione **Configuração e políticas**.  

    ![Botão Configuração e políticas](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. À esquerda, em **CONFIGURAÇÕES**, selecione **Suporte interno**.

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Para criar uma mensagem de suporte interno para os usuários neste laboratório, defina Habilitado como **Sim**.

1. No campo **Mensagem de suporte**, insira a instrução de suporte interno que você quer apresentar aos usuários do laboratório. A mensagem de suporte aceita Markdown. Quando insere o texto da mensagem, você pode ver a área de **Visualização** na parte inferior da tela para ver como a mensagem aparece para os usuários.

    ![Tela de suporte interno para criação da mensagem.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Selecione **Salvar** quando a instrução de suporte estiver pronta para postagem.

Quando não quiser mais mostrar essa mensagem de suporte para os usuários do laboratório, volte para a página **Suporte interno** e defina **Habilitado** como **Não**.

## <a name="steps-for-users-to-view-the-support-message"></a>Etapas para os usuários verem a mensagem de suporte

1. No [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório.

1. Na área **Visão Geral** do laboratório, selecione **Suporte interno**.  

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Se uma mensagem de suporte for publicada, o usuário poderá vê-la no painel de suporte interno.

    ![Painel de suporte interno mostrando mensagem de suporte publicada](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
* Instruções de suporte interno normalmente são usadas para fornecer informações de suporte que não são alteradas com frequência. Você também pode aprender como [postar um comunicado em um laboratório](devtest-lab-announcements.md) para informar os usuários de alterações temporárias ou de atualizações no laboratório.
* [Definir políticas e agendas](devtest-lab-set-lab-policy.md) fornece informações sobre como aplicar as convenções e restrições em sua assinatura usando políticas personalizadas.
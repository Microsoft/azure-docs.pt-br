---
title: Adicionar uma instrução de suporte interno a um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como publicar uma instrução de suporte interno em um laboratório no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: deb98c2c633200ab4be1d763a94fd2a04979a3b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562267"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma instrução de suporte interno em um laboratório no Azure DevTest Labs

O Azure DevTest Labs permite que você personalize seu laboratório com uma instrução de suporte interno que fornece aos usuários informações de suporte sobre o laboratório. Por exemplo, você pode fornecer informações de contato para que um usuário saiba como contatar o suporte interno quando precisar de ajuda com solução de problemas ou para acessar recursos no ambiente do laboratório. Você também pode fornecer links para sites internos ou perguntas frequentes que sua equipe pode acessar antes de entrar em contato com o suporte.

Uma instrução de suporte interno tem a finalidade de permitir que você publique informações de laboratório que normalmente não são alteradas com muita frequência. Para notificar os usuários sobre informações de laboratório que são mais temporárias – como atualizações recentes em políticas do laboratório –, consulte [Postar um comunicado em um laboratório](devtest-lab-announcements.md).

Você pode facilmente desabilitar ou editar uma instrução de suporte depois que ela não for mais aplicável.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Etapas para adicionar uma instrução de suporte a um laboratório existente

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
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
---
title: Como gerenciar configurações? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Como gerenciar configurações, criar workspace, compartilhar workspace e gerenciar a chave de assinatura no Tradutor Personalizado.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895774"
---
# <a name="how-to-manage-settings"></a>Como gerenciar configurações

Na página Configurações do tradutor personalizado, você pode compartilhar seu espaço de trabalho, modificar a chave de assinatura do tradutor e excluir o espaço de trabalho.

Para acessar a página de configurações:

1. Entrar para o [conversor personalizado](https://portal.customtranslator.azure.ai/) portal.
2. No portal do Custom Translator, clique no ícone de engrenagem na barra lateral.

    ![Configurando Link](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Associando a assinatura do Tradutor

Você precisa ter uma chave de assinatura do tradutor associada ao seu espaço de trabalho para treinar ou implantar modelos.

Se você não tem uma assinatura, siga as etapas abaixo:

1. Assine para criar um recurso de tradutor. Siga [como se inscrever no Translator](../translator-how-to-signup.md) para assinar e adquirir uma chave de tradutor.
2. Anote a chave para sua assinatura do tradutor. Qualquer uma das Key1 ou Key2 são aceitáveis.
3. Navegue de volta para o portal do Custom Translator.

## <a name="create-a-new-workspace"></a>Criar um novo workspace

1. Clique no botão "+ criar espaço de trabalho" na barra lateral "Tradutor personalizado".

    ![Criar novo workspace](media/how-to/create-new-workspace.png)

2. Na caixa de diálogo, insira o nome do novo workspace.
3. Clique em "Avançar".
4. Escolha o tipo de assinatura.
5. Selecione a região da assinatura. A região deve corresponder à região selecionada quando a chave de recurso do tradutor foi criada.
6. Insira a chave para sua assinatura do tradutor e clique no botão "salvar".

    ![Criar caixa de diálogo do novo workspace](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>O tradutor personalizado não dá suporte à criação de espaço de trabalho para API de Tradução de Texto recurso (também conhecido como Chave de assinatura do Azure) que foi criada dentro da [VNET habilitada](../../../api-management/api-management-using-with-vnet.md).

### <a name="modify-existing-key"></a>Modificar chave existente

1. Navegue até a página &quot;Configurações&quot; do seu workspace.
2. Clique em alterar chave

    ![Como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key.png)

3. Na caixa de diálogo, insira a chave para sua assinatura do tradutor e clique no botão "salvar".

    ![Caixa de diálogo como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Gerenciar seu workspace

Um workspace é uma área de trabalho para compor e construir o sistema de tradução personalizado. Um workspace pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho precisar ser compartilhada com pessoas diferentes, a criação de vários workspaces poderá ser útil.

## <a name="share-your-workspace"></a>Compartilhe seu workspace

No tradutor Personalizado você pode compartilhar seu workspace com outras pessoas, se diferentes partes do seu trabalho precisarem ser compartilhadas com pessoas diferentes.

1. Navegue até a página &quot;Configurações&quot; do workspace.
2. Clique no botão "adicionar pessoas" na seção "configurações de compartilhamento".

    ![Compartilhar o workspace](media/how-to/share-workspace.png)

3. Na caixa de diálogo, insira uma lista separada por vírgulas de endereços de e-mail com os quais você deseja compartilhar esse workspace. Não se esqueça de compartilhar com o endereço de e-mail que a pessoa usa para fazer login no Custom Translator com. Em seguida, selecione o nível apropriado de permissão de compartilhamento e clique no botão "salvar".

    ![Caixa de diálogo Compartilhar espaço de trabalho](media/how-to/share-workspace-dialog.png)

4. Se o seu workspace ainda tiver o nome padrão &quot;Meu workspace&quot;, você deverá alterá-lo antes de compartilhar seu workspace.
5. Clique em "Salvar".

## <a name="sharing-permissions"></a>Permissões de compartilhamento

1. **Leitor:** Um leitor no workspace poderá visualizar todas as informações no workspace.

2. **Editor:** Um editor no workspace poderá adicionar documentos, treinar modelos e excluir documentos e projetos. Eles podem adicionar uma chave de assinatura, mas não podem modificar com quem o workspace é compartilhado, excluir o workspace ou alterar o nome do workspace.

3. **Proprietário:** um proprietário tem permissões totais para o workspace.

## <a name="change-sharing-permission"></a>Altere a permissão de compartilhamento

Quando um espaço de trabalho é compartilhado, a seção "configurações de compartilhamento" mostra todos os endereços de email com os quais esse espaço de trabalho é compartilhado. Você pode alterar a permissão de compartilhamento existente para cada endereço de e-mail se tiver acesso de proprietário ao workspace.

1. Na seção "configurações de compartilhamento" para cada email, um menu suspenso mostra o nível de permissão atual.

2. Clique no menu suspenso e selecione o novo nível de permissão que você deseja atribuir a esse endereço de e-mail.

    ![Configurações de permissão de compartilhamento](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>Fixe seu espaço de trabalho

Seu primeiro espaço de trabalho criado é fixado por padrão. Cada vez que você entra, seu espaço de trabalho fixado é exibido na carga do site. Se você criou muitos espaços de trabalho e deseja tornar um deles seu padrão ao entrar, você precisa fixá-lo.

1. Na barra lateral, clique no nome do espaço de trabalho que você deseja fixar.
2. Navegue até a página "configurações" do seu espaço de trabalho.
3. Clique no ícone de pino.

    ![Fixar espaço de trabalho](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba [como criar seu espaço de trabalho e projetos](workspace-and-project.md)
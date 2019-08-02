---
title: Como gerenciar configurações? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Como gerenciar configurações, criar espaço de trabalho, compartilhar espaço de trabalho e gerenciar a chave de assinatura no Tradutor Personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595773"
---
# <a name="how-to-manage-settings"></a>Como gerenciar configurações

Na página de configurações do Custom Translator, você pode criar um novo espaço de trabalho, compartilhar seu espaço de trabalho e adicionar ou modificar sua chave de assinatura do Microsoft Translation.

Para acessar a página de configurações:

1. Entrar para o [conversor personalizado](https://portal.customtranslator.azure.ai/) portal.
2. No portal do Custom Translator, clique no ícone de engrenagem na barra lateral.

    ![Configurando Link](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associando a assinatura do Microsoft Translator

Você precisa ter uma chave de assinatura do API de Tradução de Texto associada ao seu espaço de trabalho para treinar ou implantar modelos.

Se você não tem uma assinatura, siga as etapas abaixo:

1. Assine a API de tradução de Texto. Este artigo mostra como se inscrever para a API de tradução de texto.
2. Anote a chave da sua assinatura de tradutor. Qualquer uma das Key1 ou Key2 são aceitáveis.
3. Navegue de volta para o portal do Custom Translator.

### <a name="add-existing-key"></a>Adicionar chave existente

1.  Navegue até a página "Configurações" do seu espaço de trabalho.
2.  Clique em Adicionar chave

    ![Como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key.png)

3. Na caixa de diálogo, insira a chave da assinatura do seu tradutor e clique no botão "Adicionar".

    ![Como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Depois de adicionar uma chave, você pode modificar ou excluir a chave a qualquer momento.

    ![Chave de assinatura depois de adicionar](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gerenciar seu espaço de trabalho

Um espaço de trabalho é uma área de trabalho para compor e construir seu sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho precisar ser compartilhada com pessoas diferentes, a criação de vários espaços de trabalho poderá ser útil.

## <a name="create-a-new-workspace"></a>Criar um novo workspace

1.  Navegue até a página "Configurações" do espaço de trabalho.
2.  Clique no botão “Novo Espaço de trabalho” na seção “Criar Novo Espaço de trabalho”.

    ![Criar novo espaço de trabalho](media/how-to/create-new-workspace.png)

4.  Na caixa de diálogo, insira o nome da nova área de trabalho.
5.  Clique em "Criar".

    ![Criar caixa de diálogo do novo espaço de trabalho](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Compartilhe seu espaço de trabalho

No tradutor Personalizado você pode compartilhar seu espaço de trabalho com outras pessoas, se diferentes partes do seu trabalho precisarem ser compartilhadas com pessoas diferentes.

1.  Navegue até a página "Configurações" do espaço de trabalho.
2.  Clique no botão "Compartilhar" na seção "Configurações de compartilhamento".

    ![Compartilhar o espaço de trabalho](media/how-to/share-workspace.png)

3.  Na caixa de diálogo, insira uma lista separada por vírgulas de endereços de e-mail com os quais você deseja compartilhar essa área de trabalho. Não se esqueça de compartilhar com o endereço de e-mail que a pessoa usa para fazer login no Custom Translator com. Em seguida, selecione o nível apropriado de permissão de compartilhamento.

4.  Se o seu espaço de trabalho ainda tiver o nome padrão "Meu espaço de trabalho", você deverá alterá-lo antes de compartilhar seu espaço de trabalho.
5.  Clique em "Salvar".

## <a name="sharing-permissions"></a>Permissões de compartilhamento

1.  **Leitor:** um leitor no workspace poderá exibir todas as informações no workspace.

2.  **Editor:** um editor no workspace poderá adicionar documentos, treinar modelos e excluir documentos e projetos. Eles podem adicionar uma chave de assinatura, mas não podem modificar com quem a área de trabalho é compartilhada, excluir a área de trabalho ou alterar o nome da área de trabalho.

3.  **Proprietário:** um proprietário tem permissões totais para o workspace.

## <a name="change-sharing-permission"></a>Altere a permissão de compartilhamento

Quando uma área de trabalho é compartilhada, a seção “Configurações de compartilhamento” mostra todos os endereços de e-mail com os quais essa área de trabalho é compartilhada. Você pode alterar a permissão de compartilhamento existente para cada endereço de e-mail se tiver acesso de proprietário ao espaço de trabalho.

1.  Na seção "Configurações de compartilhamento" de cada e-mail, um menu suspenso mostra o nível de permissão atual.

2.  Clique no menu suspenso e selecione o novo nível de permissão que você deseja atribuir a esse endereço de e-mail.

    ![Configurações de permissão de compartilhamento](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Próximas etapas

- Aprenda [como migrar sua área de trabalho e projeto](how-to-migrate.md) de [Microsoft Translator Hub](https://hub.microsofttranslator.com)

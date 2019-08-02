---
title: Gerenciar versões-LUIS
titleSuffix: Azure Cognitive Services
description: As versões permitem que você crie e publique modelos diferentes. É uma boa prática clonar o modelo ativo atual para uma versão diferente do aplicativo antes de fazer alterações no modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dd3cd13b14f58e1ece0b91f924ebefb25682bbf7
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638105"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Usar versões para editar e testar sem afetar aplicativos de preparo ou produção

As versões permitem que você crie e publique modelos diferentes. É uma boa prática clonar o modelo ativo atual para uma [versão](luis-concept-version.md) diferente do aplicativo antes de fazer alterações no modelo. 

Para trabalhar com versões, abra o aplicativo selecionando o nome dele na página **Meus Aplicativos** e, em seguida, selecione **Gerenciar** na barra superior e selecione **Versões**. 

A lista de versões mostra quais versões são publicadas, onde elas são publicadas e qual versão está ativa no momento. 

[![Gerenciar seção, página de versões](./media/luis-how-to-manage-versions/versions-import.png "Gerenciar seção, página de versões")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clonar uma versão

1. Selecione a versão que você deseja clonar e, em seguida, selecione **Clonar** na barra de ferramentas. 

2. Na caixa de diálogo **Clonar versão**, digite um nome para a nova versão, como "0.2".

   ![Caixa de diálogo Clonar Versão](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > A ID da versão pode consistir apenas em caracteres, dígitos ou '.' e não pode ter mais de 10 caracteres.
 
   Uma nova versão com o nome especificado é criada e definida como a versão ativa.

## <a name="set-active-version"></a>Definir versão ativa

Selecione uma versão na lista e selecione **Tornar Ativa** na barra de ferramentas. 

[![Gerenciar seção, página de versões, fazer uma ação de versão](./media/luis-how-to-manage-versions/versions-other.png "Gerenciar seção, página de versões, fazer uma ação de versão")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importa versão

1. Selecione **Importar versão** da barra de ferramentas. 

2. Na janela pop-up **Importar nova versão**, insira o nome da nova versão com dez caracteres. É necessário somente definir uma ID de versão se a versão no arquivo JSON já existir no aplicativo.

    ![Gerenciar seção, página de versões, importar nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Após importar uma versão, a nova versão se tornará a versão ativa.

### <a name="import-errors"></a>Erros de importação

* Erros de Criador: Se você receber um **erro criador** ao importar, você está tentando importar uma versão que usa um [criador](luis-language-support.md#custom-tokenizer-versions) diferente do que o aplicativo usa atualmente. Para corrigir isso, consulte [migrando entre versões do criador](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **excluir** uma versão, selecione-a na lista e selecione **Excluir** na barra de ferramentas. Selecione **Okey**. 
* Para **renomear** uma versão, selecione-a na lista e selecione **Renomear** na barra de ferramentas. Insira o novo nome e selecione **Concluído**. 
* Para **exporta** uma versão, selecione-a na lista e selecione **Exportar aplicativo** na barra de ferramentas. Escolha JSON para exportar para backup, escolha **exportar para contêiner** para [usar esse aplicativo em um contêiner Luis](luis-container-howto.md).  


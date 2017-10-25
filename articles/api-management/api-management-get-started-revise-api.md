---
title: "Usar revisões para atualizar sua API no Gerenciamento de API do Azure | Microsoft Docs"
description: "Execute as etapas deste tutorial para aprender a fazer alterações sem interrupção usando revisões no Gerenciamento de API."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="make-non-breaking-changes-safely-using-revisions"></a>Fazer alterações sem interrupção com segurança usando as revisões
Este tutorial descreve como fazer alterações em sua API com segurança e comunicar as alterações aos desenvolvedores.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisa criar um Serviço de Gerenciamento de API e ter uma API a qual você pode alterar (em vez da API de Conferência).

## <a name="about-revisions"></a>Sobre as revisões
Quando sua API estiver pronta e começar a ser usada por desenvolvedores, será necessário tomar cuidado com as alterações feitas nessa API, para não interromper os chamadores de sua API. Também é útil permitir que os desenvolvedores saibam sobre as alterações feitas por você. Podemos fazer isso usando **revisões** no Gerenciamento de API do Azure.

## <a name="walkthrough"></a>Passo a passo
Neste passo a passo, adicionamos uma nova revisão, adicionamos uma operação a ela tornamos essa revisão atual - criando uma entrada de log de alteração quando isso for feito.

## <a name="add-a-new-revision"></a>Adicionar uma nova revisão
1. Navegue até a página **APIs** dentro de seu serviço de Gerenciamento de API no portal do Azure.
2. Selecione a **API de Conferência** na lista de APIs, selecione a guia **Revisões** no menu na parte superior da página.
3. Selecione **+ Adicionar Revisão**

    > [!TIP]
    > Você também pode escolher **Adicionar Revisão** no menu de contexto (**...**) na API.
![Menu Revisões perto da parte superior da tela](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. Forneça uma descrição para a nova revisão, para ajudar você a se lembrar de sua função.
5. Selecione **Criar**
6. Sua nova revisão foi criada.

    > [!NOTE]
    > A API original permanece na **Revisão 1**. Essa é a revisão que seus usuários continuarão a chamar, até que você opte por tornar uma revisão diferente a atual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Fazer alterações sem interrupções em sua revisão
1. Selecione a guia **Design** na parte superior da tela.
2. Observe que o **seletor de revisão** (diretamente acima da guia Design) mostra a revisão atual como **Revisão 2**.

    > [!TIP]
    > Use o seletor de revisão para alternar entre as revisões nas quais você deseja trabalhar.

3. Selecione **+ Adicionar Operação**.
4. Defina a nova operação como **POST** e o Nome e Nome de Exibição da operação como **Comentários**
5. **Salve** sua nova operação.
6. Agora fizemos uma alteração em **Revisão 2**. Use o **Seletor de Revisão** na parte superior da página para alternar novamente para **Revisão 1**.
7. Observe que a nova operação não aparece em **Revisão 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Torne sua revisão a atual e adicione uma entrada ao log de alterações
1. Selecione a guia **Revisões** no menu próximo à parte superior da página.
![O menu de revisão na tela de revisão.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. Abra o menu de contexto (**...**) para a **Revisão 2**.
3. Selecione **Tornar Atual**.
![Torne a revisão a atual e lance no log de alterações](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. Selecione **Postar no Log de Alterações Público para esta API**
5. Forneça uma descrição para a alteração para os desenvolvedores, por exemplo **"Nova operação de Comentários adicionada".**
6. Agora, a **Revisão 2** é a atual.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procurar no portal do desenvolvedor para ver as alterações e o log de alterações
1. Selecione **Portal do Desenvolvedor** no menu superior.
2. Selecione **APIs** e, em seguida, selecione **API de Conferência**.
3. Observe que sua nova operação de **Comentários** está disponível.
4. Selecione **Histórico de Alterações da API** abaixo do nome da API.
5. Observe que a entrada no log de alterações aparece nessa lista.
![Log de alterações no Portal do Desenvolvedor](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>Próximas etapas
[Publicar as versões de API com o Gerenciamento de API do Azure](#api-management-getstarted-publish-versions.md)
---
title: Use as revisões para fazer alterações não interruptivas com segurança no Gerenciamento de API do Azure | Microsoft Docs
description: Execute as etapas deste tutorial para aprender a fazer alterações sem interrupção usando revisões no Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 757c710ddca72ec6a1383b51a8b536d196e6cb8c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203640"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Usar revisões para fazer alterações sem interrupções com segurança
Quando sua API estiver pronta e começar a ser usada por desenvolvedores, e algum momento será necessário fazer alterações nessa API e, ao mesmo tempo, não interromper os chamadores de sua API. Também é útil permitir que os desenvolvedores saibam sobre as alterações feitas por você. Podemos fazer isso usando **revisões** no Gerenciamento de API do Azure. Para obter mais informações, consulte [Versões e revisões](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) e [Controle de versão de API com o Gerenciamento de API do Azure](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Fazer alterações sem interrupções em sua revisão
> * Torne sua revisão a atual e adicione uma entrada ao log de alterações
> * Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

![Log de alterações no Portal do Desenvolvedor](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Adicionar uma nova revisão

![Adicionar revisão de API](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. Selecione a página **APIs**.
2. Selecione **Demonstração de API de Conferência** na lista de APIs (ou outra API à qual deseja adicionar as revisões).
3. Clique na guia **Revisões** no menu próximo à parte superior da página.
4. Selecione **+ Adicionar Revisão**

    > [!TIP]
    > Você também pode escolher **Adicionar Revisão** no menu de contexto ( **...** ) da API.

5. Forneça uma descrição para a nova revisão, para ajudar você a se lembrar de sua função.
6. Escolha **Criar**
7. Sua nova revisão foi criada.

    > [!NOTE]
    > Sua API original permanece na **Revisão 1**. Essa é a revisão que seus usuários continuarão a chamar até que você opte por tornar uma revisão diferente a atual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Fazer alterações sem interrupções em sua revisão

![Modificar revisão](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. Selecione **Demonstração de API de Conferência** na lista de APIs.
2. Selecione a guia **Design** na parte superior da tela.
3. Observe que o **seletor de revisão** (diretamente acima da guia de design) mostra a revisão atual como **Revisão 2**.

    > [!TIP]
    > Use o seletor de revisão para alternar entre as revisões nas quais você deseja trabalhar.

4. Selecione **+ Adicionar Operação**.
5. Defina a nova operação como **POST** e o Nome, Nome de Exibição e URL da operação como **teste**.
6. **Salve** sua nova operação.
7. Agora fizemos uma alteração em **Revisão 2**. Use o **Seletor de Revisão** na parte superior da página para alternar novamente para **Revisão 1**.
8. Observe que a nova operação não aparece em **Revisão 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Torne sua revisão a atual e adicione uma entrada ao log de alterações

1. Selecione a guia **Revisões** no menu próximo à parte superior da página.

    ![O menu de revisão na tela de revisão.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. Abra o menu de contexto ( **...** ) para a **Revisão 2**.
3. Selecione **Tornar Atual**.
4. Marque **Postar no log de alterações públicas para esta API** se você quiser postar observações sobre essa alteração. Forneça uma descrição para a alteração que os desenvolvedores vejam, por exemplo: **Teste de revisões. Adicionada uma nova operação de "teste".**
5. Agora, a **Revisão 2** é a atual.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

1. No portal do Azure, selecione **APIs**.
2. Selecione **Portal do Desenvolvedor** no menu superior.
3. Selecione **APIs** e, depois, selecione **Demonstração de API de Conferência**.
4. Observe que sua nova operação de **teste** já está disponível.
5. Selecione **Histórico de Alterações da API** abaixo do nome da API.
6. Observe que a entrada no log de alterações aparece nessa lista.

    ![Portal do desenvolvedor](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Fazer alterações sem interrupções em sua revisão
> * Torne sua revisão a atual e adicione uma entrada ao log de alterações
> * Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Publicar várias versões da sua API](api-management-get-started-publish-versions.md)

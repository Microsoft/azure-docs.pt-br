---
title: Solicitar um pacote de acesso-gerenciamento de direitos do Azure AD
description: Saiba como usar o portal meu acesso para solicitar acesso a um pacote de acesso no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996637"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Solicitar acesso a um pacote de acesso no gerenciamento de direitos do Azure AD

Com o gerenciamento de direitos do Azure AD, um pacote de acesso permite uma configuração única de recursos e políticas que administram automaticamente o acesso para a vida útil do pacote de acesso. 

Um Gerenciador de pacotes de acesso pode configurar políticas para exigir aprovação para que os usuários tenham acesso aos pacotes do Access. Um usuário que precisa acessar um pacote do Access pode enviar uma solicitação para obter acesso. Este artigo descreve como enviar uma solicitação de acesso.

## <a name="sign-in-to-the-my-access-portal"></a>Entrar no meu portal de acesso

A primeira etapa é entrar no portal meu acesso, em que você pode solicitar acesso a um pacote do Access.

**Função de pré-requisito:** Solicitante

1. Procure um email ou uma mensagem do projeto ou gerente de negócios com o qual você está trabalhando. O email deve incluir um link para o pacote de acesso ao qual você precisará de acesso. O link começa com `myaccess` , inclui uma dica de diretório e termina com uma ID de pacote de acesso.  (Para o governo dos EUA, o domínio pode estar `https://myaccess.microsoft.us` em vez disso).
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Abra o link.

1. Entre no portal meu acesso.

    Certifique-se de usar sua conta organizacional (corporativa ou de estudante). Se você não tiver certeza, verifique com seu projeto ou gerente de negócios.

## <a name="request-an-access-package"></a>Solicitar um pacote de acesso

Depois de encontrar o pacote de acesso no portal meu acesso, você pode enviar uma solicitação.

**Função de pré-requisito:** Solicitante

1. Localize o pacote de acesso na lista.  Se necessário, você pode pesquisar digitando uma cadeia de caracteres de pesquisa e, em seguida, selecionando o **nome**, o **Catálogo** ou o filtro de **recursos** .

    ![Meu Portal de acesso – pesquisa de recursos](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Clique na marca de seleção para selecionar o pacote de acesso.

1. Clique em **Solicitar acesso** para abrir o painel Solicitar acesso.

    ![Portal Meus Acessos – Pacotes de acesso](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Se a caixa de **justificativa de negócios** for exibida, digite uma justificativa para a necessidade de acesso.

1. Se **a solicitação de período específico** for habilitada, selecione **Sim** ou **não**.

1. Se necessário, especifique a data de início e a data de término.

    ![Portal Meus Acessos – Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Quando terminar, clique em **Enviar** para enviar sua solicitação.

1. Clique em **histórico de solicitações** para ver uma lista de suas solicitações e o status.

    Se o pacote de acesso exigir aprovação, a solicitação estará agora em um estado de aprovação pendente.

### <a name="select-a-policy"></a>Selecione uma política

Se você solicitar acesso a um pacote do Access que tenha várias políticas aplicáveis, você poderá ser solicitado a selecionar uma política. Por exemplo, um Gerenciador de pacotes do Access pode configurar um pacote de acesso com duas políticas para dois grupos de funcionários internos. A primeira política pode permitir o acesso por 60 dias e exigir aprovação. A segunda política pode permitir o acesso por 2 dias e não requer aprovação. Se você encontrar esse cenário, deverá selecionar a política que deseja usar.

![Meu Portal de acesso-solicitar acesso-várias políticas](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>Preencher informações do solicitante

Você pode solicitar acesso a um pacote de acesso que exija justificativa de negócios e informações adicionais do solicitante antes de conceder acesso ao pacote de acesso. Preencha todas as informações solicitadas necessárias para acessar o pacote de acesso.

![Meu Portal de acesso – solicitar acesso-informações do solicitante de preenchimento](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>Reenviar uma solicitação

Quando você solicitar acesso a um pacote do Access, sua solicitação poderá ser negada ou sua solicitação poderá expirar se os aprovadores não responderem no tempo. Se você precisar de acesso, poderá tentar novamente e reenviar sua solicitação. O procedimento a seguir explica como reenviar uma solicitação de acesso:

**Função de pré-requisito:** Solicitante

1. Entre no portal **meu acesso** .

1. Clique em **histórico de solicitações** no menu de navegação à esquerda.

1. Localize o pacote de acesso para o qual você está Reenviando uma solicitação.

1. Clique na marca de seleção para selecionar o pacote de acesso.

1. Clique no link **exibição** azul à direita do pacote de acesso selecionado.
    
    ![Selecionar o pacote de acesso e o link de exibição](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Um painel será aberto à direita com o histórico de solicitações do pacote de acesso.
    
    ![Selecionar botão reenviar](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Clique no botão **reenviar** na parte inferior do painel.

## <a name="cancel-a-request"></a>Cancelar uma solicitação

Se você enviar uma solicitação de acesso e a solicitação ainda estiver no estado de **aprovação pendente** , você poderá cancelar a solicitação.

**Função de pré-requisito:** Solicitante

1. No portal meu acesso, à esquerda, clique em **histórico de solicitações** para ver uma lista de suas solicitações e o status.

1. Clique no link **Exibir** para a solicitação que você deseja cancelar.

1. Se a solicitação ainda estiver no estado de **aprovação pendente** , você poderá clicar em **Cancelar solicitação** para cancelar a solicitação.

    ![Meu Portal de acesso-Cancelar solicitação](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Clique em **histórico de solicitações** para confirmar que a solicitação foi cancelada.

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)
- [Solicitar notificações de processo e email](entitlement-management-process.md)

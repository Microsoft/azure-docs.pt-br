---
title: Como configurar a atribuição de aplicativo de autoatendimento | Microsoft Docs
description: Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9742a21cf00733607237c0eaf548f96b434abb33
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260207"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Como configurar a atribuição de aplicativo de autoatendimento

Antes que os usuários possam detectar aplicativos automaticamente de seus meus aplicativos, você precisa habilitar o **acesso de aplicativo de autoatendimento** a todos os aplicativos para os quais deseja permitir que os usuários autodescubram e solicitem acesso. Essa funcionalidade está disponível para aplicativos que foram adicionados da [Galeria do Azure ad](./add-application-portal.md), o [Azure proxy de aplicativo do AD](./application-proxy.md) ou foram adicionados por meio do [consentimento do usuário ou do administrador](../develop/application-consent-experience.md). 

Esse recurso é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI e é altamente recomendável como parte de uma implantação de aplicativos moderna com o Azure Active Directory.

Usando esse recurso, você pode:

-   Permitir que os usuários autodescubram aplicativos dos [meus aplicativos](https://myapps.microsoft.com/) sem incomodar o grupo de ti.

-   Adicione esses usuários a um grupo pré-configurado para que você possa ver quem solicitou acesso, remover o acesso e gerenciar as funções atribuídas a eles.

-   Ou permita que um aprovador de negócios aprove solicitações de acesso ao aplicativo para que o grupo de TI não precise fazer isso.

-   Também é possível configurar até 10 pessoas que podem aprovar o acesso a esse aplicativo.

-   Opcionalmente, permita que um Aprovador de negócios defina as senhas que esses usuários podem usar para entrar no aplicativo, diretamente dos [meus aplicativos](https://myapps.microsoft.com/)do aprovador de negócios.

-   É possível, ainda, atribuir automaticamente o autoatendimento atribuído aos usuários diretamente a uma função de aplicativo.

> [!NOTE]
> Uma licença Azure Active Directory Premium (P1 ou P2) é necessária para que os usuários solicitem a União de um aplicativo de autoatendimento e que os proprietários aprovem ou neguem solicitações. Sem uma licença de Azure Active Directory Premium, os usuários não podem adicionar aplicativos de autoatendimento.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos

O acesso ao aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários autodescubram aplicativos e, opcionalmente, permitem que o grupo de negócios aprove o acesso a esses aplicativos. Para aplicativos de logon único com senha, você também pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários de seus próprios painéis de acesso dos meus aplicativos.

Para habilitar o acesso de aplicativos de autoatendimento a um aplicativo, siga as etapas abaixo:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global.

2. Selecione **Azure Active Directory**. No menu de navegação esquerdo, selecione **Aplicativos empresariais**.

3. Selecione o aplicativo na lista. Se você não encontrar o aplicativo, comece a digitar o nome dele na caixa de pesquisa. Outra opção é usar os controles de filtro para selecionar o tipo, o status ou a visibilidade do aplicativo e, em seguida, selecionar **Aplicar**.

4. No menu de navegação à esquerda, selecione **autoatendimento**.

5. Para habilitar o acesso de aplicativos de autoatendimento a este aplicativo, coloque o controle de alternância **Permitir aos usuários solicitar acesso a esse aplicativo?** na posição **Sim.**

6. Ao lado **de qual grupo os usuários atribuídos devem ser adicionados?**, clique em **selecionar grupo**. Escolha um grupo e, em seguida, clique em **selecionar**. Quando a solicitação de um usuário for aprovada, ela será adicionada a esse grupo. Ao exibir a associação deste grupo, você poderá ver quem recebeu acesso ao aplicativo por meio de acesso de autoatendimento.
  
    > [!NOTE]
    > Essa configuração não oferece suporte a grupos sincronizados do local.

7. **Opcional:** Para exigir aprovação de negócios antes que os usuários tenham permissão de acesso, defina a opção **exigir aprovação antes de conceder acesso a este aplicativo?** alterne para **Sim**.

8. **Opcional: para aplicativos que usam somente logon único de senha,** para permitir que os aprovadores de negócios especifiquem as senhas que são enviadas para este aplicativo para usuários aprovados, defina permitir que os **aprovadores definam as senhas do usuário para este aplicativo?** alterne para **Sim**.

9. **Opcional:** Para especificar os aprovadores de negócios que têm permissão para aprovar o acesso a esse aplicativo, ao lado de **quem tem permissão para aprovar o acesso a esse aplicativo?**, clique em **selecionar aprovadores** e selecione até 10 aprovadores de negócios individuais. Em seguida, clique em **Selecionar**.

    >[!NOTE]
    >Não há suporte para grupos. Você pode selecionar até 10 aprovadores de negócios individuais. Se você especificar vários Aprovadores, qualquer aprovador único poderá aprovar uma solicitação de acesso.

10. **Opcional:** **para aplicativos que expõem funções**, para atribuir usuários aprovados por autoatendimento a uma função, ao lado do **a qual função os usuários devem ser atribuídos neste aplicativo?**, clique em **selecionar função** e escolha a função à qual esses usuários devem ser atribuídos. Em seguida, clique em **Selecionar**.

11. Clique no botão **Salvar** na parte superior do painel para concluir.

Depois de concluir a configuração de aplicativo de autoatendimento, os usuários podem navegar para seus [meus aplicativos](https://myapps.microsoft.com/) e clicar no botão **adicionar aplicativos de autoatendimento** para localizar os aplicativos que são habilitados com acesso de autoatendimento. Os aprovadores de negócios também veem uma notificação em [meus aplicativos](https://myapps.microsoft.com/). Você pode habilitar um email que notifica a eles quando um usuário solicitar acesso a um aplicativo que requer sua aprovação.

## <a name="next-steps"></a>Próximas etapas
[Configuração do Azure Active Directory para gerenciamento de grupo de autoatendimento](../enterprise-users/groups-self-service-management.md)
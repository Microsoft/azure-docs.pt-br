---
title: Como usar o acesso de aplicativo de autoatendimento no Azure AD
description: Habilitar o autoatendimento para que os usuários possam encontrar aplicativos no Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: 13f91fdd9e2d9501fba426bd6facbf9824a39285
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257010"
---
# <a name="how-to-use-self-service-application-access"></a>Como usar o acesso de aplicativo de autoatendimento

Antes que os usuários possam detectar aplicativos por conta própria na página meus aplicativos, você precisa habilitar o **acesso de aplicativo de autoatendimento** a todos os aplicativos para os quais deseja permitir que os usuários autodescubram e solicitem acesso.

Esse recurso é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI e é altamente recomendável como parte de uma implantação de aplicativos moderna com o Azure Active Directory.

Para saber mais sobre como usar meus aplicativos de uma perspectiva do usuário final, consulte [a ajuda do portal de meus aplicativos](../user-help/my-apps-portal-end-user-access.md).

Usando esse recurso, você pode:

-   Permitir que os usuários autodescubram aplicativos de [meus aplicativos](https://myapps.microsoft.com/) sem incomodar o grupo de ti.
-   Adicione esses usuários a um grupo pré-configurado para que você possa ver quem solicitou acesso, remover o acesso e gerenciar as funções atribuídas a eles.
-   Opcionalmente, permita que alguém aprove as solicitações de acesso do aplicativo para que o grupo de ti não precise fazê-lo.
-   Também é possível configurar até 10 pessoas que podem aprovar o acesso a esse aplicativo.
-   Opcionalmente, permita que alguém defina as senhas que os usuários podem usar para entrar no aplicativo.
-   É possível, ainda, atribuir automaticamente o autoatendimento atribuído aos usuários diretamente a uma função de aplicativo.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos

O acesso de aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários descubram aplicativos por conta própria e, ainda, permitir que o grupo de negócios aprove o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários para a senha Single-Sign nos aplicativos diretamente da página meus aplicativos.

Para habilitar o acesso de aplicativos de autoatendimento a um aplicativo, siga as etapas abaixo:
1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**
2. Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4. Escolha **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
5. Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.
   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**
6. Selecione na lista o aplicativo ao qual você deseja habilitar o acesso do autoatendimento.
7. Depois que o aplicativo for carregado, selecione **autoatendimento** no menu de navegação esquerdo do aplicativo.
8. Para habilitar o acesso de aplicativos de autoatendimento a este aplicativo, coloque o controle de alternância **Permitir aos usuários solicitar acesso a esse aplicativo?** na posição **Sim.**
9. Em seguida, para selecionar o grupo ao qual os usuários que solicitarem acesso a esse aplicativo devem ser adicionados, selecione o seletor ao lado do rótulo **ao qual os usuários atribuídos devem ser adicionados?** e selecione um grupo.
10. **Opcional:** se quiser exigir uma aprovação de negócios antes que os usuários tenham permissão de acesso, coloque o controle de alternância **Exigir aprovação antes de conceder acesso a esse aplicativo?** na posição **Sim**.
11. **Opcional: somente para aplicativos que usam logon único com senha,** se quiser permitir que os aprovadores de negócios especifiquem as senhas que são enviadas para esse aplicativo aos usuários aprovados, coloque o controle de alternância **Permitir que os aprovadores definam senhas do usuário para este aplicativo?** na posição **Sim**.
12. **Opcional:** Especifique os aprovadores de negócios que têm permissão para aprovar o acesso a este aplicativo. Selecione **quem tem permissão para aprovar o acesso a este aplicativo?** Em seguida, selecione até 10 aprovadores de negócios individuais.
    * Não há suporte para grupos.
13. **Opcional:** **para aplicativos que expõem funções**, se você quiser atribuir usuários aprovados por autoatendimento a uma função, selecione o seletor ao lado do **a qual função os usuários devem ser atribuídos neste aplicativo?** para selecionar a função à qual esses usuários devem ser atribuídos.
14. Selecione o botão **salvar** na parte superior para concluir.

Depois de concluir a configuração de aplicativo de autoatendimento, os usuários podem navegar para [meus aplicativos](https://myapps.microsoft.com/) e selecionar o botão **+ Adicionar** para localizar os aplicativos aos quais você habilitou o acesso de autoatendimento. Os aprovadores de negócios também veem uma notificação na página [meus aplicativos](https://myapps.microsoft.com/) . Você pode habilitar um email que notifica a eles quando um usuário solicitar acesso a um aplicativo que requer sua aprovação. 

Essas aprovações dão suporte a fluxos de trabalho de aprovação únicos, o que significa que, se você especificar vários aprovadores, qualquer aprovador individual poderá aprovar o acesso ao aplicativo.

## <a name="things-to-check-if-self-service-isnt-working"></a>Coisas para verificar se o autoatendimento não está funcionando
-   Verifique se o usuário ou o grupo foi habilitado para solicitar acesso de aplicativos de autoatendimento.
-   Verifique se o usuário está visitando o local correto para acesso de aplicativos de autoatendimento. os usuários podem navegar até a página [meus aplicativos](https://myapps.microsoft.com/) e selecionar o botão **+ Adicionar** para localizar os aplicativos aos quais você habilitou o acesso de autoatendimento.
-   Se o acesso ao aplicativo de autoatendimento foi configurado recentemente, tente entrar e sair novamente nos meus aplicativos do usuário após alguns minutos para ver se as alterações de acesso de autoatendimento foram exibidas.

## <a name="next-steps"></a>Próximas etapas
[Configuração do Azure Active Directory para gerenciamento de grupo de autoatendimento](../enterprise-users/groups-self-service-management.md)

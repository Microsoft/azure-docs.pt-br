---
title: Criar coleções para meus portais de aplicativos no Azure Active Directory | Microsoft Docs
description: Use coleções de aplicativos para personalizar minhas páginas de aplicativos para uma experiência mais simples de meus aplicativos para seus usuários finais. Organize aplicativos em grupos com guias separadas.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7743a1af54b01f848c4ac6f0cb1d4526d66f8132
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254975"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Criar coleções no portal Meus Aplicativos

Os usuários podem usar o portal meus aplicativos para exibir e iniciar os aplicativos baseados em nuvem aos quais eles têm acesso. Por padrão, todos os aplicativos que um usuário pode acessar são listados juntos em uma única página. Para organizar melhor essa página para seus usuários, se você tiver uma licença Azure AD Premium P1 ou P2, poderá configurar coleções. Com uma coleção, você pode agrupar aplicativos relacionados (por exemplo, por função de trabalho, tarefa ou projeto) e exibi-los em uma guia separada. Uma coleção basicamente aplica um filtro aos aplicativos que um usuário já pode acessar, para que o usuário veja apenas os aplicativos na coleção que foram atribuídos a eles.

> [!NOTE]
> Este artigo aborda como um administrador pode habilitar e criar coleções. Para obter informações para o usuário final sobre como usar o portal e as coleções de meus aplicativos, consulte [acessar e usar coleções](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>Habilitar os recursos mais recentes dos meus aplicativos

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como administrador de usuário ou administrador global.

2. Vá para **Azure Active Directory**  >  **configurações do usuário**.

3. Em **visualizações de recurso de usuário**, selecione **gerenciar configurações de visualização de recurso do usuário**.

4. Em **os usuários podem usar os recursos de visualização para meus aplicativos**, escolha uma das seguintes opções:
   * **Selecionado** – habilita os recursos para um grupo específico. Use a opção **selecionar um grupo** para selecionar o grupo para o qual você deseja habilitar os recursos.  
   * **Todos** – habilita os recursos para todos os usuários.

> [!NOTE]
> Para abrir o portal meus aplicativos, os usuários podem usar o link `https://myapps.microsoft.com` ou o link personalizado para sua organização, como `https://myapps.microsoft.com/contoso.com` . Depois de habilitar a nova experiência meus aplicativos, a faixa **uma experiência atualizada meus aplicativos está disponível** será exibida na parte superior da página meus aplicativos, e os usuários poderão selecionar **experimentar** para exibir a nova experiência. Para parar de usar a nova experiência, os usuários podem selecionar **Sim** na faixa **deixar nova experiência** na parte superior da página.

## <a name="create-a-collection"></a>Criar uma coleção

Para criar uma coleção, você deve ter uma licença Azure AD Premium P1 ou P2.

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um administrador com uma licença Azure ad Premium P1 ou P2.

2. Vá para **Azure Active Directory**  >  **aplicativos empresariais**.

3. Em **gerenciar**, selecione **coleções**.

4. Selecione **nova coleção**. Na página **nova coleção** , insira um **nome** para a coleção (é recomendável não usar "coleção" no nome. Em seguida, insira uma **Descrição**.

   ![Nova página de coleção](media/acces-panel-collections/new-collection.png)

5. Selecione a guia **aplicativos** . Selecione **+ Adicionar aplicativo** e, em seguida, na página **adicionar aplicativos** , selecione todos os aplicativos que você deseja adicionar à coleção ou use a caixa de **pesquisa** para localizar aplicativos.

   ![Adicionar um aplicativo à coleção](media/acces-panel-collections/add-applications.png)

6. Quando terminar de adicionar aplicativos, selecione **Adicionar**. A lista de aplicativos selecionados é exibida. Você pode usar as setas para cima para alterar a ordem dos aplicativos na lista. Para mover um aplicativo para baixo ou para excluí-lo da coleção, selecione o menu **mais** (**...**).

7. Selecione a guia **proprietários** . Selecione **+ Adicionar usuários e grupos** e, em seguida, na página **Adicionar usuários e grupos** , selecione os usuários ou grupos aos quais você deseja atribuir a propriedade. Quando você terminar de selecionar usuários e grupos, escolha **selecionar**.

9. Selecione a guia **usuários e grupos** . Selecione **+ Adicionar usuários e grupos** e, em seguida, na página **Adicionar usuários e grupos** , selecione os usuários ou grupos aos quais você deseja atribuir a coleção. Ou use a caixa de **pesquisa** para localizar usuários ou grupos. Quando você terminar de selecionar usuários e grupos, escolha **selecionar**.

   ![Adicionar usuários e grupos](media/acces-panel-collections/add-users-and-groups.png)

11. Selecione **Examinar + criar**. As propriedades da nova coleção são exibidas.


## <a name="view-audit-logs"></a>Exibir logs de auditoria

Os logs de auditoria registram as operações de coleções de meus aplicativos, incluindo a criação de coleções ações do usuário final. Os seguintes eventos são gerados em meus aplicativos:

* Criar coleção
* Editar coleção
* Excluir coleção
* Iniciar um aplicativo (usuário final)
* Adição de aplicativo de autoatendimento (usuário final)
* Exclusão de aplicativo de autoatendimento (usuário final)

Você pode acessar os logs de auditoria no [portal do Azure](https://portal.azure.com) selecionando **Azure Active Directory**  >  **aplicativos empresariais**  >  **logs de auditoria** na seção atividade. Para **serviço**, selecione **meus aplicativos**.

## <a name="get-support-for-my-account-pages"></a>Obter suporte para páginas da minha conta

Na página meus aplicativos, um usuário pode selecionar **minha** conta  >  **Exibir minha conta** para abrir suas configurações de conta. Na página **minha conta** do Azure AD, os usuários podem gerenciar suas informações de segurança, dispositivos, senhas e muito mais. Eles também podem acessar suas configurações de conta do Office.

Caso você precise enviar uma solicitação de suporte para um problema com a página conta do Azure AD ou a página conta do Office, siga estas etapas para que sua solicitação seja roteada corretamente: 

* Para problemas com a página **"minha conta" do Azure ad** , abra uma solicitação de suporte no portal do Azure. Vá para **portal do Azure**  >  **Azure Active Directory**  >  **nova solicitação de suporte**.

* Para problemas com a página **"minha conta" do Office** , abra uma solicitação de suporte no centro de administração do Microsoft 365. Vá para **Microsoft 365 suporte do centro de administração**  >  . 

## <a name="next-steps"></a>Próximas etapas
[Experiências do usuário final para aplicativos no Active Directory Domain Services do Azure](end-user-experiences.md)
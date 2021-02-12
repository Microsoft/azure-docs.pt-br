---
title: Configurar o fluxo de trabalho de consentimento do administrador-Azure Active Directory | Microsoft Docs
description: Saiba como configurar uma maneira para os usuários finais solicitarem acesso a aplicativos que exigem consentimento de administrador.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: b38b8cf38d2d75493ef50c4f206758c6289be079
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259580"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Configurar o fluxo de trabalho de consentimento do administrador (versão prévia)

Este artigo descreve como habilitar o recurso de fluxo de trabalho de consentimento do administrador (versão prévia), que fornece aos usuários finais uma maneira de solicitar acesso a aplicativos que exigem o consentimento do administrador.

Sem um fluxo de trabalho de consentimento do administrador, um usuário em um locatário em que o consentimento do usuário está desabilitado será bloqueado quando tentar acessar qualquer aplicativo que exija permissões para acessar dados organizacionais. O usuário vê uma mensagem de erro genérica que diz que elas não são autorizadas a acessar o aplicativo e devem pedir ajuda ao seu administrador. Mas, muitas vezes, o usuário não sabe quem deve entrar em contato, para que eles forneçam ou criem uma nova conta local no aplicativo. Mesmo quando um administrador é notificado, nem sempre há um processo simplificado para ajudar o administrador a conceder acesso e notificar seus usuários.
 
O fluxo de trabalho de consentimento do administrador fornece aos administradores uma maneira segura de conceder acesso a aplicativos que exigem aprovação de administrador. Quando um usuário tenta acessar um aplicativo, mas não consegue fornecer consentimento, ele pode enviar uma solicitação de aprovação de administrador. A solicitação é enviada por email para administradores que foram designados como revisores. Um revisor executa uma ação na solicitação e o usuário é notificado sobre a ação.

Para aprovar solicitações, um revisor deve ser um administrador global, um administrador de aplicativos de nuvem ou um administrador de aplicativos. O revisor já deve ter uma dessas funções de administrador atribuídas; simplesmente designá-los como um revisor não eleva seus privilégios.

## <a name="enable-the-admin-consent-workflow"></a>Habilitar o fluxo de trabalho de consentimento do administrador

Para habilitar o fluxo de trabalho de consentimento do administrador e escolher revisores:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Clique em **Todos os serviços** na parte superior do menu de navegação à esquerda. A **Extensão do Active Directory do Azure** é aberta.
3. Na caixa de pesquisa de filtro, digite "**Azure Active Directory**" e selecione **o item de Azure Active Directory** .
4. No menu de navegação, clique em **Aplicativos corporativos**. 
5. Em **gerenciar**, selecione **configurações do usuário**.
6. Em **solicitações de consentimento de administrador (versão prévia)**, **os usuários podem solicitar consentimento de administrador para aplicativos para os quais eles não conseguirem consentir** para **Sim**.

   ![Definir configurações de fluxo de trabalho de consentimento do administrador](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Defina as seguintes configurações:

   * **Selecione os usuários para examinar as solicitações de consentimento do administrador**. Selecione revisores para este fluxo de trabalho de um conjunto de usuários que têm as funções administrador global, administrador de aplicativos de nuvem e administrador de aplicativos.
   * **Os usuários selecionados receberão notificações por email para solicitações**. Habilitar ou desabilitar notificações por email para os revisores quando uma solicitação for feita.  
   * **Os usuários selecionados receberão lembretes de expiração de solicitação**. Habilitar ou desabilitar notificações de email de lembrete para os revisores quando uma solicitação estiver prestes a expirar.  
   * A **solicitação de consentimento expira após (dias)**. Especifique por quanto tempo as solicitações permanecem válidas.

7. Clique em **Salvar**. Pode levar até uma hora para que o recurso fique habilitado.

> [!NOTE]
> Você pode adicionar ou remover revisores para este fluxo de trabalho modificando a lista **selecionar revisores de solicitações de consentimento de administrador** . Observe que uma limitação atual desse recurso é que os revisores podem reter a capacidade de revisar solicitações feitas enquanto foram designadas como um revisor.

## <a name="how-users-request-admin-consent"></a>Como os usuários solicitam o consentimento do administrador

Depois que o fluxo de trabalho de consentimento do administrador estiver habilitado, os usuários poderão solicitar aprovação de administrador para um aplicativo ao qual eles não estão autorizados a consentir. As etapas a seguir descrevem a experiência do usuário ao solicitar aprovação. 

1. O usuário tenta entrar no aplicativo.

2. A mensagem de **aprovação necessária** é exibida. O usuário digita uma justificativa para a necessidade de acesso ao aplicativo e, em seguida, seleciona **solicitação de aprovação**.

   ![Captura de tela mostra uma caixa de diálogo de aprovação necessária em que você pode solicitar aprovação.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Uma mensagem de **solicitação enviada** confirma que a solicitação foi enviada ao administrador. Se o usuário enviar várias solicitações, apenas a primeira solicitação será enviada ao administrador.

   ![Captura de tela mostra a solicitação de confirmação enviada.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. O usuário recebe uma notificação por email quando sua solicitação é aprovada, negada ou bloqueada. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Revisar e tomar medidas sobre solicitações de consentimento de administrador

Para examinar as solicitações de consentimento do administrador e executar a ação:

1. Entre no [portal do Azure](https://portal.azure.com) como um dos revisores registrados do fluxo de trabalho de consentimento do administrador.
2. Selecione **todos os serviços** na parte superior do menu de navegação à esquerda. A **Extensão do Active Directory do Azure** é aberta.
3. Na caixa de pesquisa de filtro, digite "**Azure Active Directory**" e selecione o item de **Azure Active Directory** .
4. No menu de navegação, clique em **Aplicativos corporativos**.
5. Em **atividade**, selecione **solicitações de consentimento de administrador (versão prévia)**.

   > [!NOTE]
   > Os revisores só verão solicitações de administrador que foram criadas depois de serem designadas como um revisor.

1. Selecione o aplicativo que está sendo solicitado.
2. Examine os detalhes sobre a solicitação:  

   * Para ver quem está solicitando o acesso e o porquê, selecione a guia **solicitado por** .
   * Para ver quais permissões estão sendo solicitadas pelo aplicativo, selecione **examinar permissões e consentimento**.

8. Avalie a solicitação e execute a ação apropriada:

   * **Aprove a solicitação**. Para aprovar uma solicitação, conceda consentimento do administrador ao aplicativo. Depois que uma solicitação é aprovada, todos os solicitantes são notificados de que receberam acesso.  
   * **Negar a solicitação**. Para negar uma solicitação, você deve fornecer uma justificativa que será fornecida a todos os solicitantes. Depois que uma solicitação é negada, todos os solicitantes são notificados de que o acesso ao aplicativo foi negado. Negar uma solicitação não impedirá que os usuários solicitem o consentimento do administrador ao aplicativo novamente no futuro.  
   * **Bloquear a solicitação**. Para bloquear uma solicitação, você deve fornecer uma justificativa que será fornecida a todos os solicitantes. Quando uma solicitação é bloqueada, todos os solicitantes são notificados de que o acesso ao aplicativo foi negado. O bloqueio de uma solicitação cria um objeto de entidade de serviço para o aplicativo em seu locatário em um estado desabilitado. Os usuários não poderão solicitar o consentimento do administrador para o aplicativo no futuro.
 
## <a name="email-notifications"></a>Notificações por email
 
Se configurado, todos os revisores receberão notificações por email quando:

* Uma nova solicitação foi criada
* Uma solicitação expirou
* Uma solicitação está se aproximando da data de validade  
 
Os solicitantes receberão notificações por email quando:

* Eles enviam uma nova solicitação de acesso
* Sua solicitação expirou
* Sua solicitação foi negada ou bloqueada
* Sua solicitação foi aprovada
 
## <a name="audit-logs"></a>Logs de auditoria 
 
A tabela a seguir descreve os cenários e os valores de auditoria disponíveis para o fluxo de trabalho de consentimento do administrador. 

> [!NOTE]
> O contexto de usuário do ator de auditoria está ausente no momento em todos os cenários. Essa é uma limitação conhecida na versão de visualização.


|Cenário  |Serviço de auditoria  |Auditar categoria  |Atividade de auditoria  |Ator de auditoria  |Limitações do log de auditoria  |
|---------|---------|---------|---------|---------|---------|
|Administração habilitando o fluxo de trabalho de solicitação de consentimento        |Revisões de acesso           |UserManagement           |Criar modelo de política de governança          |Contexto do aplicativo            |No momento, não é possível localizar o contexto do usuário            |
|Administrador desabilitando o fluxo de trabalho de solicitação de consentimento       |Revisões de acesso           |UserManagement           |Excluir modelo de política de governança          |Contexto do aplicativo            |No momento, não é possível localizar o contexto do usuário           |
|Administração atualizando as configurações de fluxo de trabalho de consentimento        |Revisões de acesso           |UserManagement           |Atualizar modelo de política de governança          |Contexto do aplicativo            |No momento, não é possível localizar o contexto do usuário           |
|Usuário final criando uma solicitação de consentimento de administrador para um aplicativo       |Revisões de acesso           |Política         |Criar solicitação           |Contexto do aplicativo            |No momento, não é possível localizar o contexto do usuário           |
|Revisores aprovando uma solicitação de consentimento de administrador       |Revisões de acesso           |UserManagement           |Aprovar todas as solicitações no fluxo de negócios          |Contexto do aplicativo            |No momento, você não pode localizar o contexto do usuário ou a ID do aplicativo que recebeu o consentimento do administrador.           |
|Revisores negando uma solicitação de consentimento de administrador       |Revisões de acesso           |UserManagement           |Aprovar todas as solicitações no fluxo de negócios          |Contexto do aplicativo            | No momento, você não pode localizar o contexto de usuário do ator que negou uma solicitação de consentimento de administrador          |

## <a name="faq"></a>Perguntas frequentes 

**Ativei esse fluxo de trabalho, mas ao testar a funcionalidade, por que não consigo ver o novo prompt de "aprovação necessária", permitindo que eu solicite o acesso?**

Depois de ativar o recurso, pode levar até 60 minutos para que os usuários finais vejam a atualização. Você pode verificar se a configuração teve efeito corretamente exibindo o valor **EnableAdminConsentRequests** na `https://graph.microsoft.com/beta/settings` API.

**Como um revisor, por que não consigo ver todas as solicitações pendentes?**

Os revisores só podem ver solicitações de administrador criadas depois de serem designadas como um revisor. Portanto, se você foi adicionado recentemente como um revisor, não verá nenhuma solicitação criada antes da atribuição.

**Como um revisor, por que vejo várias solicitações para o mesmo aplicativo?**
  
Se um desenvolvedor de aplicativos tiver configurado seu aplicativo para usar o consentimento estático e dinâmico para solicitar acesso aos dados do usuário final, você verá duas solicitações de consentimento de administrador. Uma solicitação representa as permissões estáticas e a outra representa as permissões dinâmicas.

**Como solicitante, posso verificar o status da minha solicitação?**  

Não, para os solicitantes agora, só é possível obter atualizações por meio de notificações por email.

**Como um revisor, é possível aprovar o aplicativo, mas não para todos?**
 
Se você estiver preocupado em conceder consentimento de administrador e permitir que todos os usuários no locatário usem o aplicativo, recomendamos que você negue a solicitação. Em seguida, conceda manualmente o consentimento do administrador restringindo o acesso ao aplicativo exigindo a atribuição de usuário e atribuindo usuários ou grupos ao aplicativo. Para obter mais informações, confira [Métodos para atribuir usuários e grupos](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o consentimento de aplicativos, consulte a [estrutura de consentimento do Azure Active Directory](../develop/consent-framework.md).

[Configurar como os usuários finais concordam com os aplicativos](configure-user-consent.md)

[Conceder consentimento de administrador em todo o locatário para um aplicativo](grant-admin-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)

[Azure AD no Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html)
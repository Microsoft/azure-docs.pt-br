---
title: Planejar a configuração do Azure Active Directory meus aplicativos
description: Guia de planejamento para usar efetivamente meus aplicativos em sua organização.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: f63a8fd05e1a6ed5e41eeb64aa852ff01db295af
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645460"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Planejar a configuração do Azure Active Directory meus aplicativos

> [!NOTE]
> Este artigo foi projetado para profissionais de ti que precisam planejar a configuração do portal meus aplicativos de sua organização. 
>
> **Para obter a documentação do usuário final, consulte [entrar e iniciar aplicativos no portal meus aplicativos](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (AD do Azure) meus aplicativos é um portal baseado na Web para iniciar e gerenciar aplicativos. A página meus aplicativos fornece aos usuários um único lugar para iniciar seu trabalho e localizar todos os aplicativos aos quais eles têm acesso. Os usuários acessam meus aplicativos em [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Por que configurar meus aplicativos

O portal meus aplicativos está disponível para os usuários por padrão e não pode ser desativado. É importante configurá-lo para que eles tenham a melhor experiência possível e o portal permaneça útil. 

Qualquer aplicativo na lista Azure Active Directory aplicativos empresariais aparece quando as duas condições a seguir são atendidas:

* A propriedade Visibility do aplicativo é definida como true. 

* O aplicativo é atribuído a qualquer usuário ou grupo. Ele aparece para usuários atribuídos.

Configurar o portal garante que as pessoas certas possam encontrar facilmente os aplicativos certos.

 
### <a name="how-is-the-my-apps-portal-used"></a>Como o portal meus aplicativos é usado?

Os usuários acessam o portal meus aplicativos para:

* Descubra e acesse todos os aplicativos conectados ao Azure AD de sua organização aos quais eles têm acesso.

   * É melhor garantir que os aplicativos estejam configurados para SSO (logon único) para fornecer aos usuários a melhor experiência.

* Solicite acesso a novos aplicativos configurados para autoatendimento.

* Crie coleções pessoais de aplicativos.

* Gerencie o acesso a aplicativos para outras pessoas quando atribuído a função de proprietário do grupo ou controle delegado para o grupo usado para conceder acesso aos aplicativos.

Os administradores podem configurar:

* [Experiências de consentimento](../manage-apps/configure-user-consent.md)  , incluindo termos de serviço.

* [Solicitações de acesso e descoberta de aplicativo de autoatendimento](../manage-apps/access-panel-manage-self-service-access.md).

* [Coleções de aplicativos](../manage-apps/access-panel-collections.md).

* Atribuição de ícones a aplicativos

* Nomes amigáveis para aplicativos

* Identidade visual da empresa mostrada em meus aplicativos

 

## <a name="plan-consent-configuration"></a>Configuração do consentimento do plano

Há dois tipos de consentimento: consentimento do usuário e consentimento para aplicativos que acessam dados.

![Captura de tela de configuração de consentimento](./media/my-apps-deployment-plan/my-apps-consent.png)

### <a name="user-consent-for-applications"></a>Consentimento do usuário para aplicativos 

Os usuários ou administradores devem consentir os termos de uso e as políticas de privacidade do aplicativo. Você deve decidir se os usuários ou somente administradores podem consentir com os aplicativos. **Recomendamos que, se as regras de negócio permitirem, você use o consentimento do administrador para manter o controle dos aplicativos em seu locatário**.

Para usar o consentimento do administrador, você deve ser um administrador global da organização e os aplicativos devem ser:

* Registrado em sua organização.

* Registrado em outra organização do Azure AD e consentiu anteriormente por pelo menos um usuário.

Se você quiser permitir que os usuários consentim, deverá decidir se deseja que eles consentissem em qualquer aplicativo ou somente em circunstâncias específicas.

Para obter mais informações, consulte [Configurar a maneira como os usuários finais concordam com um aplicativo no Azure Active Directory.](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Consentimento do proprietário do grupo para aplicativos que acessam dados

Determine se os proprietários dos grupos de segurança ou grupos de M365 do Azure AD podem dar consentimento aos aplicativos para acessar os dados para os grupos que eles possuem. Você pode proibir, permitir todos os proprietários do grupo ou permitir apenas um subconjunto de proprietários do grupo.

Para obter mais informações, consulte [configurar permissões de consentimento de grupo](../manage-apps/configure-user-consent-groups.md).

Em seguida, defina [as configurações de consentimento do proprietário do usuário e do grupo](https://portal.azure.com/) no portal do Azure.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Informe de forma proativa aos usuários como e quando sua experiência será alterada e como obter suporte, se necessário.

Embora meus aplicativos normalmente não criem problemas de usuário, é importante se preparar. Crie guias e uma lista de todos os recursos para sua equipe de suporte antes do início.

#### <a name="communications-templates"></a>Modelos de comunicação

A Microsoft fornece [modelos personalizáveis para emails e outras comunicações](https://aka.ms/APTemplates) para meus aplicativos. Você pode adaptar esses ativos para uso em outros canais de comunicação, conforme apropriado para sua cultura corporativa.

 

## <a name="plan-your-sso-configuration"></a>Planejar sua configuração de SSO

É melhor se o SSO estiver habilitado para todos os aplicativos no portal meus aplicativos para que os usuários tenham uma experiência simples sem a necessidade de inserir suas credenciais.

O Azure AD dá suporte a várias opções de SSO. 

* Para saber mais, consulte [Opções de logon único no Azure ad](sso-options.md).

* Para saber mais sobre como usar o Azure AD como um provedor de identidade para um aplicativo, consulte a [série de guias de início rápido sobre o gerenciamento de aplicativos](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Usar SSO Federado, se possível

Para obter a melhor experiência de usuário com a página meus aplicativos, comece com a integração de aplicativos em nuvem que estão disponíveis para SSO Federado (OpenID Connect ou SAML). O SSO Federado permite que os usuários tenham uma experiência consistente de um clique entre as superfícies de inicialização do aplicativo e tende a ser mais robusto no controle de configuração.

Para obter mais informações sobre como configurar seus aplicativos SaaS (software como serviço) para SSO, consulte o [plano de implantação de SSO de SaaS].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Considerações para circunstâncias especiais de SSO

> [!TIP]
> Para uma melhor experiência do usuário, use o SSO Federado com o Azure AD (OpenID Connect/SAML) quando um aplicativo oferecer suporte a ele, em vez de SSO baseado em senha e ADFS.

Para entrar em aplicativos de SSO baseados em senha ou em aplicativos que são acessados pelo Azure Proxy de Aplicativo do AD, os usuários precisam instalar e usar a extensão de entrada segura dos meus aplicativos. Os usuários são solicitados a instalar a extensão quando iniciam pela primeira vez o aplicativo de proxy de aplicativo ou SSO baseado em senha. 

![Captura de tela de](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Para obter informações detalhadas sobre a extensão, consulte [instalando a extensão do navegador meus aplicativos](../user-help/my-apps-portal-end-user-access.md).

Se você precisar integrar esses aplicativos, defina um mecanismo para implantar a extensão em escala com os [navegadores com suporte](../user-help/my-apps-portal-end-user-access.md). As opções incluem:

* [Download e configuração controlados pelo usuário para Chrome, Firefox, Microsoft Edge ou IE](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager para o Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

A extensão permite que os usuários iniciem qualquer aplicativo de sua barra de pesquisa, localizando acesso a aplicativos usados recentemente e tendo um link para a página meus aplicativos.

![Captura de tela da pesquisa de extensão meus aplicativos](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Planejar o acesso móvel

Para aplicativos que usam o SSO baseado em senha ou acessados usando [Microsoft Azure ad proxy de aplicativo](../manage-apps/application-proxy.md), você deve usar o Microsoft Edge Mobile. Para outros aplicativos, qualquer navegador móvel pode ser usado. 

### <a name="linked-sso"></a>SSO vinculado

Os aplicativos podem ser adicionados usando a opção de SSO vinculado. Você pode configurar um bloco de aplicativo que se vincule à URL do seu aplicativo Web existente. O SSO vinculado permite que você comece a direcionar usuários para o portal meus aplicativos sem migrar todos os aplicativos para o SSO do Azure AD. Você pode migrar gradualmente para os aplicativos configurados pelo SSO do Azure AD sem interromper a experiência dos usuários.

## <a name="plan-the-user-experience"></a>Planejar a experiência do usuário

Por padrão, todos os aplicativos aos quais o usuário tem acesso e todos os aplicativos configurados para descoberta de autoatendimento aparecem no painel meus aplicativos do usuário. Para muitas organizações, isso pode ser uma lista muito grande, o que pode se tornar um pouco organizado

### <a name="plan-my-apps-collections"></a>Planejar coleções de meus aplicativos

Cada aplicativo do Azure AD ao qual um usuário tem acesso aparecerá em meus aplicativos na coleção todos os aplicativos. Use coleções para agrupar aplicativos relacionados e apresentá-los em uma guia separada, facilitando sua localização. Por exemplo, você pode usar coleções para criar agrupamentos lógicos de aplicativos para funções de trabalho específicas, tarefas, projetos e assim por diante. 

Os usuários finais também podem personalizar sua experiência com o

* Criando suas próprias coleções de aplicativos.

* [Ocultando e reordenando coleções de aplicativos](access-panel-collections.md).

![Captura de tela da configuração de autoatendimento](./media/my-apps-deployment-plan/collections.png)

Há uma opção para ocultar aplicativos do portal meus aplicativos, enquanto ainda permite o acesso de outros locais, como o portal de Microsoft 365. Saiba mais: [ocultar um aplicativo da experiência do usuário em Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Somente os aplicativos 950 aos quais um usuário tem acesso podem ser acessados por meio de meus aplicativos. Isso inclui aplicativos ocultos pelo usuário ou pelo administrador. 

### <a name="plan-self-service-group-management-membership"></a>Planejar Associação de gerenciamento de grupo de autoatendimento

Você pode permitir que os usuários criem e gerenciem seus próprios grupos de segurança ou grupos de Microsoft 365 no Azure AD. O proprietário do grupo pode aprovar ou negar solicitações de associação e delegar o controle de associação de grupo. Os recursos de gerenciamento de grupo de autoatendimento não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas para email.

Para planejar a associação de grupo de autoatendimento, determine se você permitirá que todos os usuários em sua organização criem e gerenciem grupos ou apenas um subconjunto de usuários. Se você estiver permitindo um subconjunto de usuários, precisará configurar um grupo ao qual essas pessoas são adicionadas. 

Consulte [Configurar o gerenciamento de grupo de autoatendimento no Azure Active Directory](../enterprise-users/groups-self-service-management.md) para obter detalhes sobre como habilitar esses cenários.

### <a name="plan-self-service-application-access"></a>Planejar o acesso ao aplicativo de autoatendimento

Você pode permitir que os usuários descubram e solicitem acesso a aplicativos por meio do painel meus aplicativos. Para fazer isso, primeiro você deve 

* habilitar o gerenciamento de grupo de autoatendimento

* Habilitar aplicativo para SSO

* criar um grupo para acesso ao aplicativo

![Captura de tela da configuração de autoatendimento de meus aplicativos](./media/my-apps-deployment-plan/my-apps-self-service.png)

Quando os usuários solicitam acesso, eles estão solicitando acesso ao grupo subjacente, e os proprietários do grupo podem ser permissões delegadas para gerenciar a associação de grupo e, portanto, o acesso ao aplicativo. Os fluxos de trabalho de aprovação estão disponíveis para aprovação explícita para acessar aplicativos. Os usuários que são aprovadores receberão notificações no portal meus aplicativos quando houver uma solicitação pendente de acesso ao aplicativo.

## <a name="plan-reporting-and-auditing"></a>Planejar relatórios e auditoria

O Azure AD fornece [relatórios que oferecem informações técnicas e de negócios]. /reports-monitoring/overview-reports.md). Trabalhe com seus proprietários de aplicativos técnicos e de negócios para assumir a propriedade desses relatórios e consumi-los regularmente. A tabela a seguir fornece alguns exemplos de cenários de relatório típicos.

| Exemplo| gerenciar riscos| Aumentar a produtividade| Governança e conformidade |
| - | - | - | -|
| Tipos de relatório| Permissões e uso do aplicativo| Atividade de provisionamento de conta| Revisar quem está acessando os aplicativos |
| Ações potenciais| Acesso de auditoria; revogar permissões| Corrigir quaisquer erros de provisionamento| Revogar acesso |


O Azure AD mantém a maioria dos dados de auditoria por 30 dias. Os dados estão disponíveis por meio do portal de administração do Azure ou da API para que você faça o download em seus sistemas de análise.

#### <a name="auditing"></a>Auditoria

Os logs de auditoria para acesso ao aplicativo estão disponíveis por 30 dias. Se sua organização precisar de retenção mais longa, exporte os logs para uma ferramenta SIEM (evento de informações de segurança e gerenciamento), como Splunk ou ArcSight.

Para backups de auditoria, relatórios e recuperação de desastres, documente a frequência necessária de download, o que é o sistema de destino e quem é responsável por gerenciar cada backup. Talvez você não precise de backups de auditoria e de relatórios separados. O backup de recuperação de desastre deve ser uma entidade separada.

## <a name="validate-your-deployment"></a>Validar sua implantação

Verifique se a implantação de meus aplicativos foi exaustivamente testada e se um plano de reversão está em vigor.

Realize os testes a seguir com dispositivos corporativos e dispositivos pessoais. Esses casos de teste também devem refletir seus casos de uso de negócios. A seguir estão alguns casos com base em cenários técnicos típicos. Adicione outras pessoas específicas às suas necessidades.

#### <a name="application-sso-access-test-case-examples"></a>Exemplos de casos de teste de acesso de SSO de aplicativo:


| Caso comercial| Resultado esperado |
| - | - |
| O usuário entra no portal meus aplicativos| O usuário pode entrar e ver seus aplicativos |
| O usuário inicia um aplicativo SSO Federado| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo de SSO de senha pela primeira vez| O usuário precisa instalar a extensão meus aplicativos |
| O usuário inicia um aplicativo de SSO de senha uma hora subsequente| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo do portal Microsoft 365| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo do Managed Browser| O usuário é conectado automaticamente ao aplicativo |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exemplos de casos de teste de recursos de autoatendimento do aplicativo


| Caso comercial| Resultado esperado |
| - | - |
| O usuário pode gerenciar a associação ao aplicativo| O usuário pode adicionar/remover membros que têm acesso ao aplicativo |
| O usuário pode editar o aplicativo| O usuário pode editar a descrição e as credenciais do aplicativo para aplicativos de SSO de senha |


### <a name="rollback-steps"></a>Etapas de reversão

É importante planejar o que fazer se a implantação não funcionar conforme o planejado. Se a configuração de SSO falhar durante a implantação, você deve entender como [solucionar problemas de SSO](../hybrid/tshoot-connect-sso.md) e reduzir o impacto para os usuários. Em circunstâncias extremas, talvez seja necessário [reverter o SSO](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Gerenciar sua implementação

Use a função menos privilegiada para realizar uma tarefa necessária dentro do Azure Active Directory. [Examine as diferentes funções que estão disponíveis](../roles/permissions-reference.md) e escolha a correta para resolver suas necessidades para cada pessoa para este aplicativo. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Personas| Funções| Função do Azure AD |
| - | - | - |
| Administrador de assistência técnica| Suporte da camada 1| Nenhum |
| Administrador de identidade| Configurar e depurar quando os problemas afetam o Azure AD| Administrador global |
| Administrador do aplicativo| Atestado de usuário no aplicativo, configuração de usuários com permissões| Nenhum |
| Administradores de infraestrutura| Proprietário da substituição do certificado| Administrador global |
| Proprietário da empresa/participante| Atestado de usuário no aplicativo, configuração de usuários com permissões| Nenhum |


Você pode usar [Privileged Identity Management](../privileged-identity-management/pim-configure.md) para gerenciar suas funções para fornecer auditoria adicional, controle e revisão de acesso para usuários com permissões de diretório.

## <a name="next-steps"></a>Próximas etapas

[Planejar uma implantação da autenticação multifator do Azure AD](../authentication/howto-mfa-getstarted.md)

[Planejar uma implantação do Proxy de Aplicativo](application-proxy-deployment-plan.md)


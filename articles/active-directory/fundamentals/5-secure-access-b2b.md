---
title: Transição para colaboração regida com Azure Active Directory colaboração B2B
description: Mover para colaboração regida com a colaboração B2B do Azure AD.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 654e1c163a16f427958320ea081c38338e0ab4e1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725385"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Transição para colaboração regida com Azure Active Directory colaboração B2B 

Manter sua colaboração sob controle é fundamental para proteger o acesso externo aos seus recursos. Antes de continuar com este artigo, verifique se você tem:

* [Determinada sua postura de segurança](1-secure-access-posture.md)

* [Descobriu seu estado atual](2-secure-access-current-state.md)

* [Criou um plano de segurança](3-secure-access-plan.md)

* [Entendi como os grupos e a segurança funcionam juntos](4-secure-access-groups.md)

Depois de fazer essas coisas, você estará pronto para passar para a colaboração controlada. Este artigo orientará você a mover toda a sua colaboração externa para [Azure Active Directory colaboração B2B](../external-identities/what-is-b2b.md) (Azure ad B2B). O Azure ad B2B é um recurso de [identidades externas do Azure ad](../external-identities/compare-with-b2c.md).

## <a name="control-who-your-organization-collaborates-with"></a>Controlar com quem sua organização colabora

Você deve decidir se deseja limitar a quais organizações seus usuários podem colaborar e quem dentro de sua organização pode iniciar a colaboração. A maioria das organizações assume a abordagem de permitir que as unidades de negócios decidam com quem colaboram e delegam a aprovação e a supervisão conforme necessário. Por exemplo, algumas organizações de serviços governamentais, educacionais e financeiros não permitem a colaboração aberta. Talvez você queira usar os recursos do Azure AD para a colaboração de escopo, conforme discutido no restante desta seção.

### <a name="determine-collaboration-partners"></a>Determinar parceiros de colaboração

Primeiro, certifique-se de ter documentado as organizações com as quais você está colaborando e os domínios para os usuários das organizações. Um parceiro de colaboração pode ter vários domínios. Por exemplo, um parceiro pode ter várias unidades de negócios com domínios separados.

Em seguida, determine se você deseja habilitar a colaboração futura com o 

* qualquer domínio (mais inclusivo)

* todos os domínios, exceto aqueles explicitamente negados 

* somente domínios específicos (mais restritivos)

> [!NOTE]
> Quanto mais restritivas as configurações de colaboração, mais provável que seus usuários vão fora de sua estrutura de colaboração aprovada. É recomendável habilitar a colaboração mais ampla que suas necessidades de segurança permitirão e revisando minuciosamente essa colaboração em vez de ser excessivamente restritiva. 

Observe também que a limitação para um único domínio pode impedir inadvertidamente a colaboração autorizada com as organizações, que têm outros domínios não relacionados para seus usuários. Por exemplo, se estiver fazendo negócios com uma organização Contoso, o ponto inicial de contato com a Contoso pode ser um de seus funcionários baseados nos EUA que têm um email com um domínio ". com". No entanto, se você permitir apenas o domínio ". com", poderá omitir inadvertidamente seus funcionários canadenses que têm o domínio ". ca". 

Há circunstâncias em que você desejaria permitir apenas parceiros de colaboração específicos. Por exemplo, um sistema de Universidade pode querer apenas permitir seu próprio acesso docente a um locatário de recursos. Ou um conglomerado pode querer apenas permitir que as subsidiárias específicas colaborem entre si para obter conformidade com uma estrutura necessária.

#### <a name="using-allow-and-deny-lists"></a>Usando listas de permissão e negação

Você pode usar uma lista de permissões ou lista de negações para [restringir convites a usuários B2B](../external-identities/allow-deny-list.md) de organizações específicas. Você pode usar apenas uma lista de permissões ou de negação, não ambos.

* Uma [lista de permissões](../external-identities/allow-deny-list.md) limita a colaboração somente com os domínios listados; todos os outros domínios são efetivamente na lista de negações.

* Uma [lista de negações](../external-identities/allow-deny-list.md) permite a colaboração com qualquer domínio que não esteja na lista de negações.

> [!IMPORTANT]
> Essas listas não se aplicam a usuários que já estão em seu diretório. Eles também não se aplicam ao OneDrive for Business e ao SharePoint permitem que as listas Deny sejam separadas.

Algumas organizações usam uma lista de domínios "ator inválidos" conhecidos pelo seu provedor de segurança gerenciado para sua lista de negações. Por exemplo, se a organização estiver fazendo negócios legitimamente com a Contoso e usando um domínio. com, pode haver uma organização não relacionada que esteve usando o domínio contoso. org e tentando um ataque de phishing para representar os funcionários da contoso. 

## <a name="control-how-external-users-gain-access"></a>Controlar como os usuários externos têm acesso

Há várias maneiras de colaborar com parceiros externos usando o Azure AD B2B. Para começar a colaboração, você convida ou permite que seu parceiro acesse seus recursos. Os usuários podem obter acesso respondendo a:

* Resgatar [um convite enviado por email](../external-identities/redemption-experience.md)ou [um link direto para compartilhar](../external-identities/redemption-experience.md) um recurso.

* Solicitando acesso [por meio de um aplicativo](../external-identities/self-service-sign-up-overview.md) que você cria

* Solicitando acesso por meio do meu portal de [acesso](../governance/entitlement-management-request-access.md)

Ao habilitar o Azure AD B2B, você habilita a capacidade de convidar usuários convidados por meio de links diretos e convites por email por padrão. Os convites por meio de OTP de email e um portal de autoatendimento estão atualmente em visualização e devem ser habilitados dentro das identidades externas | Configurações de colaboração externas no portal do Azure AD.

### <a name="control-who-can-invite-guest-users"></a>Controlar quem pode convidar usuários convidados

Determine quem pode convidar usuários convidados a acessar recursos.

* A configuração mais restritiva é permitir que somente os administradores e os usuários tenham a função de emissor de [convidado](../external-identities/delegate-invitations.md) para convidar convidados.

* Se os requisitos de segurança permitirem, é recomendável permitir que todos os usuários com um UserType de membro convidem convidados.

* Determine se você deseja que os usuários com um UserType de convidado, que é o tipo de conta padrão para usuários B2B do Azure AD, possam convidar outros convidados. 

![Captura de tela das configurações de convite de convidado.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Coletar informações adicionais sobre usuários externos

Se você usar o gerenciamento de direitos do Azure AD, poderá configurar perguntas para que os usuários externos respondam. Em seguida, as perguntas serão mostradas aos aprovadores para ajudá-las a tomar uma decisão. Você pode configurar diferentes conjuntos de perguntas para cada [política de pacote de acesso](../governance/entitlement-management-access-package-approval-policy.md) para que os Aprovadores possam ter informações relevantes para o acesso que estão sendo aprovados. Por exemplo, se um pacote de acesso for destinado ao acesso do fornecedor, o solicitante poderá ser solicitado para o número do contrato do fornecedor. Um pacote de acesso diferente destinado a fornecedores pode solicitar seu país de origem.

Se você usar um portal de autoatendimento, poderá usar [conectores de API](../external-identities/api-connectors-overview.md) para coletar atributos adicionais sobre os usuários à medida que eles se inscreverem. Você pode, potencialmente, usar esses atributos para atribuir acesso. Por exemplo, se durante o processo de inscrição você coletar sua ID de fornecedor, poderá usar esse atributo para atribuí-las dinamicamente a um grupo ou a um pacote de acesso para esse fornecedor. Você pode criar atributos personalizados no portal do Azure e usá-los nos seus fluxos dos usuários de inscrição por autoatendimento. Você também pode ler e gravar esses atributos usando a [API do Microsoft Graph](../../active-directory-b2c/manage-user-accounts-graph-api.md). 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Solucionar problemas de resgate de convite para usuários do Azure AD

Há três instâncias quando convidados usuários convidados de um parceiro de colaboração usando o Azure AD terão problemas para resgatar um convite.

* Se estiver usando uma lista de permissões e o domínio do usuário não estiver incluído em uma lista de permissões.

* Se o locatário doméstico do parceiro de colaboração tiver restrições de locatário que impeçam a colaboração com usuários externos.

* Se o usuário não fizer parte do locatário do Azure AD do parceiro. Por exemplo, há usuários em contoso.com que estão apenas em Active Directory (ou em outro IdP local), eles só poderão resgatar convites por meio do processo de OTP de email. para obter mais informações, consulte o [fluxo de resgate de convite](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Controlar quais usuários externos podem acessar

A maioria das organizações não é monolítica. Ou seja, há alguns recursos que são suficientes para compartilhar com usuários externos e alguns não querem que usuários externos acessem. Portanto, você deve controlar quais usuários externos acessam. Considere o uso de [pacotes de acesso e gerenciamento de direitos para controlar o acesso](6-secure-access-entitlement-managment.md) a recursos específicos.

Por padrão, os usuários convidados podem ver informações e atributos sobre membros do locatário e outros parceiros, incluindo associações de grupo. Considere se os requisitos de segurança chamam a limitação do acesso de usuário externo a essas informações.

![Captura de tela da definição de configurações de colaboração externas.](media/secure-external-access/5-external-collaboration-settings.png)

Recomendamos as seguintes restrições para usuários convidados.

* **Limite o acesso de convidado a grupos de navegação e outras propriedades no diretório**

   * Use as configurações de colaboração externas para restringir a capacidade do convidado de ler grupos dos quais não são membros.

* **Bloquear o acesso a aplicativos somente de funcionários**.

   * Crie uma política de acesso condicional para bloquear o acesso a aplicativos integrados ao Azure AD que são apropriados somente para usuários não convidados. 

* **Bloquear o acesso ao portal do Azure. Você pode fazer exceções raras necessárias**. 

   * Crie uma política de acesso condicional que inclua todos os usuários convidados e externos e, em seguida, [implemente uma política para bloquear o acesso](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>Remover usuários que não precisam mais de acesso

Avalie o acesso atual para que você possa [examinar e remover usuários que não precisam mais de acesso](../governance/access-reviews-external-users.md). Inclua usuários externos em seu locatário como convidados e aqueles com contas de membro. 

Algumas organizações adicionaram usuários externos, como fornecedores, parceiros e contratados como membros. Esses membros podem ter um atributo específico ou nomes de acessadores que começam com, por exemplo

* v-para fornecedores

* p-para parceiros

* c-para prestadores de

Avalie todos os usuários externos com contas de membros para determinar se eles ainda precisam de acesso. Nesse caso, migre esses usuários para o Azure AD B2B conforme descrito na próxima seção.

Você também pode ter usuários convidados que não foram convidados por meio do gerenciamento de direitos ou B2B do Azure AD

Para encontrar esses usuários, você pode:

* [Encontre usuários convidados não convidados por meio do gerenciamento de direitos](../governance/access-reviews-external-users.md).

   * Fornecemos um [exemplo de script do PowerShell.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Migre esses usuários para usuários B2B do Azure AD conforme descrito na seção a seguir.

## <a name="transition-your-current-external-users-to-b2b"></a>Fazer a transição de seus usuários externos atuais para B2B

Se você não estiver usando o Azure AD B2B, provavelmente terá usuários que não são funcionários em seu locatário. Recomendamos que você migre essas contas para as contas de usuário externo B2B do Azure AD e, em seguida, altere seu UserType para Guest. Isso permite que você aproveite as várias maneiras pelas quais o Azure AD e o Microsoft 365 permitem tratar usuários externos de forma diferente. Algumas dessas maneiras incluem:

* Incluindo ou excluindo facilmente usuários convidados em políticas de acesso condicional

* Incluindo ou excluindo facilmente usuários convidados em pacotes do Access e revisões de acesso

* Incluindo ou excluindo facilmente o acesso externo a equipes, SharePoint e outros recursos.

Para fazer a transição desses usuários internos ao mesmo tempo em que mantém suas associações de acesso, UPN e grupo atuais, confira [convidar usuários externos para a colaboração B2B](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>Descomissionar métodos de colaboração indesejados

Para concluir a transição para a colaboração regida, você deve encerrar métodos de colaboração indesejados. O que você descomissiona é baseado no grau de controle que você deseja que ele exerça sobre a colaboração e sua postura de segurança. Para obter informações sobre ti versus controle de usuário final, consulte [determinar sua postura de segurança para acesso externo](1-secure-access-posture.md).

Veja a seguir os veículos de colaboração que você pode desejar avaliar.

### <a name="direct-invitation-through-microsoft-teams"></a>Convite direto por meio do Microsoft Teams

Por padrão, as equipes permitem acesso externo, o que significa que a organização pode se comunicar com todos os domínios externos. Se você quiser restringir ou permitir domínios específicos apenas para equipes, poderá fazer isso no [portal de administração de equipes](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Compartilhamento direto por meio do SharePoint e do OneDrive

O compartilhamento direto por meio do SharePoint e do OneDrive pode adicionar usuários fora do processo de gerenciamento de direitos. Para obter uma visão detalhada dessas configurações [, consulte Gerenciar o acesso com o Microsoft Teams, o SharePoint e o onedrive for Business,](9-secure-access-teams-sharepoint.md) você também pode [bloquear o uso do onedrive pessoal do usuário](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) , se desejado.

### <a name="sending-documents-through-email"></a>Enviando documentos por email

Os usuários enviarão documentos por email para usuários externos. Considere como você deseja restringir e criptografar o acesso a esses documentos usando rótulos de sensibilidade. Para obter mais informações, consulte Gerenciar o acesso com rótulos de sensibilidade.

### <a name="unsanctioned-collaboration-tools"></a>Ferramentas de colaboração não comsanções

O panorama das ferramentas de colaboração é vasto. Os usuários provavelmente usam muitas das suas tarefas oficiais, incluindo plataformas como Google Docs, DropBox, margem de atraso ou zoom. É possível bloquear o uso de tais ferramentas de uma rede corporativa no nível do firewall e com o gerenciamento de aplicativos móveis para dispositivos gerenciados pela organização. No entanto, isso também bloqueará qualquer instância do aprovado dessas plataformas e não bloquearia o acesso de dispositivos não gerenciados. Bloqueie as plataformas que você não deseja usar, se necessário, e crie políticas de negócios para nenhum uso não aprovado das plataformas que você precisa usar. 

Para obter mais informações sobre como gerenciar aplicativos não aprovados, consulte:

* [Controlando aplicativos conectados](/cloud-app-security/governance-actions)

* [Aprovar e desaprovar um aplicativo.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure ad](5-secure-access-b2b.md) (você está aqui.)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)
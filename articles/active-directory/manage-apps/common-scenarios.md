---
title: Cenários comuns de gerenciamento de aplicativos para o Azure Active Directory | Microsoft Docs
description: Centralizar o gerenciamento de aplicativos com o Azure AD
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: overview
ms.date: 03/02/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acfff8a9152c767b61463bed0165d5ae390f649
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374991"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizar o gerenciamento de aplicativos com o Azure AD

Senhas, um pesadelo para o setor de TI e uma chateação para funcionários do mundo inteiro. É por isso que cada vez mais empresas estão adotando o Azure Active Directory, a solução de Gerenciamento de acesso e identidade da Microsoft para a nuvem e para todos os outros recursos. Vá de um aplicativo para outro sem precisar inserir uma senha para cada um. Vá do Outlook para o Workday e para o ADP gastando apenas o tempo necessária para abri-los, com rapidez e segurança. Colabore com parceiros e até mesmo com pessoas de fora da organização sem precisar de suporte de TI. Além disso, o Azure AD ajuda a gerenciar riscos protegendo os aplicativos que você usa com recursos como a autenticação multifator, que verifica quem é você, e o uso de inteligência de segurança e aprendizado de máquina adaptado continuamente, que detecta entradas suspeitas e oferece acesso seguro aos aplicativos de que você precisa, onde quer que esteja. Ele não é ótimo apenas para os usuários, mas também para o setor de TI. Com as revisões de acesso just-in-time e um conjunto de governança de escala completa, o Azure AD ajuda você a manter a conformidade e a impor políticas. E veja só, você pode até mesmo automatizar o provisionamento de contas de usuário, o que facilita muito o gerenciamento de acesso. Confira alguns dos cenários comuns nos quais os clientes usam os recursos de gerenciamento de aplicativos do Azure Active Directory.

**Cenários comuns**


> [!div class="checklist"]
> * SSO para todos os seus aplicativos
> * Automatizar o provisionamento e o desprovisionamento 
> * Proteger seus aplicativos
> * Administrar o acesso a seus aplicativos
> * Acesso seguro híbrido

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Cenário 1: Configurar o SSO para todos os aplicativos

Chega de gerenciar senhas. Acesse com segurança todos os recursos de que você precisa com suas credenciais corporativas. 

|Recurso  | Descrição | Recomendação |
|---------|---------|---------|
|SSO|SSO federado baseado em padrões usando padrões confiáveis do setor.|Sempre use [SAML/OIDC](../develop/v2-howto-app-gallery-listing.md) para habilitar o SSO quando seu aplicativo for compatível.|
|Meus aplicativos|Oferece a seus usuários um hub simples para descobrir e acessar todos os seus aplicativos. Possibilite que eles sejam mais produtivos com os recursos de autoatendimento, como a solicitação de acesso a novos aplicativos e grupos ou o gerenciamento do acesso a recursos em nome de outras pessoas.| Implante [Meus Aplicativos](my-apps-deployment-plan.md) na sua organização depois de integrar seus aplicativos ao Azure AD para SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Cenário 2: Automatizar o provisionamento e o desprovisionamento 


A maioria dos aplicativos exige que o usuário seja provisionado no aplicativo antes de acessar os recursos de que precisa. O uso de arquivos CSV ou de scripts complexos pode ser dispendioso e difícil de gerenciar. Além disso, os clientes precisam garantir que as contas sejam removidas quando alguém não precisar mais de acesso. Aproveite as ferramentas abaixo para automatizar o provisionamento e o desprovisionamento. 


|Recurso  |Descrição|Recomendação |
|---------|---------|---------|
|Provisionamento de SCIM|O [SCIM](https://aka.ms/SCIMOverview) é uma melhor prática do setor para automatizar o provisionamento de usuários. Qualquer aplicativo compatível com SCIM pode ser integrado ao Azure AD. Crie, atualize e exclua contas de usuário automaticamente sem precisar manter arquivos CSV, scripts personalizados ou soluções locais.|Confira a lista crescente de aplicativos [pré-integrados](../saas-apps/tutorial-list.md) na galeria de aplicativos do Azure AD|
|Microsoft Graph|Tire proveito da variedade e da profundidade dos dados de que o Azure AD dispõe para enriquecer seu aplicativo com os dados de que ele precisa.|Aproveite o [Microsoft Graph](https://developer.microsoft.com/graph/) para obter dados de todo o ecossistema da Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Cenário 3: Proteger seus aplicativos
A identidade é o elemento central da segurança. Quando uma identidade é comprometida, é incrivelmente difícil interromper o efeito dominó antes que seja tarde demais. Em média, se passam mais de 100 dias até que as organizações descubram que ocorreu um comprometimento. Use as ferramentas fornecidas pelo Azure AD para aprimorar a postura de segurança de seus aplicativos. 

|Recurso  |Descrição| Recomendação |
|---------|---------| ---------|
|MFA do Azure AD|A MFA (Autenticação Multifator) do Azure AD é uma solução de verificação em duas etapas da Microsoft. Usando métodos de autenticação aprovados pelo administrador, a MFA do Azure AD ajuda a proteger o acesso a seus dados e aplicativos, ao mesmo tempo em que atende à demanda por um processo de logon simples.| [Habilite a MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) para seus usuários.  |
|Acesso Condicional|Com o Acesso Condicional, você pode implementar decisões de controle de acesso automatizado sobre quem pode acessar os aplicativos de nuvem com base em condições.| Examine os [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) e as [políticas comuns](../conditional-access/concept-conditional-access-policy-common.md) que os clientes estão usando. | 
|Identity Protection|O Identity Protection usa os aprendizados que a Microsoft adquiriu com sua posição em organizações com o Azure AD, com o espaço do consumidor com as contas Microsoft e com os jogos do Xbox para proteger seus usuários. A Microsoft analisa 6,5 trilhões de sinais por dia para identificar e proteger os clientes contra ameaças.|Habilite as [políticas de proteção de identidade padrão](../identity-protection/concept-identity-protection-policies.md) fornecidas pelo nosso serviço. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Cenário 4: Administrar o acesso a seus aplicativos
A Governança de Identidade ajuda as organizações a atingir um equilíbrio entre produtividade – Com que rapidez uma pessoa pode ter acesso aos aplicativos de que precisa, por exemplo, quando entra em minha organização? E segurança – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa? 

|Recurso  |Descrição|Recomendação |
|---------|---------| ---------|
|ELM|O gerenciamento de direitos do Azure AD pode ajudar usuários dentro e fora da organização a gerenciar o acesso a seus aplicativos de maneira mais eficiente.| Permita que usuários não administradores gerenciem o acesso a seus aplicativos com os [pacotes de acesso](../governance/entitlement-management-access-package-first.md).|
|Revisões de acesso|O acesso do usuário aos aplicativos pode ser examinado regularmente para garantir que somente as pessoas corretas tenham acesso contínuo.| [Examine o acesso](../governance/access-reviews-overview.md) a seus aplicativos mais confidenciais. |
|Log Analytics|Gere relatórios sobre quem está acessando quais aplicativos e armazene-os na ferramenta de SIEM de sua escolha para correlacionar dados entre fontes de dados e ao longo do tempo.| Habilite a [análise de logs](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) e configure alertas para eventos críticos relacionados a seus aplicativos. |


## <a name="scenario-5-hybrid-secure-access"></a>Cenário 5: Acesso Seguro Híbrido
A identidade só poderá ser seu plano de controle se ela puder conectar tudo, em aplicativos de nuvem e locais. Aproveite as ferramentas fornecidas pelo Azure AD e por seus parceiros para proteger o acesso a aplicativos baseados em autenticação herdada.

|Recurso  |Descrição|Recomendação |
|---------|---------|---------|
|Proxy do Aplicativo|Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Eles precisam acessar aplicativos SaaS na nuvem e aplicativos corporativos no local. O proxy de Aplicativo do Azure AD habilita esse acesso robusto sem VPNs (redes virtuais privadas) nem DMZs (redes de perímetro) dispendiosas e complexas.|Configure o [acesso remoto](./application-proxy.md) para seus aplicativos locais. |
|F5, Akamai, Zscaler|Usando o controlador de rede e entrega existente, você pode proteger facilmente aplicativos herdados que ainda são críticos para seus processos de negócios, mas que antes não podiam ser protegidos com o Azure AD. É provável que você já tenha tudo de que precisa para começar a proteger esses aplicativos.| Você usa o Akamai, o Citrix, o F5 ou o Zscaler? Confira nossas [soluções pré-criadas](./secure-hybrid-access.md). | 

## <a name="related-articles"></a>Artigos relacionados

- [Gerenciamento de aplicativos](./index.yml)
- [Provisionamento de aplicativo](../app-provisioning/user-provisioning.md)
- [Acesso seguro híbrido](./secure-hybrid-access.md)
- [Governança de identidade](../governance/identity-governance-overview.md)
- [Plataforma de identidade da Microsoft](../develop/v2-overview.md)
- [Segurança de identidade](../conditional-access/index.yml)
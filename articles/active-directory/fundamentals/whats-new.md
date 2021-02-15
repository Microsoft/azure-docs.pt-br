---
title: Quais são as novidades? Notas sobre a versão – Azure Active Directory | Microsoft Docs
description: Saiba o que há de novo no Azure Active Directory; como as notas de versão mais recentes, problemas conhecidos, correções de bugs, funcionalidade preterida e alterações futuras.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c835ddcee332499ef7853d0d2fa4fcdbcf8a1cf
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988876"
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

>Seja notificado sobre quando revisitar esta página para ver atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` no seu leitor de feed ![ícone do leitor de RSS feed](./media/whats-new/feed-icon-16x16.png).

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente. Se você estiver procurando itens com mais de seis meses, poderá encontrá-los em [arquivo morto para o que há de novo no Azure Active Directory](whats-new-archive.md).

---
## <a name="january-2021"></a>Janeiro de 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>O token secreto será um campo obrigatório ao configurar o provisionamento

**Tipo:** plano de alteração  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

No passado, o campo de token secreto poderia ser mantido vazio ao configurar o provisionamento no aplicativo personalizado/BYOA. Essa função foi destinada a ser usada apenas para teste. Atualizaremos a interface do usuário para tornar o campo necessário. 

Os clientes podem contornar esse requisito para fins de teste usando um sinalizador de recurso na URL do navegador. [Saiba mais](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-firstline-workers-at-scale"></a>Visualização pública – personalizar e configurar dispositivos compartilhados Android para trabalhos de início em escala

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** segurança e proteção da identidade
 
As equipes do Azure AD e do Microsoft Endpoint Manager foram combinadas para trazer a capacidade de personalizar, dimensionar e proteger seus primeiros dispositivos de trabalho.

Os seguintes recursos de visualização lhe permitirão:
- Provisionar dispositivos Android compartilhados em escala com o Microsoft Endpoint Manager
- Proteja seu acesso a usuários de turnos usando o acesso condicional com base no dispositivo
- Personalizar experiências de entrada para os trabalhadores com turnos com a tela inicial gerenciada

Para saber mais, consulte [Personalizar e configurar dispositivos compartilhados para trabalhos de início em escala](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Visualização pública – os logs de provisionamento agora podem ser baixados como um CSV ou JSON

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Os clientes podem baixar os logs de provisionamento como um arquivo CSV ou JSON por meio da interface do usuário e por meio da API do Graph. Para saber mais, consulte [Provisionando relatórios no portal de Azure Active Directory](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Visualização pública – atribuir grupos de nuvem a funções personalizadas do Azure AD e funções com escopo de unidade de administrador

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
Os clientes podem atribuir um grupo de nuvem a funções personalizadas do Azure AD ou uma função com escopo de unidade de administrador. Para saber como usar esse recurso, consulte [usar grupos de nuvem para gerenciar atribuições de função em Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Disponibilidade geral-Azure AD Connect a sincronização de nuvem (anteriormente conhecida como provisionamento na nuvem)

**Tipo:** novo recurso  
**Categoria de serviço:** Sincronização de nuvem Azure AD Connect  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Azure AD Connect a sincronização de nuvem agora está disponível para todos os clientes.

Azure AD Connect a nuvem move o trabalho pesado da lógica de transformação para a nuvem, reduzindo seu espaço local. Além disso, várias implantações de agentes leves estão disponíveis para maior disponibilidade de sincronização. [Saiba mais](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Disponibilidade geral – administrador de simulação de ataque e funções internas do autor da carga de ataque

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
Duas novas funções no controle de acesso Role-Based estão disponíveis para serem atribuídas a usuários, administrador de simulação de ataque e autor de carga de ataque. 

Os usuários na função de [administrador de simulação de ataque](../roles/permissions-reference.md#attack-simulation-administrator) têm acesso a todas as simulações no locatário e podem:
- criar e gerenciar todos os aspectos da criação da simulação de ataque
- inicialização/agendamento de uma simulação
-  Examine os resultados da simulação. 

Os usuários na função de [autor de carga de ataque](../roles/permissions-reference.md#attack-payload-author) podem criar cargas de ataque, mas não iniciar nem agendá-las de fato. As cargas de ataque ficam então disponíveis para todos os administradores no locatário que podem usá-las para criar uma simulação.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Disponibilidade geral-relatório Resumo de uso relatórios função interna do leitor

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários com a função leitor de relatórios de Resumo de uso podem acessar dados agregados no nível do locatário e informações associadas no centro de administração Microsoft 365 para obter a pontuação de uso e produtividade. No entanto, eles não podem acessar detalhes ou informações de nível de usuário. 

No centro de administração Microsoft 365 para os dois relatórios, diferenciamos os dados agregados no nível de locatário e os detalhes de nível de usuário. Essa função adiciona uma camada extra de proteção aos dados individuais de identificação do usuário. [Saiba mais](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Disponibilidade geral-exigir concessão de política de proteção de aplicativo no acesso condicional do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 
A concessão de acesso condicional do Azure AD para "exigir política de proteção de aplicativo" agora está disponível. 

A política fornece os seguintes recursos:
- Permite o acesso somente ao usar um aplicativo móvel que dá suporte à proteção de aplicativo do Intune
- Permite o acesso somente quando um usuário tem uma política de proteção de aplicativo do Intune entregue ao aplicativo móvel

Saiba mais sobre como configurar uma política de acesso condicional para proteção de aplicativo [aqui](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Disponibilidade geral-email One-Time senha

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
O email OTP permite que as organizações de todo o mundo colaborem com qualquer pessoa enviando um link ou convite por email. Os usuários convidados podem verificar sua identidade com a senha de uso único enviada ao seu email para acessar os recursos do parceiro. [Saiba mais](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – janeiro de 2021

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Para obter mais informações, consulte [o que é o provisionamento de usuário de aplicativo SaaS automatizado no Azure ad?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – janeiro de 2021

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em janeiro de 2021, adicionamos os seguintes 29 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto fácil](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[formação de Bienvenue](https://formation.bienvenue.pro/login), [SSO do Aida Healthcare](https://aidaforparents.com/login/organizations), [produtos de assistência do SOS Internacional](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO Dam](../saas-apps/ephoto-dam-tutorial.md), [noção](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), [timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), cheio [de](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [informações sobre o usuário de Aruba](../saas-apps/aruba-user-experience-insight-tutorial.md), Synerise [](../saas-apps/illusive-networks-tutorial.md) [SSO](../saas-apps/contentsquare-sso-tutorial.md) [](../saas-apps/imperva-data-security-tutorial.md), [perímetro 81](../saas-apps/perimeter-81-tutorial.md), [Imperva Suite Enterprise Edition](https://docs.microsoft.com/azure/active-directory/saas-apps/burp-suite-enterprise-edition-tutorial) : o [, o](../saas-apps/proware-tutorial.md) [, o](../saas-apps/splan-visitor-tutorial.md). [](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Visualização pública-o Gerenciador de segundo nível pode ser definido como Aprovador alternativo

**Tipo:** recurso alterado  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Uma opção adicional quando você seleciona aprovadores agora está disponível no gerenciamento de direitos. Se você selecionar "Gerenciador como Aprovador" para o primeiro aprovador, terá outra opção, "gerente de segundo nível como Aprovador alternativo", disponível para escolher no campo aprovador alternativo. Se você selecionar essa opção, será necessário adicionar um Aprovador de fallback para encaminhar a solicitação para, caso o sistema não possa encontrar o gerente de segundo nível. [Saiba mais](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Disponibilidade geral-navegue até as equipes diretamente do meu portal de acesso

**Tipo:** recurso alterado  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Agora você pode iniciar equipes diretamente do meu portal de acesso. 

Para fazer isso, entre no meu acesso ( https://myaccess.microsoft.com/) , navegue até "pacotes de acesso" e vá para a guia "ativo" para ver todos os pacotes de acesso aos quais você já tem acesso. Quando você expande o pacote de acesso selecionado e passa o mouse sobre equipes, você pode iniciá-lo clicando no botão "abrir". [Saiba mais](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Log aprimorado & End-User prompts para usuários convidados arriscados

**Tipo:** recurso alterado  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade
 

Os prompts de log e End-User para usuários convidados arriscados foram atualizados. Saiba mais em [proteção de identidade e usuários B2B](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>Dezembro de 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Visualização pública – inscrição Azure AD B2C telefone e entrada usando a política interna

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
A inscrição e a entrada do telefone B2C usando a política interna permitem que os administradores de ti e desenvolvedores de organizações permitam que seus usuários finais entrem e se inscrevam usando um número de telefone em fluxos de usuário. Leia [Configurar inscrição e entrada do telefone para fluxos de usuário (versão prévia)](../../active-directory-b2c/phone-authentication-user-flows.md) para saber mais.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Disponibilidade geral-os padrões de segurança agora estão habilitados para todos os novos locatários por padrão

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Para proteger as contas de usuário, todos os novos locatários criados em ou após 12 de novembro de 2020 serão fornecidos com os padrões de segurança habilitados. Os padrões de segurança impõe várias políticas, incluindo:
- Requer que todos os usuários e administradores se registrem no MFA usando o aplicativo Microsoft Authenticator
- Requer funções de administrador críticas para usar a MFA todas as vezes que entrarem. Todos os outros usuários serão solicitados a receber MFA sempre que necessário. 
- A autenticação herdada será bloqueada em todo o locatário. 

Para obter mais informações, leia [o que são os padrões de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Disponibilidade geral-suporte para grupos com membros de até 250 mil no AADConnect

**Tipo:** recurso alterado  
**Categoria de serviço:** AD Connect  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
A Microsoft implantou um novo ponto de extremidade (API) para o Azure AD Connect que aprimora o desempenho das operações de serviço de sincronização para o Azure Active Directory. Ao usar o novo [ponto de extremidade v2](../hybrid/how-to-connect-sync-endpoint-api-v2.md), você experimentará ganhos de desempenho perceptíveis na exportação e importação para o Azure AD. Esse novo ponto de extremidade dá suporte aos seguintes cenários:

- Sincronizando grupos com membros de até 250 mil
- Ganhos de desempenho na exportação e importação para o Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Disponibilidade geral-gerenciamento de autorização disponível para locatários na nuvem do Azure na China

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 

Os recursos de gerenciamento de direitos agora estão disponíveis para todos os locatários na nuvem do Azure na China. Para obter informações, visite nosso site de [documentação de governança de identidade](https://docs.azure.cn/zh-cn/active-directory/governance/) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – dezembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros

Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [BizAgi Studio para a Automação de Processos Digitais](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Analisável](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – dezembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em dezembro de 2020, adicionamos 18 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[AwareGo](../saas-apps/awarego-tutorial.md), [SSO do HOWNOW](https://gethownow.com/), [ZyLAB uma contenção legal](https://www.zylab.com/en/product/legal-hold), [guia](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [PIMS 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [Vonage](../saas-apps/vonage-tutorial.md), [conte comigo no painel de operações](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [proprof a base de dados de conhecimento](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd gerenciamento da força de obra](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL tririga](../saas-apps/jll-tririga-tutorial.md), [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Firewall do aplicativo Web](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn talento Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation app](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Navegar para equipes diretamente do meu portal de acesso

**Tipo:** recurso alterado  
**Categoria de serviço:** **Capacidade do produto** de gerenciamento de acesso do usuário: gerenciamento de direitos

Agora você pode iniciar equipes diretamente do meu portal do Access. Para fazer isso, entre no [meu acesso](https://myaccess.microsoft.com/), navegue até pacotes de **acesso** e, em seguida, vá para a guia **ativo** para ver todos os pacotes de acesso aos quais você já tem acesso. Ao expandir o pacote do Access e focalizar as equipes, você pode iniciá-lo clicando no botão **abrir** . 

Para saber mais sobre como usar o meu portal de acesso, acesse [solicitar acesso a um pacote de acesso no gerenciamento de direitos do Azure ad](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Visualização pública-o Gerenciador de segundo nível pode ser definido como Aprovador alternativo

**Tipo:** recurso alterado  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos

Uma opção extra agora está disponível no processo de aprovação no gerenciamento de direitos. Se você selecionar gerente como Aprovador para o primeiro aprovador, terá uma outra opção, gerente de segundo nível como Aprovador alternativo, disponível para escolher no campo aprovador alternativo. Ao selecionar essa opção, você precisa adicionar um Aprovador de fallback para encaminhar a solicitação para, caso o sistema não possa encontrar o gerente de segundo nível.

Para obter mais informações, vá para [alterar as configurações de aprovação de um pacote do Access no gerenciamento de direitos do Azure ad](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Novembro de 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 e substituição 3DES

**Tipo:** plano de alteração  
**Categoria de serviço:** Todos os aplicativos do Azure AD  
**Funcionalidade do produto:** Padrões

O Azure Active Directory substituirá os seguintes protocolos em Azure Active Directory regiões mundiais até 30 de junho de 2021:

- TLS 1.0
- TLS 1.1
- pacote de codificação 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Os ambientes afetados são:
- Nuvem comercial do Microsoft Azure
- Office 365 GCC e WW

Comunicado relacionado todas as combinações de servidor cliente e navegador-servidor devem usar o TLS 1,2 e os pacotes de codificação modernos para manter uma conexão segura com o Azure Active Directory para os serviços Azure, Office 365 e Microsoft 365. Essa alteração está relacionada a [Azure Active Directory TLS 1,0 & 1,1 e à substituição do conjunto de codificação 3DES no US gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – novembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em novembro de 2020, adicionamos os seguintes 52 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Gerenciamento de despesas de viagem &](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [seletor de arquivos Itslearning](https://pmteam.itslearning.com/), [crises Control](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum – ilha de estudo](https://app.studyisland.com/cfw/login/), [gerente de riscos virtual](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [plataforma de análise de pesquisa](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-recrutar](https://recruit.talview.com/login), Tradutor de tempo real, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [despesamanager](https://api.expense-manager.com/), [netsparkr Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [en-Trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/) [,](https://portal.brightbooking.eu/) [Eva check-in](https://my.evacheckin.com/organization), [HowNow webapp SSO](../saas-apps/hownow-webapp-sso-tutorial.md) [,](../saas-apps/sailpoint-identitynow-tutorial.md) [Compensating Risk avalie](../saas-apps/coupa-risk-assess-tutorial.md), [lucida (todos os produtos](../saas-apps/lucid-tutorial.md)[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [JEDOX](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [trabalho](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [appsec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), avaliação de [frete](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), título [Research](../saas-apps/nature-research-tutorial.md), [Play Digital](https://login.playsignage.com/login)Information, [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [madrugada software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [mentor® por eDriving: Business](https://www.edriving.com/), [gradle Enterprise](https://gradle.com/)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Visualização pública-funções personalizadas para aplicativos empresariais

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
 As [funções personalizadas de RBAC para o gerenciamento de aplicativos empresariais delegado](../roles/custom-available-permissions.md) agora estão em visualização pública. Essas novas permissões se baseiam nas funções personalizadas para o gerenciamento de registro de aplicativo, o que permite um controle refinado sobre o acesso dos seus administradores. Ao longo do tempo, serão lançadas permissões adicionais para delegar o gerenciamento do Azure AD.

Alguns cenários comuns de delegação:
- atribuição de usuário e grupos que podem acessar aplicativos de logon único baseados em SAML
- a criação de aplicativos da galeria do Azure AD
- atualização e leitura de configurações básicas do SAML para aplicativos de logon único baseados em SAML
- gerenciamento de certificados de autenticação para aplicativos de logon único baseados em SAML
- atualização dos endereços de email de notificação de entrada de certificados expirados para aplicativos de logon único baseados em SAML
- atualização da assinatura de token SAML e do algoritmo de entrada para aplicativos de logon único baseados em SAML
- criar, excluir e atualizar atributos de usuário e declarações para aplicativos de logon único baseados em SAML
- capacidade de ativar, desativar e reiniciar trabalhos de provisionamento
- atualizações para mapeamento de atributos
- capacidade de ler configurações de provisionamento associadas ao objeto
- capacidade de ler configurações de provisionamento associadas à entidade de serviço
- capacidade de autorizar o acesso ao aplicativo para provisionamento

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Visualização pública-o Azure Proxy de Aplicativo do AD dá suporte nativo ao acesso de logon único para aplicativos que usam cabeçalhos para autenticação

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso
 
O proxy de aplicativo Azure Active Directory (Azure AD) nativamente dá suporte ao acesso de logon único a aplicativos que usam cabeçalhos para autenticação. Você pode configurar valores de cabeçalho exigidos por seu aplicativo no Azure AD. Os valores de cabeçalho serão enviados para o aplicativo por meio do proxy de aplicativo. Para saber mais, confira [logon único baseado em cabeçalho para aplicativos locais com aplicativo Azure ad proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Disponibilidade geral-inscrição Azure AD B2C telefone e entrada usando política personalizada

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Com a inscrição e a entrada do número de telefone, os desenvolvedores e as empresas podem permitir que seus clientes se inscrevam e entrem usando uma senha de uso único enviada ao número de telefone do usuário via SMS. Esse recurso também permite que o cliente altere seu número de telefone se eles perderem o acesso ao seu telefone. Com o poder das políticas personalizadas, permita que desenvolvedores e empresas comuniquem sua marca por meio da personalização de página. Descubra como configurar a [inscrição e a entrada do telefone com políticas personalizadas no Azure ad B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – novembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Gerenciamento de identidade do Adobe](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blog](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Com conteúdo](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Jogo – TAC Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Para obter mais informações, consulte [automatizar o provisionamento de usuário para aplicativos SaaS com o Azure ad](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Visualização pública-Sign-In de email com ProxyAddresses agora implantável por meio de distribuição em etapas

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Os administradores de locatários agora podem usar a distribuição em etapas para implantar Sign-In de email com o ProxyAddresses em grupos específicos do Azure AD. Isso pode ajudar ao experimentar o recurso antes de implantá-lo em todo o locatário por meio da política de descoberta de realm inicial. As instruções para a implantação de Sign-In de email com o ProxyAddresses via distribuição em etapas estão na [documentação](../authentication/howto-authentication-use-email-signin.md)do.
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Versão prévia limitada-diagnóstico de entrada

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
Com a versão prévia inicial do diagnóstico de entrada, os administradores agora podem examinar as entradas do usuário. Os administradores podem receber detalhes contextuais, específicos e relevantes e orientações sobre o que aconteceu durante uma entrada e como corrigir problemas. O diagnóstico está disponível no nível do Azure AD e no diagnóstico condicional e na resolução de lâminas. Os cenários de diagnóstico abordados nesta versão são o acesso condicional, a autenticação multifator e a entrada bem-sucedida.

Para obter mais informações, consulte [o que é diagnóstico de entrada no Azure ad?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Propriedades de entrada desconhecidas aprimoradas

**Tipo:** recurso alterado  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

  As detecções de propriedades de entrada desconhecidas foram atualizadas. Os clientes podem notar mais detecções de propriedades de entrada desconhecidas de alto risco. Para obter mais informações, consulte [o que é risco?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>A atualização de visualização pública do agente de provisionamento de nuvem agora está disponível (versão: 1.1.281.0)

**Tipo:** recurso alterado  
**Categoria de serviço:** Provisionamento de nuvem do Azure AD  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O agente de provisionamento de nuvem foi lançado em visualização pública e agora está disponível por meio do Portal. Esta versão contém várias melhorias, incluindo suporte para GMSA para seus domínios, que fornece melhor segurança, ciclos de sincronização inicial aprimorados e suporte para grupos grandes. Confira o [histórico](../app-provisioning/provisioning-agent-release-version-history.md) de versão de lançamento para obter mais detalhes. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Ponto de extremidade da API da chave de recuperação do BitLocker agora em/informationProtection

**Tipo:** recurso alterado  
**Categoria de serviço:** Gerenciamento de acesso do dispositivo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo
 
Anteriormente, você poderia recuperar as chaves do BitLocker por meio do ponto de extremidade/BitLocker. Eventualmente, vamos substituir esse ponto de extremidade, e os clientes devem começar a consumir a API que agora está em/informationProtection. 

Consulte [API de recuperação do BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) para obter atualizações na documentação para refletir essas alterações.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Disponibilidade geral do suporte de proxy de aplicativo para Serviços de Área de Trabalho Remota cliente Web HTML5

**Tipo:** recurso alterado  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso
 
O Azure Proxy de Aplicativo do AD support para o cliente Web do Serviços de Área de Trabalho Remota (RDS) agora está em disponibilidade geral. O cliente Web do RDS permite que os usuários acessem a infraestrutura do Área de Trabalho Remota por meio de qualquer navegador compatível com HTLM5, como o Microsoft Edge, Internet Explorer 11, Google Chrome e assim por diante. Os usuários podem interagir com aplicativos ou áreas de trabalho remotas, como fariam com um dispositivo local de qualquer lugar. 

Usando o Azure Proxy de Aplicativo do AD, você pode aumentar a segurança de sua implantação de RDS impondo políticas de acesso condicional e pré-autenticação para todos os tipos de aplicativos cliente avançados. Para saber mais, consulte [publicar área de trabalho remota com o Azure proxy de aplicativo do AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>O novo serviço de grupo dinâmico avançado está em visualização pública

**Tipo:** recurso alterado  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração
 
O serviço de grupo dinâmico avançado agora está em visualização pública. Novos clientes que criam grupos dinâmicos em seus locatários usarão o novo serviço. O tempo necessário para criar um grupo dinâmico será proporcional ao tamanho do grupo que está sendo criado, em vez do tamanho do locatário. Essa atualização melhorará o desempenho de locatários grandes significativamente quando os clientes criarem grupos menores. 

O novo serviço também visa concluir a adição e a remoção de membros devido a alterações de atributo em alguns minutos. Além disso, as falhas de processamento único não bloquearão o processamento do locatário. Para saber mais sobre a criação de grupos dinâmicos, consulte nossa [documentação](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Outubro de 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Agentes híbridos locais do Azure AD afetados pelas alterações de certificado do Azure TLS

**Tipo:** plano de alteração  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** plataforma

A Microsoft está atualizando os serviços do Azure para que eles usem certificados TLS de outro conjunto de ACs (autoridades de certificação) raiz. Esta atualização se deve aos certificados de autoridade de certificação atuais que não estão em conformidade com um dos requisitos de linha de base do fórum de CA/navegador. Essa alteração afetará os agentes híbridos do Azure AD instalados no local que têm ambientes protegidos com uma lista fixa de certificados raiz e precisarão ser atualizados para confiar nos novos emissores de certificado.

Essa alteração resultará em interrupção do serviço se você não tomar uma ação imediatamente. Esses agentes incluem [conectores de proxy de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) para acesso remoto aos agentes de [autenticação de passagem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) locais, que permitem aos usuários entrar em aplicativos usando as mesmas senhas e agentes de visualização de provisionamento de [nuvem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que executam o AD para o Azure ad Sync. 

Se você tiver um ambiente com regras de firewall definidas para permitir chamadas de saída somente para download de CRL (lista de certificados revogados) específico, será necessário permitir as seguintes URLs de CRL e OCSP. Para obter detalhes completos sobre a alteração e as URLs de CRL e OCSP para habilitar o acesso, consulte  [alterações de certificado TLS do Azure](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Os eventos de provisionamento serão removidos dos logs de auditoria e publicados somente para logs de provisionamento

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A atividade pelo [serviço de provisionamento](../app-provisioning/user-provisioning.md) scim é registrada nos logs de auditoria e nos logs de provisionamento. Isso inclui a atividade, como a criação de um usuário no ServiceNow, o grupo em GSuite ou a importação de uma função do AWS. No futuro, esses eventos só serão publicados nos logs de provisionamento. Essa alteração está sendo implementada para evitar eventos duplicados em logs e custos adicionais incorridos por clientes que consomem os logs no log Analytics. 

Forneceremos uma atualização quando uma data for concluída. Essa reprovação não está planejada para o ano civil 2020. 

> [!NOTE]
> Isso não afeta nenhum evento nos logs de auditoria fora dos eventos de sincronização emitidos pelo serviço de provisionamento. Eventos como a criação de um aplicativo, política de acesso condicional, um usuário no diretório, etc., continuarão a ser emitidos nos logs de auditoria. [Saiba mais](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Agentes híbridos locais do Azure AD afetados pelas alterações de certificado de TLS (segurança de camada de transporte) do Azure

**Tipo:** plano de alteração  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** plataforma
 
A Microsoft está atualizando os serviços do Azure para que eles usem certificados TLS de outro conjunto de ACs (autoridades de certificação) raiz. Haverá uma atualização devido aos certificados de autoridade de certificação atuais não após um dos requisitos de linha de base do fórum de autoridade de certificação/navegador. Essa alteração afetará os agentes híbridos do Azure AD instalados no local que têm ambientes protegidos com uma lista fixa de certificados raiz. Esses agentes precisarão ser atualizados para confiar nos novos emissores de certificado.

Essa alteração resultará em interrupção do serviço se você não tomar uma ação imediatamente. Esses agentes incluem: 
- [Conectores de proxy de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) para acesso remoto ao local 
- Os agentes de [autenticação de passagem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que permitem que os usuários entrem em aplicativos usando as mesmas senhas
- Agentes de [visualização de provisionamento de nuvem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que fazem o AD para o Azure ad Sync. 

Se você tiver um ambiente com regras de firewall definidas para permitir chamadas de saída somente para download de CRL (lista de certificados revogados) específico, será necessário permitir URLs de CRL e OCSP. Para obter detalhes completos sobre a alteração e as URLs de CRL e OCSP para habilitar o acesso, consulte  [alterações de certificado TLS do Azure](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 e substituição 3DES no US Gov Cloud

**Tipo:** plano de alteração  
**Categoria de serviço:** Todos os aplicativos do Azure AD  
**Funcionalidade do produto:** Padrões
 
O Azure Active Directory substituirá os seguintes protocolos até 31 de março de 2021:
- TLS 1.0
- TLS 1.1
- pacote de codificação 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Todas as combinações de servidor cliente e navegador-servidor devem usar o TLS 1,2 e os pacotes de codificação modernos para manter uma conexão segura com o Azure Active Directory para o Azure, o Office 365 e os serviços de Microsoft 365.

Os ambientes afetados são:
- US Gov do Azure
- [Office 365 GCC alto & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Atribuir aplicativos a funções em AU e escopo de objeto

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
Esse recurso habilita a capacidade de atribuir um aplicativo (SPN) a uma função de administrador no escopo da unidade administrativa. Para saber mais, consulte [atribuir funções de escopo a uma unidade administrativa](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Agora você pode desabilitar e excluir usuários convidados quando eles têm acesso negado a um recurso

**Tipo:** novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade
 
Desabilitar e excluir é um controle avançado nas revisões de acesso do Azure AD para ajudar as organizações a gerenciarem melhor os convidados externos em grupos e aplicativos. Se os convidados forem negados em uma revisão de acesso, a **desabilitação e a exclusão** irão bloqueá-los automaticamente de entrar por 30 dias. Após 30 dias, eles serão removidos do locatário completamente.

Para obter mais informações sobre esse recurso, consulte [desabilitar e excluir identidades externas com revisões de acesso do Azure AD (versão prévia)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Criadores de revisão de acesso podem adicionar mensagens personalizadas em emails aos revisores

**Tipo:** novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade
 
Nas revisões de acesso do Azure AD, os administradores que criam revisões agora podem gravar uma mensagem personalizada nos revisores. Os revisores verão a mensagem no email que recebem, solicitando que eles concluam a revisão. Para saber mais sobre como usar esse recurso, consulte a etapa 14 da seção [criar uma ou mais revisões de acesso](../governance/create-access-review.md#create-one-or-more-access-reviews) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – outubro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [Serviços de Diretório OpenText](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Sincronização Global de Identidade de Retransmissão](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Assistente de integração para Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
A experiência do assistente de integração (versão prévia) agora está disponível para Azure AD B2C Registros de aplicativo. Essa experiência ajuda a orientá-lo a configurar seu aplicativo para cenários comuns. Saiba mais sobre [as práticas recomendadas e recomendações da plataforma de identidade da Microsoft](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Exibir a ID do modelo de função na interface do usuário portal do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure  
**Funcionalidade do produto:** Controle de Acesso
 

Agora você pode exibir a ID do modelo de cada função do Azure AD na portal do Azure. No Azure AD, selecione a  **Descrição** da função selecionada. 

É recomendável que os clientes usem IDs de modelo de função no código e script do PowerShell, em vez de no nome de exibição. A ID do modelo de função tem suporte para uso para objetos [directoryRoles](/graph/api/resources/directoryrole) e [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Para obter mais informações sobre IDs de modelo de função, consulte [IDs de modelo de função](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Os conectores de API para Azure AD B2C fluxos de usuário de inscrição agora estão em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 

Os conectores de API agora estão disponíveis para uso com Azure Active Directory B2C. Os conectores de API permitem que você use APIs Web para personalizar os fluxos de usuário de inscrição e integrá-los a sistemas de nuvem externos. Você pode usar conectores de API para:

- Integrar com fluxos de trabalho de aprovação personalizados
- Validar dados de entrada do usuário
- Substituir atributos de usuário 
- Executar lógica de negócios personalizada 

 Visite o [usar conectores de API para personalizar e estender](../../active-directory-b2c/api-connectors-overview.md) a documentação de inscrição para saber mais.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Propriedade de estado para organizações conectadas no gerenciamento de direitos

**Tipo:** novo recurso  
**Categoria de serviço:** Funcionalidade do **produto** de gerenciamento de diretório: gerenciamento de direitos
 

 Todas as organizações conectadas agora terão uma propriedade adicional chamada "State". O estado controlará como a organização conectada será usada em políticas que se referem a "todas as organizações conectadas configuradas". O valor será "configurado" (o que significa que a organização está no escopo das políticas que usam a cláusula "All") ou "propostas" (o que significa que a organização não está no escopo).  

As organizações conectadas criadas manualmente terão uma configuração padrão de "configurado". Enquanto isso, os criados automaticamente (criados por meio de políticas que permitem que qualquer usuário da Internet solicitem acesso) serão padronizados como "propostos".  Todas as organizações conectadas criadas antes de setembro de 9 2020 serão definidas como "configuradas". Os administradores podem atualizar essa propriedade conforme necessário. [Saiba mais](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory identidades externas agora tem configurações de segurança avançadas Premium para B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de detecção de risco e acesso condicional com base em risco do Identity Protection agora estão disponíveis no [Azure ad B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Com esses recursos avançados de segurança, os clientes agora podem:
- Aproveite as informações inteligentes para avaliar o risco com os aplicativos B2C e as contas de usuário final. As detecções incluem viagem atípicos, endereços IP anônimos, endereços IP vinculados a malware e inteligência contra ameaças do Azure AD. O portal e os relatórios baseados em API também estão disponíveis.
- Resolva riscos automaticamente Configurando políticas de autenticação adaptáveis para usuários do B2C. Os desenvolvedores e administradores de aplicativos podem reduzir o risco em tempo real exigindo a autenticação multifator (MFA) ou bloquear o acesso dependendo do nível de risco do usuário detectado, com controles adicionais disponíveis com base no local, grupo e aplicativo.
- Integre com Azure AD B2C fluxos de usuário e políticas personalizadas. As condições podem ser disparadas de fluxos de usuários internos no Azure AD B2C ou podem ser incorporadas em políticas personalizadas do B2C. Assim como ocorre com outros aspectos do fluxo de usuário B2C, as mensagens de experiência do usuário final podem ser personalizadas. A personalização é de acordo com as alternativas de voz, marca e mitigação da organização.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – outubro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em outubro de 2020, adicionamos os seguintes 27 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee-Productivity superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [integração de nuvem Genesys para Azure](https://apps.mypurecloud.com/msteams-integration/), [portal de tecnologias de zona](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [lembrete de compromissos](https://app.appointmentreminder.co.nz/account/login), [Cloud. Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [saudação](https://app.greetly.com/), OrgVitality [SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web de carga aérea](../saas-apps/web-cargo-air-tutorial.md), fluxo de [tráfego virtual](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) de [circuito](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [](../saas-apps/samsara-tutorial.md) [Workstem](https://hrm.workstem.com/login), zipline de [varejo](../saas-apps/retail-zipline-tutorial.md) [, Hoxhunt,](../saas-apps/hoxhunt-tutorial.md) [MEVISIO, samsara](../saas-apps/mevisio-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), Gerenciador de

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Os logs de provisionamento agora podem ser transmitidos para o log Analytics

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 

Publique seus logs de provisionamento no log Analytics para:
- Armazenar logs de provisionamento por mais de 30 dias
- Definir notificações e alertas personalizados
- Criar painéis para visualizar os logs
- Executar consultas complexas para analisar os logs 

Para saber como usar o recurso, consulte [entender como o provisionamento se integra com os logs de Azure monitor](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Os logs de provisionamento agora podem ser exibidos por proprietários do aplicativo

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
Agora você pode permitir que os proprietários do aplicativo monitorem a atividade pelo serviço de provisionamento e solucionem problemas sem fornecer a eles uma função privilegiada ou torná-lo um afunilamento. [Saiba mais](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Renomeando 10 funções Azure Active Directory

**Tipo:** recurso alterado  
**Categoria de serviço:** Funções do Azure  
**Funcionalidade do produto:** Controle de Acesso
 
Algumas funções internas do Azure Active Directory (AD) têm nomes que diferem daqueles que aparecem no centro de administração Microsoft 365, no portal do AD do Azure e Microsoft Graph. Essa inconsistência pode causar problemas em processos automatizados. Com essa atualização, estamos renomeando 10 nomes de função para torná-los consistentes. A tabela a seguir tem os novos nomes de função:

![Tabela de novos nomes de função](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C suporte para o fluxo de código de autenticação para SPAs usando o MSAL JS 2. x

**Tipo:** recurso alterado  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
O MSAL.js versão 2. x agora inclui suporte para o fluxo de código de autorização para aplicativos Web de página única (SPAs). Azure AD B2C agora dará suporte ao uso do tipo de aplicativo SPA no portal do Azure e ao uso de MSAL.js fluxo de código de autorização com PKCE para aplicativos de página única. Isso permitirá que o SPAs use Azure AD B2C para manter o SSO com navegadores mais recentes e obedecer às recomendações de protocolo de autenticação mais recentes. Introdução ao " [registrar um aplicativo de página única (Spa)" no tutorial Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) .

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Atualizações para lembrar a MFA (autenticação multifator) em uma configuração de dispositivo confiável

**Tipo:** recurso alterado  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 

Recentemente, atualizamos o [Lembre-se da autenticação multifator (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) em um recurso de dispositivo confiável para estender a autenticação por até 365 dias. As licenças Premium do Azure Active Directory (Azure AD) também podem usar a [política de acesso condicional – frequência de entrada](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) que fornece mais flexibilidade para configurações de reautenticação.

Para obter a melhor experiência do usuário, é recomendável usar a frequência de entrada de acesso condicional para estender os tempos de vida de sessão em dispositivos confiáveis, locais ou sessões de baixo risco como uma alternativa para lembrar a MFA em uma configuração de dispositivo confiável. Para começar, examine nossas [diretrizes mais recentes sobre como otimizar a experiência de reautenticação](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>Setembro de 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – setembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Sincronização de Destinatário da Cofense](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Reconhecimento de segurança do Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Atualização de visualização pública de provisionamento de nuvem

**Tipo:** novo recurso  
**Categoria de serviço:** **Funcionalidade do produto** de provisionamento de nuvem do Azure AD: gerenciamento do ciclo de vida de identidade
 
Azure AD Connect a atualização de visualização pública de provisionamento de nuvem apresenta dois principais aprimoramentos desenvolvidos com base nos comentários dos clientes: 

- Experiência de mapeamento de atributos por meio de portal do Azure

    Com esse recurso, os administradores de ti podem mapear atributos de usuário, grupo ou contato do AD para o Azure AD usando vários tipos de mapeamento presentes hoje. O mapeamento de atributos é um recurso usado para padronizar os valores dos atributos que fluem de Active Directory para Azure Active Directory. É possível determinar se o valor do atributo deve ser mapeado diretamente como é do AD para o Azure AD ou usar expressões para transformar os valores de atributo ao provisionar os usuários. [Saiba mais](../cloud-provisioning/how-to-attribute-mapping.md)

- Provisionamento sob demanda ou experiência do usuário de teste

    Depois de configurar sua configuração, talvez você queira testar para ver se a transformação do usuário está funcionando conforme o esperado antes de aplicá-la a todos os usuários no escopo. Com o provisionamento sob demanda, os administradores de ti podem inserir o DN (nome distinto) de um usuário do AD e ver se eles estão sendo sincronizados conforme o esperado. O provisionamento sob demanda fornece uma ótima maneira de garantir que os mapeamentos de atributo que você realizou anteriormente funcionem conforme o esperado. [Saiba mais](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Recuperação do BitLocker auditada no Azure AD – visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de acesso do dispositivo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo
 
Quando os administradores de ti ou os usuários finais lêem as chaves de recuperação do BitLocker às quais eles têm acesso, Azure Active Directory agora gera um log de auditoria que captura quem acessou a chave de recuperação. A mesma auditoria fornece detalhes do dispositivo ao qual a chave do BitLocker foi associada.

Os usuários finais podem [acessar suas chaves de recuperação por meio da minha conta](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Os administradores de ti podem acessar as chaves de recuperação por meio da [API de chave de recuperação do BitLocker na versão beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) ou por meio do portal do Azure AD. Para saber mais, consulte [Exibir ou copiar chaves do BitLocker no portal do Azure ad](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Função interna Administrador de dispositivos de equipes

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários com a função de [administrador de dispositivos de equipes](../roles/permissions-reference.md#teams-devices-administrator) podem gerenciar [dispositivos certificados por equipes](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) no centro de administração de equipes. 

Essa função permite que o usuário exiba todos os dispositivos em um único relance, com a capacidade de Pesquisar e filtrar dispositivos. O usuário também pode verificar os detalhes de cada dispositivo, incluindo a conta conectada e a marca e o modelo do dispositivo. O usuário pode alterar as configurações no dispositivo e atualizar as versões do software. Essa função não concede permissões para verificar a atividade de equipes e chamar a qualidade do dispositivo.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Recursos de consulta avançada para objetos de diretório

**Tipo:** novo recurso  
**Categoria de serviço:** MS Graph  
**Funcionalidade do produto:** experiência de desenvolvedor
 
Todos os novos recursos de consulta introduzidos para objetos de diretório em APIs do Azure AD agora estão disponíveis no ponto de extremidade v 1.0 e pronto para produção. Os desenvolvedores podem contar, Pesquisar, filtrar e classificar objetos de diretório e links relacionados usando os operadores OData padrão.

Para saber mais, confira a documentação [aqui](https://aka.ms/BlogPostMezzoGA)e você também pode enviar comentários com esta [breve pesquisa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Visualização pública: avaliação de acesso contínuo para locatários que configuraram políticas de acesso condicional

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** segurança e proteção da identidade
 
A avaliação de acesso contínuo (CAE) agora está disponível em visualização pública para locatários do Azure AD com políticas de acesso condicional. Com o CAE, eventos de segurança e políticas críticas são avaliados em tempo real. Isso inclui desativação de conta, redefinição de senha e alteração de local. Para saber mais, consulte [avaliação de acesso contínuo](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Visualização pública: solicitar que os usuários solicitem a um pacote de acesso outras perguntas para melhorar as decisões de aprovação

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Os administradores agora podem exigir que os usuários que solicitam um pacote de acesso respondam a perguntas adicionais além da justificativa de negócios no meu portal de acesso do gerenciamento de direitos do Azure AD. As respostas dos usuários serão mostradas aos aprovadores para ajudá-los a tomar uma decisão de aprovação de acesso mais precisa. Para saber mais, consulte [coletar informações adicionais do solicitante para aprovação (versão prévia)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Visualização pública: gerenciamento de usuários aprimorado

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de usuários  
**Funcionalidade do produto:** Gerenciamento de usuários
 

O portal do AD do Azure foi atualizado para facilitar a localização de usuários nas páginas todos os usuários e usuários excluídos. As alterações na visualização incluem: 
- Mais propriedades de usuário visíveis, incluindo ID de objeto, status de sincronização de diretório, tipo de criação e emissor de identidade.
- A pesquisa agora permite a pesquisa combinada de nomes, emails e IDs de objeto.
- Filtragem aprimorada por tipo de usuário (membro, convidado e nenhum), status de sincronização de diretório, tipo de criação, nome da empresa e nome de domínio.
- Novos recursos de classificação em propriedades como nome, nome principal do usuário e data de exclusão.
- Uma nova contagem total de usuários que é atualizada com quaisquer pesquisas ou filtros.

Para obter mais informações, consulte [aprimoramentos de gerenciamento de usuário (versão prévia) no Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Novo campo de observações para aplicativos empresariais

**Tipo:** novo recurso  
**Categoria de serviço:** Funcionalidade do **produto** de aplicativos empresariais: SSO

Você pode adicionar anotações de texto livre a aplicativos empresariais. Você pode adicionar qualquer informação relevante que ajudará você a gerentes de aplicativos em aplicativos empresariais. Para obter mais informações, consulte [início rápido: configurar propriedades para um aplicativo em seu locatário do Azure Active Directory (AD do Azure)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – setembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em setembro de 2020, adicionamos os seguintes 34 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[VMware horizonte – Unified Access Gateway](), [Pulse Secure PCs](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [sexo adequação](https://app.genderfitness.com/), [COEO portal](https://my.coeo.com/), [gramatical](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA arqueiro Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile armário](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [análise de Simfoni](https://simfonianalytics.com/accounts/microsoft/login/), [priva Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [FEXA](../saas-apps/fexa-tutorial.md), [assinatura protegida Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [assinatura protegida Enterprise Portal configuração do AAD](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft-protegido por F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui: https://aka.ms/AppsTutorial .

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nova função de delegação no gerenciamento de direitos do Azure AD: Gerenciador de atribuição de pacotes de acesso

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Uma nova função de Gerenciador de atribuição de pacote de acesso foi adicionada ao gerenciamento de direitos do Azure AD para fornecer permissões granulares para gerenciar atribuições. Agora você pode delegar tarefas a um usuário nessa função, que pode delegar o gerenciamento de atribuições de um pacote de acesso a um proprietário de negócios. No entanto, um Gerenciador de atribuição de pacote do Access não pode alterar as políticas de pacote de acesso ou outras propriedades que são definidas pelos administradores. 

Com essa nova função, você se beneficia dos privilégios mínimos necessários para delegar o gerenciamento de atribuições e manter o controle administrativo em todas as outras configurações de pacote de acesso. Para saber mais, consulte [funções de gerenciamento](../governance/entitlement-management-delegate.md#entitlement-management-roles)de direitos.
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Alterações no fluxo de integração de Privileged Identity Management

**Tipo:** recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Anteriormente, a integração ao Privileged Identity Management (PIM) exigia o consentimento do usuário e um fluxo de integração na folha do PIM que incluía o registro no Azure AD MFA. Com a recente integração da experiência do PIM na folha de funções e administradores do Azure AD, estamos removendo essa experiência. Qualquer locatário com licença P2 válida será integrado automaticamente ao PIM.

A integração ao PIM não tem nenhum efeito adverso direto no seu locatário. Você pode esperar as seguintes alterações:
- Opções de atribuição adicionais, como ativas versus qualificadas com hora de início e de término, quando você faz uma atribuição na folha de funções e administradores do PIM ou do Azure AD. 
- Mecanismos de escopo adicionais, como unidades administrativas e funções personalizadas, introduzidos diretamente na experiência de atribuição. 
- Se você for um administrador global ou administrador de função com privilégios, poderá começar a obter alguns emails adicionais como o resumo semanal do PIM. 
- Você também pode ver a entidade de serviço MS-PIM no log de auditoria relacionado à atribuição de função. Essa alteração esperada não deve afetar o fluxo de trabalho regular.

 Para obter mais informações, consulte [começar a usar Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Gerenciamento de direitos do Azure AD: o painel Selecionar de recursos de pacote do Access agora é mostrado por padrão nos recursos atualmente no catálogo selecionado

**Tipo:** recurso alterado  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 

No fluxo de criação do pacote do Access, na guia funções de recurso, o comportamento selecionar painel é alterado. Atualmente, o comportamento padrão é mostrar todos os recursos que pertencem ao usuário e os recursos adicionados ao catálogo selecionado. 

Essa experiência será alterada para exibir apenas os recursos atualmente adicionados ao catálogo por padrão, para que os usuários possam facilmente escolher recursos do catálogo. A atualização ajudará na descoberta dos recursos para adicionar aos pacotes do Access e reduzirá o risco de adicionar inadvertidamente recursos pertencentes ao usuário que não fazem parte do catálogo. Para saber mais, confira [criar um novo pacote de acesso no gerenciamento de direitos do Azure ad](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Agosto de 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Atualizações para os requisitos do firewall do Azure Servidor de Autenticação Multifator

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 
A partir de 1 de outubro de 2020, os requisitos de firewall do servidor do Azure MFA exigirão intervalos IP adicionais.

Se você tiver regras de firewall de saída em sua organização, atualize as regras para que seus servidores MFA possam se comunicar com todos os intervalos de IP necessários. Os intervalos de IP são documentados nos [requisitos do firewall do Azure servidor de autenticação multifator](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Alterações futuras na experiência do usuário na pontuação segura de identidade

**Tipo:** plano de alteração  
**Categoria de serviço:** Funcionalidade do **produto** de proteção de identidade: proteção de & de segurança de identidade

Estamos atualizando o portal de Pontuação segura de identidade para alinhar com as alterações introduzidas no [novo lançamento](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)da Pontuação de segurança da Microsoft. 

A versão de visualização com as alterações estará disponível no início de setembro. As alterações na versão de visualização incluem:
- "Pontuação segura de identidade" renomeado como "Pontuação segura para identidade" para o alinhamento de marca com a pontuação de segurança da Microsoft
- Pontos normalizados para a escala padrão e relatados em percentuais em vez de pontos

Nesta versão prévia, os clientes podem alternar entre a experiência existente e a nova experiência. Essa versão prévia durará até o final de novembro de 2020. Após a versão prévia, os clientes serão automaticamente direcionados para a nova experiência de UX.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Novas permissões de acesso de convidado restrito no Azure AD – visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Controle de acesso   
**Funcionalidade do produto:** Gerenciamento de usuários

Atualizamos as permissões de nível de diretório para usuários convidados. Essas permissões permitem que os administradores exijam restrições e controles adicionais no acesso de usuário convidado externo. Agora, os administradores podem adicionar restrições adicionais para acesso de convidados externos ao perfil de usuários e grupos e informações de associação. Com esse recurso de visualização pública, os clientes podem gerenciar o acesso de usuário externo em escala por meio da ofuscação de associações de grupo, incluindo a restrição de usuários convidados de ver associações dos grupos nos quais eles estão.

Para saber mais, consulte [permissões de acesso de convidado restrito](../enterprise-users/users-restrict-guest-permissions.md) e [permissões padrão de usuários](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Disponibilidade geral de consultas Delta para entidades de serviço

**Tipo:** novo recurso  
**Categoria de serviço:** MS Graph  
**Funcionalidade do produto:** experiência de desenvolvedor
 
Microsoft Graph consulta Delta agora dá suporte ao tipo de recurso em v 1.0:
- Entidade de Serviço

Agora, os clientes podem controlar as alterações feitas nesses recursos com eficiência e fornecer a melhor solução para sincronizar as alterações desses recursos com um armazenamento de dados local. Para saber como configurar esses recursos em uma consulta, consulte [usar a consulta Delta para controlar alterações nos dados de Microsoft Graph](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Disponibilidade geral de consultas Delta para oAuth2PermissionGrant

**Tipo:** novo recurso  
**Categoria de serviço:** MS Graph  
**Funcionalidade do produto:** experiência de desenvolvedor

Microsoft Graph consulta Delta agora dá suporte ao tipo de recurso em v 1.0:
- OAuth2PermissionGrant

Os clientes agora podem controlar as alterações nesses recursos com eficiência e fornecer a melhor solução para sincronizar as alterações nesses recursos com um armazenamento de dados local. Para saber como configurar esses recursos em uma consulta, consulte [usar a consulta Delta para controlar alterações nos dados de Microsoft Graph](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – agosto de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em agosto de 2020, adicionamos os seguintes 25 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [alma SIS](https://almau.getalma.com/), [conector Enlyft do Dynamics 365](http://enlyft.com/), [soluções de software de utilização de espaço Serraview](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniqs](https://web.uniq.app/), [visivelmente](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [comparações de Edmentum-cursos exatos caminho](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [zix conformidade e captura](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [, Genetec](https://www.clearance.network/)de trabalho, [ISAMs](../saas-apps/isams-tutorial.md) [, VeraSMART](../saas-apps/verasmart-tutorial.md) [, Amiko](https://amiko.web.rivero.app/), [Twingate,](https://auth.twingate.com/signup) [concessão de funil](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/), [Bpanda](https://goto.bpanda.com/login), [](https://www.wandera.com/) [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect), [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>As florestas de recursos agora estão disponíveis para o Azure AD DS 

**Tipo:** Nova **categoria de serviço** de recurso: Azure AD Domain Services   
**Capacidade do produto:** Serviços de Domínio do Azure AD
 
A capacidade das florestas de recursos no Azure AD Domain Services agora está disponível para o público em geral. Agora você pode habilitar a autorização sem a sincronização de hash de senha para usar Azure AD Domain Services, incluindo a autorização de cartão inteligente. Para saber mais, consulte [conjuntos de réplicas e recursos para Azure Active Directory Domain Services (versão prévia)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>O suporte à réplica regional para os domínios gerenciados do Azure AD DS agora está disponível

**Tipo:** novo recurso   
**Categoria de serviço:** Serviços de Domínio do Azure AD  
**Capacidade do produto:** Serviços de Domínio do Azure AD
 
Você pode expandir um domínio gerenciado para ter mais de um conjunto de réplicas por locatário do Azure AD. Os conjuntos de réplicas podem ser adicionados a qualquer rede virtual emparelhada em qualquer região do Azure que ofereça suporte a Azure AD Domain Services. Conjuntos de réplicas adicionais em diferentes regiões do Azure fornecem recuperação de desastre geográfica para aplicativos herdados se uma região do Azure fica offline. Para saber mais, consulte [conjuntos de réplicas e recursos para Azure Active Directory Domain Services (versão prévia)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilidade geral do meu Sign-Ins do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Experiências do usuário final
 
O meu Sign-Ins do Azure AD é um novo recurso que permite que os usuários empresariais examinem seu histórico de entrada para verificar se há atividades incomuns. Além disso, esse recurso permite que os usuários finais relatem "isso não foi" ou "isso me foi" em atividades suspeitas. Para saber mais sobre como usar esse recurso, confira [Exibir e pesquisar sua atividade de entrada recente na página meu Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>O provisionamento de usuário controlado pelo SAP SuccessFactors HR para o Azure AD já está disponível para o público em geral

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Agora você pode integrar o SAP SuccessFactors como a fonte de identidade autoritativa com o Azure AD e automatizar o ciclo de vida de identidade de ponta a ponta usando eventos de RH, como novas contratações e rescisão, para impulsionar o provisionamento e desprovisionamento de contas no Azure AD. 

Para saber mais sobre como configurar o provisionamento de entrada do SAP SuccessFactors para o Azure AD, consulte o tutorial [Configurar o SAP SuccessFactors para Active Directory provisionamento de usuário](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Suporte personalizado do Open ID Connect do MS API do Graph para Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Anteriormente, os provedores de conexão Open ID personalizados só podiam ser adicionados ou gerenciados por meio do portal do Azure. Agora, a Azure AD B2C clientes podem adicioná-los e gerenciá-los por meio da versão beta de APIs do Microsoft Graph também. Para saber como configurar esse recurso com APIs, consulte [tipo de recurso identityprovider](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Atribuir funções internas do Azure AD a grupos de nuvem

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso

Agora você pode atribuir funções internas do Azure AD a grupos de nuvem com esse novo recurso. Por exemplo, você pode atribuir a função de administrador do SharePoint a Contoso_SharePoint_Admins grupo. Você também pode usar o PIM para tornar o grupo um membro qualificado da função, em vez de conceder acesso à sua posição. Para saber como configurar esse recurso, consulte [usar grupos de nuvem para gerenciar atribuições de função no Azure Active Directory (versão prévia)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Função interna do líder de negócios do insights agora disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários na função de líder de negócios do insights podem acessar um conjunto de dashboards e ideias por meio do [aplicativo M365 insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Isso inclui acesso completo a todos os dashboards e a funcionalidade de exploração de dados e informações apresentadas. No entanto, os usuários nessa função não têm acesso às definições de configuração do produto, que é responsabilidade da função de administrador do insights. Para saber mais sobre essa função, consulte [permissões de função de administrador no Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Função interna do administrador do insights agora disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários na função Administrador do insights podem acessar o conjunto completo de recursos administrativos no [aplicativo M365 insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Um usuário nessa função pode ler informações de diretório, monitorar a integridade do serviço, tíquetes de suporte de arquivo e acessar os aspectos das configurações do administrador do insights. Para saber mais sobre essa função, consulte [permissões de função de administrador no Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>O administrador de aplicativos e o administrador de aplicativos de nuvem podem gerenciar Propriedades de extensão de aplicativos

**Tipo:** recurso alterado  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Anteriormente, somente o administrador global poderia gerenciar a [propriedade de extensão](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Agora estamos habilitando esse recurso para o administrador de aplicativos e o administrador de aplicativos de nuvem.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>Hotfix 4.6.263.0 e conectores do MIM 2016 SP2 1.1.1301.0

**Tipo:** recurso alterado  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Um [pacote cumulativo de atualizações de hotfix (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) está disponível para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Este pacote cumulativo contém atualizações para o MIM CM, o Gerenciador de sincronização do MIM e os componentes do PAM. Além disso, os conectores genéricos do MIM criam 1.1.1301.0 incluem atualizações para o conector do Graph.

---
 



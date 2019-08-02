---
title: O que há de novo? Notas sobre a versão – Azure Active Directory | Microsoft Docs
description: Conheça as novidades do Azure Active Directory, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: af215c80148010d526c951e7a5128d6e4622b1cd
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625561"
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

>Seja notificado sobre quando visitar novamente esta página para atualizações, copiando e colando nesta URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` em seu ![ícone de leitor de RSS feed](./media/whats-new/feed-icon-16x16.png) leitor de feed.

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente. Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo no Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2019"></a>Junho de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>RiskDetections nova API do Microsoft Graph (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que o novo riskDetections API para o Microsoft Graph está agora em visualização pública. Você pode usar essa nova API para exibir uma lista de usuários relacionadas a proteção de identidade e detecções de risco de entrada da sua organização. Você também pode usar essa API para consultar com mais eficiência suas detecções de risco, incluindo detalhes sobre o tipo de detecção, status, nível e muito mais.

Para obter mais informações, consulte o [documentação de referência de API de detecção de riscos](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativo do Azure AD - junho de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

De 2019 de junho, adicionamos que suportam a esses novos 22 aplicativos com federação na Galeria do aplicativo:

[Kit de ferramentas do Azure AD SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [cliente VPN do Azure](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [ Auxiliar do auxiliar](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes Benz no carro Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [ Console de infraestrutura de nuvem Oracle](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [autenticação SAML CyberArk](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx ](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise SO](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Gerenciador de acesso do Oracle para o varejo Oracle Merchandising, Gerenciador de acesso do Oracle para Oracle E-Business Suite, Oracle IDCS para E-Business Suite, Oracle IDCS para PeopleSoft, Oracle IDCS para JD Edwards

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos de SaaS com suporte

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento e relatório

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos recentemente integrado:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização usando o provisionamento automatizado de usuário, consulte [automatizar o provisionamento de usuário para aplicativos SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Exibir o progresso em tempo real do serviço de provisionamento do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades

Nós atualizamos o provisionamento do Azure AD experiência para incluir uma nova barra de progresso que mostra o quão longe você está no processo de provisionamento de usuário. Essa experiência atualizada fornece também informações sobre o número de usuários provisionados durante o ciclo atual, em, bem como a muitos usuários foram provisionados para data.

Para obter mais informações, consulte [verificar o status de provisionamento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Identidade visual agora aparece em Sair e telas de erro

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

Nós atualizamos o Azure AD para que sua identidade visual agora apareça em telas de erro e de saída, bem como a página de entrada. Você não precisa fazer nada para ativar esse recurso, simplesmente, o Azure AD usa os ativos que você já configurou na **identidade visual da empresa** área do portal do Azure.

Para obter mais informações sobre como configurar sua identidade visual da empresa, consulte [adicionar identidade visual para páginas do Azure Active Directory da sua organização](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>O servidor de autenticação multifator (MFA) do Azure não está mais disponível para novas implantações

**Tipo:** Preteridos  
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** Segurança de identidade e proteção

A partir de 1 de julho de 2019, Microsoft não oferecerá o servidor MFA para novas implantações. Novos clientes que desejam exigir a autenticação multifator em sua organização agora devem usar a autenticação de multifator do Azure baseado em nuvem. Clientes que ativaram o servidor de MFA antes de 1 de julho não verá nenhuma alteração. Você ainda poderá baixar a versão mais recente, obtenha as atualizações futuras e gerar credenciais de ativação.

Para obter mais informações, consulte [Introdução ao servidor Azure multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Para obter mais informações sobre autenticação de multifator do Azure baseado em nuvem, consulte [Planejando uma implantação de autenticação multifator do Azure baseado em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maio de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Alteração de serviço: Suporte futuro para somente os protocolos TLS 1.2 no serviço de Proxy de aplicativo

**Tipo:** Plano para alteração  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Para ajudar a fornecer o melhor da criptografia para nossos clientes, estamos nos limitando o acesso a somente os protocolos TLS 1.2 no serviço de Proxy de aplicativo. Essa alteração está sendo distribuída gradualmente para clientes que já somente estão usando protocolos TLS 1.2, portanto, você não deve ver todas as alterações.

Substituição do TLS 1.0 e TLS 1.1 ocorre em 31 de agosto de 2019, mas forneceremos adicionais de antecedência, para que você tenha tempo para se preparar para essa alteração. Para se preparar para essa alteração Verifique se suas combinações de cliente-servidor e o servidor e navegador, incluindo todos os clientes que os usuários usam para acessar a aplicativos publicados por meio do Proxy de aplicativo, são atualizados para usar o protocolo TLS 1.2 para manter a conexão ao aplicativo Serviço de proxy. Para obter mais informações, consulte [adicionar um aplicativo local para acesso remoto por meio do Proxy de aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Use o relatório de uso e insights para exibir seu aplicativo dados relacionados ao entrar

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento e relatório

Agora você pode usar o relatório de uso e insights, localizado na **aplicativos empresariais** área do portal do Azure, para obter uma exibição centrada no aplicativo de seus dados de entrada, incluindo informações sobre:

- Top usado aplicativos para sua organização

- Aplicativos com as entradas mais com falha

- Principais erros de entrada para cada aplicativo

Para obter mais informações sobre esse recurso, consulte [relatam de uso e os insights no portal do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizar o provisionamento de usuário para aplicativos de nuvem usando o Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento e relatório

Siga estes novos tutoriais para usar o serviço de provisionamento do AD do Azure para automatizar a criação, exclusão, atualização e das contas de usuário para os seguintes aplicativos baseados em nuvem:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Você também pode seguir este novo [Dropbox tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), que fornece informações sobre como provisionar objetos de grupo.

Para obter mais informações sobre como proteger melhor a sua organização por meio de provisionamento de conta de usuário automatizado, consulte [automatizar o provisionamento de usuário para aplicativos SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Pontuação de seguro de identidades agora está disponível no Azure AD (disponibilidade geral)

**Tipo:** New recurso  
**Categoria de serviço:** N/D  
**Funcionalidade do produto:** Segurança de identidade e proteção

Agora você pode monitorar e melhorar sua identidade postura de segurança usando a identidade proteger o recurso de pontuação no Azure AD. A identidade de segurança pontuação recurso usa um único painel para ajudar você a:

- Medir objetivamente a sua postura de segurança de identidade, com base em uma pontuação entre 1 e 223.

- Planejar sua identidade para aprimoramentos de segurança

- Examine o sucesso das suas melhorias de segurança

Para obter mais informações sobre o recurso de pontuação de segurança de identidade, consulte [qual é a pontuação segura de identidade no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nova experiência de registros de aplicativo agora está disponível (disponibilidade geral)

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Experiência do desenvolvedor

O novo [registros de aplicativo](https://aka.ms/appregistrations) experiência está agora em disponibilidade geral. Esta nova experiência inclui todos os recursos principais que você já conhece do portal do Azure e o portal de registro de aplicativo e aprimora a eles por meio de:

- **Melhor gerenciamento de aplicativo.** Em vez de ver seus aplicativos em portais diferentes, agora você pode ver todos os seus aplicativos em um único local.

- **Registro de aplicativo simplificado.** Com a experiência de navegação aprimorada para a experiência de seleção de permissão reformulada, agora é mais fácil de registrar e gerenciar seus aplicativos.

- **Informações mais detalhadas.** Você pode encontrar mais detalhes sobre seu aplicativo, incluindo guias de início rápido e muito mais.

Para obter mais informações, consulte [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) e o [experiência de registros de aplicativo agora está disponível!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) Comunicado do blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Os novos recursos disponíveis na API de usuários arriscados para proteção de identidade

**Tipo:** New recurso  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que agora você pode usar a API dos usuários arriscados para recuperar o histórico de risco dos usuários, ignorar usuários arriscados e para confirmar que os usuários como comprometida. Essa alteração ajuda a atualizar o status de risco dos seus usuários com mais eficiência e entender seu histórico de risco.

Para obter mais informações, consulte o [documentação de referência de API de usuários arriscados](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativo do Azure AD - maio de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Maio de 2019, adicionamos que suportam a esses novos 21 aplicativos com federação na Galeria do aplicativo:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real vincula](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [entrada simples](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [entre em contato com vendas Marketo](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API nuvem](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Controle](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Experiências de criação de grupos aprimorada e gerenciamento no portal do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Fizemos aprimoramentos para as experiências de grupos no portal do Azure AD. Esses aprimoramentos permitem aos administradores a gerenciar melhor os grupos de listas, listas de membros e para fornecer opções adicionais de criação.

As melhorias incluem:

- Filtragem básica por tipo de associação e o tipo de grupo.

- Adição de novas colunas, como o código-fonte e endereço de Email.

- Capacidade de selecionados vários grupos, membros e proprietário da lista para facilitar a exclusão.

- Capacidade de escolher um endereço de email e adicionar proprietários durante a criação de grupo.

Para obter mais informações, consulte [criar um grupo básico e adicionar membros usando o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do Azure AD (disponibilidade geral)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Os administradores agora podem configurar uma política de nomenclatura de grupos do Office 365, usando o portal do Azure AD. Essa alteração ajuda a impor as convenções de nomenclatura consistentes para os grupos do Office 365 criados ou editados pelos usuários em sua organização.

Você pode configurar a política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Defina os prefixos ou sufixos que são adicionados automaticamente ao nome de um grupo.

- Carrega um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidos em nomes de grupo (por exemplo, "CEO, folha de pagamento, RH").

Para obter mais informações, consulte [impor uma política de nomenclatura de grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Pontos de extremidade de API do Microsoft Graph agora estão disponíveis para os logs de atividade do Azure AD (disponibilidade geral)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Estamos felizes em anunciar a disponibilidade geral do suporte de pontos de extremidade de API do Microsoft Graph para atividades do Azure AD logs. Com esta versão, agora você pode usar a versão 1.0 do Azure AD de auditoria registra em log, bem como os logs de entrada APIs.

Para obter mais informações, consulte [visão geral da API do log de auditoria do Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Os administradores agora podem usar o acesso condicional para o processo de registro combinado (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção  

Os administradores agora podem criar políticas de acesso condicional para uso pela página de registro combinado. Isso inclui a aplicação de políticas para permitir o registro se:

- Os usuários estão em uma rede confiável.

- Os usuários são um risco de entrada baixo.

- Os usuários estão em um dispositivo gerenciado.

- Os usuários concordam em termos da organização de uso (TOU).

Para obter mais informações sobre a redefinição de senha e de acesso condicional, você pode ver os [combinados de acesso condicional para o Azure AD MFA e postagem de blog de experiência de registro de redefinição de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Para obter mais informações sobre as políticas de acesso condicional para o processo de registro combinado, consulte [políticas de acesso condicional para o registro combinado](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Para obter mais informações sobre os termos do AD do Azure de uso de recurso, consulte [termos do Azure Active Directory do uso de recurso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Nova detecção de inteligência de ameaças do Azure AD agora está disponível no Azure AD Identity Protection atualizado

**Tipo:** New recurso  
**Categoria de serviço:** Azure AD Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

Detecção de inteligência de ameaças do Azure AD agora está disponível no Azure AD Identity Protection atualizado. Essa nova funcionalidade ajuda a indicar a atividade do usuário que é incomum para um usuário específico ou que é consistente com os padrões de ataques conhecidos com base na inteligência de ameaças internas e externas da Microsoft.

Para obter mais informações sobre a versão atualizada do Azure AD Identity Protection, consulte o [quatro aprimoramentos principais do Azure AD Identity Protection agora estão em visualização pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) blog e o [o que é o Azure Active Directory Proteção de identidade (atualizada)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artigo. Para obter mais informações sobre a detecção de inteligência de ameaças do Azure AD, consulte o [eventos de risco do Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) artigo.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Gerenciamento de direitos do AD do Azure agora está disponível (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Governança de Identidade  
**Funcionalidade do produto:** Governança de Identidade

Gerenciamento de direitos do AD do Azure, agora em visualização pública, ajuda os clientes para delegar o gerenciamento de pacotes de acesso, que define como os funcionários e parceiros de negócios podem solicitar acesso, quem deve aprovar e quanto tempo eles têm acesso. Pacotes de acesso podem gerenciar a associação no Azure AD e Office 365 grupos, as atribuições de função em aplicativos empresariais e atribuições de função para sites do SharePoint Online. Leia mais sobre o gerenciamento de direitos na [visão geral do gerenciamento de direitos do AD do Azure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Para saber mais sobre a variedade de recursos de governança de identidade do AD do Azure, incluindo o Privileged Identity Management, as revisões de acesso e termos de uso, consulte [o que é controle de identidade do Azure AD?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do Azure AD (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Os administradores agora podem configurar uma política de nomenclatura de grupos do Office 365, usando o portal do Azure AD. Essa alteração ajuda a impor as convenções de nomenclatura consistentes para os grupos do Office 365 criados ou editados pelos usuários em sua organização.

Você pode configurar a política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Defina os prefixos ou sufixos que são adicionados automaticamente ao nome de um grupo.

- Carrega um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidos em nomes de grupo (por exemplo, "CEO, folha de pagamento, RH").

Para obter mais informações, consulte [impor uma política de nomenclatura de grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Logs de atividade do AD do Azure agora estão disponíveis no Azure Monitor (disponibilidade geral)

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Para ajudar a resolver seus comentários sobre visualizações com os logs de atividades do Azure AD, estamos apresentando um novo recurso de Insights do Log Analytics. Esse recurso ajuda você a obter insights sobre seus recursos do AD do Azure usando nossos modelos interativos, chamados de pastas de trabalho. Eles pré-criados em pastas de trabalho podem fornecer detalhes para aplicativos ou usuários e incluem:

- **Entradas.** Fornece detalhes para aplicativos e usuários, incluindo o local de entrada, o sistema operacional em uso ou cliente de navegador e versão e o número de êxito ou falhas entradas.

- **Autenticação e acesso condicional.** Fornece detalhes para aplicativos e usuários usando a autenticação herdada, incluindo o uso da autenticação multifator disparado pelas políticas de acesso condicional, aplicativos usando políticas de acesso condicional, e assim por diante.

- **Análise de falha no logon.** Ajuda você a determinar se os erros de entrada estão ocorrendo devido a uma ação do usuário, problemas de política ou sua infraestrutura.

- **Relatórios personalizados.** Você pode criar um novo ou editar pastas de trabalho existentes para ajudar a personalizar o recurso de Insights para sua organização.

Para obter mais informações, consulte [como usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativo do Azure AD - abril de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Em abril de 2019, adicionamos que suportam a esses novos 21 aplicativos com federação na Galeria do aplicativo:

[No SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [NetScaler da Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Conectar-se](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel conectar](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [nuvem Alibaba (SSO baseado em função)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent patrimônio gerenciamento](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Gerenciador de certificados Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ Enterprise SurveyMonkey](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Revisões de acesso nova opção de frequência e seleção múltipla de função

**Tipo:** New recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de Identidade

Revisões de acesso de novas atualizações no Azure AD permitem que você:

- Alterar a frequência de seu acesso revisões para **ou semestralmente**, além das opções existentes anteriormente do semanalmente, mensalmente, trimestralmente e anualmente.

- Selecione vários Azure AD e examine as funções de recurso do Azure durante a criação de um único acesso. Nessa situação, todas as funções são configuradas com as mesmas configurações e todos os revisores serão notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso, consulte [criar uma revisão de acesso de grupos ou revisões de acesso de aplicativos no Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect sistemas de alerta de email estão fazendo a transição, envio de novas informações de remetente de email para alguns clientes

**Tipo:** Recurso alterado  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** Plataforma

O Azure AD Connect está no processo de transição de nosso sistema de alerta de email, potencialmente mostrando alguns clientes de um novo remetente do email. Para resolver isso, você deve adicionar `azure-noreply@microsoft.com` à lista de permissões de sua organização ou você não poderá continuar a receber alertas importantes do seu Office 365, Azure ou seus serviços de sincronização.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Alterações de sufixo UPN agora são bem-sucedida entre domínios federados no Azure AD Connect

**Tipo:** Correção  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** Plataforma

Você pode agora alterar com êxito sufixo UPN do usuário de um domínio federado para outro domínio federado no Azure AD Connect. Essa correção significa que você não deve receber a mensagem de erro de FederatedDomainChangeError durante o ciclo de sincronização ou receber uma notificação de email informando, "não é possível atualizar este objeto no Active Directory do Azure, porque o atributo [ FederatedUser.UserPrincipalName], não é válido. Atualize o valor nos serviços de diretório local".

Para obter mais informações, consulte [sobre como solucionar erros durante a sincronização](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Aumentar a segurança usando a política de acesso condicional com base em proteção do aplicativo no Azure AD (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Acesso condicional com base em proteção do aplicativo agora está disponível usando o **exigir proteção de aplicativo** política. Essa nova política ajuda a aumentar a segurança de sua organização, ajudando a evitar:

- Usuários obtenham acesso a aplicativos sem uma licença do Microsoft Intune.

- Usuários não é possível obter uma política de proteção de aplicativo do Microsoft Intune.

- Usuários obtenham acesso a aplicativos sem uma política de proteção de aplicativo configurado do Microsoft Intune.

Para obter mais informações, consulte [como exigir a política de proteção do aplicativo para acesso de aplicativo de nuvem com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para logon único do Azure AD e o acesso condicional no Microsoft Edge (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Nós aprimoramos o nosso suporte do Azure AD para o Microsoft Edge, inclusive fornecendo o novo suporte para logon único do Azure AD e o acesso condicional. Se você tiver usado anteriormente o Microsoft Intune Managed Browser, agora você pode usar Microsoft Edge em vez disso.

Para obter mais informações sobre como configurar e gerenciar seus dispositivos e aplicativos usando o acesso condicional, consulte [exigem dispositivos para acesso de aplicativo de nuvem com acesso condicional gerenciados](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [exigir aplicativos de cliente para a nuvem aprovados acesso a aplicativos com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Para obter mais informações sobre como gerenciar o acesso usando o Microsoft Edge com políticas do Microsoft Intune, consulte [gerenciar o acesso à Internet usando um navegador protegido por política do Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Estrutura de experiência de identidade e a política personalizada do suporte no Azure Active Directory B2C agora está disponível (GA)

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode criar políticas personalizadas no B2C do Azure AD, incluindo as tarefas a seguir, que têm suporte em escala e, em nosso SLA do Azure:

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.

- Descrever percursos do usuário passo a passo como trocas entre provedores de declarações.

- Definir a ramificação condicional em percursos do usuário.

- Transforme e mapear declarações para uso em decisões em tempo real e comunicações.

- Use os serviços habilitados para API REST no seu percurso do usuário de autenticação personalizada. Por exemplo, com provedores de email, os CRMs e sistemas de autorização do proprietário.

- Federar com provedores de identidade que estão em conformidade com o protocolo OpenIDConnect. Por exemplo, com vários locatários do Azure AD, provedores de conta social ou provedores de verificação de dois fatores.

Para obter mais informações sobre como criar políticas personalizadas, consulte [notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leia [postagem de blog de Alex Simon, incluindo estudos de caso](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativo do Azure AD - março de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Março de 2019, adicionamos que suportam a esses novos 14 aplicativos com federação na Galeria do aplicativo:

[Delegado de Exchange Mobile ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Sistema de auditoria com base em explicação](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [questões de desempenho do Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [íris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizontes](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novo Zscaler e Atlassian na Galeria do Azure AD - março de 2019 de conectores de provisionamento

**Tipo:** New recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Integração de terceiros

Automatize a criação, atualização e exclusão de contas de usuário para os seguintes aplicativos:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler três](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Para obter mais informações sobre como proteger melhor a sua organização por meio de provisionamento de conta de usuário automatizado, consulte [automatizar o provisionamento de usuário para aplicativos SaaS com o Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerenciar seus grupos do Office 365 excluídos no portal do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Agora você pode exibir e gerenciar seus grupos do Office 365 excluídos no portal do Azure AD. Essa alteração ajuda você a ver quais grupos estão disponíveis para restaurar, juntamente com permitindo que você permanentemente, exclua todos os grupos que não são necessários pela sua organização.

Para obter mais informações, consulte [restauração expirou ou grupos excluídos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>O logon único agora está disponível para aplicativos protegidos pelo Azure AD SAML no local por meio do Proxy de aplicativo (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Agora você pode fornecer uma experiência de logon único (SSO) para aplicativos SAML autenticadas, juntamente com o acesso remoto a esses aplicativos por meio do Proxy de aplicativo no local. Para obter mais informações sobre como configurar o SSO do SAML com seus aplicativos locais, consulte [SAML SSO para aplicativos locais com o Proxy de aplicativo (visualização)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Aplicativos de cliente em loops de solicitação serão interrompidos para melhorar a confiabilidade e experiência do usuário

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

Aplicativos cliente incorretamente podem emitir centenas das mesmas solicitações de logon em um curto período de tempo. Essas solicitações, se elas são bem-sucedidas ou não, contribuem para uma experiência de usuário insatisfatória e cargas de trabalho maior para o IDP, aumenta a latência para todos os usuários e reduz a disponibilidade do IDP.

Essa atualização envia um `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` para aplicativos cliente que emitem solicitações duplicadas várias vezes em um curto período de tempo, além do escopo da operação normal. Aplicativos cliente que encontram esse problema devem mostrar um prompt interativo, exigir que o usuário fazer logon novamente. Para obter mais informações sobre essa alteração e sobre como corrigir seu aplicativo se ele encontrar esse erro, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nova experiência do usuário Logs de auditoria agora disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Criamos um novo AD do Azure **logs de auditoria** para ajudar a melhorar a legibilidade e a como você pesquisa por suas informações. Para ver o novo **logs de auditoria** página, selecione **logs de auditoria** no **atividade** seção do AD do Azure.

![Nova página de logs de auditoria, com informações de exemplo](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre os novos **logs de auditoria** página, consulte [auditoria de relatórios de atividade no portal do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e diretrizes para ajudar a evitar o bloqueio acidental do administrador de políticas de acesso condicional configuradas incorretamente

**Tipo:** Recurso alterado  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Para ajudar a impedir que os administradores bloqueiem a mesmos acidentalmente fora de seus próprios locatários por meio de políticas de acesso condicional configuradas incorretamente, criamos novos avisos e as diretrizes atualizadas no portal do Azure. Para obter mais informações sobre as novas diretrizes, consulte [quais são as dependências de serviço no acesso condicional do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Termos do usuário final melhorada de experiências de uso em dispositivos móveis

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Governança

Nós atualizamos nossos termos existentes das experiências de uso para ajudar a melhorar a como você examine e concorde com os termos de uso em um dispositivo móvel. Agora você pode ampliar e reduzir, voltar, baixar as informações e selecione hiperlinks. Para obter mais informações sobre os termos de uso atualizados, consulte [termos do Azure Active Directory do uso de recurso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Disponível de experiência de download de novos logs de atividades do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Agora você pode baixar grandes quantidades de logs de atividades diretamente do portal do Azure. Essa atualização permite que você:

- Baixe até 250.000 linhas.

- Seja notificado após a conclusão do download.

- Personalize o nome do arquivo.

- Determine o formato de saída, JSON ou CSV.

Para obter mais detalhes sobre esse recurso, consulte [guia de início rápido: Baixar um relatório de auditoria usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alterações da falha: Atualizações para a avaliação da condição pelo Exchange ActiveSync (EAS)

**Tipo:** Plano para alteração  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Controle de Acesso

Estamos em processo de atualização como o Exchange ActiveSync (EAS) avalia as condições a seguir:

- Local do usuário, com base no país, região ou endereço IP

- Risco de entrada

- Plataforma de dispositivos

Se você tiver usado anteriormente essas condições em suas políticas de acesso condicional, lembre-se de que o comportamento de condição pode ser alterado. Por exemplo, se você tiver usado a condição de localização do usuário em uma política, talvez a política agora está sendo ignorada com base na localização do seu usuário.

---

## <a name="february-2019"></a>fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configurável criptografia de token de SAML do Azure AD (visualização pública) 

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Agora você pode configurar qualquer aplicativo SAML com suporte para receber tokens SAML criptografados. Quando configurado e usado com um aplicativo, o AD do Azure criptografa as declarações do SAML emitidas usando uma chave pública obtida de um certificado armazenado no Azure AD.

Para obter mais informações sobre como configurar a criptografia de token SAML, consulte [criptografia do token de configurar o Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicativos que usam as revisões de acesso do AD do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança

Agora você pode incluir vários grupos ou revisão para associação de grupo ou a atribuição de aplicativo de acesso de aplicativos em um único Azure AD. Revisões de acesso com vários grupos ou aplicativos são configurados usando as mesmas configurações e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso usando as revisões de acesso do AD do Azure, consulte [criar uma revisão de acesso de grupos ou aplicativos em revisões de acesso do AD do Azure](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativo do Azure AD - fevereiro de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Fevereiro de 2019, adicionamos que suportam a esses novos 27 aplicativos com federação na Galeria do aplicativo:

[Passport Euromonitor](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [acidental](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Qmlativ Skyward](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Clique em uma permissão [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sísmicas ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Compartilhamento um sonho](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integração de nuvem](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [conhecimento em qualquer lugar LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [Campus de UO](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dados Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Portal Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp plataforma de produtividade](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registro MFA/SSPR combinado aprimorado

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha por autoatendimento  
**Funcionalidade do produto:** Autenticação de usuário

Em resposta aos comentários dos clientes, aprimoramos a experiência de visualização de registro MFA/SSPR combinada, ajudando seus usuários registrem mais rapidamente as informações de segurança para MFA e o SSPR. 

**Para ativar a experiência aprimorada para seus usuários hoje, siga estas etapas:**

1. Como um administrador global ou administrador de usuários, entre portal do Azure e acesse **Azure Active Directory > configurações do usuário > Gerenciar as configurações de recursos de visualização do painel de acesso**. 

2. No **recursos de usuários que podem usar a visualização para registrar e gerenciar informações de segurança – atualize** opção, escolha a opção Ativar os recursos para um **grupo selecionado de usuários** ou para **todos os usuários** .

Nas próximas semanas, podemos irá remover a capacidade de ativar a antiga combinado MFA/SSPR registro experiência de visualização para locatários que ainda não o tiver ativado.

**Para ver se o controle será removido do seu locatário, siga estas etapas:**

1. Como um administrador global ou administrador de usuários, entre portal do Azure e acesse **Azure Active Directory > configurações do usuário > Gerenciar as configurações de recursos de visualização do painel de acesso**.  

2. Se o **usuários que podem usar os recursos de visualização para registrar e gerenciar informações de segurança** opção for definida como **None**, a opção será removida do seu locatário.

Independentemente se você ativou anteriormente o registro MFA/SSPR combinado antigo experiência de visualização para usuários ou não, a experiência antiga será desativada em uma data futura. Por causa disso, sugerimos que você mova para a experiência nova e aprimorada assim que possível.

Para obter mais informações sobre a experiência aprimorada de registro, consulte o [combinados de aprimoramentos avançados para o Azure AD MFA e experiência de registro de redefinição de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência de gerenciamento de política atualizada para fluxos de usuário

**Tipo:** Recurso alterado  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Nós atualizamos o processo de criação e gerenciamento de política para fluxos de usuário (anteriormente conhecidos como internas políticas) mais fácil. Esta nova experiência agora é o padrão para todos os seus locatários do AD do Azure.

Você pode fornecer sugestões e comentários adicionais usando o Smiley ou rosto triste ícones na **enviar comentários** área na parte superior da tela do portal.

Para obter mais informações sobre a nova experiência de gerenciamento de política, consulte o [do Azure AD B2C agora tem muito mais novos recursos e personalização de JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolher versões de elemento de página específicos fornecidas pelo Azure AD B2C

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode escolher uma versão específica dos elementos de página fornecida pelo Azure AD B2C. Ao selecionar uma versão específica, você pode testar suas atualizações antes que elas aparecem em uma página e você pode obter um comportamento previsível. Além disso, você pode agora optar por impor versões da página específica para permitir que as personalizações de JavaScript. Para ativar esse recurso, vá para o **propriedades** página em seus fluxos de usuário.

Para obter mais informações sobre como escolher versões específicas dos elementos da página, consulte o [do Azure AD B2C agora tem muito mais novos recursos e personalização de JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de senha do usuário final configurável para B2C (GA)

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode configurar a complexidade da senha da sua organização para seus usuários finais, em vez de precisar usar seu nativo política de senha do Azure AD. Dos **propriedades** folha de seu usuário fluxos (anteriormente conhecidos como suas políticas internas), você pode escolher uma complexidade de senha **simples** ou **forte**, ou você pode criar uma **personalizado** conjunto de requisitos.

Para obter mais informações sobre a configuração de requisito de complexidade de senha, consulte [configurar requisitos de complexidade de senhas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos padrão para experiências de autenticação com marca personalizada

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Você pode usar os novos modelos padrão, localizados na **layouts de página** folha de seus fluxos de usuário (anteriormente conhecido como as políticas internas) criar um personalizado com a marca da experiência de autenticação para seus usuários.

Para obter mais informações sobre como usar os modelos, consulte [do Azure AD B2C agora tem muito mais novos recursos e personalização de JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração do Active Directory B2B usando a autenticação de senha de uso único (Versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Apresentamos a OTP (autenticação por senha de uso único) para usuários convidados de B2B que não podem ser autenticados por outros meios, tais como o Azure AD, uma MSA (conta Microsoft) ou uma federação do Google. Esse novo método de autenticação significa que os usuários convidados não precisarão criar uma nova conta Microsoft. Em vez disso, ao resgatar um convite ou acessar um recurso compartilhado, o usuário convidado poderá solicitar um código temporário que será enviado para um endereço de email. Usando esse código temporário, o usuário convidado pode continuar a conexão.

Para obter mais informações, consulte [Autenticação de senha de uso único por email (versão prévia)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e o blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (O Azure AD torna a colaboração e o compartilhamento perfeitos para qualquer usuário com qualquer conta).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas configurações de cookies do Proxy de Aplicativo do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Apresentamos três novas configurações de cookie, disponíveis para seus aplicativos publicados por meio do Proxy de Aplicativo:

- **Usar Cookie Somente HTTP.** Define o sinalizador **HTTPOnly** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração fornece benefícios de segurança adicionais, como ajudar a prevenir cópia ou modificação de cookies por meio de script do lado do cliente. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar um cookie seguro.** Define o sinalizador **Secure** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração proporciona benefícios de segurança adicionais ao garantir que os cookies só sejam transmitidos em canais seguros TLS, como HTTPS. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar cookie persistente.** Impede que cookies de acesso expirem quando o navegador da Web é fechado. Esses cookies duram todo o tempo de vida do token de acesso. No entanto, os cookies são redefinidos se o tempo de término é atingido ou se o usuário o exclui manualmente. Recomendamos que você mantenha a configuração padrão de **Não**, apenas ativando essa configuração para aplicativos mais antigos que não compartilham cookies entre processos.

Para obter mais informações sobre os novos cookies, confira [Configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD – janeiro de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em janeiro de 2019, adicionamos esses 35 novos aplicativos com suporte para Federação à galeria de aplicativos:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talento paleta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco guarda-chuva](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Administrador de acesso da Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lembrete de expiração](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR visualizador](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital feche](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ PICCO do serviço de nuvem](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Sistema de SSO GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES para Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 para o Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas plataforma](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novas melhorias do Azure AD Identity Protection (versão prévia pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que adicionamos os seguintes aprimoramentos à oferta de versão prévia pública do Azure AD Identity Protection, incluindo:

- Uma interface do usuário mais integrada e atualizada

- APIs adicionais

- Avaliação de risco aprimorada por meio de aprendizado de máquina

- Alinhamento de todo o produto entre usuários arriscados e entradas arriscadas

Para obter mais informações sobre os aprimoramentos, confira [O que é o Azure Active Directory Identity Protection (atualizado)?](https://aka.ms/IdentityProtectionDocs) Para saber mais e compartilhar suas ideias por meio dos prompts no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Novo recurso de Bloqueio de Aplicativo para o aplicativo Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativo Microsoft Authenticator  
**Funcionalidade do produto:** Segurança de identidade e proteção

Para manter suas senhas únicas, informações do aplicativo e configurações de aplicativo mais seguras, você pode ativar o recurso de Bloqueio de Aplicativo no aplicativo Microsoft Authenticator. Ativar o Bloqueio de Aplicativo significa que você será solicitado a autenticar-se usando seu PIN ou biometria sempre que abrir o aplicativo Microsoft Authenticator.

Para obter mais informações, confira as [Perguntas frequentes do aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Funcionalidades de exportação aprimoradas do Azure AD PIM (Privileged Identity Management)

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management

Administradores do PIM (Privileged Identity Management) agora podem exportar todas as atribuições de função ativas e qualificadas para um recurso específico, incluindo atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico.

Para obter mais informações, confira [Exibir histórico de atividade e auditoria para funções de recurso do Azure no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

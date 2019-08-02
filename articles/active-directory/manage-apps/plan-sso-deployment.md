---
title: Planejar uma implantação de logon único do Azure Active Directory
description: Guia para ajudá-lo a planejar, implantar e gerenciar o SSO em sua organização.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0602de13fefbf105e69ba42651216fb479c4c86
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477120"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planejar uma implantação de logon único

O SSO (logon único) significa acessar todos os aplicativos e recursos de que um usuário precisa ao entrar apenas uma vez usando uma única conta de usuário. Com o SSO, os usuários podem acessar todos os aplicativos necessários sem precisar autenticar uma segunda vez.

## <a name="benefits-of-sso"></a>Benefícios do SSO

O SSO (logon único) adiciona segurança e conveniência quando os usuários entram em aplicativos no Azure Active Directory (Azure AD). 

Muitas organizações contam com aplicativos SaaS (software como serviço), como Office 365, Box e Salesforce, para produtividade do usuário final. Historicamente, a equipe de ti precisava criar e atualizar individualmente as contas de usuário em cada aplicativo SaaS, e os usuários precisavam lembrar uma senha para cada uma delas.

O Azure Marketplace tem mais de 3000 aplicativos com conexões de SSO previamente integradas, facilitando sua integração em seu locatário.

## <a name="licensing"></a>Licenciamento

- **Licenciamento do Azure ad** -o SSO para aplicativos SaaS previamente integrados é gratuito. No entanto, o número de objetos em seu diretório e os recursos que você deseja implantar podem exigir licenças adicionais. Para obter uma lista completa dos requisitos de licença, consulte [preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenciamento de aplicativos** – você precisará das licenças apropriadas para seus aplicativos SaaS atenderem às suas necessidades de negócios. Trabalhe com o proprietário do aplicativo para determinar se os usuários atribuídos ao aplicativo têm as licenças apropriadas para suas funções no aplicativo. Se o Azure AD gerenciar o provisionamento automático com base em funções, as funções atribuídas no Azure AD deverão ser alinhadas com o número de licenças de Propriedade do aplicativo. O número inadequado de licenças pertencentes ao aplicativo pode levar a erros durante o provisionamento/atualização de um usuário.

## <a name="plan-your-sso-team"></a>Planejar sua equipe de SSO

- **Envolva os participantes certos** – quando os projetos de tecnologia falham, normalmente devido a expectativas incompatíveis em impacto, resultados e responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](https://aka.ms/deploymentplans) e se os participantes entendem suas funções.
- **Planejar comunicações** – a comunicação é crítica para o sucesso de qualquer novo serviço. Comunique-se proativamente com seus usuários sobre como sua experiência será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas. Examine as opções de [como os usuários finais acessarão seus aplicativos habilitados para SSO](end-user-experiences.md)e crie suas comunicações para corresponder à sua seleção. 

## <a name="plan-your-sso-protocol"></a>Planejar o protocolo SSO

Uma implementação de SSO baseada em protocolos de Federação melhora a segurança, a confiabilidade e as experiências do usuário final e é mais fácil de implementar. Muitos aplicativos são integrados ao Azure AD com [guias passo a passo disponíveis](../saas-apps/tutorial-list.md). Você pode encontrá-los em nosso [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Informações detalhadas sobre cada método SSO podem ser encontradas no artigo [logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md).

Há duas maneiras principais de permitir que seus usuários façam logon único em seus aplicativos:

- **Com logon único federado** O Azure AD autentica o usuário no aplicativo usando sua conta do Azure AD. Esse método tem suporte para aplicativos que dão suporte a protocolos como SAML 2,0, WS-Federation ou OpenID Connect, e é o modo mais avançado de logon único. É recomendável usar o SSO Federado com o Azure AD quando um aplicativo dá suporte a ele, em vez de SSO baseado em senha e ADFS.

- Com os usuários de **logon único baseados em senha** entram no aplicativo com um nome de usuário e senha na primeira vez, eles o acessam. Após o primeiro logon, o Azure AD fornece o nome de usuário e a senha ao aplicativo. O logon único baseado em senha permite o armazenamento e a reprodução segura de senhas do aplicativo usando uma extensão de navegador da Web ou aplicativo móvel. Essa opção aproveita o processo de entrada existente fornecido pelo aplicativo, permite que um administrador gerencie as senhas e não exige que o usuário saiba a senha.

### <a name="considerations-for-federation-based-sso"></a>Considerações para SSO baseado em Federação

- **Usando o OpenID Connect e o OAuth** – se o aplicativo ao qual você está se conectando oferecer suporte a ele, use o método OIDC/OAuth 2,0 para habilitar o SSO para esse aplicativo. Esse método requer menos configuração e permite uma experiência de usuário mais rica. Para obter mais informações, consulte [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [OpenID connect 1,0](../develop/v2-protocols-oidc.md)e [Azure Active Directory guia do desenvolvedor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Configurações de ponto de extremidade para SSO baseado em SAML** – se você usar SAML, os desenvolvedores precisarão de informações específicas antes de configurar o aplicativo. Para obter mais informações, consulte [Editar a configuração básica do SAML](configure-single-sign-on-non-gallery-applications.md).
- **Gerenciamento de certificados para SSO baseado em SAML** – quando você HABILITA o SSO Federado para seu aplicativo, o Azure ad cria um certificado que é, por padrão, válido por três anos. Você pode personalizar a data de validade para esse certificado, se necessário. Verifique se você tem processos em vigor para renovar certificados antes de sua expiração. Para saber mais, confira [AD do Azure gerenciando certificados](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Considerações para SSO baseado em senha

Usar o Azure AD para SSO baseado em senha requer a implantação de uma extensão de navegador que irá recuperar as credenciais com segurança e preencher os formulários de logon. Defina um mecanismo para implantar a extensão em escala com os [navegadores com suporte](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). As opções incluem:

- [Política de Grupo para o Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [System Center Configuration Manager (SCCM) para o Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Download e configuração controlados pelo usuário para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Para saber mais, consulte [como configurar o logon único com senha](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Capturando metadados de formulários de logon para aplicativos que não estão na Galeria

A Microsoft dá suporte à captura de metadados em um aplicativo Web para a compartimentação de senha (captura dos campos de nome de usuário e senha). Navegue até a URL de logon durante o processo de configuração do aplicativo para capturar os metadados de formulários. Peça ao proprietário do aplicativo a URL de logon exata. Essas informações são usadas durante o processo de logon, mapeando as credenciais do Azure AD para o aplicativo durante o logon.

Para saber mais, consulte [o que é o acesso a aplicativos e SSO com o Azure ad? – SSO baseado em senha](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indicações de que os metadados em formulários precisam ser recapturados

Quando os aplicativos alteram seu layout HTML, talvez seja necessário recapturar os metadados para ajustar as alterações. Sintomas comuns que indicam que o layout HTML tem alterações incluem:

- Os usuários relatando que clicar no aplicativo são "presos" na página de logon
- Os usuários relatando que o nome de usuário ou a senha não está preenchido

#### <a name="shared-accounts"></a>Contas compartilhadas

Da perspectiva de entrada, os aplicativos com contas compartilhadas não são diferentes de um aplicativo de galeria que usa o SSO de senha para usuários individuais. No entanto, há algumas etapas adicionais necessárias ao planejar e configurar um aplicativo destinado a usar contas compartilhadas:

1. Trabalhe com usuários empresariais de aplicativos para documentar o seguinte:
   1. Conjunto de usuários na organização que usarão o aplicativo
   1. Conjunto existente de credenciais no aplicativo associado ao conjunto de usuários 
1. Para cada combinação de conjunto de usuários e credenciais, crie um grupo de segurança na nuvem ou no local com base em seus requisitos.
1. Redefina as credenciais compartilhadas. Depois que o aplicativo for implantado no Azure AD, os indivíduos não precisarão da senha da conta compartilhada. Como o Azure AD armazenará a senha, considere defini-la como muito longa e complexa. 
1. Configure a substituição automática da senha se o aplicativo der suporte a ela. Dessa forma, nem mesmo o administrador que fez a configuração inicial saberá a senha da conta compartilhada. 

## <a name="plan-your-authentication-method"></a>Planejar o método de autenticação

Escolher o método de autenticação correto é uma primeira decisão fundamental na configuração de uma solução de identidade híbrida do Azure AD. Implemente o método de autenticação que é configurado usando o Azure AD Connect, que também provisiona usuários na nuvem.

Para escolher um método de autenticação, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação de sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo. Você deve escolher o que mais se aproximará do seu cenário específico. Para obter mais informações, consulte [escolher o método de autenticação correto para sua solução de identidade híbrida Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>Planeje sua segurança e governança 

Identidade é o novo pivô principal para a atenção de segurança e investimentos porque os perímetros de rede se tornaram cada vez mais porosos e menos efetivos com a explosão de dispositivos BYOD e aplicativos em nuvem. 

### <a name="plan-access-reviews"></a>Planejar revisões de acesso

As [revisões de acesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) permitem que as organizações gerenciem com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Você deve planejar revisar o acesso do usuário regularmente para garantir que apenas as pessoas certas tenham acesso contínuo.

Alguns dos principais tópicos a serem planejados durante a configuração das revisões de acesso incluem:

1. Identificar uma cadência para revisões de acesso que atendam às suas necessidades de negócios. Isso pode ser tão frequente quanto uma vez por semana, mensal, anual ou como um exercício sob demanda.

1. Crie grupos que representam os revisores dos relatórios de acesso do aplicativo. Você precisará garantir que os participantes mais familiarizados com o aplicativo e seus usuários de destino e casos de uso sejam participantes de suas revisões de acesso

1. A conclusão de uma revisão de acesso inclui a retirada de permissões de acesso do aplicativo a usuários que não precisam mais de acesso. Planeje o tratamento de possíveis solicitações de suporte de usuários negados. Um usuário excluído permanecerá excluído no Azure AD por 30 dias durante o qual eles poderão ser restaurados por um administrador, se necessário. Após 30 dias, esse usuário será excluído permanentemente. Usando o portal de Azure Active Directory, um administrador global pode excluir permanentemente um usuário excluído recentemente antes que esse período de tempo seja atingido.

### <a name="plan-auditing"></a>Planejar a auditoria

O Azure AD fornece [relatórios que contêm informações técnicas e de negócios](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Os relatórios de segurança e atividade estão disponíveis. Os relatórios de segurança mostram os usuários sinalizados para risco e entradas arriscadas. Os relatórios de atividades ajudam você a entender o comportamento dos usuários em sua organização, detalhando a atividade de entrada e fornecendo trilhas de auditoria de todos os logons. Você pode usar relatórios para gerenciar riscos, aumentar a produtividade e monitorar a conformidade.

| Tipo de relatório | Revisão de acesso | Relatórios de segurança | Relatório de entrada |
|-------------|---------------|------------------|----------------|
| Usar para examinar | Uso e permissões do aplicativo. | Contas potencialmente comprometidas | Quem está acessando os aplicativos |
| Ações potenciais | Acesso de auditoria; revogar permissões | Revogar o acesso; forçar redefinição de segurança | Revogar acesso |

O Azure AD retém a maioria dos dados de auditoria por 30 dias e disponibiliza os dados por meio do portal de administração do Azure ou por meio de uma API para você baixar em seus sistemas de análise.

### <a name="consider-using-microsoft-cloud-application-security"></a>Considere usar a segurança do aplicativo Microsoft Cloud

Microsoft Cloud App Security (MCAS) é uma solução de CASB (agente de segurança de acesso de nuvem). Ele oferece visibilidade sobre seus serviços e aplicativos de nuvem, fornece análises sofisticadas para identificar e combater ameaças cibernéticas e permite que você controle como seus dados viajam.

A implantação do MCAS permite que você:

- Use Cloud Discovery para mapear e identificar seu ambiente de nuvem e os aplicativos de nuvem que sua organização está usando.
- Aplicativos de sanções e não aprovados em sua nuvem
- Use conectores de aplicativos fáceis de implantar que aproveitam as APIs do provedor, para visibilidade e governança de aplicativos aos quais você se conecta
- Use a proteção Controle de Aplicativos de Acesso Condicional para obter visibilidade e controle em tempo real sobre o acesso e as atividades em seus aplicativos de nuvem
- Ajuda você a ter controle contínuo definindo e, em seguida, ajustar continuamente as políticas.

O controle de sessão MCAS (segurança de aplicativo) Microsoft Cloud está disponível para qualquer navegador em qualquer plataforma principal em qualquer sistema operacional. Aplicativos móveis e aplicativos de área de trabalho também podem ser bloqueados ou permitidos. Ao integrar nativamente com o Azure AD, todos os aplicativos configurados com SAML ou aplicativos Open ID Connect com logon único no Azure AD podem ter suporte, incluindo [vários aplicativos em destaque](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Para obter informações sobre MCAS, consulte a [visão geral de Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS é um serviço de assinatura baseado em usuário. Você pode examinar os detalhes de licenciamento na [folha de MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)de licenciamento do.

### <a name="use-conditional-access"></a>Usar acesso condicional

Com o acesso condicional, você pode automatizar decisões de controle de acesso com base em critérios para seus aplicativos de nuvem.

As políticas de Acesso Condicional são impostas após a conclusão da autenticação multifator. Portanto, o acesso condicional não se destina como uma defesa de primeira linha para cenários como ataques de DoS (negação de serviço), mas pode usar sinais desses eventos para determinar o acesso. Por exemplo, o nível de risco de entrada, o local da solicitação e assim por diante podem ser usados. Para obter mais informações sobre o acesso condicional, consulte [a visão geral](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e o [plano de implantação](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Requisitos técnicos do SSO do Azure

A seção a seguir detalha os requisitos para configurar seu aplicativo específico, incluindo os ambientes necessários, pontos de extremidade, mapeamento de declaração, atributos necessários, certificados e protocolos usados. Você precisará dessas informações para configurar o SSO no [portal do AD do Azure](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Detalhes do mecanismo de autenticação

Para todos os aplicativos SaaS previamente integrados, a Microsoft fornece um tutorial e você não precisará dessas informações. Se o aplicativo não estiver em nosso Marketplace/Galeria de aplicativos, talvez seja necessário coletar os seguintes dados:

- **Provedor de identidade atual que o aplicativo usa para SSO, se aplicável** , por exemplo: AD FS, PingFederate, Okta
- **Protocolos com suporte no aplicativo de destino** -por exemplo, SAML 2,0, OpenID Connect, OAuth, autenticação baseada em formulários, WS-enalimentado, WS-Trust
- **Protocolo que está sendo configurado com o Azure ad** -por exemplo, SAML 2,0 ou 1,1, OpenID Connect, OAuth, baseado em formulários, WS-alimentado

### <a name="attribute-requirements"></a>Requisitos de atributo

Há um conjunto pré-configurado de atributos e mapeamentos de atributo entre objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos. Planeje o mapeamento de atributos de usuário do Azure AD para seu aplicativo e [Personalize os mapeamentos de atributo padrão](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) de acordo com suas necessidades de negócios.

### <a name="certificate-requirements"></a>Requisitos de certificado

O certificado para o aplicativo deve estar atualizado, ou há um risco de os usuários não poderem acessar o aplicativo. A maioria dos certificados de aplicativo SaaS é válida por 36 meses. Você altera essa duração do certificado na folha do aplicativo. Certifique-se de documentar a expiração e saber como você gerenciará sua renovação de certificado. 

Há duas maneiras de gerenciar seus certificados. 

- **Substituição automática de certificado** -a Microsoft dá suporte à [substituição de chave de assinatura no Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Embora esse seja o nosso método preferido para gerenciar certificados, nem todos os ISVs oferecem suporte a esse cenário.

- **Atualização manual** – cada aplicativo tem seu próprio certificado que expira com base em como ele é definido. Antes de o certificado do aplicativo expirar, crie um novo certificado e envie-o para o ISV. Essas informações podem ser extraídas dos metadados de Federação. [Leia mais sobre metadados de Federação aqui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementar SSO

Use as fases a seguir para planejar e implantar sua solução em sua organização:

### <a name="user-configuration-for-sso"></a>Configuração do usuário para SSO

- **Identificar os usuários de teste**

   Entre em contato com o proprietário do aplicativo e peça para criar um mínimo de três usuários de teste no aplicativo. Verifique se as informações que você usará como o identificador primário está preenchida corretamente e se correspondem a um atributo que está disponível no Azure AD. Na maioria dos casos, isso será mapeado para o "NameID" para aplicativos baseados em SAML. Para tokens JWT, é o "preferred_username".
   
   Crie o usuário no Azure AD manualmente como um usuário baseado em nuvem ou Sincronize o usuário do local usando o mecanismo de sincronização Azure AD Connect. Verifique se as informações correspondem às declarações que estão sendo enviadas para o aplicativo.

- **Configurar SSO**

   Na [lista de aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), localize e abra o tutorial de SSO para seu aplicativo e siga as etapas do tutorial para configurar com êxito seu aplicativo SaaS.

   Se você não conseguir localizar seu aplicativo, consulte [documentação personalizada do aplicativo](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Isso explicará como adicionar um aplicativo que não está localizado na galeria do Azure AD.

   Opcionalmente, você pode usar declarações emitidas no token SAML para o aplicativo empresarial usando a [documentação de diretrizes da Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Verifique se isso é mapeado para o que você espera receber na resposta SAML para seu aplicativo. Se você encontrar problemas durante a configuração, use nossas diretrizes sobre [como depurar a integração de SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

A integração de aplicativos personalizados é um recurso de licenças Azure AD Premium P1 ou P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Fornecer comunicações de alteração de SSO para usuários finais

Implemente seu plano de comunicação. Certifique-se de que você está permitindo que os usuários finais saibam que uma alteração está chegando, quando chegou, o que fazer agora e como buscar assistência.

### <a name="verify-end-user-scenarios-for-sso"></a>Verificar cenários do usuário final para SSO

Você pode usar os seguintes casos de teste para conduzir testes em dispositivos corporativos e pessoais para garantir que suas configurações de SSO estejam funcionando conforme o esperado. Os cenários a seguir pressupõem que um usuário está navegando para uma URL de aplicativo e passando por um fluxo de autenticação iniciado pelo provedor de serviços (fluxo de autenticação iniciado pelo SP).

| Cenário | Resultado esperado no fluxo de autenticação iniciado pelo SP pelo usuário |
|----------|---------------------------------------------------|
| Faça logon no aplicativo com o IE enquanto estiver em corpnet. | A autenticação integrada do Windows (IWA) ocorre sem prompts adicionais. |
| Faça logon no aplicativo com IE enquanto estiver off corpnet com nova tentativa de logon. | Prompt baseado em formulários no servidor de AD FS. O usuário faz logon com êxito e prompts do navegador para MFA. |
| Faça logon no aplicativo com o IE enquanto estiver fora do corpnet com uma sessão atual e nunca executou a MFA. | O usuário não recebe o prompt do primeiro fator. O usuário recebe um prompt para MFA. |
| Faça logon no aplicativo com o IE enquanto estiver off corpnet com uma sessão atual e já executou a MFA nesta sessão. | O usuário não recebe o prompt do primeiro fator. O usuário não recebe MFA. SSOs do usuário no aplicativo. |
| Faça logon no aplicativo com Chrome/Firefox/Safari enquanto estiver fora do corpnet com uma sessão atual e já executou a MFA nesta sessão. | O usuário não recebe o prompt do primeiro fator. O usuário não recebe MFA. SSO do usuário no aplicativo. |
| Faça logon no aplicativo com Chrome/Firefox/Safari enquanto estiver fora do corpnet com a nova tentativa de logon. | Prompt baseado em formulários no servidor de AD FS. O usuário faz logon com êxito e prompts do navegador para MFA. |
| Faça logon no aplicativo com o Chrome/Firefox enquanto estiver na rede corporativa com uma sessão atual. | O usuário não recebe o prompt do primeiro fator. O usuário não recebe MFA. SSO do usuário no aplicativo. |
| Faça logon no aplicativo com aplicativo móvel de aplicativo com uma nova tentativa de logon. | Prompt baseado em formulários no servidor de AD FS. O usuário faz logon com êxito e solicita solicitações do cliente para MFA. |
| O usuário não autorizado tenta fazer logon no aplicativo com a URL de logon. | Prompt baseado em formulários no servidor de AD FS. O usuário não consegue fazer logon com o primeiro fator. |
| O usuário autorizado tenta fazer logon, mas insere uma senha incorreta. | O usuário navega para a URL do aplicativo e recebe um erro de nome de usuário/senha inválidos. |
| O usuário autorizado clica no link em um email e já está autenticado. | O usuário clica em URL e é conectado ao aplicativo sem prompts adicionais. |
| O usuário autorizado clica no link em um email e ainda não está autenticado. | O usuário clica em URL e é solicitado a autenticar com o primeiro fator. |
| O usuário autorizado faz logon no aplicativo com aplicativo móvel do aplicativo (iniciado pelo SP) com uma nova tentativa de logon. | Prompt baseado em formulários no servidor de AD FS. O usuário faz logon com êxito e solicita solicitações do cliente para MFA. |
| O usuário não autorizado tenta fazer logon no aplicativo com a URL de logon (iniciada pelo SP). | Prompt baseado em formulários no servidor de AD FS. O usuário não consegue fazer logon com o primeiro fator. |
| O usuário autorizado tenta fazer logon, mas insere uma senha incorreta.| O usuário navega para a URL do aplicativo e recebe um erro de nome de usuário/senha inválidos. |
| O usuário autorizado faz logoff e, em seguida, faz logon novamente. | Se a URL de saída estiver configurada, o usuário será desconectado de todos os serviços e solicitará a autenticação. |
| O usuário autorizado faz logoff e, em seguida, faz logon novamente. | Se a URL de saída não estiver configurada, o usuário será automaticamente conectado novamente usando o token existente da sessão de navegador do Azure AD existente. |
| O usuário autorizado clica no link em um email e já está autenticado. | O usuário clica em URL e é conectado ao aplicativo sem prompts adicionais. |
| O usuário autorizado clica no link em um email e ainda não está autenticado. | O usuário clica em URL e é solicitado a autenticar com o primeiro fator. |

## <a name="manage-sso"></a>Gerenciar SSO

Esta seção descreve os requisitos e as recomendações para gerenciar com êxito o SSO.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

Sempre use a função com o mínimo de permissões disponíveis para realizar a tarefa necessária no Azure Active Directory. A Microsoft recomenda [examinar as diferentes funções que estão disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher a correta para resolver suas necessidades para cada pessoa para este aplicativo. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Persona| Funções | Função do Azure AD (se necessário) |
|--------|-------|-----------------------------|
| Administrador de assistência técnica | Suporte da camada 1 | Nenhum |
| Administrador de identidade | Configurar e depurar quando os problemas afetam o Azure AD | Administrador global |
| Administrador do aplicativo | Atestado de usuário no aplicativo, configuração de usuários com permissões | Nenhum |
| Administradores de infraestrutura | Proprietário da substituição do certificado | Administrador global |
| Proprietário da empresa/participante | Atestado de usuário no aplicativo, configuração de usuários com permissões | Nenhum |

Recomendamos o uso de [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) para gerenciar suas funções para fornecer auditoria adicional, controle e revisão de acesso para usuários com permissões de diretório.

### <a name="sso-certificate-lifecycle-management"></a>Gerenciamento do ciclo de vida do certificado SSO

É importante identificar as funções corretas e as listas de distribuição de emails com o gerenciamento do ciclo de vida do certificado de autenticação entre o Azure AD e o aplicativo que está sendo configurado com o logon único. Aqui estão algumas das principais funções que recomendamos ter em vigor:

- Proprietário para atualizar as propriedades do usuário no aplicativo
- Proprietário na chamada para suporte de quebra/conserto de aplicativo
- Lista de distribuição de email com monitoramento rigoroso para notificações de alteração relacionadas a certificados

O tempo de vida máximo de um certificado é de três anos. É recomendável estabelecer um processo sobre como você tratará uma alteração de certificado entre o Azure AD e seu aplicativo. Isso pode ajudar a impedir ou minimizar uma interrupção devido à expiração de um certificado ou à substituição forçada do certificado.

### <a name="rollback-process"></a>Processo de reversão

Depois de concluir os testes com base nos seus casos de teste, é hora de passar para a produção com seu aplicativo. Migrar para produção significa que você implementará suas configurações planejadas e testadas em seu locatário de produção e as implantará para os usuários. No entanto, é importante planejar o que fazer caso sua implantação não vá conforme o planejado. Se a configuração de SSO falhar durante a implantação, você deve entender como mitigar qualquer interrupção e reduzir o impacto para os usuários.

A disponibilidade de métodos de autenticação dentro do aplicativo determinará sua melhor estratégia. Sempre verifique se você tem documentação detalhada para os proprietários de aplicativos em exatamente como retornar ao estado de configuração de logon original, caso sua implantação seja executada em problemas.

- **Se seu aplicativo der suporte a vários provedores de identidade**, por exemplo, LDAP e AD FS e ping, não exclua a configuração de SSO existente durante a distribuição. Em vez disso, desabilite-o durante a migração, caso você precise alternar novamente mais tarde. 

- **Se seu aplicativo não oferecer suporte a vários IDPs** , mas permitir que os usuários façam logon usando a autenticação baseada em formulários (nome de usuário/senha), verifique se os usuários podem retornar a essa abordagem caso a nova distribuição da configuração de SSO falhe.

### <a name="access-management"></a>gerenciamento de acesso

É recomendável escolher uma abordagem dimensionada ao gerenciar o acesso aos recursos. Abordagens comuns incluem a utilização de grupos locais por meio da sincronização por meio de Azure AD Connect, a [criação de grupos dinâmicos no Azure AD com base em atributos de usuário](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)ou a criação de [grupos](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) de autoatendimento no Azure ad gerenciado por um proprietário de recurso.

### <a name="monitor-security"></a>Monitorar a segurança

É recomendável configurar uma cadência regular na qual você examine os diferentes aspectos da segurança de aplicativo SaaS e execute quaisquer ações corretivas necessárias.

### <a name="troubleshooting"></a>Solução de problemas

Os links a seguir apresentam cenários de solução de problemas. Talvez você queira criar um guia específico para sua equipe de suporte que incorpore esses cenários e as etapas para corrigi-los.

#### <a name="consent-issues"></a>Problemas de consentimento

- [Erro de consentimento inesperado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Erro de consentimento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemas de credenciais

- [Problemas ao entrar por meio de um portal personalizado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemas para entrar no painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Erro na página de entrada do aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problema ao entrar em um aplicativo da Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemas de SSO para aplicativos listados na Galeria de Aplicativo Azure

- [Problema com o SSO de senha para aplicativos listados na Galeria de Aplicativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema com o SSO Federado para aplicativos listados na Galeria de Aplicativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemas de SSO para aplicativos não listados na Galeria de Aplicativo Azure

- [Problema com o SSO de senha para aplicativos não listados na Galeria de Aplicativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema com SSO Federado para aplicativos não listados na Galeria de Aplicativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Próximas etapas

[Depurar o SSO baseado em SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapeamento de declaração para aplicativos por meio do PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalizando declarações emitidas no token SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocolo SAML de logon único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocolo SAML de Logout Único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[B2B do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (para usuários externos, como parceiros e fornecedores)

[Acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Proteção de identidade do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Acesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Tutorial do SSO de aplicativo](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

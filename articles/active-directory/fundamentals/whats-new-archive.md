---
title: Arquivar as Novidades no Azure Active Directory? | Microsoft Docs
description: As notas da versão Novidades na seção Visão geral deste conjunto de conteúdo contêm seis meses de atividade. Após 6 meses, os itens são removidos do artigo principal e colocados neste artigo.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40dafe13dc0e8866c99e56879bed419808a0487c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560175"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arquivar as Novidades no Azure Active Directory?

O artigo principal [Notas sobre a versão das Novidades no Azure Active Directory](whats-new.md) contém as atualizações dos últimos seis meses, enquanto este artigo contém todas as informações mais antigas.

As notas sobre a versão das Novidades no Azure Active Directory fornecem informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

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

## <a name="july-2020"></a>Julho de 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Como administrador de ti, quero direcionar aplicativos cliente usando o acesso condicional

**Tipo:** plano de alteração   
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Com a versão GA da condição de aplicativos cliente no acesso condicional, novas políticas agora serão aplicadas por padrão a todos os aplicativos cliente. Isso inclui clientes de autenticação herdados. As políticas existentes permanecerão inalteradas, mas a opção *Configurar Sim/não* será removida das políticas existentes para ver facilmente quais aplicativos cliente são aplicados pela política. 

Ao criar uma nova política, certifique-se de excluir usuários e contas de serviço que ainda estão usando a autenticação herdada; caso contrário, eles serão bloqueados. [Saiba mais](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Correções de conformidade SCIM futuras

**Tipo:** plano de alteração  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento do Azure AD utiliza o padrão SCIM para integração com aplicativos. Nossa implementação do padrão SCIM está em evolução, e esperamos fazer alterações em nosso comportamento em relação a como executamos operações de PATCH, bem como definir a propriedade "active" em um recurso. [Saiba mais](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>A configuração do proprietário do grupo no portal de administração do Azure será alterada

**Tipo:** plano de alteração  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

As configurações de proprietário na página de configuração geral de grupos podem ser configuradas para restringir os privilégios de atribuição de proprietário a um grupo limitado de usuários no portal de administração do Azure e no painel de acesso. Em breve, você terá a capacidade de atribuir o privilégio de proprietário do grupo não apenas nesses portais de UX, mas também impor a política no back-end para fornecer comportamento consistente entre pontos de extremidade, como PowerShell e Microsoft Graph. 

Começaremos a desabilitar a configuração atual para os clientes que não estão usando-o e oferecerão uma opção para definir o escopo dos usuários para o privilégio de proprietário do grupo nos próximos meses. Para obter orientação sobre como atualizar configurações de grupo, consulte Editar suas informações de grupo usando [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>O serviço de registro Azure Active Directory está terminando o suporte para TLS 1,0 e 1,1

**Tipo:** plano de alteração  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** plataforma

Os servidores de segurança de camada de transporte (TLS) 1,2 e de atualização serão comunicados em breve com o serviço Registro de Dispositivos do Azure Active Directory. O suporte para TLS 1,0 e 1,1 para comunicação com Registro de Dispositivos do Azure AD serviço será desativado:
- Em 31 de agosto de 2020, em todas as nuvens soberanas (GCC alto, DoD, etc.)
- Em 30 de outubro de 2020, em todas as nuvens comerciais

[Saiba mais](../devices/reference-device-registration-tls-1-2.md) sobre o TLS 1,2 para o serviço de registro do Azure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Entradas do Windows Hello para empresas visíveis nos logs de entrada do Azure AD

**Tipo:** corrigido  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
O Windows Hello para empresas permite que os usuários finais entrem em computadores Windows com um gesto (como um PIN ou biométrica). Os administradores do Azure AD podem querer diferenciar as entradas do Windows Hello para empresas de outras entradas do Windows como parte da jornada de uma organização até a autenticação sem senha. 

Os administradores agora podem ver se uma autenticação do Windows usou o Windows Hello para empresas verificando a guia detalhes de autenticação de um evento de entrada do Windows na folha Sign-Ins do Azure AD no portal do Azure. As autenticações do Windows Hello para empresas incluirão "WindowsHelloForBusiness" no campo método de autenticação. Para obter mais informações sobre como interpretar logs de Sign-In, consulte a [documentação de logs de entrada](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Correções para melhorias de desempenho e comportamento de exclusão de grupos

**Tipo:** corrigido  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Anteriormente, quando um grupo foi alterado de "dentro do escopo" para "fora do escopo" e um administrador clicou em reiniciar antes de a alteração ser concluída, o objeto de grupo não estava sendo excluído. Agora o objeto de grupo será excluído do aplicativo de destino quando ele sair do escopo (desabilitado, excluído, não atribuído ou não aprovado no filtro de escopo). [Saiba mais](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Visualização pública: os administradores agora podem adicionar conteúdo personalizado no email aos revisores ao criar uma revisão de acesso

**Tipo:** novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade
 
Quando uma nova revisão de acesso é criada, o revisor recebe um email solicitando que ele conclua a revisão de acesso. Muitos de nossos clientes solicitaram a capacidade de adicionar conteúdo personalizado ao email, como informações de contato ou outro conteúdo adicional de suporte para guiar o revisor. 

Agora disponível na visualização pública, os administradores podem especificar o conteúdo personalizado no email enviado aos revisores adicionando conteúdo na seção "avançado" das revisões de acesso do Azure AD. Para obter orientação sobre a criação de revisões de acesso, consulte [criar uma revisão de acesso de grupos e aplicativos nas revisões de acesso do Azure ad](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Fluxo de código de autorização para aplicativos de página única disponível

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** experiência de desenvolvedor
 
Devido às restrições de cookie de terceiros do navegador moderno, como o Safari ITP, o SPAs terá que usar o fluxo do código de autorização em vez do fluxo implícito para manter o SSO, e MSAL.js v 2. x agora dará suporte ao fluxo do código de autorização. 

Há atualizações correspondentes ao portal do Azure para que você possa atualizar seu SPA para que ele seja do tipo "Spa" e use o fluxo do código de autenticação. Consulte [conectar usuários e obter um token de acesso em um spa JavaScript usando o fluxo do código de autenticação](../develop/quickstart-v2-javascript-auth-code.md) para obter mais diretrizes.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>O Azure Proxy de Aplicativo do AD agora dá suporte ao cliente Web Serviços de Área de Trabalho Remota

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

O Azure Proxy de Aplicativo do AD agora dá suporte ao cliente Web do Serviços de Área de Trabalho Remota (RDS). O cliente Web do RDS permite que os usuários acessem a infraestrutura do Área de Trabalho Remota por meio de qualquer navegador compatível com HTLM5, como o Microsoft Edge, Internet Explorer 11, Google Chrome etc. Os usuários podem interagir com aplicativos ou áreas de trabalho remotas, como fariam com um dispositivo local de qualquer lugar. Usando o Azure Proxy de Aplicativo do AD você pode aumentar a segurança de sua implantação de RDS impondo políticas de acesso condicional e pré-autenticação para todos os tipos de aplicativos cliente avançados. Para obter diretrizes, consulte [publicar área de trabalho remota com o proxy de aplicativo do AD do Azure](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Próxima geração Azure AD B2C fluxos de usuário na visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
A experiência simplificada de fluxo de usuário oferece paridade de recursos com recursos de visualização e é a página inicial para todos os novos recursos. Os usuários poderão habilitar novos recursos dentro do mesmo fluxo de usuário, reduzindo a necessidade de criar várias versões com cada nova versão de recurso. Por fim, o novo UX amigável do usuário simplifica a seleção e a criação de fluxos de usuário. Experimente agora [criando um fluxo de usuário](../../active-directory-b2c/tutorial-create-user-flows.md). 

Para obter mais informações sobre fluxos de usuários, consulte [versões de fluxo de usuário em Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – julho de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em julho de 2020, adicionamos os seguintes 55 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepcionista](https://msteams.peterconnects.com/), [ALOHACLOUD](https://appfusions.alohacloud.com/auth), [Control torre](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [moedas de construção Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GREMINDERS](https://app.greminders.com/o365-oauth), [titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [bloge](../saas-apps/blogin-tutorial.md), [INTSIGHTS](../saas-apps/intsights-tutorial.md), [Kpifire](../saas-apps/kpifire-tutorial.md) [, Textline,](../saas-apps/textline-tutorial.md) [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [comunidade Spark](../saas-apps/community-spark-tutorial.md), [chat](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [controle de nuvem C3M](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan data Hub logon único](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [egresso](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [direito de segurança cibernética Adi](https://right-hand.ai/), Fyde [Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [Lenses.Io](../saas-apps/lensesio-tutorial.md), [momentânea](../saas-apps/momenta-tutorial.md), [uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme bot](https://tellme365liteweb.azurewebsites.net/), [inspirar](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (sistema de gerenciamento escolar)](https://smartschoolz.com/login), [Zepto – timecontinueing inteligente](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack logon único](../saas-apps/browserstack-single-sign-on-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Exibir atribuições de função em todos os escopos e capacidade de baixá-las em um arquivo CSV

**Tipo:** recurso alterado  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Agora você pode exibir as atribuições de função em todos os escopos para uma função na guia "funções e administradores" no portal do Azure AD. Você também pode baixar essas atribuições de função para cada função em um arquivo CSV. Para obter orientação sobre como exibir e adicionar atribuições de função, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Reprovação do desenvolvimento de software de autenticação multifator do Azure (SDK do Azure MFA)

**Tipo:** preterido  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 
O desenvolvimento de software da autenticação multifator do Azure (SDK do Azure MFA) atingiu o fim da vida útil em 14 de novembro de 2018, como anunciado pela primeira vez em 2017 de novembro. A Microsoft estará desligando o serviço SDK em vigor em 30 de setembro de 2020. Qualquer chamada feita ao SDK falhará.

Se sua organização estiver usando o SDK do Azure MFA, você precisará migrar até 30 de setembro de 2020:
- SDK do Azure MFA para MIM: se você usar o SDK com o MIM, deverá migrar para o servidor do Azure MFA e ativar Privileged Access Management (PAM) seguindo estas [instruções](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- SDK do Azure MFA para aplicativos personalizados: considere integrar seu aplicativo ao Azure AD e use o acesso condicional para impor a MFA. Para começar, examine esta [página](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>Junho de 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condição de risco do usuário na política de acesso condicional

**Tipo:** plano de alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 

O suporte a riscos do usuário na política de acesso condicional do Azure AD permite que você crie várias políticas baseadas em risco do usuário. Diferentes níveis de risco do usuário mínimo podem ser necessários para diferentes usuários e aplicativos. Com base no risco do usuário, você pode criar políticas para bloquear o acesso, exigir autenticação multifator, alteração de senha segura ou redirecionar para Microsoft Cloud App Security para impor a política de sessão, como auditoria adicional.

A condição de risco do usuário requer o Azure AD Premium P2 porque ele usa a proteção de identidade do Azure, que é uma oferta P2. para obter mais informações sobre o acesso condicional, consulte a [documentação de acesso condicional do Azure ad](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>O SSO do SAML agora dá suporte a aplicativos que exigem que o SPNameQualifier seja definido quando solicitado

**Tipo:** corrigido  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
Alguns aplicativos SAML exigem que SPNameQualifier sejam retornados no assunto da asserção quando solicitado. Agora, o Azure AD responde corretamente quando um SPNameQualifier é solicitado na política NameID da solicitação. Isso também funciona para entrada iniciada pelo SP e a entrada iniciada pelo IdP será a seguinte.  Para saber mais sobre o protocolo SAML no Azure Active Directory, consulte [protocolo SAML de Sign-On único](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>A colaboração B2B do Azure AD dá suporte a convidar usuários do MSA e do Google nos locatários do Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 

Os locatários do Azure governamental usando os recursos de colaboração B2B agora podem convidar usuários que têm uma conta da Microsoft ou do Google. Para descobrir se o seu locatário pode usar esses recursos, siga as instruções em [como posso saber se a colaboração B2B está disponível no meu locatário do governo dos EUA do Azure?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>O objeto de usuário no MS Graph v1 agora inclui as propriedades externalUserState e externalUserStateChangedDateTime

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 

As propriedades externalUserState e externalUserStateChangedDateTime podem ser usadas para encontrar convidados B2B convidados que ainda não aceitaram seus convites, bem como a automação da compilação, como a exclusão de usuários que não aceitaram seus convites após um número de dias. Essas propriedades agora estão disponíveis no MS Graph v1. Para obter orientação sobre como usar essas propriedades, consulte [tipo de recurso de usuário](/graph/api/resources/user).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gerenciar sessões de autenticação no acesso condicional do Azure AD agora está disponível para o público geral

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Os recursos de gerenciamento de sessão de autenticação permitem que você configure com que frequência os usuários precisam fornecer credenciais de entrada e se precisam fornecer credenciais após fechar e reabrir os navegadores para oferecer mais segurança e flexibilidade em seu ambiente.
 
Além disso, o gerenciamento de sessão de autenticação usado para se aplicar somente à autenticação de primeiro fator no Azure AD ingressado, ingressado no Azure AD híbrido e dispositivos registrados no Azure AD. Agora, o gerenciamento de sessão de autenticação também será aplicado ao MFA. Para obter mais informações, consulte [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – junho de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em junho de 2020, adicionamos os 29 novos aplicativos a seguir em nossa galeria de aplicativos com suporte à Federação:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [Segment](../saas-apps/segment-tutorial.md), [ai auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.Io](../saas-apps/proto.io-tutorial.md), [gatekeeper](https://www.gatekeeperhq.com/), [planejador de Hub](../saas-apps/hub-planner-tutorial.md), [caixa de ferramentas de Ansira para o mercado](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation na nuvem](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [segurança física Kisi](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [zero](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [edX para negócios SAML 2,0 integração](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning plataforma](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [ID de campo](../saas-apps/field-id-tutorial.md), [currículos SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core-Helix serviço de autenticação](../saas-apps/perforce-helix-core-tutorial.md), minha [conformidade Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial . Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Conectores de API para identidade externa a inscrição de autoatendimento agora estão em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Conectores de API de identidades externas permitem que você aproveite as APIs da Web para integrar a inscrição de autoatendimento com sistemas de nuvem externos. Isso significa que agora você pode invocar APIs Web como etapas específicas em um fluxo de inscrição para disparar fluxos de trabalho personalizados baseados em nuvem. Por exemplo, você pode usar conectores de API para:

- Integre-se a fluxos de trabalho de aprovação personalizados.
- Executar a verificação de identidade
- Validar dados de entrada do usuário
- Substituir atributos de usuário
- Executar lógica de negócios personalizada

Para obter mais informações sobre todas as experiências possíveis com conectores de API, consulte [usar conectores de API para personalizar e estender a inscrição de autoatendimento](../external-identities/api-connectors-overview.md)ou [Personalizar identidades externas inscrição de autoatendimento com integrações de API Web](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Provisione sob demanda e obtenha usuários em seus aplicativos em segundos

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento do Azure AD opera em um momento cíclico. O serviço é executado a cada 40 minutos. O [recurso de provisionamento sob demanda](https://aka.ms/provisionondemand) permite que você escolha um usuário e provisione-os em segundos. Essa funcionalidade permite que você solucione problemas de provisionamento rapidamente, sem precisar fazer uma reinicialização para forçar o ciclo de provisionamento a iniciar novamente. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nova permissão para usar o gerenciamento de direitos do Azure AD no grafo

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Uma nova permissão delegada EntitlementManagement. Read. All agora está disponível para uso com a API de gerenciamento de direitos no Microsoft Graph beta. Para saber mais sobre as APIs disponíveis, consulte [trabalhando com a API de gerenciamento de direitos do Azure ad](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true).

---

### <a name="identity-protection-apis-available-in-v10"></a>APIs de proteção de identidade disponíveis em v 1.0

**Tipo:** novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade
 
As APIs de Microsoft Graph riskyUsers e riskDetections agora estão disponíveis para o público geral. Agora que estão disponíveis no ponto de extremidade v 1.0, convidamos você a usá-los na produção. Para obter mais informações, consulte o [Microsoft Graph docs](/graph/api/resources/identityprotectionroot).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Os rótulos de sensibilidade para aplicar políticas a grupos de Microsoft 365 agora estão disponíveis para o público geral

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração
 

Agora você pode criar rótulos de sensibilidade e usar as configurações de rótulo para aplicar políticas a grupos de Microsoft 365, incluindo privacidade (pública ou privada) e política de acesso de usuário externo. Você pode criar um rótulo com a política de privacidade como particular e a política de acesso de usuário externo para não permitir que o adicione usuários convidados. Quando um usuário aplica esse rótulo a um grupo, o grupo será particular e nenhum usuário convidado poderá ser adicionado ao grupo. 

Rótulos de sensibilidade são importantes para proteger seus dados críticos para os negócios e permitem gerenciar grupos em escala, de maneira segura e em conformidade. Para obter orientação sobre como usar rótulos de sensibilidade, consulte [atribuir rótulos de sensibilidade a grupos de Microsoft 365 no Azure Active Directory (versão prévia)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Atualizações para suporte para Microsoft Identity Manager para clientes do Azure AD Premium

**Tipo:** recurso alterado  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O suporte do Azure agora está disponível para os componentes de integração do Azure AD do Microsoft Identity Manager 2016, por meio do fim do suporte estendido para Microsoft Identity Manager 2016. Leia mais em [atualização de suporte para clientes Azure ad Premium usando Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>O uso de condições de associação de grupo na configuração de declarações de SSO é aumentado

**Tipo:** recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
Anteriormente, o número de grupos que você poderia usar ao alterar condicionalmente as declarações com base na associação de grupo em qualquer configuração de aplicativo individual era limitada a 10. O uso de condições de associação de grupo na configuração de declarações de SSO agora aumentou para um máximo de 50 grupos. Para obter mais informações sobre como configurar declarações, consulte [configuração de declarações de SSO de aplicativos empresariais](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Habilitando a formatação básica no componente texto da página de entrada na identidade visual da empresa.

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
A funcionalidade de identidade visual da empresa na experiência de logon do Azure AD/Microsoft 365 foi atualizada para permitir que o cliente adicione hiperlinks e formatação simples, incluindo negrito, fonte, sublinhado e itálico. Para obter orientação sobre como usar essa funcionalidade, consulte [Adicionar identidade visual à página de entrada Azure Active Directory de sua organização](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Aprimoramentos de desempenho de provisionamento

**Tipo:** recurso alterado  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento foi atualizado para reduzir o tempo de conclusão de um [ciclo incremental](../app-provisioning/how-provisioning-works.md#incremental-cycles) . Isso significa que os usuários e grupos serão provisionados em seus aplicativos mais rapidamente do que antes. Todos os novos trabalhos de provisionamento criados após 6/10/2020 irão se beneficiar automaticamente das melhorias de desempenho. Todos os aplicativos configurados para provisionamento antes de 6/10/2020 precisarão ser reiniciados uma vez após 6/10/2020 para aproveitar as melhorias de desempenho. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Anunciando a reprovação da ADAL e da paridade do MS Graph

**Tipo:** preterido  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo

Agora que as MSAL (bibliotecas de autenticação da Microsoft) estão disponíveis, não adicionaremos mais novos recursos às bibliotecas de autenticação de Azure Active Directory (ADAL) e encerraremos os patches de segurança em 30 de junho de 2022. Para obter mais informações sobre como migrar para o MSAL, consulte [migrar aplicativos para a biblioteca de autenticação da Microsoft (MSAL)](../develop/msal-migration.md).

Além disso, concluimos o trabalho para disponibilizar todas as funcionalidades do Azure AD Graph por meio do MS Graph. Assim, as APIs do Azure AD Graph receberão apenas Bugfix e correções de segurança até 30 de junho de 2022. Para obter mais informações, consulte [atualizar seus aplicativos para usar a biblioteca de autenticação da Microsoft e a API de Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
## <a name="may-2020"></a>Maio de 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Desativação de propriedades em APIs entrada, riskyUsers e riskDetections

**Tipo:** plano de alteração  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Atualmente, os tipos enumerados são usados para representar a propriedade riscotype na API riskDetections e riskyUserHistoryItem (em versão prévia). Os tipos enumerados também são usados para a propriedade riskEventTypes na API entrada. No futuro, representaremos essas propriedades como cadeias de caracteres. 

Os clientes devem fazer a transição para a propriedade riskEventType na API beta riskDetections e riskyUserHistoryItem e para riskEventTypes_v2 Propriedade na API beta entrada por 9 de setembro de 2020. Nessa data, iremos desativar as propriedades CurrentType e riskEventTypes atuais. Para obter mais informações, consulte [alterações nas propriedades do evento de risco e APIs de proteção de identidade no Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Substituição da propriedade riskEventTypes na API entrada v 1.0 no Microsoft Graph

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** segurança e proteção da identidade

Os tipos enumerados mudarão para os tipos de cadeia de caracteres ao representar as propriedades do evento de risco no Microsoft Graph de setembro de 2020. Além de impactar as APIs de visualização, essa alteração também afetará a API entrada em produção.

Introduzimos uma nova propriedade riskEventsTypes_v2 (String) para a API entrada v 1.0. Desativaremos a propriedade riskEventTypes (enum) atual em 11 de junho de 2022 de acordo com nossa política de substituição de Microsoft Graph. Os clientes devem fazer a transição para a propriedade riskEventTypes_v2 na API entrada v 1.0 até 11 de junho de 2022. Para obter mais informações, consulte [reprovação da propriedade riskEventTypes na API entrada v 1.0 no Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Alterações futuras nas notificações de email do MFA

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 

Estamos fazendo as seguintes alterações nas notificações de email para a MFA de nuvem:

As notificações de email serão enviadas do seguinte endereço: azure-noreply@microsoft.com e msonlineservicesteam@microsoftonline.com . Estamos atualizando o conteúdo dos emails de alerta de fraude para indicar melhor as etapas necessárias para desbloquear os usos.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nova inscrição de autoatendimento para usuários em domínios federados que não podem acessar o Microsoft Teams porque eles não são sincronizados com o Azure Active Directory.

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 

Atualmente, os usuários que estão em domínios federados no Azure AD, mas que não estão sincronizados com o locatário, não podem acessar as equipes. A partir do final de junho, essa nova funcionalidade permitirá que ele faça isso estendendo o recurso de inscrição verificada por email existente. Isso permitirá que os usuários que podem entrar em um IdP federado, mas que ainda não tenham um objeto de usuário na ID do Azure, tenham um objeto de usuário criado automaticamente e sejam autenticados para as equipes. Seu objeto de usuário será marcado como "inscrição de autoatendimento". Essa é uma extensão da capacidade existente de enviar por email a autoinscrição verificada que os usuários em domínios gerenciados podem fazer e podem ser controlados usando o mesmo sinalizador. Essa alteração concluirá a distribuição durante os dois meses a seguir. Veja as atualizações de documentação [aqui](../enterprise-users/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Próxima correção: o documento de descoberta do OIDC para a nuvem do Azure governamental está sendo atualizado para fazer referência aos pontos de extremidade corretos do grafo.

**Tipo:** plano de alteração  
**Categoria de serviço:** nuvens soberanas  
**Funcionalidade do produto:** Autenticação do usuário
 
A partir de junho, o documento de descoberta do OIDC da [plataforma Microsoft Identity e o protocolo OpenID Connect](../develop/v2-protocols-oidc.md) no ponto de extremidade de [nuvem do Azure governamental](../develop/authentication-national-cloud.md) (login.microsoftonline.us) começarão a retornar o ponto de extremidade correto do [grafo de nuvem nacional](/graph/deployments) ( https://graph.microsoft.us ou https://dod-graph.microsoft.us) , com base no locatário fornecido.  Atualmente, ele fornece o campo de ponto de extremidade de grafo incorreto (graph.microsoft.com) "msgraph_host".  

Essa correção de bug será distribuída gradualmente por aproximadamente 2 meses.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Os usuários do Azure governamental não poderão mais entrar no login.microsoftonline.com

**Tipo:** Planejar alteração  
**Categoria de serviço:** nuvens soberanas  
**Funcionalidade do produto:** Autenticação do usuário
 
Em 1 de junho de 2018, a autoridade oficial do Azure Active Directory (Azure AD) para o Azure governamental mudou de https://login-us.microsoftonline.com para https://login.microsoftonline.us . Se você possui um aplicativo em um locatário do Azure governamental, você deve atualizar seu aplicativo para conectar os usuários no ponto de extremidade. EUA.

A partir de 1º de maio, o Azure AD começará a impor a alteração do ponto de extremidade, bloqueando os usuários do Azure governamental de entrar em aplicativos hospedados nos locatários do Azure governamental usando o ponto de extremidade público (microsoftonline.com). Os aplicativos impactados começarão a ver um erro AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Haverá uma distribuição gradual dessa alteração com a imposição prevista para ser concluída em todos os aplicativos de junho de 2020. Para obter mais detalhes, consulte a [postagem no blog do Azure governamental](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>A solicitação de logoff único do SAML agora envia NameID no formato correto

**Tipo:** corrigido  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Quando um usuário clica em sair (por exemplo, no portal myapps), o Azure AD envia uma mensagem de logoff único do SAML para cada aplicativo que está ativo na sessão do usuário e tem uma URL de logout configurada. Essas mensagens contêm uma NameID em um formato persistente.

Se o token de entrada SAML original usou um formato diferente para NameID (por exemplo, email/UPN), o aplicativo SAML não poderá correlacionar a NameID na mensagem de logout a uma sessão existente (já que o NameIDs usado em ambas as mensagens é diferente), o que fez com que a mensagem de logout seja descartada pelo aplicativo SAML e o usuário permaneça conectado. Essa correção torna a mensagem de saída consistente com a NameID configurada para o aplicativo.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>A função de administrador de identidade híbrida agora está disponível com provisionamento de nuvem

**Tipo:** novo recurso  
**Categoria de serviço:** Provisionamento de nuvem do Azure AD  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Os administradores de ti podem começar a usar a nova função "administrador híbrido" como a função menos privilegiada para configurar Azure AD Connect provisionamento de nuvem. Com essa nova função, você não precisa mais usar a função de administrador global para configurar e configurar o provisionamento de nuvem. [Saiba mais](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – maio de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em maio de 2020, adicionamos os seguintes 36 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-engrenado](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), [meu IBISWorld](https://identity.imagesoftinc.com/), [adequação](../saas-apps/insuite-tutorial.md), [Gerenciamento de processo de alteração](../saas-apps/change-process-management-tutorial.md), plataforma de garantia do [Cyara CX](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [governança inteligente](../saas-apps/smart-global-governance-tutorial.md) [,,](../saas-apps/prezi-tutorial.md) [Mapbox](../saas-apps/mapbox-tutorial.md), [datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [estranho](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO para Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO para BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO para bambu](../saas-apps/easysso-for-bamboo-tutorial.md), [torii](../saas-apps/torii-tutorial.md), [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md), [humanment](../saas-apps/humanage-tutorial.md), ColorTokens [ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [cch Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [superexpander](../saas-apps/textexpander-tutorial.md), [todos os CRM](../saas-apps/anyone-home-crm-tutorial.md), [askSpoke](../saas-apps/askspoke-tutorial.md), [centro de contato Ice](../saas-apps/ice-contact-center-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial .

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo somente de relatório para acesso condicional agora está disponível para o público geral

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

O [modo somente de relatório para acesso condicional do Azure ad](../conditional-access/concept-conditional-access-report-only.md) permite avaliar o resultado de uma política sem impor controles de acesso. Você pode testar políticas somente de relatório em sua organização e entender seu impacto antes de habilitá-las, tornando a implantação mais segura e fácil. Nos últimos meses, vimos uma adoção forte do modo somente de relatório – em 26M, os usuários já estão no escopo de uma política somente de relatório. Com o comunicado hoje, as novas políticas de acesso condicional do Azure AD serão criadas no modo somente de relatório por padrão. Isso significa que você pode monitorar o impacto de suas políticas desde o momento em que elas são criadas. E para aqueles que usam as APIs do MS Graph, você também pode [gerenciar políticas somente de relatório de forma programática](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Inscrição de autoatendimento para usuários convidados

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Com identidades externas no Azure AD, você pode permitir que as pessoas fora da sua organização acessem seus aplicativos e recursos enquanto permitem que eles entrem usando qualquer identidade que preferir. Quando compartilhar um aplicativo com usuários externos, é possível que você não saiba com antecedência quem precisará de acesso a um aplicativo. Com a [inscrição de autoatendimento](../external-identities/self-service-sign-up-overview.md), você pode habilitar usuários convidados para inscrever-se e obter uma conta de convidado para seus aplicativos de linha de negócios (LOB). O fluxo de inscrição pode ser criado e personalizado para dar suporte a identidades sociais e do Azure AD. Você também pode coletar informações adicionais sobre o usuário durante a inscrição.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>As informações de acesso condicional e a pasta de trabalho de relatórios estão geralmente disponíveis

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

A [pasta de trabalho insights e Reporting](../conditional-access/howto-conditional-access-insights-reporting.md) fornece aos administradores uma exibição resumida do acesso condicional do Azure AD em seu locatário. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorar as alterações em tempo real. A pasta de trabalho transmite os dados armazenados no Azure Monitor, que você pode configurar em alguns minutos [seguindo estas instruções](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Para tornar o painel mais detectável, nós o movemos para a guia novas informações e relatórios no menu de acesso condicional do Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A folha detalhes da política para acesso condicional está em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

A [folha detalhes](../conditional-access/troubleshoot-conditional-access.md) da nova política exibe as atribuições, as condições e os controles atendidos durante a avaliação da política de acesso condicional. Você pode acessar a folha selecionando uma linha nas guias acesso condicional ou somente relatório dos detalhes de entrada.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Novos recursos de consulta para objetos de diretório no Microsoft Graph estão em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Funcionalidade do **produto** MS Graph: experiência do desenvolvedor

Novos recursos estão sendo introduzidos para APIs de objetos do Microsoft Graph Directory, habilitando as operações de contagem, pesquisa, filtro e classificação. Isso dará aos desenvolvedores a capacidade de consultar rapidamente nossos objetos de diretório sem soluções alternativas como filtragem e classificação na memória. Saiba mais nesta [postagem no blog](https://aka.ms/CountFilterMSGraphAAD).

Estamos atualmente em visualização pública, procurando comentários. Envie seus comentários com esta [breve pesquisa](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Configurar o logon único baseado em SAML usando a API do Microsoft Graph (beta)

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
O suporte para criar e configurar um aplicativo da galeria do Azure AD usando APIs do MS Graph no beta agora está disponível. Se você precisar configurar o logon único baseado em SAML para várias instâncias de um aplicativo, Economize tempo usando as APIs de Microsoft Graph para [automatizar a configuração do logon único baseado em SAML](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – maio de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic por Organização](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Console da Infraestrutura de Nuvem da Oracle](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>A criptografia de token SAML está geralmente disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
A [criptografia de token SAML](../manage-apps/howto-saml-token-encryption.md) permite que os aplicativos sejam configurados para receber asserções SAML criptografadas. Agora, o recurso está disponível em todas as nuvens.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>As declarações de nome de grupo em tokens de aplicativo estão geralmente disponíveis

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
As declarações de grupo emitidas em um token agora podem ser limitadas apenas aos grupos atribuídos ao aplicativo.  Isso é especialmente importante quando os usuários são membros de um grande número de grupos e houve um risco de exceder os limites de tamanho de token. Com esse novo recurso em vigor, a capacidade de [Adicionar nomes de grupo a tokens](../hybrid/how-to-connect-fed-group-claims.md) está disponível para o público geral.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>O Write-back do workday agora dá suporte à configuração de atributos de número de

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Aprimoramos o aplicativo de provisionamento do workday write-back para agora dar suporte ao Write-back dos atributos número de telefone comercial e número móvel. Além de email e nome de usuário, agora você pode configurar o aplicativo de provisionamento de write-back do WORKDAY para transmitir valores de número de telefone do Azure AD para o workday. Para obter mais detalhes sobre como configurar o Write-back de número de telefone, consulte o tutorial do aplicativo de [write-back do workday](../saas-apps/workday-writeback-tutorial.md) . 

---

### <a name="publisher-verification-preview"></a>Verificação do Publicador (versão prévia)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** experiência de desenvolvedor
 
A verificação do editor (versão prévia) ajuda administradores e usuários finais a entender a autenticidade dos desenvolvedores de aplicativos que se integram com a plataforma de identidade da Microsoft. Para obter detalhes, consulte [verificação do Publicador (versão prévia)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Fluxo de código de autorização para aplicativos de página única

**Tipo:** Categoria do **serviço** de recurso alterado: **funcionalidade do produto** de autenticação: experiência do desenvolvedor

Devido às restrições de cookie de terceiros do navegador moderno, [como o Safari ITP](../develop/reference-third-party-cookies-spas.md), o spas terá que usar o fluxo do código de autorização em vez do fluxo implícito para manter o SSO; MSAL.js v 2. x agora dará suporte ao fluxo do código de autorização. Como as atualizações correspondentes para o portal do Azure, você pode atualizar seu SPA para que ele seja do tipo "Spa" e usar o fluxo de código de autenticação. Para obter diretrizes, consulte [início rápido: conectar usuários e obter um token de acesso em um JavaScript Spa usando o fluxo de código de autenticação](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>A filtragem aprimorada para dispositivos está em visualização pública

**Tipo:** Recurso alterado   
**Categoria de serviço:** Funcionalidade do **produto** de gerenciamento de dispositivos: gerenciamento de ciclo de vida do dispositivo
 
Anteriormente, os únicos filtros que você poderia usar eram "Enabled" e "data da atividade". Agora, você pode [filtrar a lista de dispositivos em mais propriedades](../devices/device-management-azure-portal.md#device-list-filtering-preview), incluindo tipo de sistema operacional, tipo de junção, conformidade e muito mais. Essas adições devem simplificar a localização de um dispositivo específico.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>A nova experiência de Registros de aplicativo para Azure AD B2C agora está disponível para o público geral

**Tipo:** Recurso alterado   
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
A nova experiência de Registros de aplicativo para Azure AD B2C agora está disponível para o público em geral. 

Anteriormente, era necessário gerenciar seus aplicativos voltados ao consumidor B2C separadamente do restante dos seus aplicativos usando a experiência de "aplicativos" herdada. Isso significava experiências de criação de aplicativo diferentes em locais diferentes no Azure.

A nova experiência mostra todos os registros de aplicativo do B2C e registros de aplicativo do Azure AD em um único local e fornece uma maneira consistente de gerenciá-los. Independentemente de você precisar gerenciar um aplicativo voltado para o cliente ou um aplicativo que tenha acesso ao Microsoft Graph para gerenciar programaticamente Azure AD B2C recursos, você só precisa aprender uma maneira de fazer coisas.

Você pode acessar a nova experiência navegando pelo serviço Azure AD B2C e selecionando a folha Registros de aplicativo. A experiência também pode ser acessada pelo serviço de Azure Active Directory.

A experiência de Registros de aplicativo Azure AD B2C é baseada na experiência geral de [registro de aplicativo](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) para locatários do Azure AD, mas é adaptada para Azure ad B2C. A experiência de "aplicativos" herdada será preterida no futuro.

Para obter mais informações, visite [a nova experiência de registro de aplicativo para Azure ad B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---
## <a name="april-2020"></a>Abril de 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>A experiência de registro de informações de segurança combinada já está disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** autenticações (logons)

**Funcionalidade do produto:** segurança e proteção da identidade

A experiência de registro combinada para MFA (autenticação multifator) e Self-Service redefinição de senha (SSPR) já está disponível para o público em geral. Essa nova experiência de registro permite que os usuários se registrem para MFA e SSPR em um único processo passo a passo. Quando você implanta a nova experiência para sua organização, os usuários podem se registrar em menos tempo e com menos complicações. Confira a postagem do blog [aqui](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Avaliação de acesso contínuo

**Tipo:** novo recurso

**Categoria de serviço:** autenticações (logons)

**Funcionalidade do produto:** segurança e proteção da identidade

A avaliação de acesso contínuo é um novo recurso de segurança que permite a imposição quase em tempo real de políticas em partes confiáveis que consomem tokens de acesso do Azure AD quando ocorrem eventos no Azure AD (como a exclusão da conta de usuário). Estamos lançando esse recurso primeiro para equipes e clientes do Outlook. Para obter mais detalhes, leia nosso [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e  [documentação](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Entrada do SMS: os trabalhos de início podem entrar em aplicativos com suporte do Azure AD com seu número de telefone e nenhuma senha

**Tipo:** novo recurso

**Categoria de serviço:** autenticações (logons)

**Funcionalidade do produto:** Autenticação do usuário

O Office está lançando uma série de aplicativos de negócios para dispositivos móveis que atendem a organizações não tradicionais e para funcionários em grandes organizações que não usam email como seu método de comunicação principal. Esses aplicativos visam funcionários frentes, trabalhadores sem mesa, agentes de campo ou funcionários de varejo que podem não receber um endereço de email de seu empregador, ter acesso a um computador ou a ele. Esse projeto permitirá que esses funcionários entrem em aplicativos de negócios inserindo um número de telefone e roundtripping um código. Para obter mais detalhes, consulte nossa [documentação de administração](../authentication/howto-authentication-sms-signin.md) e [documentação do usuário final](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Convidar usuários internos para usar a colaboração B2B

**Tipo:** novo recurso

**Categoria de serviço:** B2B

**Funcionalidade do produto:**

Estamos expandindo o recurso de convite B2B para permitir que contas internas existentes sejam convidadas para usar as credenciais de colaboração B2B no futuro. Isso é feito passando o objeto de usuário para a API de convite, além de parâmetros típicos, como o endereço de email convidado. A ID de objeto do usuário, o UPN, a associação de grupo, a atribuição de aplicativo etc. permanecem intactos, mas continuará a usar o B2B para autenticar com suas credenciais de locatário inicial, em vez das credenciais internas usadas antes do convite. Para obter detalhes, consulte a [documentação](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo somente de relatório para acesso condicional agora está disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** Acesso condicional

**Funcionalidade do produto:** segurança e proteção da identidade

O [modo somente de relatório para acesso condicional do Azure ad](../conditional-access/concept-conditional-access-report-only.md) permite avaliar o resultado de uma política sem impor controles de acesso. Você pode testar políticas somente de relatório em sua organização e entender seu impacto antes de habilitá-las, tornando a implantação mais segura e fácil. Nos últimos meses, vimos uma adoção forte do modo somente de relatório, com 26M usuários já no escopo de uma política somente de relatório. Com este anúncio, as novas políticas de acesso condicional do Azure AD serão criadas no modo somente de relatório por padrão. Isso significa que você pode monitorar o impacto de suas políticas desde o momento em que elas são criadas. E para aqueles que usam as APIs do MS Graph, você também pode [gerenciar políticas somente de relatório programaticamente](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>As informações de acesso condicional e a pasta de trabalho de relatórios estão geralmente disponíveis

**Tipo:** novo recurso

**Categoria de serviço:** Acesso condicional

**Funcionalidade do produto:** segurança e proteção da identidade

A pasta de [trabalho informações](../conditional-access/howto-conditional-access-insights-reporting.md) de acesso condicional e relatórios fornece aos administradores uma exibição resumida do acesso condicional do Azure AD em seu locatário. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorar as alterações em tempo real. A pasta de trabalho transmite os dados armazenados no Azure Monitor, que você pode configurar em alguns minutos [seguindo estas instruções](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Para tornar o painel mais detectável, nós o movemos para a guia novas informações e relatórios no menu de acesso condicional do Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A folha detalhes da política para acesso condicional está em visualização pública

**Tipo:** novo recurso

**Categoria de serviço:** Acesso condicional

**Funcionalidade do produto:** segurança e proteção da identidade

A [folha detalhes](../conditional-access/troubleshoot-conditional-access.md) da nova política exibe quais atribuições, condições e controles foram atendidos durante a avaliação da política de acesso condicional. Você pode acessar a folha selecionando uma linha nas guias **acesso condicional** ou **somente relatório** dos detalhes de entrada.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – abril de 2020

**Tipo:** novo recurso

**Categoria de serviço:** Aplicativos empresariais

**Funcionalidade do produto:** integração de terceiros

Em abril de 2020, adicionamos esses 31 novos aplicativos com suporte à Federação para a Galeria de aplicativos: 

[Aplicativos SincroPool](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT de compras](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO para Jira](../saas-apps/easysso-for-jira-tutorial.md), [virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 mover](https://app.mover.io/login), [palestrante](https://speakerengage.com/login.php), [honestamente](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 People](../saas-apps/gr8-people-tutorial.md), [pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [harmonia](../saas-apps/harmony-tutorial.md), [timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet Click](../saas-apps/synchronet-click-tutorial.md), [capacitação](https://www.made-in-office.com/en/), [Forts](../saas-apps/fortes-change-cloud-tutorial.md), decisivo [, GroupTalk](../saas-apps/litmus-tutorial.md) [,](https://recorder.grouptalk.com/) [dataFrontify](../saas-apps/frontify-tutorial.md), [nuvem do MongoDB](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md), [coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md), TMWS [(Trend Micro Web Security)](../saas-apps/trend-micro-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Suporte de consulta Delta Microsoft Graph para oAuth2PermissionGrant disponível para visualização pública

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

A consulta Delta para oAuth2PermissionGrant está disponível para visualização pública! Agora você pode controlar as alterações sem precisar sondar continuamente Microsoft Graph. [Saiba mais.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Suporte de consulta Delta Microsoft Graph para contato organizacional disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

A consulta Delta para contatos organizacionais está geralmente disponível! Agora você pode controlar as alterações nos aplicativos de produção sem precisar sondar continuamente Microsoft Graph. Substitua qualquer código existente que sonda continuamente dados de orgContact por consulta Delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Suporte de consulta Delta Microsoft Graph para o aplicativo disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

A consulta Delta para aplicativos está geralmente disponível! Agora você pode controlar as alterações nos aplicativos de produção sem precisar sondar continuamente Microsoft Graph. Substitua qualquer código existente que sonda continuamente os dados do aplicativo por consulta Delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Suporte de consulta Delta Microsoft Graph para unidades administrativas disponíveis para visualização pública

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** A consulta Delta de experiência do desenvolvedor para unidades administrativas está disponível para visualização pública! Agora você pode controlar as alterações sem precisar sondar continuamente Microsoft Graph. [Saiba mais.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gerenciar números de telefone de autenticação e mais em novas APIs do Microsoft Graph beta

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

Essas APIs são uma ferramenta importante para gerenciar os métodos de autenticação dos seus usuários. Agora, você pode pré-configurar e gerenciar programaticamente os autenticadores usados para MFA e redefinição de senha de autoatendimento (SSPR). Esse é um dos recursos mais solicitados no Azure AD MFA, SSPR e espaços de Microsoft Graph. As novas APIs que lançamos nesta onda oferecem a você a capacidade de:

- Ler, adicionar, atualizar e remover telefones de autenticação de um usuário
- Redefinir a senha de um usuário
- Ligar e desligar o SMS-Sign-in

Para obter mais informações, consulte [visão geral da API dos métodos de autenticação do Azure ad](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

---

### <a name="administrative-units-public-preview"></a>Visualização pública de unidades administrativas

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

Unidades administrativas permitem que você conceda permissões de administrador restritas a um departamento, uma região ou outro segmento de sua organização que você define. Você pode usar unidades administrativas para delegar permissões a administradores regionais ou definir uma política em um nível granular. Por exemplo, um administrador de conta de usuário poderia atualizar as informações do perfil, redefinir senhas e atribuir licenças para usuários apenas na unidade administrativa dele.

Usando unidades administrativas, um administrador central poderia:

- Criar uma unidade administrativa para o gerenciamento descentralizado de recursos
- Atribuir uma função com permissões administrativas somente para usuários do Azure AD em uma unidade administrativa
- Popular as unidades administrativas com usuários e grupos conforme necessário

Para obter mais informações, consulte [Gerenciamento de unidades administrativas em Azure Active Directory (versão prévia)](../roles/administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Funções internas de administrador de impressora e técnico de impressora

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

**Administrador de impressora**: os usuários com essa função podem registrar impressoras e gerenciar todos os aspectos de todas as configurações de impressora na solução de impressão universal da Microsoft, incluindo as configurações do conector de impressão universal. Eles podem consentir com todas as solicitações de permissão de impressão delegadas. Os Administradores de impressora também têm acesso aos relatórios de impressão. 

**Técnico de impressora**: os usuários com essa função podem registrar impressoras e gerenciar o status da impressora na solução de impressão universal da Microsoft. Eles também podem ler todas as informações do conector. As principais tarefas que um técnico de impressora não pode fazer são definir permissões de usuário em impressoras e compartilhar impressoras. [Saiba mais.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Função interna do administrador de identidade híbrida

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

Os usuários com essa função podem habilitar, definir e gerenciar serviços e configurações relacionados à habilitação da identidade híbrida no Azure AD. Essa função concede a capacidade de configurar o Azure AD para um dos três métodos de autenticação com suporte&#8212;sincronização de hash de senha (PHS), autenticação de passagem (PTA) ou Federação (AD FS ou provedor de Federação de terceiros) &#8212;e implantar a infraestrutura local relacionada para habilitá-los. A infraestrutura local inclui agentes de provisionamento e PTA. Essa função concede a capacidade de habilitar o S-SSO (Logon único contínuo) para habilitar a autenticação direta em dispositivos que não usem o Windows 10 ou em computadores que não usem o Windows Server 2016. Além disso, essa função concede a capacidade de ver os logs de entrada e acessar a integridade e a análise para fins de monitoramento e solução de problemas. [Saiba mais.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Função interna de administrador de rede

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

Os usuários com essa função podem revisar as recomendações de arquitetura de perímetro de rede da Microsoft que se baseiam na telemetria de rede de seus locais de usuário. O desempenho de rede para Microsoft 365 conta com uma arquitetura de perímetro de rede de cliente de negócios cuidadosa, que geralmente é específica ao local do usuário. Essa função permite a edição de locais de usuários descobertos e a configuração de parâmetros de rede desses locais para facilitar medidas de telemetria e recomendações de design melhores. [Saiba mais.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Atividade em massa e downloads na experiência do portal de administração do Azure AD

**Tipo:** novo recurso

**Categoria de serviço:** Gerenciamento de usuários

**Funcionalidade do produto:** diretório

Agora você pode executar atividades em massa em usuários e grupos no Azure AD carregando um arquivo CSV na experiência do portal de administração do Azure AD. Você pode criar usuários, excluir usuários e convidar usuários convidados. E você pode adicionar e remover membros de um grupo.

Você também pode baixar listas de recursos do Azure AD da experiência do portal de administração do Azure AD. Você pode baixar a lista de usuários no diretório, a lista de grupos no diretório e os membros de um grupo específico.

Para obter mais informações, confira o seguinte:

- [Criar usuários](../enterprise-users/users-bulk-add.md) ou [convidar usuários convidados](../external-identities/tutorial-bulk-invite.md)
- [Excluir usuários](../enterprise-users/users-bulk-delete.md) ou [restaurar usuários excluídos](../enterprise-users/users-bulk-restore.md)
- [Baixar lista de usuários](../enterprise-users/users-bulk-download.md) ou [baixar lista de grupos](../enterprise-users/groups-bulk-download.md)
- [Adicionar (importar) Membros](../enterprise-users/groups-bulk-import-members.md) ou [remover membros](../enterprise-users/groups-bulk-remove-members.md) ou [baixar a lista de membros](../enterprise-users/groups-bulk-download-members.md) de um grupo

---

### <a name="my-staff-delegated-user-management"></a>Minha equipe delegou o gerenciamento de usuários

**Tipo:** novo recurso

**Categoria de serviço:** Gerenciamento de usuários

**Funcionalidade do produto:**

Minha equipe permite que os gerentes de primeira a, como um gerente de loja, verifiquem se os membros da equipe podem acessar suas contas do Azure AD. Em vez de depender de um helpdesk central, as organizações podem delegar tarefas comuns, como redefinição de senhas ou alteração de números de telefone, para um gerente de primeira linha. Com a minha equipe, um usuário que não consegue acessar sua conta pode obter acesso novamente em apenas alguns cliques, sem necessidade de assistência técnica ou equipe de ti. Para obter mais informações, consulte [gerenciar seus usuários com minha equipe (versão prévia)](../roles/my-staff-configure.md) e [delegar o gerenciamento de usuários com minha equipe (versão prévia)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Uma experiência do usuário final atualizada nas revisões de acesso

**Tipo:** recurso alterado

**Categoria de serviço:** Revisões de acesso

**Funcionalidade do produto:** Governança de identidade

Atualizamos a experiência do revisor para as revisões de acesso do Azure AD no portal meus aplicativos. No final de abril, os revisores que estiverem conectados à experiência de revisor de revisões de acesso do Azure AD verão uma faixa que permitirá que eles experimentem a experiência atualizada no meu acesso. Observe que a experiência de revisões de acesso atualizada oferece a mesma funcionalidade que a experiência atual, mas com uma interface de usuário aprimorada sobre novos recursos para permitir que os usuários sejam produtivos. [Você pode aprender mais sobre a experiência atualizada aqui](../governance/perform-access-review.md). Essa visualização pública durará até o final de julho de 2020. No final de julho, os revisores que não tiverem optado pela experiência de visualização serão automaticamente direcionados para o meu acesso para realizar revisões de acesso. Se você quiser que os revisores alternem permanentemente para a experiência de visualização em meu acesso agora, [faça uma solicitação aqui](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>O provisionamento e os aplicativos de write-back do usuário de entrada do workday agora dão suporte às versões mais recentes da API de serviços Web do workday

**Tipo:** recurso alterado

**Categoria de serviço:** provisionamento de aplicativos

**Funcionalidade do produto:** 

Com base nos comentários dos clientes, atualizamos agora os aplicativos de write-back e de provisionamento do usuário de entrada do workday na Galeria de aplicativos empresariais para dar suporte às versões mais recentes da API do WWS (workday Web Services). Com essa alteração, os clientes podem especificar a versão da API do WWS que gostaria de usar na cadeia de conexão. Isso oferece aos clientes a capacidade de recuperar mais atributos de RH disponíveis nas versões do workday. O aplicativo de write-back do workday agora usa o serviço Web Change_Work_Contact_Info workday recomendado para superar as limitações do Maintain_Contact_Info.

Se nenhuma versão for especificada na cadeia de conexão, por padrão, os aplicativos de provisionamento de entrada do workday continuarão a usar o WWS v 21.1 para alternar para as APIs mais recentes do WORKDAY para o provisionamento de usuário de entrada, os clientes precisarão atualizar a cadeia de conexão conforme documentado [no tutorial](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) e também atualizar os XPaths usados para os atributos workday, conforme documentado no [Guia de referência](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)de 

Para usar a nova API para Write-back, não há nenhuma alteração necessária no aplicativo de provisionamento de write-back do workday. No lado do workday, verifique se a conta de usuário do sistema de integração do workday (ISU) tem permissões para invocar o processo comercial Change_Work_Contact conforme documentado na seção tutorial, [configurar permissões de política de segurança de processo de negócios](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

Atualizamos nosso [Guia de tutorial](../saas-apps/workday-inbound-tutorial.md) para refletir o novo suporte à versão de API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Os usuários com função de acesso padrão agora estão no escopo para provisionamento

**Tipo:** recurso alterado

**Categoria de serviço:** provisionamento de aplicativos

**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Historicamente, os usuários com a função de acesso padrão estão fora do escopo para provisionamento. Ouvimos comentários de que os clientes querem que os usuários com essa função estejam no escopo para provisionamento. A partir de 16 de abril de 2020, todas as novas configurações de provisionamento permitem que os usuários com a função de acesso padrão sejam provisionados. Gradativamente, alteraremos o comportamento das configurações de provisionamento existentes para dar suporte ao provisionamento de usuários com essa função. [Saiba mais.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Interface do usuário de provisionamento atualizada

**Tipo:** recurso alterado

**Categoria de serviço:** provisionamento de aplicativos

**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Atualizamos nossa experiência de provisionamento para criar uma exibição de gerenciamento mais focalizada. Ao navegar até a folha de provisionamento de um aplicativo empresarial que já foi configurado, você poderá monitorar facilmente o progresso do provisionamento e do gerenciamento de ações, como iniciar, parar e reiniciar o provisionamento.... [Saiba mais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A validação de regra de grupo dinâmico agora está disponível para visualização pública

**Tipo:** recurso alterado

**Categoria de serviço:** gerenciamento de grupo

**Recurso de produto:** colaboração

O Azure Active Directory (Azure AD) agora fornece os meios para validar regras de grupo dinâmicos. Na guia **validar regras** , você pode validar sua regra dinâmica em relação aos membros do grupo de exemplo para confirmar se a regra está funcionando conforme o esperado. Ao criar ou atualizar regras de grupo dinâmicos, os administradores desejam saber se um usuário ou um dispositivo será membro do grupo. Isso ajuda a avaliar se um usuário ou dispositivo atende aos critérios de regra e auxilia na solução de problemas quando a associação não é esperada.

Para obter mais informações, consulte [validar uma regra de associação de grupo dinâmico (versão prévia)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Pontuação segura de identidade-padrões de segurança e atualizações de ação de melhoria de MFA

**Tipo:** recurso alterado

**Categoria de serviço:** N/A

**Funcionalidade do produto:** segurança e proteção da identidade

**Suporte a padrões de segurança para ações de aperfeiçoamento do Azure AD:** A pontuação segura da Microsoft atualizará as ações de aperfeiçoamento para dar suporte aos [padrões de segurança no Azure ad](./concept-fundamentals-security-defaults.md), o que facilita ajudar a proteger sua organização com configurações de segurança pré-configuradas para ataques comuns. Isso afetará as seguintes ações de aprimoramento:

- Verifique se todos os usuários podem concluir a autenticação multifator para acesso seguro
- Exigir MFA para funções administrativas
- Habilitar política para bloquear autenticação herdada
 
**Atualizações de ação de melhoria de MFA:** Para refletir a necessidade de que as empresas assegurem a maior parte da segurança ao aplicar políticas que funcionam com seus negócios, a pontuação segura da Microsoft removeu três ações de melhoria centralizadas em relação à autenticação multifator e adicionadas duas.

Ações de aperfeiçoamento removidas:

- Registrar todos os usuários para autenticação multifator
- Exigir MFA para todos os usuários
- Exigir MFA para funções privilegiadas do Azure AD

Ações de aprimoramento adicionadas:

- Verifique se todos os usuários podem concluir a autenticação multifator para acesso seguro
- Exigir MFA para funções administrativas

Essas novas ações de aprimoramento exigem o registro de seus usuários ou administradores para a MFA (autenticação multifator) em seu diretório e o estabelecimento do conjunto certo de políticas que atenda às suas necessidades organizacionais. O principal objetivo é ter flexibilidade ao garantir que todos os usuários e administradores possam autenticar com vários fatores ou prompts de verificação de identidade com base em risco. Isso pode assumir a forma de ter várias políticas que aplicam decisões com escopo definido ou definir padrões de segurança (a partir de 16 de março) que permitem que a Microsoft decida quando desafiar os usuários para MFA. [Leia mais sobre o que há de novo na pontuação segura da Microsoft](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Contas de Azure Active Directory não gerenciadas na atualização B2B para março de 2021

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
A **partir de 31 de março de 2021**, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários não gerenciados do Azure Active Directory (AD do Azure) para cenários de colaboração B2B. Na preparação para isso, incentivamos você a aceitar o [email de autenticação de senha de uso único](../external-identities/one-time-passcode.md).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Os usuários com a função de acesso padrão estarão no escopo para provisionamento

**Tipo:** plano de alteração  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Historicamente, os usuários com a função de acesso padrão estão fora do escopo para provisionamento. Ouvimos comentários de que os clientes querem que os usuários com essa função estejam no escopo para provisionamento. Estamos trabalhando na implantação de uma alteração para que todas as novas configurações de provisionamento permitam que os usuários com a função de acesso padrão sejam provisionados. Gradualmente, alteraremos o comportamento das configurações de provisionamento existentes para dar suporte ao provisionamento de usuários com essa função. Nenhuma ação do cliente é necessária. Publicaremos uma atualização em nossa [documentação](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) quando essa alteração estiver em vigor.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>A colaboração B2B do Azure AD estará disponível no Microsoft Azure operado por locatários da 21Vianet (Azure China 21Vianet)

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de colaboração B2B do Azure AD serão disponibilizados no Microsoft Azure operado por locatários da 21Vianet (Azure China 21Vianet), permitindo que os usuários em um locatário do Azure China 21Vianet colaborem de forma integrada com os usuários em outros locatários da 21Vianet da China do Azure. [Saiba mais sobre a colaboração B2B do Azure ad](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Redesignação de email de convite de colaboração B2B do Azure AD

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os [emails](../external-identities/invitation-email-elements.md) enviados pelo serviço de convite de colaboração B2B do Azure ad para convidar usuários para o diretório serão reprojetados para tornar mais claras as informações de convite e as próximas etapas do usuário.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>As alterações de política de HomeRealmDiscovery aparecerão nos logs de auditoria

**Tipo:** corrigido  
**Categoria de serviço:** Submeti  
**Funcionalidade do produto:** monitoramento e relatórios
 
Corrigimos um bug em que as alterações na [política HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) não foram incluídas nos logs de auditoria. Agora você poderá ver quando e como a política foi alterada e por quem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – março de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em março de 2020, adicionamos esses 51 novos aplicativos com suporte de Federação à galeria de aplicativos: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co aplicativo SAML](../saas-apps/profitco-saml-app-tutorial.md), [provedor de serviços iPoint](../saas-apps/ipoint-service-provider-tutorial.md), [esfera Contexxt.ai](https://contexxt-sphere.com/login), [sabedoria por Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), inscrições digitais do [FLARE](https://spark-dev.pixelnebula.com/login), [LOGZ.Io-observação de capacidade de nuvem para engenheiros](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [ESPECTROu](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://bizzcontact.app/), [Elqano SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision compr](../saas-apps/netvision-compas-tutorial.md), Hub de [FCM](../saas-apps/fcm-hub-tutorial.md) [, RIB](https://www.devfinition.com/) [A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks](https://demo.asterapp.io/login) [,](../saas-apps/golinks-tutorial.md)Datadog [,](../saas-apps/datadog-tutorial.md)portal de usuário [B2B,](../saas-apps/zscaler-b2b-user-portal-tutorial.md)Planview Enterprise One [](../saas-apps/skills-workflow-tutorial.md) [plataforma IP](../saas-apps/ip-platform-tutorial.md), [](https://admin.nodeinsight.com/AADLogin.aspx) [Invision](../saas-apps/invision-tutorial.md), [Pipedrive](../saas-apps/pipedrive-tutorial.md), [Workshop de apresentação](https://app.showcaseworkshop.com/), [plataforma de integração Greenlight](../saas-apps/greenlight-integration-platform-tutorial.md), [Gerenciamento de acesso compatível com Greenlight](../saas-apps/greenlight-compliant-access-management-tutorial.md), [compreendo Learning](../saas-apps/grok-learning-tutorial.md), [Miradore online](https://login.online.miradore.com/), Khoros [Care](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [BizAgi Studio para automação de processo digital](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-Days](../saas-apps/e-days-tutorial.md), [Kollective Sdn](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md), [campus café](../saas-apps/campus-cafe-tutorial.md), [captura](../saas-apps/catchpoint-tutorial.md), [Code42](../saas-apps/code42-tutorial.md) [](../saas-apps/lift-tutorial.md) [](../saas-apps/planview-enterprise-one-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Colaboração B2B do Azure AD disponível nos locatários do Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de colaboração B2B do Azure AD agora estão disponíveis entre alguns locatários do Azure governamental.  Para descobrir se o seu locatário é capaz de usar esses recursos, siga as instruções em [como posso saber se a colaboração B2B está disponível no meu locatário do Azure no governo dos EUA?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>A integração do Azure Monitor para logs do Azure agora está disponível no Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A integração do Azure Monitor com os logs do Azure AD agora está disponível no Azure governamental. Você pode rotear logs do Azure AD (logs de auditoria e de logon) para uma conta de armazenamento, Hub de eventos e Log Analytics. Confira a [documentação detalhada](../reports-monitoring/concept-activity-logs-azure-monitor.md) , bem como os [planos de implantação para relatórios e monitoramento](../reports-monitoring/plan-monitoring-and-reporting.md) para cenários do Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Atualização da proteção de identidade no Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Estamos empolgados em compartilhar que agora distribuímos a experiência de [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md)atualizada   no [portal de Microsoft Azure governamental](https://portal.azure.us/). Para obter mais informações, consulte nossa [postagem no blog de anúncios](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Recuperação de desastre: Baixe e armazene sua configuração de provisionamento

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento do Azure AD fornece um rico conjunto de recursos de configuração. Os clientes precisam ser capazes de salvar suas configurações para que possam consultá-las mais tarde ou reverta para uma versão válida conhecida. Adicionamos a capacidade de baixar sua configuração de provisionamento como um arquivo JSON e carregá-la quando necessário. [Saiba mais](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (autoatendimento de redefinição de senha) agora requer duas Gates para administradores no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet) 

**Tipo:** recurso alterado  
**Categoria de serviço:** Self-Service redefinição de senha  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Anteriormente no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet), os administradores usando a redefinição de senha de autoatendimento (SSPR) para redefinir suas próprias senhas precisavam apenas de um "portão" (desafio) para provar sua identidade. No público e em outras nuvens nacionais, os administradores geralmente devem usar duas Gates para provar sua identidade ao usar o SSPR. Mas como não damos suporte a chamadas SMS ou telefônicas na 21Vianet da China do Azure, permitimos a redefinição de senha de uma porta por administradores.

Estamos criando a paridade do recurso SSPR entre o Azure China 21Vianet e a nuvem pública. No futuro, os administradores devem usar dois Gates ao usar o SSPR. O SMS, chamadas telefônicas e códigos e notificações do aplicativo autenticador serão suportados. [Saiba mais](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>O comprimento da senha é limitado a 256 caracteres

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Para garantir a confiabilidade do serviço do Azure AD, as senhas de usuário agora são limitadas de comprimento a 256 caracteres. Os usuários com senhas maiores que isso serão solicitados a alterar sua senha no logon subsequente, seja entrando em contato com o administrador ou usando o recurso de redefinição de senha de autoatendimento.

Essa alteração foi habilitada em 13 de março de 2020, em 10h PST (18:00 UTC) e o erro é AADSTS 50052, InvalidPasswordExceedsMaxLength. Consulte o [aviso de alteração significativa](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) para obter mais detalhes.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Os logs de entrada do Azure AD agora estão disponíveis para todos os locatários gratuitos por meio do portal do Azure

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A partir de agora, os clientes que têm locatários gratuitos podem acessar os [logs de entrada do Azure ad da portal do Azure](../reports-monitoring/concept-sign-ins.md) por até 7 dias. Anteriormente, os logs de entrada estavam disponíveis apenas para clientes com licenças Azure Active Directory Premium. Com essa alteração, todos os locatários podem acessar esses logs por meio do Portal.

> [!NOTE]
> Os clientes ainda precisam de uma licença Premium (Azure Active Directory Premium P1 ou P2) para acessar os logs de entrada por meio da API Microsoft Graph e Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Substituição da opção de grupos em todo o diretório por meio de grupos configurações gerais no portal do Azure

**Tipo:** preterido  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Para fornecer uma maneira mais flexível para os clientes criarem grupos de todo o diretório que melhor atendam às suas necessidades, substituímos a opção **grupos de todo o diretório** das configurações gerais dos **grupos**  >   na portal do Azure com um link para a [documentação do grupo dinâmico](../enterprise-users/groups-dynamic-membership.md). Aperfeiçoamos nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os usuários que incluem ou excluem usuários convidados.

---

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="upcoming-changes-to-custom-controls"></a>Alterações futuras em controles personalizados

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Estamos planejando substituir a visualização dos controles personalizados atuais por uma abordagem que permite que os recursos de autenticação fornecidos pelo parceiro funcionem diretamente com as experiências de administrador Azure Active Directory e usuário final. Hoje, as soluções de MFA do parceiro enfrentam as seguintes limitações: elas só funcionam depois que uma senha é inserida; Eles não servem como MFA para autenticação de Step-up em outros cenários principais; e eles não se integram com funções de usuário final ou de gerenciamento de credenciais administrativas. A nova implementação permitirá que fatores de autenticação fornecidos por parceiros funcionem junto com fatores internos para cenários-chave, incluindo registro, uso, declarações de MFA, autenticação de depuração, relatórios e registro em log. 

Os controles personalizados continuarão a ter suporte na visualização junto com o novo design até atingirem a disponibilidade geral. Nesse ponto, daremos tempo para que os clientes migrem para o novo design. Devido às limitações da abordagem atual, não integraremos novos provedores até que o novo design esteja disponível. Estamos trabalhando junto com clientes e provedores e comunicaremos a linha do tempo à medida que ficarmos mais próximos. [Saiba mais](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Pontuação segura de identidade-atualizações de ação de melhoria de MFA

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Para refletir a necessidade de que as empresas assegurem a maior parte da segurança ao aplicar políticas que funcionam com seus negócios, a pontuação segura da Microsoft está removendo três ações de aprimoramento centralizadas em relação à MFA (autenticação multifator) e à adição de duas.

As seguintes ações de aprimoramento serão removidas:

- Registrar todos os usuários para MFA
- Exigir MFA para todos os usuários
- Exigir MFA para funções privilegiadas do Azure AD

As seguintes ações de aprimoramento serão adicionadas:

- Garantir que todos os usuários possam concluir a MFA para acesso seguro
- Exigir MFA para funções administrativas

Essas novas ações de aprimoramento exigirão o registro de seus usuários ou administradores para MFA em seu diretório e o estabelecimento do conjunto certo de políticas que atendam às suas necessidades organizacionais. O principal objetivo é ter flexibilidade ao garantir que todos os usuários e administradores possam autenticar com vários fatores ou prompts de verificação de identidade com base em risco. Isso pode assumir a forma de definir os padrões de segurança que permitem que a Microsoft decida quando desafiar os usuários para MFA ou ter várias políticas que apliquem decisões com escopo. Como parte dessas atualizações de ação de aperfeiçoamento, as políticas de proteção de linha de base não serão mais incluídas nos cálculos de pontuação. [Leia mais sobre o que está chegando na pontuação segura da Microsoft](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Seleção Azure AD Domain Services SKU

**Tipo:** novo recurso  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
**Capacidade do produto:** Serviços de Domínio do Azure AD
 
Ouvimos comentários que Azure AD Domain Services os clientes desejam mais flexibilidade na seleção de níveis de desempenho para suas instâncias. A partir de 1º de fevereiro de 2020, mudamos de um modelo dinâmico (no qual o Azure AD determina o desempenho e o tipo de preço com base na contagem de objetos) para um modelo de seleção automática. Agora, os clientes podem escolher um nível de desempenho que corresponda a seu ambiente. Essa alteração também nos permite habilitar novos cenários como florestas de recursos e recursos premium como backups diários. A contagem de objetos agora é ilimitada para todas as SKUs, mas continuaremos a oferecer sugestões de contagem de objetos para cada camada.

**Nenhuma ação imediata do cliente é necessária.** Para clientes existentes, a camada dinâmica que estava em uso em 1º de fevereiro de 2020 determina a nova camada padrão. Não há nenhum impacto de preço ou desempenho como resultado dessa alteração. No futuro, os clientes do Azure AD DS precisarão avaliar os requisitos de desempenho à medida que o tamanho do diretório e as características da carga de trabalho forem alterados. A alternância entre as camadas de serviço continuará a ser uma operação sem tempo de inatividade, e não mais moveremos os clientes automaticamente para novas camadas com base no crescimento de seu diretório. Além disso, não haverá aumento de preço e novos preços serão alinhados com nosso modelo de cobrança atual. Para obter mais informações, consulte a [documentação dos SKUs do Azure AD DS](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) e a [página de preços do Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – fevereiro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em fevereiro de 2020, adicionamos esses 31 novos aplicativos com suporte à Federação para a Galeria de aplicativos: 

[Plataforma de patente IamIP](../saas-apps/iamip-patent-platform-tutorial.md), [experiência em nuvem](../saas-apps/experience-cloud-tutorial.md), [SSO ns1 para Azure](../saas-apps/ns1-sso-azure-tutorial.md), [serviço de segurança de email Barracuda](https://ess.barracudanetworks.com/sso/azure), [relatório aba](https://myaba.co.uk/client-access/signin/auth/msad), [no caso de crise – portal online](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), [design de nuvem de BIC](../saas-apps/bic-cloud-design-tutorial.md), conector de [dados do Azure ad](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md), [avaliações de ferry Korn](https://www.kornferry.com/solutions/kf-digital/kf-assess), [comando Verkada](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense](../saas-apps/syxsense-tutorial.md), [EAB navegar](../saas-apps/eab-navigate-tutorial.md), [novo Relic (versão limitada)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulium](https://admin.thulium.com/login/instance), [Gerenciador de tíquetes](../saas-apps/ticketmanager-tutorial.md), [seletor de modelo para equipes](https://links.officeatwork.com/templatechooser-download-teams), [abelhas](https://www.beesy.me/index.php/site/login), sistema de [suporte de integridade](../saas-apps/health-support-system-tutorial.md), [mural](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](../saas-apps/firmex-vdr-tutorial.md), [ThingLink para professores e escolas](https://www.thinglink.com/), [Coda](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md), [InvitePeople](https://invitepeople.com/login), [reimpressões de mesa-artigo Galaxy](../saas-apps/reprints-desk-article-galaxy-tutorial.md), [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – fevereiro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Suporte do Azure AD para chaves de segurança FIDO2 em ambientes híbridos

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Estamos anunciando a visualização pública do suporte do Azure AD para chaves de segurança FIDO2 em ambientes híbridos. Agora, os usuários podem usar as chaves de segurança FIDO2 para entrar em seus dispositivos Windows 10 híbridos ingressados no Azure AD e obter logon contínuo em seus recursos locais e na nuvem. O suporte para ambientes híbridos tem sido o principal recurso mais solicitado de nossos clientes sem senha, pois inicialmente lançamos a visualização pública para suporte FIDO2 em dispositivos ingressados no Azure AD. A autenticação sem senha usando tecnologias avançadas, como a biometria e a criptografia de chave pública/privada, proporcionam conveniência e facilidade de uso enquanto são seguras. Com essa visualização pública, agora você pode usar autenticação moderna, como chaves de segurança FIDO2, para acessar recursos tradicionais de Active Directory. Para obter mais informações, acesse [SSO para recursos locais](../authentication/howto-authentication-passwordless-security-key-on-premises.md). 

Para começar, visite [habilitar chaves de segurança FIDO2 para seu locatário](../authentication/howto-authentication-passwordless-security-key.md) para obter instruções passo a passo. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>A nova experiência de minha conta agora está disponível para o público geral

**Tipo:** recurso alterado  
**Categoria de serviço:** Meu perfil/conta  
**Funcionalidade do produto:** Experiências do usuário final
 
Minha conta, a única loja para todas as necessidades de gerenciamento de conta de usuário final, já está disponível para o público geral! Os usuários finais podem acessar esse novo site via URL ou no cabeçalho da nova experiência meus aplicativos. Saiba mais sobre todos os recursos de autoatendimento que a nova experiência oferece em [minha visão geral do portal de contas](../user-help/my-account-portal-overview.md).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL do site da minha conta Atualizando para o myaccount.microsoft.com

**Tipo:** recurso alterado  
**Categoria de serviço:** Meu perfil/conta  
**Funcionalidade do produto:** Experiências do usuário final
 
A nova experiência do usuário final da minha conta atualizará sua URL para `https://myaccount.microsoft.com` no próximo mês. Encontre mais informações sobre a experiência e todos os recursos de autoatendimento da conta que ele oferece aos usuários finais na [ajuda do portal da minha conta](../user-help/my-account-portal-overview.md).

---

## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>O novo portal meus aplicativos agora está disponível para o público geral

**Tipo:** plano de alteração  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** Experiências do usuário final
 
Atualize sua organização para o novo portal meus aplicativos que agora está disponível para o público geral! Encontre mais informações sobre o novo portal e coleções em [criar coleções no portal meus aplicativos](../manage-apps/access-panel-collections.md).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Os espaços de trabalho no Azure AD foram renomeados para coleções

**Tipo:** recurso alterado  
**Categoria de serviço:** Meus Aplicativos   
**Funcionalidade do produto:** Experiências do usuário final
 
Nos espaços de trabalho, os administradores de filtros podem configurar o para organizar os aplicativos dos usuários, agora serão chamados de coleções. Encontre mais informações sobre como configurá-los em [criar coleções no portal meus aplicativos](../manage-apps/access-panel-collections.md).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C inscrição e entrada no telefone usando a política personalizada (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Com a inscrição e a entrada do número de telefone, os desenvolvedores e as empresas podem permitir que seus clientes se inscrevam e entrem usando uma senha de uso único enviada ao número de telefone do usuário via SMS. Esse recurso também permite que o cliente altere seu número de telefone se eles perderem o acesso ao seu telefone. Com o poder das políticas personalizadas e a inscrição e entrada no telefone, permite que desenvolvedores e empresas comuniquem sua marca por meio da personalização da página. Descubra como configurar a [inscrição e a entrada do telefone com políticas personalizadas no Azure ad B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – janeiro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – janeiro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em janeiro de 2020, adicionamos esses 33 novos aplicativos com suporte de Federação à galeria de aplicativos: 

[Josa](../saas-apps/josa-tutorial.md), [nuvem de ponta rápida](../saas-apps/fastly-edge-cloud-tutorial.md), [Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [upshoty](../saas-apps/upshotly-tutorial.md), [LeaveBot](https://appsource.microsoft.com/en-us/product/office/WA200001175), [Datacamp](../saas-apps/datacamp-tutorial.md), [TripActions](../saas-apps/tripactions-tutorial.md), [inteligente](https://www.intumit.com/teams-smartwork/), [Dotcom-monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN-gateway de SSO do Azure ad para Oracle E-Business Suite-EBS, PeopleSoft e JDE](../saas-apps/ssogen-tutorial.md), [hospedagem MYCIRQA SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [Yuhu plataforma de gerenciamento de propriedade](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps](https://sites.lumapps.com/login), [trabalho corporativo](../saas-apps/upwork-enterprise-tutorial.md), [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB para Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxient conduzindo o software Manager](../saas-apps/maxient-conduct-manager-software-tutorial.md), [Helpshift](../saas-apps/helpshift-tutorial.md), PortalTalk [365](https://www.portaltalk.com/), [coreview](https://portal.coreview.com/), silenciar Cloud Office365 Connector, [autenticação PingFlow](https://app-staging.pingview.io/), [ PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md), [tarefa Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/) [, EZRentOut](../saas-apps/ezrentout-tutorial.md), [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Akari assistente virtual](https://akari.io/akari-virtual-assistant/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="two-new-identity-protection-detections"></a>Duas novas detecções de proteção de identidade

**Tipo:** novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Adicionamos dois novos tipos de detecção vinculados de entrada à proteção de identidade: regras de manipulação de caixa de entrada suspeita e viagem impossível. Essas detecções offline são descobertas por Microsoft Cloud App Security (MCAS) e influenciam o usuário e o risco de entrada no Identity Protection. Para obter mais informações sobre essas detecções, consulte nossos [tipos de risco de entrada](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Alteração significativa: fragmentos de URI não serão transferidos por meio do redirecionamento de logon

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
A partir de 8 de fevereiro de 2020, quando uma solicitação for enviada ao login.microsoftonline.com para entrar em um usuário, o serviço acrescentará um fragmento vazio à solicitação.  Isso impede uma classe de ataques de redirecionamento, garantindo que o navegador apague qualquer fragmento existente na solicitação. Nenhum aplicativo deve ter uma dependência desse comportamento. Para obter mais informações, consulte [alterações recentes](../develop/reference-breaking-changes.md#february-2020) na documentação da plataforma de identidade da Microsoft.

---

## <a name="december-2019"></a>Dezembro de 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrar o provisionamento do SAP SuccessFactors ao Azure AD e ao AD local (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Agora você pode integrar o SAP SuccessFactors como uma fonte de identidade autoritativa no Azure AD. Essa integração ajuda a automatizar o ciclo de vida de identidade de ponta a ponta, incluindo o uso de eventos baseados em RH, como novas contratações ou encerramentos, para controlar o provisionamento de contas do Azure AD.

Para obter mais informações sobre como configurar o provisionamento de entrada do SAP SuccessFactors para o Azure AD, consulte o tutorial [Configurar provisionamento automático do SAP SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Suporte para emails personalizados no Azure AD B2C (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode usar Azure AD B2C para criar emails personalizados quando os usuários se inscrevem para usar seus aplicativos. Usando o DisplayControls (atualmente em visualização) e um provedor de email de terceiros (como, [SendGrid](https://sendgrid.com/), [SPARKPOST](https://sparkpost.com/)ou uma API REST personalizada), você pode usar seu próprio modelo de email, endereço e texto **de** assunto, bem como a localização de suporte e configurações de senha de uso único (OTP) personalizadas.

Para obter mais informações, consulte [verificação de email personalizada em Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Substituição de políticas de linha de base com padrões de segurança

**Tipo:** recurso alterado  
**Categoria de serviço:** outro  
**Capacidade do produto:** Segurança e proteção de identidade

Como parte de um modelo seguro por padrão para autenticação, estamos removendo as políticas de proteção de linha de base existentes de todos os locatários. Essa remoção é destinada à conclusão no final de fevereiro. A substituição para essas políticas de proteção de linha de base é o padrão de segurança. Se você estiver usando políticas de proteção de linha de base, deverá planejar a movimentação para a nova política de padrões de segurança ou para o acesso condicional. Se você não usou essas políticas, não há nenhuma ação a ser tomada.

Para obter mais informações sobre os novos padrões de segurança, consulte [o que são os padrões de segurança?](./concept-fundamentals-security-defaults.md) Para obter mais informações sobre políticas de acesso condicional, consulte [políticas de acesso condicional comum](../conditional-access/concept-conditional-access-policy-common.md).

---

## <a name="november-2019"></a>Novembro de 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Suporte para o atributo SameSite e Chrome 80

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

Como parte de um modelo seguro por padrão para cookies, o navegador Chrome 80 está alterando como ele trata cookies sem o `SameSite` atributo. Qualquer cookie que não especifique o `SameSite` atributo será tratado como se ele tivesse sido definido como `SameSite=Lax` , o que resultará no cromo bloquear determinados cenários de compartilhamento de cookies entre domínios em que seu aplicativo possa depender. Para manter o comportamento mais antigo do Chrome, você pode usar o `SameSite=None` atributo e adicionar um `Secure` atributo adicional, de modo que os cookies entre sites só possam ser acessados por conexões HTTPS. O Chrome está agendado para concluir essa alteração até 4 de fevereiro de 2020.

Recomendamos que todos os nossos desenvolvedores testem seus aplicativos usando estas diretrizes:

- Defina o valor padrão para a configuração **usar cookie seguro** como **Sim**.

- Defina o valor padrão para o atributo **SameSite** como **None**.

- Adicione um `SameSite` atributo adicional de **Secure**.

Para obter mais informações, consulte [próximas alterações de cookie de SameSite em ASP.net e ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e [potencial interrupção em sites de clientes e produtos e serviços da Microsoft no Chrome versão 79 e posterior](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Novo hotfix para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** corrigido  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Um pacote cumulativo de atualizações de hotfix (Build 4.6.34.0) está disponível para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Esse pacote cumulativo resolve problemas e adiciona melhorias que são descritas na seção "problemas corrigidos e aprimoramentos adicionados nesta atualização".

Para obter mais informações e baixar o pacote de hotfix, consulte [Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0). o pacote cumulativo de atualizações está disponível](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Novo relatório de atividade do aplicativo AD FS para ajudar a migrar aplicativos para o Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Use o novo relatório de atividade de aplicativo Serviços de Federação do Active Directory (AD FS) (AD FS), no portal do Azure, para identificar quais dos seus aplicativos podem ser migrados para o Azure AD. O relatório avalia todos os aplicativos AD FS para compatibilidade com o Azure AD, verifica se há problemas e fornece orientação sobre como preparar aplicativos individuais para migração.

Para obter mais informações, consulte [usar o relatório de atividade do aplicativo AD FS para migrar aplicativos para o Azure ad](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Novo fluxo de trabalho para os usuários solicitarem o consentimento do administrador (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Controle de Acesso

O novo fluxo de trabalho de consentimento do administrador fornece aos administradores uma maneira de conceder acesso a aplicativos que exigem aprovação de administrador. Se um usuário tentar acessar um aplicativo, mas não puder fornecer consentimento, ele agora poderá enviar uma solicitação de aprovação de administrador. A solicitação é enviada por email e colocada em uma fila que pode ser acessada pelo portal do Azure, para todos os administradores que foram designados como revisores. Depois que um revisor executa uma ação em uma solicitação pendente, os usuários solicitantes são notificados sobre a ação.

Para obter mais informações, consulte [Configurar o fluxo de trabalho de consentimento do administrador (versão prévia)](../manage-apps/configure-admin-consent-workflow.md).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nova experiência de configuração de token de registros de Aplicativo Azure AD para gerenciar declarações opcionais (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** experiência de desenvolvedor

A folha de configuração do novo **token de registros de aplicativo Azure ad** no portal do Azure agora mostra aos desenvolvedores de aplicativos uma lista dinâmica de declarações opcionais para seus aplicativos. Essa nova experiência ajuda a simplificar as migrações de aplicativo do Azure AD e a minimizar as configurações incorretas de declarações opcionais.

Para obter mais informações, consulte [fornecer declarações opcionais para seu aplicativo do Azure ad](../develop/active-directory-optional-claims.md).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Novo fluxo de trabalho de aprovação de dois estágios no gerenciamento de direitos do Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** Gerenciamento de direitos

Apresentamos um novo fluxo de trabalho de aprovação de dois estágios que permite que você exija que dois aprovadores aprovem a solicitação de um usuário para um pacote do Access. Por exemplo, você pode defini-lo para que o gerente do usuário solicitante deva primeiro aprovar e, em seguida, você também pode exigir que um proprietário do recurso aprove. Se um dos aprovadores não aprovar, o acesso não será concedido.

Para obter mais informações, consulte [alterar as configurações de solicitação e aprovação para um pacote de acesso no gerenciamento de direitos do Azure ad](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizações para a página meus aplicativos junto com novos espaços de trabalho (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** integração de terceiros

Agora você pode personalizar a maneira como os usuários da sua organização exibem e acessam a experiência dos meus aplicativos atualizados. Essa nova experiência também inclui o novo recurso de espaços de trabalho, o que torna mais fácil para os usuários localizar e organizar aplicativos.

Para obter mais informações sobre a nova experiência meus aplicativos e criar espaços de trabalho, consulte [criar espaços de trabalho no portal meus aplicativos](../manage-apps/access-panel-collections.md).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Suporte de ID do Google social para colaboração B2B do Azure AD (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** Autenticação do usuário

O novo suporte para o uso de IDs sociais do Google (contas do Gmail) no Azure AD ajuda a tornar a colaboração mais simples para seus usuários e parceiros. Não há mais necessidade de que seus parceiros criem e gerenciem uma nova conta específica da Microsoft. O Microsoft Teams agora dá suporte completo aos usuários do Google em todos os clientes e nos pontos de extremidade de autenticação comuns e relacionados ao locatário.

Para obter mais informações, consulte [Adicionar o Google como um provedor de identidade para usuários de convidado B2B](../external-identities/google-federation.md).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Suporte móvel do Microsoft Edge para acesso condicional e logon único (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

O Azure AD para Microsoft Edge no iOS e Android agora dá suporte a Sign-On único do Azure AD e acesso condicional:

- **Logon único (SSO) do Microsoft Edge:** O logon único agora está disponível entre clientes nativos (como o Microsoft Outlook e o Microsoft Edge) para todos os aplicativos conectados ao Azure AD.

- **Acesso condicional do Microsoft Edge:** Por meio de políticas de acesso condicional baseadas em aplicativo, os usuários devem usar navegadores protegidos por Microsoft Intune, como o Microsoft Edge.

Para obter mais informações sobre o acesso condicional e o SSO com o Microsoft Edge, consulte a postagem do blog de [suporte móvel do Microsoft Edge para acesso condicional e logon único agora disponível](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) . Para obter mais informações sobre como configurar seus aplicativos cliente usando o [acesso condicional baseado em aplicativo](../conditional-access/app-based-conditional-access.md) ou o [acesso condicional baseado em dispositivo](../conditional-access/require-managed-devices.md), consulte Gerenciar o [acesso à Web usando um navegador Microsoft Intune protegido por política](/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gerenciamento de direitos do Azure AD (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** Gerenciamento de direitos

O gerenciamento de direitos do Azure AD é um novo recurso de governança de identidade, que ajuda as organizações a gerenciar o ciclo de vida de identidade e acesso em escala. Esse novo recurso ajuda a automatizar fluxos de trabalho de solicitação de acesso, atribuições de acesso, análises e expiração entre grupos, aplicativos e sites do SharePoint Online.

Com o gerenciamento de direitos do Azure AD, você pode gerenciar o acesso de forma mais eficiente para funcionários e também para usuários fora da sua organização que precisam de acesso a esses recursos.

Para obter mais informações, consulte [o que é o gerenciamento de direitos do Azure ad?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS com suporte recente

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros  

Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

[Serviço de autenticação de identidade da SAP Cloud Platform](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [OfficeSpace software](../saas-apps/officespace-software-provisioning-tutorial.md), [matriz de prioridade](../saas-apps/priority-matrix-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – novembro de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em novembro de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

O [HootSuite](../saas-apps/hootsuite-tutorial.md) [, o](../saas-apps/airtable-tutorial.md) [acesso azul para Membros (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal SSO (logon único)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), Motus, [MyAryaka,](../saas-apps/motus-tutorial.md)BlueMail, [](https://loginself1.bluemail.me/) [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [foko Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets Idea & gerenciamento de inovação](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), uniFLOW [online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [JISC aluno eleitor Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/) [](../saas-apps/myaryaka-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galeria de aplicativos novos e aprimorados do Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Atualizamos a Galeria de aplicativos do Azure AD para facilitar a localização de aplicativos previamente integrados que dão suporte ao provisionamento, ao OpenID Connect e ao SAML em seu locatário Azure Active Directory.

Para obter mais informações, consulte [Adicionar um aplicativo ao seu locatário Azure Active Directory](../manage-apps/add-application-portal.md).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Maior limite de tamanho de definição de função de aplicativo de 120 a 240 caracteres

**Tipo:** recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Ouvimos clientes de que o limite de comprimento para o valor de definição de função de aplicativo em alguns aplicativos e serviços é muito curto em 120 caracteres. Em resposta, aumentamos o comprimento máximo da definição do valor da função para 240 caracteres.

Para obter mais informações sobre como usar definições de função específicas do aplicativo, consulte [adicionar funções de aplicativo em seu aplicativo e recebê-las no token](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Outubro de 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Substituição da API identityRiskEvent para detecção de risco do Azure AD Identity Protection

**Tipo:** Plano para alteração **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

Em resposta aos comentários do desenvolvedor, os assinantes P2 do Azure AD Premium podem agora executar consultas complexas nos dados de detecção de risco do Azure AD Identity Protection ao usar nova API riskDetection do Microsoft Graph. A versão beta da API [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) deixará de retornar dados por volta de **10 de janeiro de 2020**. Se sua organização estiver usando a API identityRiskEvent, você deverá fazer a transição para a nova API riskDetection.

Para obter mais informações sobre a nova API riskDetection, consulte a [documentação de referência da API de detecção de risco](/graph/api/resources/riskdetection).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Suporte ao proxy de aplicativo para o atributo SameSite e Chrome 80

**Tipo:** Plano para alteração **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

Algumas semanas antes do lançamento do navegador Chrome 80, planejamos atualizar como os cookies do Proxy de Antes trata o atributo **SameSite**. Com o lançamento do Chrome 80, cookies que não especifiquem o atributo **SameSite** serão tratados como se estivessem definidos como `SameSite=Lax`.

Para ajudar a evitar possíveis impactos negativos devido a essa alteração, estamos atualizando o acesso do Proxy de Aplicativo e cookies de sessão ao:

- Definir o valor padrão para a configuração **Usar cookie seguro** como **Sim**.

- Definir o valor padrão para o atributo **SameSite** como **nenhum**.

    >[!NOTE]
    > Os cookies de acesso ao Proxy de Aplicativo sempre foram transmitidos exclusivamente por canais seguros. Essas alterações se aplicam somente a cookies de sessão.

Para saber mais sobre as configurações de cookie do Proxy de Aplicativo, consulte [Configurações de cookies para acessar aplicativos locais no Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Os Registros de aplicativo (herdado) e o gerenciamento de aplicativos no portal de registro de aplicativos (apps.dev.microsoft.com) não estão mais disponíveis

**Tipo:** Plano para alteração **Categoria de serviço:** N/D **Capacidade do produto:** Experiência do desenvolvedor

Os usuários com contas do Azure Active Directory não podem mais registrar ou gerenciar aplicativos com o portal de registro de aplicativo (apps.dev.microsoft.com), ou registrar e gerenciar aplicativos na experiência de Registros de aplicativo (herdado) no portal do Azure.

Para saber mais sobre a nova experiência de registros de aplicativo, consulte [Registros de aplicativo no guia de treinamento do portal do Azure](../develop/quickstart-register-app.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Os usuários não precisam mais se registrar novamente durante a migração do MFA por usuário para MFA baseada em acesso condicional

**Tipo:** Fixo **Categoria de serviço:** MFA **Capacidade do produto:** Segurança de identidade e proteção

Corrigimos um problema conhecido em que os usuários eram solicitados a se registrar novamente se estivessem desabilitados para MFA (Autenticação Multifator) por usuário e, em seguida, habilitados para MFA por meio de uma política de acesso condicional.

Para exigir que os usuários se registrem novamente, você pode selecionar a opção **Exigir novo registro de MFA** nos métodos de autenticação do usuário no portal do Azure Active Directory. Para obter mais informações sobre como migrar usuários do MFA por usuário para o MFA baseado em acesso condicional, consulte [Converter usuários do MFA por usuário para o acesso condicional baseado em MFA](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Novos recursos para transformar e enviar declarações em seu token SAML

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Adicionamos mais recursos para ajudar a personalizar e enviar declarações em seu token SAML. Esses novos recursos incluem:

- Funções de transformação de declarações adicionais, que ajudam a modificar o valor enviado na declaração.

- Capacidade de aplicar várias transformações a uma única declaração.

- Capacidade de especificar a origem da declaração, com base no tipo de usuário e no grupo ao qual o usuário pertence.

Para obter mais informações desses novos recursos, incluindo instruções de uso, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](../develop/active-directory-saml-claims-customization.md).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nova página Minhas Entradas para usuários finais no Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Monitoramento e relatório

Adicionamos uma nova página **Minhas entradas** (https://mysignins.microsoft.com) para permitir que os usuários da sua organização vejam o histórico de entrada recente para verificar se há atividades incomuns. Essa nova página permite que os usuários vejam:

- Se alguém está tentando adivinhar a senha deles.

- Se um invasor conseguiu entrar na conta deles e a localização do invasor.

- Quais aplicativos o invasor tentou acessar.

Para obter mais informações, consulte o blog [Os usuários agora podem verificar seu histórico de entrada quanto a atividades incomuns](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066).

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migração do Azure Active Directory Domain Services (Azure AD DS) do clássico para redes virtuais do Azure Resource Manager

**Tipo:** Novo recurso **Categoria de serviço:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Temos excelentes notícias aos nossos clientes que ficaram estagnados em redes virtuais clássicas! Agora, vocês podem executar uma migração única de uma rede virtual clássica para uma rede virtual do Resource Manager existente. Depois de mudar para a rede virtual do Resource Manager, você poderá aproveitar recursos adicionais e atualizados, como políticas de senha detalhadas, notificações por email e logs de auditoria.

Para obter mais informações, consulte [Versão prévia - Migrar o Azure AD Domain Services do modelo de rede virtual clássico para o Resource Manager](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Atualizações para o layout do contrato de página Azure AD B2C

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Apresentamos algumas novidades na versão 1.2.0 do contrato de página para Azure AD B2C. Nesta versão atualizada, você pode controlar a ordem de carga dos seus elementos, o que também pode ajudar a eliminar a cintilação ao carregar a folha de estilos (CSS).

Para obter uma lista completa das alterações feitas no contrato de página, consulte o [Log de alterações da versão](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizar para a página Meus Aplicativos junto com novos workspaces (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Meus Aplicativos **Capacidade do produto:** Controle de acesso

Agora você pode personalizar como os usuários da sua organização veem e acessam a nova experiência dos Meus Aplicativos, incluindo o uso de recursos de workspace para facilitar a localização de aplicativos. A nova funcionalidade de workspaces atua como um filtro para os aplicativos aos quais os usuários da sua organização já têm acesso.

Para obter mais informações sobre como distribuir a nova experiência dos Meus Aplicativos e criar workspaces, consulte [Criar workspaces nos Meus Aplicativos (versão prévia)](../manage-apps/access-panel-collections.md).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Suporte para o modelo de cobrança baseada em usuário ativo mensal (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

O Azure AD B2C agora dá suporte à cobrança de usuário ativo mensal (MAU). A cobrança do MAU é baseada no número de usuários individuais com atividade de autenticação durante um mês do calendário. Os clientes existentes podem trocar para esse novo método de cobrança a qualquer momento.

A partir de 1º de novembro de 2019, todos os novos clientes serão automaticamente cobrados usando esse método. Esse método de cobrança beneficia os clientes com economia de custo e capacidade de planejar com antecedência.

Para obter mais informações, consulte [Atualizar para o modelo de cobrança de usuário ativo mensal](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Novos aplicativos federados disponíveis na galeria de Aplicativo Azure AD - Outubro de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em outubro de 2019, adicionamos estes 35 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Em caso de crise – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact](https://www.tact.ai/products/tact-assistant), [OpusCapita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [Dynatrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [Contentful](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europe](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - US](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Item de menu de segurança consolidado no portal do Azure Active Directory

**Tipo:** Recurso alterado **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

Agora você pode acessar todos os recursos de segurança disponíveis do Azure Active Directory no novo item de menu **Segurança** e na barra **Pesquisa**, no portal do Azure. Além disso, a nova página de aterrissagem **Segurança** chamada de **Segurança - Introdução** fornecerá links para nossa documentação pública, nossas orientações de segurança e nossos guias de implantação.

O novo menu **Segurança** inclui:

- Acesso Condicional
- Identity Protection
- Central de Segurança
- Classificação de segurança de identidade
- Métodos de autenticação
- MFA
- Relatórios de risco - Usuários suspeitos, entradas suspeitas, detecções de risco
- E muito mais…

Para obter mais informações, consulte [Segurança - Introdução](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Política de expiração dos grupos do Office 365 aprimorada com renovação automática

**Tipo:** Recurso alterado **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

A política de expiração dos grupos do Office 365 foi aprimorada para renovar de maneira automática os grupos usados ativamente por seus membros. Os grupos serão renovados com base na atividade do usuário em todos os aplicativos do Office 365, incluindo Outlook, SharePoint e Teams.

Esse aprimoramento ajuda a reduzir as notificações de expiração do grupo e garantir que os grupos ativos continuem disponíveis. Se já houver uma política de expiração ativa para seus grupos do Office 365, você não precisará fazer nada para ativar essa nova funcionalidade.

Para obter mais informações, consulte [Configurar a política de expiração para grupos do Office 365](../enterprise-users/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Experiência de criação atualizada do Azure AD Domain Services (Azure AD DS)

**Tipo:** Recurso alterado **Categoria de serviço:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Atualizamos o Azure AD Domain Services (Azure AD DS) para incluir uma nova e aprimorada experiência de criação, que ajuda a criar um domínio gerenciado em apenas três cliques! Além disso, agora você pode carregar e implantar o Azure AD DS de um modelo.

Para saber mais, confira [Tutorial: Criar e configurar uma instância do Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>Setembro de 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plano para alteração: Substituição dos pacotes de conteúdo do Power BI

**Tipo:** Plano para alteração **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

A partir de 1º de outubro de 2019, o Power BI começará a substituir todos os pacotes de conteúdo, incluindo o Power BI do Azure Active Directory. Como alternativa a esse pacote de conteúdo, você pode usar Pastas de Trabalho do Azure Active Directory para obter informações sobre seus serviços relacionados ao Azure Active Directory. Pastas de trabalho adicionais são disponibilizadas, incluindo aquelas sobre políticas de acesso condicional no modo somente de relatório, informações baseadas em consentimento do aplicativo e muito mais.

Para obter mais informações sobre pastas de trabalho, consulte [Como usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Para obter mais informações sobre a substituição dos pacotes de conteúdo, consulte a postagem no blog [Anúncio da disponibilidade geral dos aplicativos de modelo do Power BI](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/).

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Meu perfil está sendo renomeado e integrado à página da conta do Microsoft Office

**Tipo:** Plano para alteração **Categoria de serviço:** Meu Perfil/Minha Conta **Capacidade do produto:** Colaboração

A partir de outubro, Meu Perfil passará a ser chamado de Minha Conta. Como parte dessa alteração, qualquer lugar que mostra agora **Meu Perfil** será alterado para **Minha Conta**. Além da alteração de nomenclatura e de algumas melhorias de design, a experiência atualizada oferecerá integração adicional com a página de conta do Microsoft Office. Especificamente, você poderá acessar instalações e assinaturas do Office na página **Visão geral da conta**, juntamente com as preferências de contato relacionadas ao Office, na página **Privacidade**.

Para obter mais informações sobre a experiência do Meu Perfil (versão prévia), consulte [Visão geral do portal Meu Perfil (versão prévia)](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gerencie grupos e membros em massa usando arquivos CSV no Portal do Azure Active Directory (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências de gerenciamento de grupo em massa no portal do Azure Active Directory. Agora. você pode usar um arquivo CSV e o portal do Azure Active Directory para gerenciar grupos e listas de membros, incluindo:

- Adicionar ou remover membros de um grupo.

- Baixar a lista de grupos do diretório.

- Baixar a lista de membros do grupo para um grupo específico.

Para obter mais informações, consulte [Adicionar membros em massa](../enterprise-users/groups-bulk-import-members.md), [Remover membros em massa](../enterprise-users/groups-bulk-remove-members.md), [Lista de membros de download em massa](../enterprise-users/groups-bulk-download-members.md)e [Lista de grupos de download em massa](../enterprise-users/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>O consentimento dinâmico agora tem suporte por meio de um novo ponto de extremidade de consentimento do administrador

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Criamos um novo ponto de extremidade de consentimento do administrador para dar suporte ao consentimento dinâmico, que é útil para aplicativos que querem usar o modelo de consentimento dinâmico na plataforma de identidade da Microsoft.

Para obter mais informações sobre como usar esse novo ponto de extremidade, consulte [Usar o ponto de extremidade de consentimento do administrador](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Novos aplicativos federados disponíveis na galeria de Aplicativo Azure AD – Setembro de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em setembro de 2019, adicionamos estes 29 novos aplicativos com suporte de Federação à galeria de aplicativos:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex Compliance Office&trade; - Single sign-on](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/`, [ARC Facilities](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [Wide Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](../saas-apps/renraku-tutorial.md), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [Hosted Heritage Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [CakeHR](../saas-apps/cakehr-tutorial.md), [BIS](../saas-apps/bis-tutorial.md), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), [Discovery Benefits SSO](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-global-reader-role"></a>Nova função de leitor global do Azure Active Directory

**Tipo:** Nova **categoria de serviço** de recursos: recurso de produto de funções do Azure ad **:** controle de acesso

A partir de 24 de setembro de 2019, começaremos a distribuir uma nova função de Azure Active Directory (AD) chamada de Leitor global. Essa distribuição começará pela produção e pelos clientes de nuvem global (GCC) e será concluída em todo o mundo em outubro.

A função Leitor global equivale à função somente leitura do administrador global. Os usuários com essa função podem ler configurações e informações administrativas nos serviços do Microsoft 365, mas não podem realizar ações de gerenciamento. Criamos a função de Leitor global para ajudar a reduzir o número de administradores globais na sua organização. Como as contas de administrador global são poderosas e vulneráveis a ataques, recomendamos menos de cinco administradores globais. Recomendamos usar a função de Leitor global para planejamento, auditorias ou investigações. Recomendamos também usar a função de Leitor global em combinação com outras funções de administrador limitadas, como o administrador do Exchange, para ajudar a realizar o trabalho sem exigir a função de administrador global.

A função Leitor global funciona com a Central do Administrador do Microsoft 365, a Central do Administrador do Exchange, a Central do Administrador do Teams, a Central de Segurança, a Central de Conformidade, a Central do Administrador do Azure Active Directory e a Central do Administrador do Gerenciamento de dispositivo.

>[!NOTE]
> No início da visualização pública, a função de Leitor global não funcionará com: SharePoint, Privileged Access Management, Sistema de Proteção de Dados do Cliente, rótulos de confidencialidade, Teams Lifecycle, Teams Reporting & Call Analytics, Teams IP Phone Device Management e Teams App Catalog.

Para obter mais informações, consulte [Permissões da função de administrador no Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Acesse um servidor de relatório local do seu aplicativo Power BI Mobile usando o Proxy de Aplicativo do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

A nova integração entre o aplicativo móvel Power BI e o Proxy de Aplicativo do Azure Active Directory permite entrar com segurança no aplicativo móvel Power BI e exibir os relatórios de sua organização hospedados no Servidor de Relatórios do Microsoft Power BI.

Para informações sobre o aplicativo Power BI Mobile, incluindo onde baixar o aplicativo, consulte o [site do Power BI](https://powerbi.microsoft.com/mobile/). Para obter mais informações sobre como configurar o aplicativo Power BI Mobile com o Proxy de Aplicativo do Azure Active Directory, consulte [Habilitar acesso remoto ao Power BI Móvel com o Proxy de Aplicativo do Azure Active Directory](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>A nova versão do módulo AzureADPreview do PowerShell está disponível

**Tipo:** Recurso alterado **Categoria de serviço:** Outros **Capacidade do produto:** Diretório

Novos cmdlets foram adicionados ao módulo AzureADPreview para ajudar a definir e atribuir funções personalizadas no Azure Active Directory, incluindo:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nova versão do Azure AD Connect

**Tipo:** Recurso alterado **Categoria de serviço:** Outros **Capacidade do produto:** Diretório

Lançamos uma versão atualizada do Azure AD Connect para clientes de atualização automática. Essa nova versão inclui vários recursos novos, aprimoramentos e correções de bugs. Para obter mais informações sobre essa atualização, consulte [Azure AD Connect: histórico de lançamento de versões](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>O servidor da Autenticação multifator (MFA), versão 8.0.2 está agora disponível

**Tipo:** Fixo **Categoria de serviço:** MFA **Capacidade do produto:** Segurança de identidade e proteção

Se você já é cliente e ativou o servidor MFA antes de 1º de julho de 2019, agora poderá baixar a versão mais recente do servidor MFA (versão 8.0.2). Nesta nova versão, nós:

- Corrigimos um problema, de forma que, quando a sincronização do Azure Active Directory muda um usuário de Desabilitado para Habilitado, um email é enviado ao usuário.

- Corrigimos um problema, de forma que os clientes podem atualizar com sucesso, mantendo o uso das funções de marcação.

- Adicionamos o código de país do Kosovo (+ 383).

- Adicionamos log de auditoria de bypass avulso para MultiFactorAuthSvc.log.

- Melhoramos o desempenho do SDK do Serviço Web.

- Corrigimos outros bugs secundários.

A partir de 1º de julho de 2019, a Microsoft parou de oferecer o servidor MFA para novas implantações. Novos clientes que exigem autenticação multifator devem usar a autenticação multifator do Azure AD baseada em nuvem. Para obter mais informações, consulte [planejando uma implantação da autenticação multifator do Azure ad baseada em nuvem](../authentication/howto-mfa-getstarted.md).

---

## <a name="august-2019"></a>Agosto de 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A pesquisa aprimorada, a filtragem e a classificação para grupos estão disponíveis no Portal do Azure Active Directory (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências relacionadas a grupos aprimoradas no portal do Azure Active Directory. Esses aprimoramentos ajudam a gerenciar melhor grupos e listas de membros, fornecendo:

- Recursos de pesquisa avançada, como a pesquisa de substring em listas de grupos.
- Opções avançadas de filtragem e classificação em listas de membros e proprietários.
- Novos recursos de pesquisa para listas de membros e proprietários.
- Contagens de grupos mais precisas para grupos grandes.

Para obter mais informações, consulte [Gerenciar grupos no portal do Azure](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Estão disponíveis novas funções personalizadas para o gerenciamento de registro do aplicativo (visualização pública)

**Tipo:** Nova **categoria de serviço** de recursos: recurso de produto de funções do Azure ad **:** controle de acesso

As funções personalizadas (disponíveis com uma assinatura P1 ou P2 do Azure Active Directory) agora podem ajudar a fornecer acesso detalhado, permitindo criar definições de função com permissões específicas e, em seguida, atribuir essas funções a recursos específicos. No momento, você cria funções personalizadas usando permissões para gerenciar registros de aplicativo e, em seguida, atribui a função a um aplicativo específico. Para obter mais informações sobre funções personalizadas, consulte [Funções Administrador personalizadas no Azure Active Directory (versão prévia)](../roles/custom-overview.md).

Se precisar de permissões ou recursos adicionais com suporte indisponíveis no momento, você poderá enviar comentários para nosso [site de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) e adicionaremos sua solicitação ao nosso roteiro de atualização.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Os novos logs de provisionamento podem ajudá-lo a monitorar e solucionar problemas de implantação do provisionamento do seu aplicativo (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

Novos logs de provisionamento estão disponíveis para ajudar a monitorar e solucionar problemas de implantação de provisionamento de usuários e grupos. Esses novos arquivos de log contêm informações sobre:

- Quais grupos foram criados com êxito no [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Quais funções foram importadas do [AWS Single-Account Access](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Quais funcionários não foram importados do [Workday](../saas-apps/workday-inbound-tutorial.md)

Para obter mais informações, consulte [Relatórios de provisionamento no portal do Azure Active Directory portal (versão prévia)](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Novos relatórios de segurança para todos os administradores do Azure Active Directory (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

Por padrão, todos os administradores do Azure Active Directory serão capazes de acessar relatórios de segurança modernos no Azure Active Directory. Até o fim de setembro, você poderá usar a faixa na parte superior dos relatórios de segurança modernos para retornar aos relatórios antigos.

Os relatórios de segurança modernos fornecerão recursos adicionais das versões mais antigas, incluindo:

- Filtragem e classificação avançadas
- Ações em massa, como ignorar o risco do usuário
- Confirmação de entidades comprometidas ou seguras
- Estado de risco, abrangendo: Em risco, ignorado, remediado e confirmado comprometido
- Novas detecções relacionadas a riscos (disponíveis para assinantes do Azure Active Directory Premium)

Para obter mais informações, consulte [Usuários suspeitos](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [Entradas suspeitas](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)e [Detecções de riscos](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A identidade gerenciada atribuída ao usuário está disponível para Máquinas Virtuais e Conjuntos de Dimensionamento de Máquinas Virtuais (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** Identidades gerenciadas para recursos do Azure **Capacidade do produto:** Experiência do desenvolvedor

Agora, as identidades gerenciadas atribuídas pelo usuário estão disponíveis para Máquinas Virtuais e Conjuntos de Dimensionamento de Máquinas Virtuais. Como parte disso, o Azure pode criar uma identidade no locatário do Azure Active Directory confiável para a assinatura em uso e que pode ser atribuída a uma ou mais instâncias de serviço do Azure. Para obter mais informações sobre identidades gerenciadas atribuídas ao usuário, confira [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Os usuários podem redefinir as senhas usando um aplicativo móvel ou um token de hardware (disponibilidade geral)

**Tipo:** Recurso alterado **Categoria de serviço:** Redefinição de senha de autoatendimento **Capacidade do produto:** Autenticação de usuário

Os usuários que registraram um aplicativo móvel com sua organização agora podem redefinir sua própria senha ao aprovar uma notificação do aplicativo Microsoft Authenticator ou inserir um código de seu aplicativo móvel ou token de hardware.

Para obter mais informações, confira [Como funciona: Redefinição de senha self-service do Azure AD](../authentication/concept-sspr-howitworks.md). Para obter mais informações sobre a experiência do usuário, consulte a [Visão geral de redefinir sua própria senha corporativa ou de estudante](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>O ADAL.NET ignora o cache compartilhado do MSAL.NET para cenários de “em nome do usuário”

**Tipo:** Fixo **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Começando com a biblioteca de autenticação do Azure Active Directory (ADAL.NET) versão 5.0.0 (versão prévia), os desenvolvedores de aplicativos precisam [serializar um cache por conta para aplicativos Web e APIs Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Caso contrário, alguns cenários que usam o [fluxo em nome de](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) para Java, juntamente com alguns casos de uso específicos do `UserAssertion` , podem resultar em uma elevação de privilégio. Para evitar essa vulnerabilidade, o ADAL.NET agora ignora o cache compartilhado da biblioteca de autenticação da Microsoft para dotNet (MSAL.NET) para cenários em nome de.

Para obter mais informações sobre esse problema, consulte [Vulnerabilidade de elevação de privilégio da Biblioteca de Autenticação do Active Directory](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Novos aplicativos federados disponíveis na galeria Aplicativo Azure AD – Agosto de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em agosto de 2019, adicionamos estes 26 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Civic Platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport's Inativ Portal (Europe)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy Attendance](../saas-apps/academy-attendance-tutorial.md), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [deliver.media&trade; Portal](https://portal.deliver.media), [Frontline Education](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](../saas-apps/blink-tutorial.md), [Vocoli](../saas-apps/vocoli-tutorial.md), [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr](../saas-apps/sigstr-tutorial.md), [Darwinbox](../saas-apps/darwinbox-tutorial.md), [Watch by Colors](../saas-apps/watch-by-colors-tutorial.md), [Harness](../saas-apps/harness-tutorial.md), [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Estão disponíveis novas versões dos módulos do PowerShell AzureAD e AzureADPreview

**Tipo:** Recurso alterado **Categoria de serviço:** Outros **Capacidade do produto:** Diretório

Novas atualizações para os módulos PowerShell AzureAD e AzureADPreview estão disponíveis:

- Um novo parâmetro `-Filter` foi adicionado ao parâmetro `Get-AzureADDirectoryRole` no módulo AzureAD. Esse parâmetro ajuda a filtrar as funções do diretório retornadas pelo cmdlet.
- Novos cmdlets foram adicionados ao módulo AzureADPreview para ajudar a definir e atribuir funções personalizadas no Azure Active Directory, incluindo:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Melhorias na interface do usuário do construtor de regras de grupo dinâmico no portal do Azure

**Tipo:** Recurso alterado **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Fizemos algumas melhorias na interface do usuário no construtor de regras de grupo dinâmico, disponível na portal do Azure, para facilitar a configuração de uma nova regra ou a alteração das regras existentes. Essa melhoria de design permite criar regras com até cinco expressões, em vez de apenas uma. Também atualizamos a lista de propriedades do dispositivo para remover as que foram preteridas.

Para obter mais informações, consulte [Gerenciar regras de associação dinâmica](../enterprise-users/groups-dynamic-membership.md).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nova permissão de aplicativo do Microsoft Graph disponível para uso com revisões de acesso

**Tipo:** Recurso alterado **Categoria de serviço:** Revisões de acesso **Capacidade do produto:** Governança de Identidade

Apresentamos uma nova permissão do aplicativo Microsoft Graph, `AccessReview.ReadWrite.Membership`, que permite aos aplicativos criar e recuperar automaticamente as revisões de acesso para associações de grupo e atribuições de aplicativo. Essa permissão pode ser usada por seus trabalhos agendados ou como parte de sua automação, sem necessidade de um contexto de usuário conectado.

Para obter mais informações, consulte o blog [Exemplo de como criar revisões de acesso do Azure Active Directory usando permissões de aplicativo Microsoft Graph com PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Os logs de atividade do Azure Active Directory agora estão disponíveis para instâncias de nuvem governamental no Azure Monitor

**Tipo:** Recurso alterado **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Estamos empolgados em anunciar que os logs de atividades do Azure Active Directory agora estão disponíveis para instâncias de nuvem do governo no Azure Monitor. Agora, você pode enviar logs do Azure Active Directory à sua conta de armazenamento ou para um hub de eventos para integrar com suas ferramentas de SIEM, como [Sumologic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md) e [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md).

Para obter mais informações sobre como configurar o Azure Monitor, consulte [Logs de atividades do Azure Active Directory no Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Atualize os seus usuários para a nova experiência de informações de segurança aprimorada

**Tipo:** Recurso alterado **Categoria de serviço:**  Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Em 25 de setembro de 2019, desativaremos a experiência de informações de segurança antiga e não avançada para registrar e gerenciar informações de segurança do usuário e ativaremos apenas a versão nova e [avançada](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Isso significa que os usuários não poderão mais usar a experiência antiga.

Para obter mais informações sobre a experiência de informações de segurança aprimorada, consulte nossa [documentação de administração](../authentication/concept-registration-mfa-sspr-combined.md) e nossa [documentação do usuário](../user-help/security-info-setup-signin.md).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Para ativar essa nova experiência, você deve:

1. Entrar no portal do Azure como administrador global ou administrador de usuários.

2. Navegar até **Azure Active Directory > Configurações do usuário > Gerenciar configurações da versão prévia do recurso do painel de acesso**.

3. Na área **Os usuários podem usar versões prévias dos recursos para registro e gerenciamento de informações de segurança – aprimorado**, selecione **Selecionado** e depois escolha um grupo de usuários ou **Todos** para ativar esse recurso para todos os usuários no locatário.

4. Na área **Os usuários podem usar versões prévias dos recursos para registro e gerenciamento de informações de segurança**, selecione **Nenhum**.

5. Salve suas configurações.

    Depois de salvar suas configurações, você não terá mais acesso à antiga experiência de informações de segurança.

>[!Important]
>Se você não concluir essas etapas até 25 de setembro de 2019, seu locatário de Azure Active Directory será habilitado automaticamente para a experiência aprimorada. Se tiver dúvidas, entre em contato conosco em registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>As solicitações de autenticação que usam logons POST serão mais rigorosamente validadas

**Tipo:** Recurso alterado **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Padrões

A partir de 2 de setembro de 2019, as solicitações de autenticação que usam o método POST terão uma validação mais rigorosa em relação aos padrões HTTP. Especificamente, espaços e aspas duplas (") não serão mais removidos dos valores do formulário de solicitação. Essas alterações não devem interromper clientes existentes e ajudarão a garantir que as solicitações enviadas ao Azure Active Directory sejam sempre manipuladas de maneira confiável.

Para obter mais informações, consulte [Notificações de alteração da falha do Azure Active Directory](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Julho de 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plano para alteração: Atualização do serviço de proxy de aplicativo para dar suporte apenas a TLS 1.2

**Tipo:** Plano para alteração **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

Para fornecer nossa criptografia mais forte a você, começaremos limitando o acesso do serviço de proxy de aplicativo a protocolos TLS 1.2. Essa limitação será inicialmente distribuída para os clientes que já usam protocolos TLS 1.2, portanto, você não notará o impacto. Os protocolos TLS 1.0 e TLS 1.1 serão totalmente preteridos até 31 de agosto de 2019. Os clientes que ainda usam TLS 1.0 e TLS 1.1 receberão um aviso prévio para se preparar para essa alteração.

Para manter a conexão com o serviço de proxy de aplicativo durante essa mudança, recomendamos verificar se as combinações de servidor-cliente e navegador-servidor estão atualizadas para usar TLS 1.2. Recomendamos também incluir todos os sistemas clientes usados por seus funcionários para acessar os aplicativos publicados por meio do serviço de proxy de aplicativo.

Para obter mais informações, consulte [Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plano para alteração: Atualizações de design são chegando para a galeria de aplicativos

**Tipo:** Plano para alteração **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Novas alterações de interface do usuário estão chegando ao design da área **Adicionar da galeria** da folha **Adicionar um aplicativo**. Essas alterações facilitarão a localização dos aplicativos que dão suporte ao provisionamento automático, ao OpenID Connect, Security Assertion Markup Language (SAML) e SSO de senha.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plano para alteração: Remoção do endereço IP do servidor MFA do endereço IP do Office 365

**Tipo:** Plano para alteração **Categoria de serviço:** MFA **Capacidade do produto:** Segurança de identidade e proteção

Estamos removendo o endereço IP do servidor MFA do [serviço Web de URL e endereço IP do Office 365](/office365/enterprise/office-365-ip-web-service). Se, no momento, você conta com essas páginas para atualizar as configurações do firewall, precisará também incluir a lista de endereços IP documentados na seção **Requisitos do firewall do Servidor de Autenticação Multifator do Microsoft Azure** do artigo [Introdução ao Servidor de Autenticação Multifator do Microsoft Azure](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokens somente de aplicativo agora exigem que o aplicativo cliente exista no locatário de recursos

**Tipo:** Fixo **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Em 26 de julho de 2019, alteramos o fornecimento de tokens somente de aplicativo por meio da [concessão de credenciais de cliente](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Anteriormente, os aplicativos podiam obter tokens para chamar outros aplicativos, independentemente de o aplicativo do cliente estar no locatário ou não. Atualizamos esse comportamento para que recursos de locatário único, às vezes chamados de APIs Web, só possam ser chamados por aplicativos cliente que existem no locatário do recurso.

Se seu aplicativo não estiver no locatário de recursos, você receberá a mensagem de erro: `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`. Para corrigir esse problema, você precisa criar a entidade de serviço de aplicativo cliente no locatário, usando o [ponto de extremidade de consentimento do administrador ](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) ou [por meio do PowerShell](../develop/howto-authenticate-service-principal-powershell.md), o que garantirá que seu locatário tenha concedido a permissão do aplicativo para operar dentro do locatário.

Para obter mais informações, consulte [Quais são as novidades para autenticação?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Ainda não é necessário existir um consentimento entre o cliente a API. Os aplicativos ainda precisam fazer suas próprias verificações de autorização.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Novas credenciais sem senha para o Azure Active Directory usando chaves de segurança FIDO2

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Os clientes do Azure Active Directory agora podem definir políticas para gerenciar chaves de segurança FIDO2 para usuários e grupos de sua organização. Os usuários finais também podem autorregistrar suas chaves de segurança, usar as chaves para entrar em suas contas da Microsoft em sites com dispositivos com capacidade de FIDO, bem como entrar em seus dispositivos Windows 10 ingressados no Azure Active Directory.

Para obter mais informações relacionadas a administrador, consulte [Habilitar entrada sem senha para Azure Active Directory (versão prévia)](../authentication/concept-authentication-passwordless.md) e, para informações relacionadas ao usuário final, consulte [Configurar informações de segurança para usar uma chave de segurança (versão prévia)](../user-help/security-info-setup-security-key.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Aplicativo Azure AD - Julho de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em julho de 2019, adicionamos estes 18 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md), [Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [Clever Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex Compliance Office&trade;](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Hype](../saas-apps/hype-tutorial.md), [Abstract](../saas-apps/abstract-tutorial.md), [Ascentis](../saas-apps/ascentis-tutorial.md), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](../saas-apps/wandera-tutorial.md), [TwineSocial](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS com suporte recente

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Monitoramento e relatório

Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Diretório federado](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nova marca de serviço do Azure AD Domain Services para grupo de segurança de rede

**Tipo:** Novo recurso **Categoria de serviço:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Se estiver cansado de gerenciar listas longas de endereços IP e intervalos, você poderá usar a nova marca de serviço de rede **AzureActiveDirectoryDomainServices** em seu grupo de segurança de rede do Azure para ajudar a proteger o tráfego de entrada em sua sub-rede da rede virtual do Azure AD Domain Services.

Para obter mais informações sobre essa nova marca de serviço, consulte [Grupos de segurança de rede do Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio Azure Active Directory para visualização pública. A auditoria de segurança ajuda a fornecer insights críticos sobre seus serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o Armazenamento do Azure, os workspaces do Azure Log Analytics e o hub de eventos do Azure, usando o portal do serviço de domínio do Azure Active Directory.

Para obter mais informações, consulte [Habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Uso e insights de novos métodos de autenticação (Visualização Pública)

**Tipo:** Novo recurso **Categoria de serviço:** Redefinição de senha de autoatendimento **Capacidade do produto:** Monitoramento e relatório

Os novos métodos de autenticação são usados & relatórios do insights podem ajudá-lo a entender como recursos como a autenticação multifator do Azure AD e a redefinição de senha de autoatendimento estão sendo registrados e usados em sua organização, incluindo o número de usuários registrados para cada recurso, com que frequência a redefinição de senha de autoatendimento é usada para redefinir senhas e por qual método a

Para obter mais informações, consulte [Uso e insights dos métodos de autenticação (versão prévia)](../authentication/howto-authentication-methods-activity.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Estão disponíveis novos relatórios de segurança para todos os administradores do Azure Active Directory (Visualização Pública)

**Tipo:** Novo recurso **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

Todos os administradores do Azure Active Directory podem agora selecionar a faixa na parte superior dos relatórios de segurança existentes, como o relatório **Usuários sinalizados para risco**, para começar a usar a nova experiência de segurança mostrada nos relatórios **Usuários suspeitos** e **Entradas suspeitas**. Ao longo do tempo, todos os relatórios de segurança serão movidos das versões mais antigas para as novas versões, e os novos relatórios fornecerão os seguintes recursos adicionais:

- Filtragem e classificação avançadas

- Ações em massa, como ignorar o risco do usuário

- Confirmação de entidades comprometidas ou seguras

- Estado de risco, abrangendo: Em risco, ignorado, remediado e confirmado comprometido

Para obter mais informações, consulte [Relatório de usuários suspeitos](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) e [Relatório de entradas suspeitas](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio Azure Active Directory para visualização pública. A auditoria de segurança ajuda a fornecer insights críticos sobre seus serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o Armazenamento do Azure, os workspaces do Azure Log Analytics e o hub de eventos do Azure, usando o portal do serviço de domínio do Azure Active Directory.

Para obter mais informações, consulte [Habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nova federação direta B2B usando SAML/WS-Fed (Visualização Pública)

**Tipo:** Novo recurso **Categoria de serviço:** B2B **Capacidade do produto:** B2B/B2C

A federação direta ajuda a facilitar o trabalho com parceiros cuja solução de identidade gerenciada pela TI não é o Azure Active Directory, trabalhando com sistemas de identidade que dão suporte aos padrões SAML ou WS-Fed. Depois de configurar uma relação de federação direta com um parceiro, qualquer usuário convidado novo desse domínio poderá colaborar com você usando sua conta organizacional existente, tornando a experiência do usuário mais direta para seus convidados.

Para obter mais informações, consulte [Federação direta com AD FS e provedores de terceiros para usuários convidados (versão prévia)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS com suporte recente

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Monitoramento e relatório

Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Diretório federado](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nova verificação para nomes de grupo duplicados no portal do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Agora, ao criar ou atualizar um nome de grupo no portal do Azure Active Directory, realizaremos uma verificação para ver se você está duplicando um nome de grupo existente em seu recurso. Se determinarmos que outro grupo já usa esse nome, você será solicitado a modificá-lo.

Para obter mais informações, consulte [Gerenciar grupos no portal do Azure Active Directory](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>O Azure Active Directory agora dá suporte a parâmetros de consulta estáticos em URIs de resposta (redirecionamento)

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Os aplicativos do Azure Active Directory agora podem registrar e usar URIs de resposta (redirecionamento) com parâmetros de consulta estáticos (por exemplo, `https://contoso.com/oauth2?idp=microsoft`) para solicitações do OAuth 2.0. O parâmetro de consulta estática está sujeito à correspondência de cadeia de caracteres para URIs de resposta, assim como qualquer outra parte do URI de resposta. Se não houver nenhuma cadeia de caracteres registrada que corresponda ao URI de redirecionamento decodificado por URL, a solicitação será rejeitada. Se o URI de resposta for encontrado, a cadeia de caracteres inteira será usada para redirecionar o usuário, incluindo o parâmetro de consulta estática.

Os URIs de resposta dinâmica ainda são proibidos porque representam um risco de segurança e não podem ser usados para manter informações de estado em uma solicitação de autenticação. Para essa finalidade, use o parâmetro `state`.

Atualmente, as telas de registro do aplicativo do portal do Azure ainda bloqueiam parâmetros de consulta. No entanto, você pode editar manualmente o manifesto do aplicativo para adicionar e testar parâmetros de consulta no seu aplicativo. Para obter mais informações, consulte [Quais são as novidades para autenticação?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Os logs de atividades (APIs do MS Graph) para o Azure Active Directory agora estão disponíveis por meio de Cmdlets do PowerShell

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Estamos empolgados em anunciar que os logs de atividades do Azure Active Directory (relatórios de auditoria e de entradas) agora estão disponíveis por meio do módulo do PowerShell do Azure Active Directory. Anteriormente, você podia criar seus próprios scripts usando os pontos de extremidade da API do MS Graph. Agora, estendemos esse recurso aos cmdlets do PowerShell.

Para obter mais informações sobre como usar esses cmdlets, consulte [cmdlets do PowerShell do Azure Active Directory para relatórios](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controles de filtro atualizados para logs de auditoria e de entrada no Azure Active Directory

**Tipo:** Recurso alterado **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Atualizamos os relatórios de auditoria e log de entrada para que você possa aplicar vários filtros sem precisar adicioná-los como colunas nas telas de relatório. Além disso, agora você pode decidir quantos filtros mostrar na tela. Todas as atualizações funcionam em conjunto para facilitar a leitura dos relatórios e deixá-los com um escopo melhor de acordo com suas necessidades.

Para obter mais informações sobre essas atualizações, consulte [Filtrar log de auditoria](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) e [Filtrar atividades de entrada](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Junho de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nova API riskDetections para Microsoft Graph (visualização Pública)

**Tipo:** Novo recurso **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que a nova API riskDetections para Microsoft Graph está em visualização pública. Você pode usar essa nova API para exibir uma lista dos usuários relacionados à proteção de identidade e as detecções de risco de entrada da sua organização. Você também pode usar essa API para consultar com mais eficiência suas detecções de risco, incluindo detalhes sobre o tipo de detecção, status, nível e muito mais.

Para obter mais informações, consulte [Documentação de referência da API de detecção de risco](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure Active Directory - Junho de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em junho de 2019, adicionamos estes 22 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md), [Helper Helper](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS com suporte recente

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Monitoramento e relatório

Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Exibir o progresso em tempo real do serviço de provisionamento do Azure Active Directory

**Tipo:** Recurso alterado **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

Atualizamos a experiência de provisionamento do Azure Active Directory com uma nova barra que mostra o progresso no processo de provisionamento do usuário. Essa experiência atualizada também fornece informações sobre o número de usuários provisionados durante o ciclo atual, bem como quantos foram provisionados até o momento.

Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A identidade visual da empresa agora é exibida na saída e nas telas de erro

**Tipo:** Recurso alterado **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Atualizamos o Azure Active Directory para que a identidade visual da sua empresa agora apareça nas telas de saída e erro, bem como na página de entrada. Você não precisa fazer nada para ativar esse recurso, o Azure Active Directory simplesmente usa os ativos que você já configurou na área de **identidade visual da empresa** do portal do Azure.

Para obter mais informações sobre como configurar a identidade visual da sua empresa, consulte [Adicionar identidade visual às páginas do Azure Active Directory da sua organização](./customize-branding.md).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>O Servidor de MFA (Autenticação Multifator) do Azure não está mais disponível para novas implantações

**Tipo:** Preterido **Categoria de serviço:** MFA **Capacidade do produto:** Segurança de identidade e proteção

A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a autenticação multifator em sua organização agora devem usar a autenticação multifator do Azure AD baseada em nuvem. Os clientes que ativaram o servidor MFA antes de 1º de julho não verão alterações. Você ainda poderá baixar a versão mais recente, obter atualizações futuras e gerar credenciais de ativação.

Para obter mais informações, consulte [Introdução ao Servidor de Autenticação Multifator do Microsoft Azure](../authentication/howto-mfaserver-deploy.md). Para obter mais informações sobre a autenticação multifator do Azure AD baseada em nuvem, consulte [planejando uma implantação da autenticação multifator do Azure ad baseada em nuvem](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Maio de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Alteração de serviço: Suporte futuro somente para protocolos TLS 1.2 no serviço de proxy de aplicativo

**Tipo:** Plano para alteração **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

Para ajudar a oferecer a melhor criptografia para nossos clientes, estamos limitando o acesso somente a protocolos TLS 1.2 no serviço de proxy de aplicativo. Essa alteração é gradualmente distribuída para clientes que já usam protocolos TLS 1.2, portanto, você não deve ver nenhuma alteração.

A substituição do TLS 1.0 e do TLS 1.1 ocorre em 31 de agosto de 2019, mas avisaremos previamente, portanto, você terá tempo para se preparar para a alteração. Para se preparar, verifique se as combinações de servidor-cliente e navegador-servidor, incluindo os clientes que os usuários usam para acessar os aplicativos publicados por meio do proxy de aplicativo, foram atualizadas para usar o protocolo TLS 1.2 para manter a conexão com o serviço de proxy de aplicativo. Para obter mais informações, consulte [Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Use o relatório de uso e insights para ver seus dados de entrada relacionados a aplicativo

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Monitoramento e relatório

Agora você pode usar o relatório de uso e insights localizado na área **Aplicativos empresariais** do portal do Azure, para obter uma exibição centrada no aplicativo dos seus dados de entrada, incluindo informações sobre:

- Principais aplicativos usados para sua organização

- Aplicativos com mais falhas de entrada

- Principais erros de entrada para cada aplicativo

Para obter mais informações sobre esse recurso, consulte [Relatório de uso e insights no portal do Azure Active Directory](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizar o provisionamento de usuário para aplicativos de nuvem usando o Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Monitoramento e relatório

Siga estes novos tutoriais para usar o serviço de provisionamento do Azure Active Directory para automatizar a criação, a exclusão e a atualização de contas de usuário para os seguintes aplicativos baseados em nuvem:

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Você também pode seguir este novo [tutorial do Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) que oferece informações sobre como provisionar objetos de grupo.

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>A classificação de segurança de identidade está agora disponível no Azure Active Directory (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** N/D **Capacidade do produto:** Segurança de identidade e proteção

Agora você pode monitorar e melhorar sua postura de segurança de identidade usando o recurso de classificação de segurança de identidade no Azure Active Directory. O recurso de classificação de segurança de identidade usa um painel simples para ajudar a:

- Medir objetivamente sua postura de segurança de identidade com base em uma pontuação entre 1 e 223.

- Planejar melhorias de segurança de identidade

- Examinar o sucesso das melhorias de segurança

Para obter mais informações sobre o recurso de pontuação de segurança de identidade, consulte [Qual é a classificação de segurança de identidade no Azure Active Directory?](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nova experiência de Registros de aplicativo está agora disponível (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Experiência do desenvolvedor

A nova experiência de [Registros de aplicativo](https://aka.ms/appregistrations) está em disponibilidade geral. Essa nova experiência inclui todos os principais recursos que você conhece do portal do Azure e do portal de registro de aplicativos, com melhorias de:

- **Melhor gerenciamento de aplicativos.** Em vez de ver seus aplicativos em portais diferentes, agora eles aparecem em um único local.

- **Registro de aplicativo simplificado.** Da melhor experiência de navegação à experiência de seleção de permissão remodelada, agora é mais fácil registrar e gerenciar seus aplicativos.

- **Informações mais detalhadas.** Você pode encontrar mais detalhes sobre seu aplicativo, incluindo guias de início rápido e muito mais.

Para obter mais informações, consulte [Plataforma de identidade da Microsoft](../develop/index.yml) e o anúncio de blog [Experiência de Registros de aplicativo está agora disponível de maneira geral!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) .

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Novos recursos disponíveis na API de Usuários suspeitos para a proteção de identidade

**Tipo:** Novo recurso **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que agora você pode usar a API de Usuários suspeitos para recuperar o histórico de riscos dos usuários, ignorar usuários suspeitos e confirmar os usuários como comprometidos. Essa alteração ajuda a atualizar com mais eficiência o status de risco de seus usuários e a entender seu histórico de riscos.

Para obter mais informações, consulte [Documentação de referência da API de Usuário Suspeitos](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure Active Directory - Maio de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em maio de 2019, adicionamos estes 21 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [Real Links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Displayr](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](../saas-apps/aclp-tutorial.md), [OutSystems](../saas-apps/outsystems-tutorial.md), [Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), [Quantum Workplace](../saas-apps/quantum-workplace-tutorial.md), [Cobalt](../saas-apps/cobalt-tutorial.md), [webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Experiência aprimorada de criação e gerenciamento de grupos no portal do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Fizemos melhorias nas experiências relacionadas a grupos no portal do Azure Active Directory. Essas melhorias permitem que os administradores gerenciem melhor listas de grupos e de membros e forneçam opções de criação adicionais.

As melhorias incluem:

- Filtragem básica por tipo de associação e tipo de grupo.

- Adição de novas colunas, como a origem e o endereço de email.

- Capacidade de selecionar vários grupos, membros e listas de proprietários para fácil exclusão.

- Capacidade de escolher um endereço de email e adicionar proprietários durante a criação do grupo.

Para obter mais informações, consulte [Criar um grupo básico e adicionar membros usando o Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do Azure Active Directory (disponibilidade geral)

**Tipo:** Recurso alterado **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Agora, os administradores podem configurar uma política de nomenclatura para grupos do Office 365 usando o portal do Azure Active Directory. Essa alteração ajuda a impor convenções de nomenclatura uniformes para grupos do Office 365 criados ou editados por usuários em sua organização.

Você pode configurar uma política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Definir prefixos ou sufixos, que são adicionados automaticamente a um nome de grupo.

- Carregar um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidas em nomes de grupos (por exemplo, "CEO, Folha de pagamento, RH").

Para obter mais informações, confira [Impor uma política de nomenclatura para grupos do Office 365](../enterprise-users/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Os pontos de extremidade da API do Microsoft Graph agora estão disponíveis para logs de atividade do Azure Active Directory (disponibilidade geral)

**Tipo:** Recurso alterado **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Estamos felizes em anunciar a disponibilidade geral do suporte aos pontos de extremidade da API do Microsoft Graph para logs de atividades do Azure Active Directory. Agora, você pode usar a versão 1.0 dos logs de auditoria do Azure Active Directory, além da API de logs de entrada.

Para obter mais informações, consulte [Visão geral da API de log de auditoria do Azure Active Directory](/graph/api/resources/azure-ad-auditlog-overview).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Os administradores agora podem usar o acesso condicional para o processo de registro combinado (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Agora, os administradores podem criar políticas de acesso condicional para uso pela página de registro combinada. Isso inclui a aplicação de políticas para permitir o registro se:

- Os usuários estiverem em uma rede confiável.

- Os usuários forem de baixo risco de entrada.

- Os usuários estiverem em um dispositivo gerenciado.

- Os usuários concordarem com os termos de uso da organização (TOU).

Para obter mais informações sobre o acesso condicional e a redefinição de senha, você pode ver a [postagem no blog Acesso condicional para a experiência de registro de redefinição de senha e MFA combinado do Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Para obter mais informações sobre políticas de acesso condicional para o processo de registro combinado, consulte [Políticas de acesso condicional para registro combinado](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration). Para obter mais informações sobre os recurso de termos de uso do Azure Active Directory, consulte [Recurso de termos de uso do Azure Active Directory](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>A nova detecção de inteligência contra ameaças do Azure AD agora está disponível como parte do Azure AD Identity Protection

**Tipo:** Novo recurso **Categoria de serviço:** Azure AD Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

A detecção de inteligência contra ameaças do Azure AD agora está disponível como parte do recurso Azure AD Identity Protection atualizado. Essa nova funcionalidade ajuda a indicar atividade de usuário incomum para um usuário ou atividade específica que é compatível com padrões de ataque conhecidos com base nas fontes de inteligência contra ameaças internas e externas da Microsoft.

Para obter mais informações sobre a versão atualizada do Azure AD Identity Protection, consulte o blog [Quatro grandes melhorias do Azure AD Identity Protection que estão agora em visualização pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) e o artigo [O que é o Azure Active Directory Identity Protection (atualizado)?](../identity-protection/overview-identity-protection.md) . Para obter mais informações sobre a detecção de inteligência contra ameaças do Azure AD, consulte o artigo [Azure Active Directory Identity Protection](../identity-protection/concept-identity-protection-risks.md) de detecções de risco.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>O gerenciamento de direitos do Azure Active Directory agora está disponível (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Governança de identidade **Capacidade do produto:** Governança de Identidade

O gerenciamento de direitos do Azure Active Directory, agora em visualização pública, ajuda os clientes a delegarem o gerenciamento de pacotes de acesso, que define como os funcionários e parceiros comerciais podem solicitar acesso, quem deve aprovar, e por quanto tempo eles têm acesso. Os pacotes de acesso podem gerenciar a associação nos grupos do Azure Active Directory e do Office 365, atribuições de função em aplicativos empresariais e em sites do SharePoint Online. Leia mais sobre o gerenciamento de direitos na [visão geral do gerenciamento de direitos do Azure Active Directory](../governance/entitlement-management-overview.md). Para saber mais sobre a abrangência dos recursos do Azure AD Identity Governance, incluindo Privileged Identity Management, revisão de acesso e termos de uso, consulte [O que é o Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do Azure Active Directory (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Agora, os administradores podem configurar uma política de nomenclatura para grupos do Office 365 usando o portal do Azure Active Directory. Essa alteração ajuda a impor convenções de nomenclatura uniformes para grupos do Office 365 criados ou editados por usuários em sua organização.

Você pode configurar uma política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Definir prefixos ou sufixos, que são adicionados automaticamente a um nome de grupo.

- Carregar um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidas em nomes de grupos (por exemplo, "CEO, Folha de pagamento, RH").

Para obter mais informações, confira [Impor uma política de nomenclatura para grupos do Office 365](../enterprise-users/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Os logs de atividades do Azure Active Directory agora estão disponíveis no Azure Monitor (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Para ajudar a tratar dos seus comentários sobre visualizações com os logs de atividades do Azure Active Directory, estamos introduzindo um novo recurso de insights no Log Analytics. Esse recurso ajuda a obter insights sobre seus recursos do Azure Active Directory usando nossos modelos interativos, chamados de Pastas de Trabalho. Essas Pastas de Trabalho podem oferecer detalhes para aplicativos ou usuários e incluem:

- **Entradas.** Oferece detalhes para aplicativos e usuários, incluindo o local de entrada, o sistema operacional em uso ou o cliente e a versão do navegador e o número de entradas com êxito ou com falha.

- **Autenticação herdada e acesso condicional.** Oferece detalhes para aplicativos e usuários usando autenticação herdada, incluindo uso de Autenticação Multifator disparado por políticas de acesso condicional, aplicativos usando políticas de acesso condicional etc.

- **Análise de falha de entrada.** Ajuda a determinar se os erros de entrada estão ocorrendo devido a uma ação do usuário, a problemas de política ou à sua infraestrutura.

- **Relatórios personalizados.** Você pode criar novas Pastas de Trabalho ou editar as que já existem para ajudar a personalizar o recurso de insights para sua organização.

Para obter mais informações, consulte [Como usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure Active Directory - Abril de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em abril de 2019, adicionamos estes 21 novos aplicativos com suporte de Federação à galeria de aplicativos:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [Shibumi](../saas-apps/shibumi-tutorial.md), [Benchling](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md), [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [Mitel Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (Role-based SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md), [Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggo](https://indiggolead.com/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nova opção de frequência de revisões de acesso e seleção de várias funções

**Tipo:** Novo recurso **Categoria de serviço:** Revisões de acesso **Capacidade do produto:** Governança de Identidade

Novas atualizações nas revisões de acesso do Azure Active Directory permitem que você:

- Altere a frequência de suas revisões de acesso para **semestral**, além das opções semanal, mensal, trimestral e anual, existentes anteriormente.

- Selecione várias funções do Azure Active Directory e recursos do Azure ao criar uma revisão de acesso único. Nessa situação, todas as funções são configuradas da mesma forma e todos os revisores são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso, consulte [Criar uma revisão de acesso de grupos ou aplicativos na revisão de acesso do Azure Active Directory](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Os sistemas de alerta de email do Azure AD Connect estão em transição, enviando informações de novo remetente de email para alguns clientes

**Tipo:** Recurso alterado **Categoria de serviço:** AD Sync **Capacidade do produto:** Plataforma

O Azure AD Connect está no processo de transição de nossos sistemas de alerta de email e pode estar mostrando um novo remetente de email a alguns clientes. Para resolver isso, você deve adicionar `azure-noreply@microsoft.com` à lista de permissões da sua organização ou não poderá continuar recebendo alertas importantes do seu Office 365, do Azure ou dos serviços de sincronização.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>As alterações de sufixo UPN agora são bem-sucedidas entre domínios federados no Azure AD Connect

**Tipo:** Fixo **Categoria de serviço:** AD Sync **Capacidade do produto:** Plataforma

Agora você pode alterar com êxito o sufixo UPN do usuário de um domínio federado para outro domínio federado no Azure AD Connect. Essa correção significa que você não deve mais receber a mensagem de erro FederatedDomainChangeError durante o ciclo de sincronização ou um email de notificação informando “Não é possível atualizar este objeto no Azure Active Directory, pois o atributo [FederatedUser. UserPrincipalName] não é válido. Atualize o valor nos seus serviços de diretório locais”.

Para obter mais informações, consulte [Solucionar erros durante a sincronização](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maior segurança ao usar a política de acesso condicional com base na proteção do aplicativo no Azure Active Directory (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

O acesso condicional baseado em proteção para aplicativo está agora disponível usando a política **Exigir proteção de app**. Essa nova política aumenta a segurança da sua organização, ajudando a evitar:

- Usuários que obtêm acesso a aplicativos sem uma licença de Microsoft Intune.

- Usuários que não conseguem obter uma política de proteção de aplicativo do Microsoft Intune.

- Usuários que obtêm acesso a aplicativos sem uma política de proteção de aplicativo do Microsoft Intune configurada.

Para obter mais informações, consulte [Como exigir política de proteção de aplicativos para acesso a aplicativo de nuvem com o acesso condicional](../conditional-access/app-protection-based-conditional-access.md).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para logon único do Azure Active Directory e acesso condicional no Microsoft Edge (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Aprimoramos nosso suporte do Azure Active Directory para o Microsoft Edge, incluindo o fornecimento de novo suporte para logon único do Azure Active Directory e acesso condicional. Se já usou o Managed Browser do Microsoft Intune, você pode agora usar o Microsoft Edge.

Para obter mais informações sobre como configurar e gerenciar seus dispositivos e aplicativos com o acesso condicional, consulte [Exigir dispositivos gerenciados para acesso a aplicativo de nuvem com acesso condicional](../conditional-access/require-managed-devices.md) e [Exigir aplicativos cliente aprovados para acesso a aplicativo de nuvem com acesso condicional](../conditional-access/app-based-conditional-access.md). Para obter mais informações sobre como gerenciar o acesso usando o Microsoft Edge com políticas de Microsoft Intune, consulte [Gerenciar o acesso à Internet usando um navegador protegido por política do Microsoft Intune](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>O Identity Experience Framework e o suporte de política personalizada no Azure Active Directory B2C estão agora disponíveis (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Agora você pode criar políticas personalizadas no Azure AD B2C, incluindo as seguintes tarefas, que têm suporte em escala e em nosso SLA do Azure:

- \* Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.

- Descrever percursos do usuário passo a passo como trocas entre provedores de declarações.

- Definir a ramificação condicional em percursos do usuário.

- Transformar e mapear declarações para uso em decisões e comunicações em tempo real.

- \* Usar os serviços habilitados para API REST aos seus percursos do usuário de autenticação personalizada. Por exemplo, com provedores de email, CRMs e sistemas de autorização proprietários.

- Federar com provedores de identidade que estão em conformidade com o protocolo OpenIDConnect. Por exemplo, com Azure Active Directory de vários locatários, provedores de conta social ou provedores de verificação de dois fatores.

Para obter mais informações sobre como criar políticas personalizadas, consulte [Notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) e leia [a postagem no blog de Alex Simon, incluindo estudos de caso](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure Active Directory - Março de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em março de 2019, adicionamos estes 14 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), [Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), [Iris Intranet](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md), [TAS](../saas-apps/tas-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novos conectores de provisionamento Zscaler e Atlassian na galeria do Azure Active Directory – Março de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Integração de terceiros

Automatize a criação, a atualização e a exclusão de contas de usuário para os seguintes aplicativos:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler Two](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerenciar seus grupos excluídos do Office 365 no portal do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Agora você pode exibir e gerenciar seus grupos excluídos do Office 365 no portal do Azure Active Directory. Essa alteração ajuda a ver quais grupos estão disponíveis para restauração, além de permitir excluir permanentemente todos os grupos que não são necessários para sua organização.

Para obter mais informações, consulte [Restaurar grupos expirados ou excluídos](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>O logon único agora está disponível para aplicativos locais protegidos por SAML do Azure Active Directory por meio do proxy de aplicativo (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

Agora você pode fornecer uma experiência de logon único (SSO) para aplicativos locais autenticados por SAML, junto com o acesso remoto a esses aplicativos por meio do proxy de aplicativo. Para obter mais informações sobre como configurar o SSO do SAML com seus aplicativos locais, consulte [SSO do SAML para aplicativos locais com o proxy de aplicativos (versão prévia)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Os aplicativos cliente em loops de solicitação serão interrompidos para melhorar a confiabilidade e a experiência do usuário

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Os aplicativos cliente podem emitir incorretamente centenas de solicitações de logon iguais em um curto período de tempo. Essas solicitações, bem-sucedidas ou não, contribuem para uma experiência de usuário ruim e um aumento de cargas de trabalho para o IDP, o que aumenta a latência para todos os usuários e reduz a disponibilidade do IDP.

Essa atualização envia um erro `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` a aplicativos cliente que emitem solicitações duplicadas várias vezes por um curto período de tempo, além do escopo da operação normal. Os aplicativos cliente que encontram esse problema devem mostrar um prompt interativo, exigindo que o usuário entre novamente. Para obter mais informações sobre essa alteração e sobre como corrigir seu aplicativo se ele encontrar esse erro, consulte [Quais são as novidades para autenticação?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nova experiência do usuário dos logs de auditoria agora disponível

**Tipo:** Recurso alterado **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Criamos uma nova página de **Log de auditoria** do Azure Active Directory para ajudar a melhorar a legibilidade a pesquisa de informações. Para ver a nova página de **Logs de auditoria**, selecione **Logs de auditoria** na seção **Atividade** do Azure Active Directory.

![Novas páginas de log de auditoria com informações de amostra](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre a nova página de **logs de auditoria**, consulte [Relatórios de atividade de auditoria no portal Azure Active Directory](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e diretrizes para ajudar a impedir o bloqueio acidental de administrador de políticas de acesso condicional configuradas incorretamente

**Tipo:** Recurso alterado **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Para ajudar a impedir que os administradores bloqueiem acidentalmente seus próprios locatários devido a políticas de acesso condicional configuradas incorretamente, criamos novos avisos e atualizamos as diretrizes no portal do Azure. Para obter mais informações sobre as novas diretrizes, consulte [O que são dependências de serviço no acesso condicional do Azure Active Directory](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Experiências aprimoradas dos termos de uso do usuário final em dispositivos móveis

**Tipo:** Recurso alterado **Categoria de serviço:** Termos de uso **Capacidade do produto:** Governança

Atualizamos as experiências existentes de termos de uso para melhorar o modo de revisar e consentir com os termos de uso em um dispositivo móvel. Agora você pode ampliar e reduzir, voltar, baixar as informações e selecionar hiperlinks. Para obter mais informações sobre os termos de uso atualizados, consulte [Recurso de termos de uso do Azure Active Directory](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nova experiência de download de logs de atividades do Azure Active Directory disponível

**Tipo:** Recurso alterado **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Agora você pode baixar grandes quantidades de logs de atividade diretamente do portal do Azure. Essa atualização permite:

- Baixar até 250.000 linhas.

- Receber notificação após a conclusão do download.

- Personalizar o nome do arquivo.

- Determinar o formato de saída, JSON ou CSV.

Para obter mais informações sobre esse recurso, consulte [Início Rápido: Baixar um relatório de auditoria usando o portal do Azure](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alterações da falha: Atualizações da avaliação de condição pelo Exchange ActiveSync (EAS)

**Tipo:** Plano para alteração **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Controle de acesso

Estamos atualizando como o Exchange ActiveSync (EAS) avalia as seguintes condições:

- Local do usuário, com base no país, na região ou no endereço IP

- Risco de entrada

- Plataforma de dispositivos

Se você já usou essas condições em suas políticas de acesso condicional, lembre-se de que o comportamento da condição pode mudar. Por exemplo, se usou anteriormente a condição de local de usuário em uma política, você pode descobrir que a política agora está sendo ignorada com base no local do usuário.

---

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Criptografia configurável de token SAML do Azure Active Directory (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Agora você pode configurar qualquer aplicativo SAML com suporte para receber tokens SAML criptografados. Quando configurado e usado com um aplicativo, o Azure Active Directory criptografa as instruções de declaração SAML emitidas usando uma chave pública obtida de um certificado armazenado no Azure Active Directory.

Para obter mais informações sobre como configurar sua criptografia de token SAML, consulte [Configurar criptografia de token SAML do Azure Active Directory](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicativos usando revisões de acesso do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Revisões de acesso **Capacidade do produto:** Governança

Agora você pode incluir vários grupos ou aplicativos em uma única revisão de acesso do Azure Active Directory para associação de grupo ou atribuição de aplicativo. As revisões de acesso com vários grupos ou aplicativos têm as mesmas configurações e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso usando a revisão de acesso do Azure Active Directory, consulte [Criar uma revisão de acesso para grupos ou aplicativos nas revisões de acesso do Azure Active Directory](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure Active Directory – Fevereiro de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em fevereiro de 2019, adicionamos estes 27 novos aplicativos com suporte para Federação à galeria de aplicativos:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle](../saas-apps/mindtickle-tutorial.md), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [IDrive](../saas-apps/idrive-tutorial.md), [Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), Permission Click, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile](../saas-apps/smartfile-tutorial.md), [Pexip](../saas-apps/pexip-tutorial.md), [Stormboard](../saas-apps/stormboard-tutorial.md), [Seismic](../saas-apps/seismic-tutorial.md), [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [Knowledge Anywhere LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU Campus](../saas-apps/ou-campus-tutorial.md), [Periscope Data](../saas-apps/periscope-data-tutorial.md), [Netop Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registro de MFA/SSPR combinado aprimorado

**Tipo:** Recurso alterado **Categoria de serviço:** Redefinição de senha de autoatendimento **Capacidade do produto:** Autenticação de usuário

Em resposta a comentários dos clientes, aprimoramos a experiência combinada de visualização do registro MFA/SSPR, ajudando os usuários a registrarem com mais rapidez as informações de segurança tanto para MFA quanto para SSPR.

**Para ativar a experiência aprimorada para os seus usuários hoje mesmo, siga estas etapas:**

1. Como administrador global ou de usuários, entre no portal do Azure e acesse **Azure Active Directory > Configurações de usuário > Gerenciar configurações para recursos de versão prévia do painel de acesso**.

2. Na opção **Os usuários que podem usar recursos de versão prévia dos recursos para registro e gerenciamento de informações de segurança – atualizar**, selecione para ligar os recursos para um **Grupo selecionado de usuários** ou para **Todos os usuários**.

Nas próximas semanas, removeremos a capacidade de ativar a experiência de visualização de registro MFA/SSPR combinada antiga para locatários que ainda não a têm ativada.

**Para ver se o controle será removido para seu locatário, siga estas etapas:**

1. Como administrador global ou de usuários, entre no portal do Azure e acesse **Azure Active Directory > Configurações de usuário > Gerenciar configurações para recursos de versão prévia do painel de acesso**.

2. Se a opção **Os usuários que podem usar recursos de versão prévia dos recursos para registro e gerenciamento de informações de segurança** for definida como **Nenhum**, a opção será removida do locatário.

Apresar de você ter ativado a antiga experiência de visualização de registro MFA/SSPR combinada para usuários, a experiência antiga será desativada em uma data futura. Por isso, recomendamos migrar para a nova experiência aprimorada assim que possível.

Para obter mais informações sobre a experiência de registro aprimorada, consulte [Melhorias legais para a experiência de registro de redefinição de senha e MFA combinados do Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência de gerenciamento de política atualizada para fluxos de usuário

**Tipo:** Recurso alterado **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Atualizamos o processo de criação e facilitamos o gerenciamento de política para fluxos de usuário (antes conhecidos como políticas internas). Essa nova experiência agora é padrão para todos os locatários do Azure Active Directory.

Você pode enviar mais comentários e sugestões usando os ícones de sorriso ou rosto triste na área **Enviar seus comentários** na parte superior da tela do portal.

Para obter mais informações sobre a nova experiência de gerenciamento de política, consulte o blog [Azure AD B2C agora tem a personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolher versões de elemento de página específicas fornecidas pelo Azure AD B2C

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Agora você pode escolher uma versão específica dos elementos da página fornecidos pelo Azure AD B2C. Ao selecionar uma versão específica, você pode testar suas atualizações antes que elas apareçam em uma página e obter um comportamento previsível. Além disso, agora você pode optar por impor versões de página específicas para permitir personalizações de JavaScript. Para ativar esse recurso, acesse a página **Propriedades** nos seus fluxos de usuário.

Para obter mais informações sobre como escolher versões específicas de elementos de página, consulte o blog [O Azure AD B2C agora tem a personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos configuráveis de senha do usuário final para B2C (disponibilidade geral)

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Agora você pode configurar a complexidade das senha dos usuários finais da sua organização, em vez de ter que usar a política de senha nativa do Azure Active Directory. Na folha **Propriedades** dos seus fluxos de usuário (anteriormente conhecidos como suas políticas integradas), você pode escolher a complexidade da senha: **Simples** ou **Forte** ou criar um conjunto de requisitos **Personalizado**.

Para obter mais informações sobre a configuração de requisito de complexidade de senha, consulte [Configurar requisitos de complexidade para senhas no Azure Active Directory B2C](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos padrão para experiências de autenticação personalizada com marca

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Você pode usar nossos novos modelos padrão, localizados na folha **Layouts de página** dos seus fluxos de usuário (anteriormente conhecidos como políticas integradas) para criar uma experiência de autenticação personalizada com marca para os usuários.

Para obter mais informações sobre como usar os modelos, consulte [O Azure AD B2C agora tem personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração do Active Directory B2B usando a autenticação de senha de uso único (Versão prévia pública)

**Tipo:** Novo recurso **Categoria de serviço:** B2B **Capacidade do produto:** B2B/B2C

Apresentamos a OTP (autenticação por senha de uso único) para usuários convidados de B2B que não podem ser autenticados por outros meios, tais como o Azure AD, uma MSA (conta Microsoft) ou uma federação do Google. Esse novo método de autenticação significa que os usuários convidados não precisarão criar uma nova conta Microsoft. Em vez disso, ao resgatar um convite ou acessar um recurso compartilhado, o usuário convidado poderá solicitar um código temporário que será enviado para um endereço de email. Usando esse código temporário, o usuário convidado pode continuar a conexão.

Para obter mais informações, consulte [Autenticação de senha de uso único por email (versão prévia)](../external-identities/one-time-passcode.md) e o blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (O Azure AD torna a colaboração e o compartilhamento perfeitos para qualquer usuário com qualquer conta).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas configurações de cookies do Proxy de Aplicativo do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

Apresentamos três novas configurações de cookie, disponíveis para seus aplicativos publicados por meio do Proxy de Aplicativo:

- **Usar Cookie Somente HTTP.** Define o sinalizador **HTTPOnly** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração fornece benefícios de segurança adicionais, como ajudar a prevenir cópia ou modificação de cookies por meio de script do lado do cliente. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar um cookie seguro.** Define o sinalizador **Secure** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração proporciona benefícios de segurança adicionais ao garantir que os cookies só sejam transmitidos em canais seguros TLS, como HTTPS. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar cookie persistente.** Impede que cookies de acesso expirem quando o navegador da Web é fechado. Esses cookies duram todo o tempo de vida do token de acesso. No entanto, os cookies são redefinidos se o tempo de término é atingido ou se o usuário o exclui manualmente. Recomendamos que você mantenha a configuração padrão de **Não**, apenas ativando essa configuração para aplicativos mais antigos que não compartilham cookies entre processos.

Para obter mais informações sobre os novos cookies, confira [Configurações de cookie para acessar aplicativos locais no Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD – janeiro de 2019

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em janeiro de 2019, adicionamos esses 35 novos aplicativos com suporte para Federação à galeria de aplicativos:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [Expiration Reminder](../saas-apps/expiration-reminder-tutorial.md), [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [Workable](../saas-apps/workable-tutorial.md), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [Visitly](../saas-apps/visitly-tutorial.md), [Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novas melhorias do Azure AD Identity Protection (versão prévia pública)

**Tipo:** Recurso alterado **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que adicionamos os seguintes aprimoramentos à oferta de versão prévia pública do Azure AD Identity Protection, incluindo:

- Uma interface do usuário mais integrada e atualizada

- APIs adicionais

- Avaliação de risco aprimorada por meio de aprendizado de máquina

- Alinhamento de todo o produto entre usuários arriscados e entradas arriscadas

Para obter mais informações sobre os aprimoramentos, confira [O que é o Azure Active Directory Identity Protection (atualizado)?](../identity-protection/overview-identity-protection.md) Para saber mais e compartilhar suas ideias por meio dos prompts no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Novo recurso de Bloqueio de Aplicativo para o aplicativo Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativo Microsoft Authenticator **:** Segurança de identidade e proteção

Para manter suas senhas únicas, informações do aplicativo e configurações de aplicativo mais seguras, você pode ativar o recurso de Bloqueio de Aplicativo no aplicativo Microsoft Authenticator. Ativar o Bloqueio de Aplicativo significa que você será solicitado a autenticar-se usando seu PIN ou biometria sempre que abrir o aplicativo Microsoft Authenticator.

Para obter mais informações, confira as [Perguntas frequentes do aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Funcionalidades de exportação aprimoradas do Azure AD PIM (Privileged Identity Management)

**Tipo:** Novo recurso **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Administradores do PIM (Privileged Identity Management) agora podem exportar todas as atribuições de função ativas e qualificadas para um recurso específico, incluindo atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico.

Para obter mais informações, confira [Exibir histórico de atividade e auditoria para funções de recurso do Azure no PIM](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>Novembro/dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Os usuários removidos do escopo de sincronização não alternam mais para contas somente nuvem

**Tipo:** Fixo **Categoria de serviço:** Gerenciamento de usuário **Capacidade do produto:** Diretório

>[!Important]
>Nós ouvimos e entendemos sua frustração devido a essa correção. Portanto, revertemos essa alteração até o momento em que possamos facilitar a correção, para que você possa implementar em sua organização.

Corrigimos um bug no qual o sinalizador DirSyncEnabled de um usuário seria alternado erroneamente para **Falso** quando o objeto de AD DS (Active Directory Domain Services) fosse excluído do escopo de sincronização e, em seguida, movido para a Lixeira no Azure Active Directory no ciclo de sincronização a seguir. Como resultado dessa correção, se o usuário for excluído do escopo de sincronização e, em seguida, restaurado da Lixeira do Azure AD, a conta do usuário permanecerá como sincronizada do AD local, conforme o esperado, e não poderá ser gerenciada na nuvem, já que a SoA (origem da autoridade) permanecerá como AD local.

Antes dessa correção, havia um problema quando o sinalizador DirSyncEnabled era alternado para Falso. Isso resultava na impressão errada de que essas contas eram convertidas em objetos somente nuvem e que as contas poderiam ser gerenciadas na nuvem. No entanto, as contas ainda retinham a SoA como propriedades locais e todas as propriedades sincronizadas (atributos de sombra) provenientes do AD local. Essa condição causou vários problemas no Azure AD e em outras cargas de trabalho de nuvem (como o Exchange Online) que esperavam tratar essas contas como sincronizadas do AD, mas agora se comportavam como contas somente nuvem.

No momento, a única maneira de converter verdadeiramente uma conta do AD de sincronizada para uma conta somente nuvem é desabilitando o DirSync no nível do locatário, que disparará uma operação de back-end para transferir a SoA. Esse tipo de alteração da SoA exige (mas não limita-se a) limpar todos os atributos relacionados locais (como LastDirSyncTime e atributos de sombra) e enviar um sinal para outras cargas de trabalho de nuvem para que o respectivo objeto também seja convertido em uma conta somente nuvem.

Essa correção, consequentemente, impede atualizações diretas no atributo ImmutableID de um usuário sincronizado do AD, que em alguns cenários no passado foram necessários. Por padrão, o ImmutableID de um objeto no Azure AD, como o nome indica, é imutável. Os novos recursos implementados no Azure AD Connect Health e no cliente de Sincronização do Azure AD Connect estão disponíveis para tratar esses cenários:

- **Atualização do ImmutableID em grande escala para muitos usuários em uma abordagem em etapas**

  Por exemplo, você precisa fazer uma migração longa entre florestas do AD DS. Solução: Use o Azure AD Connect para **Configurar Âncora de Origem** e, enquanto o usuário migra, copie os valores ImmutableID existentes do Azure Active Directory para o atributo ms-DS-Consistency-Guid do usuário local do AD DS da nova floresta. Para obter mais informações, consulte [Usar ms-DS-ConsistencyGuid como sourceAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações do ImmutableID em grande escala para muitos usuários em um único disparo**

  Por exemplo, ao implementar o Azure AD Connect, você comete um erro e agora precisa alterar o atributo SourceAnchor. Solução: Desabilite o DirSync no nível do locatário e limpe todos os valores ImmutableID inválidos. Para obter mais informações, consulte [Desativar a sincronização de diretório do Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Corresponder novamente o usuário local com um usuário existente no Azure AD** Por exemplo, um usuário que foi recriado no AD DS gera uma duplicata na conta do Azure AD, em vez de correspondê-la novamente com uma conta do Azure AD (objeto órfão) existente. Solução: Use o Azure AD Connect Health no portal do Azure para remapeamento da Âncora de Origem/ImmutableID. Para obter mais informações, consulte [Cenário de objeto órfão](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alteração da falha: Atualizações no esquema de auditoria e de logs de entrada por meio do Azure Monitor

**Tipo:** Recurso alterado **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

No momento, estamos publicando os fluxos de log de Auditoria e Entrada por meio do Azure Monitor, para que você possa integrar os arquivos de log às suas ferramentas SIEM ou ao Log Analytics. Com base em seus comentários e em preparação para o comunicado de disponibilidade geral desse recurso, estamos fazendo as alterações a seguir em nosso esquema. Essas alterações de esquema e suas atualizações de documentação relacionadas ocorrerão até a primeira semana de janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de Auditoria
Estamos adicionando um novo campo **Tipo de Operação**, para fornecer o tipo de operação executada no recurso. Por exemplo, **Adicionar**, **Atualizar** ou **Excluir**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de Auditoria
Os seguintes campos estão sendo alterados no esquema de Auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|Categoria|Esse era o campo **Nome do Serviço**. Agora ele passou a ser o campo **Categorias de Auditoria**. **Nome do Serviço** foi renomeado para o campo **loggedByService**.|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|<ul><li>Gerenciamento de Usuários</li><li>Gerenciamento de grupos</li><li>Gerenciamento de aplicativos</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicativo</li><li>Usuário</li><li>Agrupar</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o log de auditoria.|Nulo|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|
|Result|Fornece o resultado dos logs de auditoria. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sucesso</li><li>Falha</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de Entrada
Os seguintes campos estão sendo alterados no esquema de Entrada:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Esse era o campo **conditionalaccessPolicies**. Agora ele passou a ser o campo **appliedConditionalAccessPolicies**.|Nenhuma alteração|Nenhuma alteração|
|conditionalAccessStatus|Fornece o resultado do Status da Política de Acesso Condicional na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Falha</li><li>Não aplicado</li><li>Desabilitado</li></ul>|
|appliedConditionalAccessPolicies: resultado|Fornece o resultado do Status da Política de Acesso Condicional individual na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Falha</li><li>Não aplicado</li><li>Desabilitado</li></ul>|

Para obter mais informações sobre o esquema, confira [Interpretar o esquema de logs de auditoria do Azure AD no Azure Monitor (versão prévia)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias de Proteção de Identidade no modelo de machine learning supervisionado e no mecanismo de pontuação de risco

**Tipo:** Recurso alterado **Categoria de serviço:** Identity Protection **Capacidade do produto:** Pontuações de risco

As melhorias no mecanismo e avaliação de risco de entrada e de usuário relacionadas à Proteção de Identidade podem ajudar a melhorar a cobertura e a precisão de risco de usuário. Os administradores talvez observem que o nível de risco de usuário deixou de estar vinculado ao nível de risco de detecções específicas e que há um aumento no número e no nível de eventos de entrada de risco.

As detecções de risco agora são avaliadas pelo modelo de machine learning supervisionado, que calcula o risco de usuário usando recursos adicionais de entradas do usuário e um padrão de detecções. Com base nesse modelo, o administrador poderá encontrar usuários com pontuações de alto risco, mesmo se as detecções associadas a esse usuário forem de risco baixo ou médio.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir suas próprias senhas usando o aplicativo Microsoft Authenticator (versão prévia pública)

**Tipo:** Recurso alterado **Categoria de serviço:** Redefinição de senha de autoatendimento **Capacidade do produto:** Autenticação de usuário

Os administradores do Azure AD agora podem redefinir suas próprias senhas usando as notificações do aplicativo Microsoft Authenticator ou um código em qualquer aplicativo de autenticador móvel ou token de hardware. Para redefinir suas próprias senhas, os administradores agora poderão usar dois dos seguintes métodos:

- Notificação do aplicativo Microsoft Authenticator

- Outro aplicativo de autenticador móvel/código de token de hardware

- Email

- chamada telefônica

- mensagem de texto

Para obter mais informações sobre como usar o aplicativo Microsoft Authenticator para redefinir senhas, confira [Redefinição de senha de autoatendimento do Azure AD – aplicativo móvel e SSPR (versão prévia)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função Administrador de Dispositivo de Nuvem do Azure AD (versão prévia pública)

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento e registro de dispositivos **Capacidade do produto:** Controle de acesso

Os administradores podem atribuir usuários à nova função Administrador de Dispositivo de Nuvem para executar tarefas de administrador de dispositivo de nuvem. Os usuários que recebem a função Administradores de Dispositivo de Nuvem podem habilitar, desabilitar e excluir dispositivos no Azure AD, além de poder ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure.

Para obter mais informações sobre funções e permissões, confira [Atribuindo funções de administrador no Azure Active Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerenciar seus dispositivos usando o novo carimbo de data/hora de atividade no Azure AD (versão prévia pública)

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento e registro de dispositivos **Capacidade do produto:** Gerenciamento do Ciclo de Vida do Dispositivo

Percebemos que, ao longo do tempo, você precisa atualizar e desativar os dispositivos de suas organizações no Azure Active Directory, para evitar a presença de dispositivos obsoletos no ambiente. Para ajudar com esse processo, o Azure AD agora atualiza seus dispositivos com um novo carimbo de data/hora de atividade, ajudando você a gerenciar o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e usar esse carimbo de data/hora, confira [Como: Gerenciar os dispositivos obsoletos no Azure AD](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os usuários aceitem os termos de uso em cada dispositivo

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Governança

Os administradores agora podem ativar a opção **Exigir o consentimento dos usuários em todos os dispositivos** para exigir que os usuários aceitem os termos de uso em todos os dispositivos que estiverem usando em seu locatário.

Para obter mais informações, confira a [seção de termos de uso por dispositivo do recurso de termos de uso do Azure Active Directory](../conditional-access/terms-of-use.md#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar os termos de uso para que expirem com base em um agendamento recorrente

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Governança


Os administradores agora podem ativar a opção **Expirar consentimentos** para fazer com que os termos de uso expirem para todos os usuários com base no agendamento recorrente especificado. O agendamento pode ser anual, semestral, trimestral ou mensalmente. Depois que os termos de uso expirarem, os usuários precisarão aceitá-los novamente.

Para obter mais informações, confira a [seção Adicionar termos de uso do recurso de termos de uso do Azure Active Directory](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar os termos de uso para que expirem com base no agendamento de cada usuário

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Governança

Os administradores agora podem especificar uma duração na qual o usuário precisam aceitar novamente os termos de uso. Por exemplo, os administradores podem especificar que os usuários precisam aceitar novamente os termos de uso a cada 90 dias.

Para obter mais informações, confira a [seção Adicionar termos de uso do recurso de termos de uso do Azure Active Directory](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos emails do Azure AD PIM (Privileged Identity Management) para funções do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Os clientes que usam o Azure AD PIM (Privileged Identity Management) agora podem receber um email de resumo semanal, incluindo as seguintes informações para os últimos sete dias:

- Visão geral sobre as principais atribuições de função qualificadas e permanentes

- Número de usuários que ativaram funções

- Número de usuários atribuídos a funções no PIM

- Número de usuários atribuídos a funções fora do PIM

- Número de usuários que passaram a ser "permanentes" no PIM

Para obter mais informações sobre o PIM e as notificações por email disponíveis, confira [Notificações por email no PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo agora está disponível

**Tipo:** Recurso alterado **Categoria de serviço:** Outros **Capacidade do produto:** Diretório

O licenciamento baseado em grupo está fora da versão prévia pública e agora está disponível. Como parte dessa versão geral, tornamos esse recurso mais escalonável e adicionamos a capacidade de reprocessar as atribuições de licenciamento baseado em grupo para um único usuário e a capacidade de usar o licenciamento baseado em grupo com licenças do Office 365 E3/A3.

Para obter mais informações sobre o licenciamento baseado em grupo, confira [O que é o licenciamento baseado em grupo no Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novos Aplicativos Federados disponíveis na galeria de aplicativos do Azure AD – novembro de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em novembro de 2018, adicionamos estes 26 novos aplicativos com suporte de Federação à galeria de aplicativos:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [Gra-Pe](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [Infinite Campus](../saas-apps/infinitecampus-tutorial.md), [Alaya](https://alayagood.com), [HeyBuddy](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [Drift](../saas-apps/drift-tutorial.md), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](../saas-apps/everbridge-tutorial.md), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [Plex Apps – Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps – UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/) e [CRAFTS – Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os Logs do Azure AD agora funcionam com o Azure Log Analytics (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Temos o prazer de anunciar que agora você pode encaminhar seus logs do Azure AD ao Azure Log Analytics! Esse recurso mais solicitados ajuda dão acesso ainda melhor a análise para seu negócio, operações e segurança, bem como uma maneira de ajudar a monitorar sua infra-estrutura. Para obter mais informações, consulte o [logs de atividades de diretório do Azure Active Directory no Azure Log Analytics agora disponível](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - outubro de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em outubro de 2018, adicionamos esses 14 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Meus Pontos de Prêmio](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [BorrowBox](../saas-apps/borrowbox-tutorial.md), Teclado de discagem, [ON24 Ambiente Virtual](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [Controle do Workspot](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações por Email de serviços de domínio do AD do Azure

**Tipo:** Novo recurso **Categoria de serviço:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Os Serviços de Domínio do Azure AD fornecem alertas no portal do Azure sobre configurações incorretas ou problemas com o domínio gerenciado. Esses alertas incluem guias passo a passo para que você possa tentar corrigir os problemas sem precisar entrar em contato com o suporte.

A partir de outubro, você poderá personalizar as configurações de notificação do seu domínio gerenciado para que, quando novos alertas ocorrerem, um e-mail seja enviado a um grupo designado de pessoas, eliminando a necessidade de verificar constantemente o portal em busca de atualizações.

Para obter mais informações, consulte [Configurações de notificação nos Serviços de Domínio do AD do Azure](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD portal dá suporte ao uso do domínio ForceDelete API para excluir os domínios personalizados

**Tipo:** Recurso alterado **Categoria de serviço:** Gerenciamento de diretório **Capacidade do produto:** Diretório

Temos o prazer de anunciar que agora você pode usar o domínio ForceDelete API para excluir nomes de domínios personalizados renomeando referências, como usuários, grupos e aplicativos de seu nome de domínio personalizado (contoso.com) volta para o (de nome de domínio padrão inicial de forma assíncrona Contoso.onmicrosoft.com).

Essa alteração ajuda você a excluir mais rapidamente seus nomes de domínio personalizados se sua organização não usar mais o nome ou se você precisar usar o nome de domínio com outro AD do Azure.

Para obter mais informações, consulte [Excluir um nome de domínio personalizado](../enterprise-users/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de função de administrador atualizadas para grupos dinâmicos

**Tipo:** Fixo **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Corrigimos um problema para que funções de administrador específicas agora possam criar e atualizar regras de associação dinâmica, sem a necessidade de ser o proprietário do grupo.

As funções são:

- Administrador global

- Administrador do Intune

- Administrador de usuários

Para obter mais informações, consulte [Criar um grupo dinâmico e verificar o status](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Definições de configuração simplificada de SSO (logon único) para alguns aplicativos de terceiros

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Sabemos que configurar SSO (logon único) para aplicativos SaaS (software como serviço) pode ser um desafio devido à natureza específica de cada configuração de aplicativo. Criamos uma experiência de configuração simplificada para preencher automaticamente as definições de configuração de SSO para os seguintes aplicativos SaaS de terceiros:

- Zendesk

- ArcGIS Online

- Jamf Pro

Para começar a usar essa experiência de um único clique, acesse a página do **portal do Azure** > **configuração de SSO** do aplicativo. Para obter mais informações, consulte [integração de aplicativos SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Página: Azure Active Directory – Onde seus dados estão localizados?

**Tipo:** Novo recurso **Categoria de serviço:** Outros **Capacidade do produto:** GoLocal

Selecione a região da sua empresa na página **Azure Active Directory – Onde seus dados estão localizados** para exibir qual datacenter do Azure armazena os dados em repouso do Azure AD de todos os seus serviços do Azure AD. Você pode filtrar as informações por serviços específicos do Azure AD da região da sua empresa.

Para acessar esse recurso e obter mais informações, consulte [Azure Active Directory – Onde seus dados estão localizados](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implantação disponível para o painel de acesso Meus Aplicativos

**Tipo:** Novo recurso **Categoria de serviço:** Meus Aplicativos **Capacidade do produto:** SSO

Confira o novo plano de implantação que está disponível para o painel de acesso Meus Aplicativos (https://aka.ms/deploymentplans).
O painel de acesso Meus Aplicativos fornece aos usuários um único lugar para localizar e acessar os aplicativos. Esse portal também fornece aos usuários oportunidades de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciamento de acesso a esses recursos em nome de outros.

Para obter mais informações, consulte [O que é o portal Meus Aplicativos?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nova guia de Solução de problemas e Suporte na página de entradas de Logs do portal do Azure

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

A nova guia **Solução de problemas e Suporte** na página **Entradas** do portal do Azure serve para ajudar administradores e engenheiros de suporte a solucionar problemas relacionados a entradas do Azure AD. Esta nova guia oferece o código de erro, a mensagem de erro e as recomendações de correção (caso haja alguma) para ajudar a resolver o problema. Se você não conseguir resolver o problema, também oferecemos uma nova maneira de criar um tíquete de suporte usando a experiência de **Copiar para a área de transferência**, que preenche os campos **ID da Solicitação** e **Data (UTC)** do arquivo de log em seu tíquete de suporte.

![Logs de entrada mostrando a nova guia](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte aprimorado para propriedades de extensão personalizadas usadas para criar regras de associação dinâmica

**Tipo:** Recurso alterado **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

Com essa atualização, agora você pode clicar no link **Obter propriedades de extensão personalizadas** no construtor de regra dinâmico de grupo dos usuários, inserir sua ID do aplicativo exclusiva e receber a lista completa das propriedades de extensão personalizadas para usar ao criar uma regra de associação dinâmica para usuários. Essa lista também pode ser atualizada para obter as novas propriedades de extensão personalizadas do aplicativo.

Para obter mais informações sobre como usar propriedades de extensão personalizadas para regras de associação dinâmica, consulte [Propriedades de extensão e propriedades de extensão personalizadas](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

**Tipo:** Plano para alteração **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, consulte:

- [Acesso condicional com base no aplicativo do Azure Active Directory](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para redefinição de senha de autoatendimento da tela de bloqueio do Windows 7/8/8.1

**Tipo:** Novo recurso **Categoria de serviço:** SSPR **Capacidade do produto:** Autenticação de usuário

Depois de configurar esse novo recurso, os usuários verão um link para redefinir a senha da tela de **bloqueio** de um dispositivo que executa o Windows 7, Windows 8 ou Windows 8.1. Ao clicar nesse link, o usuário é guiado pelo mesmo fluxo de redefinição de senha que no navegador da Web.

Para obter mais informações, consulte [Como habilitar a redefinição de senha do Windows 7, 8 e 8.1](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização não ficarão mais disponíveis para reutilização

**Tipo:** Plano para alteração **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – setembro de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em setembro de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Software de Recrutamento Comeet](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Snowflake](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO for Azure, SurveyMonkey, [Convene](../saas-apps/convene-tutorial.md), [dmarcian](../saas-apps/dmarcian-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Suporte para métodos de transformações de declarações adicionais

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Apresentamos os novos métodos de transformação de declaração, ToLower () e ToUpper (), que podem ser aplicados aos tokens SAML por meio da página baseada em SAML **Configuração de logon único**.

Para obter mais informações, consulte [Como personalizar declarações emitidas no token SAML para aplicativos empresariais no Azure AD](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interface do usuário atualizada da configuração de aplicativo baseado em SAML (versão prévia)

**Tipo:** Recurso alterado **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Como parte da nossa interface do usuário atualizada de configuração de aplicativo baseado em SAML, você obterá:

- Uma experiência atualizada de explicação passo a passo para configurar seus aplicativos baseados em SAML.

- Maior visibilidade sobre o que está ausente ou incorreto em sua configuração.

- A capacidade de adicionar vários endereços de email para notificação de expiração de certificado.

- Novos métodos de transformação de declaração, ToLower () e ToUpper () e muito mais.

- Uma forma de carregar seu próprio token de assinatura de certificado para aplicativos empresariais.

- Uma maneira de definir o formato NameID para aplicativos SAML e uma maneira de definir o valor NameID como Extensões de diretório.

Para ativar este modo de exibição atualizado, clique no link **Experimentar nossa nova experiência** na parte superior da página **Logon único**. Para saber mais, confira [Tutorial: Configurar o logon único baseado em SAML para um aplicativo com o Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Agosto de 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Alterações em intervalos de endereços IP do Azure Active Directory

**Tipo:** Plano para alteração **Categoria de serviço:** Outros **Capacidade do produto:** Plataforma

Estamos introduzindo intervalos de IP maiores no Azure AD, o que significa que, se você tiver configurado intervalos de endereços IP do Azure AD para firewalls, roteadores ou grupos de segurança de rede, será necessário atualizá-los. Estamos fazendo essa atualização para que você não tenha que alterar as configurações do intervalo IP, do roteador ou do grupo de segurança de rede novamente quando o Azure AD adicionar novos pontos de extremidade.

O tráfego de rede está se movendo para esses novos intervalos nos próximos dois meses. Para continuar com o serviço ininterrupto, você deve adicionar esses valores atualizados aos seus endereços IP antes de 10 de setembro de 2018:

- 20.190.128.0/18

- 40.126.0.0/18

É altamente recomendável não remover os intervalos de endereços IP antigos até que todo o tráfego da rede seja movido para os novos intervalos. Para obter atualizações sobre a movimentação e saber quando você pode remover os intervalos antigos, consulte [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização não ficarão mais disponíveis para reutilização

**Tipo:** Plano para alteração **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gerenciamento de informações de segurança convergida para SSPR (senha de autoatendimento) e MFA (Autenticação Multifator do Microsoft Azure)

**Tipo:** Novo recurso **Categoria de serviço:** SSPR **Capacidade do produto:** Autenticação de usuário

Esse novo recurso ajuda as pessoas a gerenciar suas informações de segurança (por exemplo, número de telefone, aplicativo móvel e assim por diante) para SSPR e MFA em um único local e a experiência; anteriormente, isso era feito em dois locais diferentes.

Essa experiência convergida também funciona para pessoas que usam SSPR ou MFA. Além disso, se sua organização não impõe registro de MFA ou SSPR, as pessoas ainda podem registrar métodos de informações de segurança de MFA ou SSPR permitidos pela sua organização no portal Meus aplicativos.

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado ou para todos os usuários em um locatário. Para obter mais informações sobre a experiência convergida, consulte o [Blog da experiência convergida](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nova configuração de cookies somente HTTP em aplicativos do proxy de aplicativo do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

Há uma nova configuração chamada **Cookies somente HTTP** em seus aplicativos de proxy de aplicativo. Essa configuração ajuda a fornecer segurança adicional, pois inclui o sinalizador HTTPOnly no cabeçalho de resposta HTTP para os cookies de sessão e acesso de proxy de aplicativo, impede o acesso ao cookie de um script do lado do cliente e também impede ações como copiar ou modificar o cookie. Embora esse sinalizador não tenha sido usado anteriormente, os cookies sempre foram criptografados e transmitidos usando uma conexão TLS para ajudar a proteger de modificações inadequadas.

Essa configuração não é compatível com aplicativos que usam os controles ActiveX, como Área de Trabalho Remota. Caso esteja nessa situação, é recomendável desativar essa configuração.

Para obter mais informações sobre a configuração Cookies somente HTTP, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>O PIM (Privileged Identity Management) para recursos do Azure dá suporte aos tipos de recurso do grupo de gerenciamento

**Tipo:** Novo recurso **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Agora, as configurações de ativação e atribuição Just-In-Time podem ser aplicadas aos tipos de recursos de Grupo de Gerenciamento, como você já faz para Assinaturas, Grupos de Recursos e Recursos (tais como VMs, Serviços de Aplicativos e muito mais). Além disso, qualquer pessoa com uma função que conceda acesso de administrador para um Grupo de Gerenciamento pode descobrir e gerenciar tal recurso no PIM.

Para obter mais informações sobre o PIM e os recursos do Azure, consulte [Descobrir e gerenciar recursos do Azure usando o Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>O acesso do aplicativo (versão prévia) fornece um acesso mais rápido ao portal do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Hoje, ao ativar uma função usando o PIM, as permissões podem levar mais de 10 minutos para entrar em vigor. Se você optar por usar o acesso do aplicativo, que está em visualização pública no momento, os administradores poderão acessar o portal do Azure AD assim que a solicitação de ativação for concluída.

Atualmente, o acesso do aplicativo dá suporte apenas à experiência do portal do Azure AD e aos recursos do Azure. Para obter mais informações sobre o PIM e o acesso do Aplicativo, confira [O que é o Privileged Identity Management do Azure AD?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – agosto de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em agosto de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Hornbill](../saas-apps/hornbill-tutorial.md), [Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), [Sauce Labs - Mobile and Web Testing](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [Meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md), [Way We Do](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [ProMaster (por Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [Dossier](../saas-apps/dossier-tutorial.md), [N2F - Expense reports](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>O suporte nativo ao Tableau já está disponível no Proxy de Aplicativo do Azure AD

**Tipo:** Recurso alterado **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

Com nossa atualização do OpenID Connect para o protocolo OAuth 2.0 Code Grant para nosso protocolo de pré-autenticação, você não precisa mais fazer nenhuma configuração adicional para usar o Tableau com Proxy de Aplicativo. Essa alteração de protocolo também ajuda o Proxy de Aplicativo a oferecer um suporte melhor a aplicativos mais modernos usando apenas redirecionamentos de HTTP, que normalmente recebem suporte em marcações HTML e JavaScript.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Novo suporte para adicionar o Google como provedor de identidade para usuários convidados B2B no Azure Active Directory (versão prévia)

**Tipo:** Novo recurso **Categoria de serviço:** B2B **Capacidade do produto:** B2B/B2C

Ao configurar a federação com o Google em sua organização, você pode permitir que os usuários convidados do Gmail entrem em seus aplicativos e recursos compartilhados usando as respectivas contas do Google existentes, sem precisar criar uma MSA (Conta Microsoft) pessoal ou uma conta do Azure AD.

Esta é uma visualização pública opt-in. Para obter mais informações sobre a federação do Google, consulte [Adicionar o Google como provedor de identidade para usuários convidados B2B](../external-identities/google-federation.md).

---

## <a name="july-2018"></a>Julho de 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Melhorias nas notificações por email do Azure Active Directory

**Tipo:** Recurso alterado **Categoria de serviço:** Outros **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

Os emails do Active Directory do Azure (Azure AD) agora apresentam um design atualizado, bem como alterações no endereço de email do remetente e no nome de exibição do remetente, quando enviados dos seguintes serviços:

- Revisões de acesso do Azure AD
- Azure AD Connect Health
- Azure AD Identity Protection
- Gerenciamento de identidades com privilégios do AD do Azure
- Notificação de certificado expirando do Aplicativo Enterprise
- Notificações do serviço de provisionamento do Aplicativo Enterprise

As notificações por email serão enviadas a partir do seguinte endereço de email e nome de exibição:

- Endereço de email: azure-noreply@microsoft.com
- Nome de exibição: Microsoft Azure

Para obter um exemplo de alguns dos novos designs de email e mais informações, consulte [Notificações por email no PIM do Azure AD](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Os Logs de atividade do Azure AD agora estão disponíveis por meio do Azure Monitor

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Os Logs de atividade do Azure AD agora estão disponíveis na visualização pública do Monitor do Azure (serviço de monitoramento da plataforma do Azure). O Monitor do Azure oferece retenção a longo prazo e integração perfeita, além dessas melhorias:

- Retenção de longo prazo roteando seus arquivos de log para sua própria conta de armazenamento do Azure.

- Integração perfeita do SIEM, sem exigir que você escreva ou mantenha scripts personalizados.

- Integração perfeita com suas próprias soluções personalizadas, ferramentas de análise ou soluções de gerenciamento de incidentes.

Para obter mais informações sobre esses novos recursos, consulte o [log de atividades do Azure AD no diagnóstico do Monitor do Azure agora na visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e nossa documentação, [logs de atividades do Azure Active Directory no Azure Monitor (visualização)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informações de acesso condicional adicionadas ao relatório de entradas do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Segurança de identidade e proteção

Esta atualização permite ver quais políticas são avaliadas quando um usuário faz login junto com o resultado da política. Além disso, o relatório agora inclui o tipo de aplicativo cliente usado pelo usuário, para que você possa identificar o tráfego de protocolo herdado. Agora, as entradas de relatório também podem ser pesquisadas em busca de um ID de correlação, que pode ser encontrado na mensagem de erro do usuário, e pode ser usado para identificar e solucionar problemas da solicitação de entrada correspondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Veja autenticações herdadas por meio dos logs de atividades de Entradas

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Com a introdução do campo **Client App** nos logs de atividades de login, os clientes agora podem ver usuários que estão usando autenticações herdadas. Os clientes poderão acessar essas informações usando a API do Microsoft Graph de entradas ou por meio dos logs de atividades de entrada no portal do Azure Active Directory, onde você pode usar o controle **Aplicativo Cliente** para filtrar as autenticações herdadas. Confira a documentação para obter mais detalhes.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - julho de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em julho de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Innovation Hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [Certos SSO de administrador](../saas-apps/certainadminsso-tutorial.md), PSUC Staging, [iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [Screencast-O-Matic](../saas-apps/screencast-tutorial.md) , PowerSchool Unified Classroom, [Eli Onboarding](../saas-apps/elionboarding-tutorial.md), [Suporte Remoto Bomgar](../saas-apps/bomgarremotesupport-tutorial.md), [Nimblex](../saas-apps/nimblex-tutorial.md), [ImagineVisualizar Web](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Base de Habilidades](../saas-apps/skillsbase-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Novas integrações de aplicativos SaaS para provisionamento de usuários - julho de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Integração de terceiros

O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como Dropbox, Salesforce, ServiceNow e muito mais. Para julho de 2018, adicionamos o suporte ao aprovisionamento de usuários aos seguintes aplicativos na galeria de aplicativos do Azure AD:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na Galeria do Azure AD, consulte [integração de aplicativos SaaS ao Azure Active Directory](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Conecte o Health for Sync - Uma maneira mais fácil de corrigir erros de sincronização de atributos órfãos e duplicados

**Tipo:** Novo recurso **Categoria de serviço:** AD Connect **Capacidade do produto:** Monitoramento e relatório

O Azure AD Connect Health apresenta a correção de autoatendimento para ajudá-lo a destacar e corrigir erros de sincronização. Esse recurso soluciona erros de sincronização de atributos duplicados e corrige objetos que são órfãos do AD do Azure. Este diagnóstico tem os seguintes benefícios:

- Diminui os erros de sincronização de atributos duplicados, fornecendo correções específicas

- Aplica uma correção para cenários dedicados do AD do Azure, resolvendo erros em uma única etapa

- Nenhuma atualização ou configuração é necessária para ativar e usar esse recurso

Para obter mais informações, consulte [Diagnosticar e corrigir erros de sincronização de atributos duplicados](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Atualizações visuais nas experiências de entrada do Azure AD e MSA

**Tipo:** Recurso alterado **Categoria de serviço:** Azure Active Directory **Capacidade do produto:** Autenticação de usuário

Atualizamos a interface do usuário para a experiência de login de serviços on-line da Microsoft, como no Office 365 e no Azure. Essa alteração torna as telas menos confusas e mais simples. Para obter mais informações sobre essa alteração, consulte as [próximas melhorias no blog de experiência de logon do Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nova versão do Azure AD Connect - julho de 2018

**Tipo:** Recurso alterado **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

A versão mais recente do Azure AD Connect inclui:

- Correções de bugs e atualizações de capacidade de suporte

- Disponibilidade geral da integração federada de Ping

- Atualizações para o cliente mais recente do SQL 2012

Para obter mais informações sobre essa atualização, confira [Azure AD Connect: Histórico de lançamento de versões](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Atualizações da interface do usuário final dos termos de uso

**Tipo:** Recurso alterado **Categoria de serviço:** Termos de uso **Capacidade do produto:** Governança

Estamos atualizando a sequência de aceitação na interface do usuário final do TOU.

**Texto atual.** Para acessar os recursos do [tenantName], você deve aceitar os termos de uso.<br>**Novo texto.** Para acessar o recurso [tenantName], você deve ler os termos de uso.

**Texto atual:** A escolha de aceitação significa que você concorda com todos os termos de uso acima.<br>**Novo texto:** Clique em Aceitar para confirmar que leu e compreendeu os termos de uso.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>A autenticação de passagem dá suporte a aplicativos e protocolos herdados

**Tipo:** Recurso alterado **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

A autenticação de passagem agora suporta protocolos e aplicativos legados. As limitações a seguir agora são totalmente suportadas:

- Logins de usuário para aplicativos cliente legados do Office, Office 2010 e Office 2013, sem exigir autenticação moderna.

- Acesso ao compartilhamento de calendário e informações livres/ocupadas nos ambientes híbridos do Exchange somente no Office 2010.

- Entradas de usuário para aplicativos cliente do Skype for Business sem a necessidade de autenticação moderna.

- Entradas de usuário no PowerShell versão 1.0.

- O Programa de Inscrição de Dispositivos da Apple (Apple DEP), usando o Assistente de Configuração do iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gerenciamento de informações de segurança convergida para redefinição de senha de autoatendimento e a autenticação multifator

**Tipo:** Novo recurso **Categoria de serviço:** SSPR **Capacidade do produto:** Autenticação de usuário

Esse novo recurso permite que os usuários gerenciem suas informações de segurança (por exemplo, número de telefone, endereço de email, aplicativo móvel etc.) para redefinição de senha de autoatendimento (SSPR) e autenticação de vários fatores (MFA) em uma única experiência. Os usuários não precisarão mais registrar as mesmas informações de segurança para SSPR e MFA em duas experiências diferentes. Essa nova experiência também se aplica a usuários que possuem SSPR ou MFA.

Se uma organização não estiver aplicando o registro de MFA ou SSPR, os usuários poderão registrar suas informações de segurança no portal **Meus aplicativos**. A partir daí, os usuários podem registrar qualquer método habilitado para MFA ou SSPR.

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado de usuários ou todos os usuários em um locatário.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Use o aplicativo Microsoft Authenticator para verificar sua identidade quando você redefinir sua senha

**Tipo:** Recurso alterado **Categoria de serviço:** SSPR **Capacidade do produto:** Autenticação de usuário

Esse recurso permite que não administradores verifiquem sua identidade ao redefinir uma senha usando uma notificação ou código do Microsoft Authenticator (ou qualquer outro aplicativo autenticador). Depois que os administradores ativarem esse método de redefinição de senha de autoatendimento, os usuários que tiverem registrado um aplicativo móvel por meio de aka.ms/mfasetup ou aka.ms/setupsecurityinfo poderão usar o aplicativo móvel como um método de verificação ao redefinir a senha.

A notificação de aplicativos para dispositivos móveis só pode ser ativada como parte de uma política que exige dois métodos para redefinir sua senha.

---

## <a name="june-2018"></a>Junho de 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Aviso de alteração: Correção de segurança para o fluxo de autorização delegada para aplicativos que usam a API de Logs de Atividades do Azure AD

**Tipo:** Plano para alteração **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Devido a nossa aplicação de segurança mais forte, foi necessário fazer uma alteração nas permissões para aplicativos que usam um fluxo de autorização delegado para acessar [APIs dos logs de atividades do Azure Active Directory](../reports-monitoring/concept-reporting-api.md). Essa alteração ocorrerá até **26 de junho de 2018**.

Se algum dos aplicativos usar APIs do log de atividades do Azure Active Directory, siga estas etapas para garantir que o aplicativo não seja interrompido após a alteração.

**Para atualizar as permissões do aplicativo**

1. Entre no portal do Azure, selecione **Active Directory do Azure**, e em seguida, selecione **Registros de Aplicativos**.
2. Selecione seu aplicativo que usa a API de Logs de Atividade do Azure AD, selecione **Configurações**, selecione **Permissões necessárias**, e em seguida, selecione a **API do Windows Azure Active Directory**.
3. Na área **Permissões delegadas** da folha **Habilitar acesso**, selecione a caixa ao lado de **Ler dados de diretório** e, em seguida, selecione **Salvar**.
4. Selecione **Conceder permissões** e, em seguida, selecione **Sim**.

    >[!Note]
    >Você deve ser um administrador Global para conceder permissões ao aplicativo.

Para obter mais informações, consulte a área [ Conceder permissões ](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) dos Pré-requisitos para acessar o artigo da API de relatórios do Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Definir configurações de TLS para se conectar aos serviços do Azure AD para conformidade com o PCI DSS

**Tipo:** Novo recurso **Categoria de serviço:** N/D **Capacidade do produto:** Plataforma

O TLS (Transport Layer Security) é um protocolo que fornece privacidade e integridade de dados entre dois aplicativos de comunicação e é o protocolo de segurança mais utilizado atualmente.

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) determinou que as primeiras versões de TLS e Secure Sockets Layer (SSL) devem ser desativadas em favor de permitir protocolos de aplicativos novos e mais seguros, com conformidade a partir de **30 de junho de 2018**. Essa alteração significa que, se você se conectar aos serviços do Azure AD e exigir conformidade com o PCI DSS, deverá desabilitar o TLS 1.0. Várias versões do TLS estão disponíveis, mas o TLS 1.2 é a versão mais recente disponível para os Serviços do Active Directory do Azure. É altamente recomendável mover diretamente para o TLS 1.2 para as combinações cliente / servidor e navegador / servidor.

Os navegadores desatualizados podem não suportar versões mais recentes do TLS, como o TLS 1.2. Para ver quais versões do TLS são suportadas pelo seu navegador, acesse o site [Qualys SSL Labs](https://www.ssllabs.com/) e clique em **Test your browser**. Recomendamos que você atualize para a versão mais recente do seu navegador da Web e, de preferência, ative somente o TLS 1.2.

**Para ativar o TLS 1.2, pelo navegador**

- **Microsoft Edge e Internet Explorer (ambos são definidos usando o Internet Explorer)**

    1. Abra o Internet Explorer, selecione **Ferramentas** > **Opções da Internet**  >  **Avançadas**.
    2. Na área **Segurança**, selecione **use TLS 1.2** e, em seguida, selecione **OK**.
    3. Feche todas as janelas do navegador e reinicie o Internet Explorer.

- **Google Chrome**

    1. Abra o Google Chrome, digite *chrome: // settings /* na barra de endereço e pressione **Enter**.
    2. Expanda as opções **Avançadas**, vá para a área **System** e selecione **Open proxy settings**.
    3. Na caixa **Propriedades da Internet**, selecione a guia **Avançado**, vá para a área **Segurança**, selecione **use TLS 1.2** e selecione **OK**.
    4. Feche todas as janelas do navegador e reinicie o Google Chrome.

- **Mozilla Firefox**

    1. Abra o Firefox, digite *about: config* na barra de endereços e, em seguida, pressione **Enter**.
    2. Pesquise pelo termo *TLS* e selecione a entrada **security.tls.version.max**.
    3. Defina o valor para **3** para forçar o navegador a usar a versão TLS 1.2 e, em seguida, selecione **OK**.

        >[!NOTE]
        >A versão 60.0 do Firefox suporta o TLS 1.3, portanto você também pode definir o valor de security.tls.version.max como **4**.

    4. Feche todas as janelas do navegador e reinicie o Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - junho de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em junho de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Skytap](../saas-apps/skytap-tutorial.md), [Liquidação de música](../saas-apps/settlingmusic-tutorial.md), [SAML 1.1 Aplicativo de LOB habilitado para tokens](../saas-apps/saml-tutorial.md), [Supermood](../saas-apps/supermood-tutorial.md), [Autotask](../saas-apps/autotaskendpointbackup-tutorial.md), [Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint local](../saas-apps/sharepoint-on-premises-tutorial.md), [ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [ChronicX](../saas-apps/chronicx-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>A proteção de senha do Azure AD está disponível na visualização pública

**Tipo:** Novo recurso **Categoria de serviço:** Identity Protection **Capacidade do produto:** Autenticação de usuário

Use a proteção de senha do Azure AD para ajudar a eliminar senhas fáceis de adivinhar do seu ambiente. A eliminação dessas senhas ajuda a diminuir o risco de comprometimento de um tipo de ataque de spray de senha.

Especificamente, o Azure AD Password Protection ajuda você a:

- Proteja as contas da sua organização no AD do Azure e no Active Directory (AD) do Windows Server.
- Impede que os usuários usem senhas em uma lista com mais de 500 das senhas mais usadas e mais de 1 milhão de variações de substituição de caracteres dessas senhas.
- Administre a Proteção de Senha do Azure AD a partir de um único local no portal do Azure AD, tanto para o Azure AD quanto para o Windows Server AD local.

Para obter mais informações sobre o Azure AD Password Protection, consulte [Eliminar senhas incorretas em sua organização](../authentication/concept-password-ban-bad.md).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "todos os convidados" criado durante a criação dos termos de uso

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Governança

Durante a criação dos seus termos de uso, um novo modelo de política de acesso condicional também é criado para "todos os convidados" e "todos os aplicativos". Esse novo modelo de política aplica os ToU recém-criados, simplificando o processo de criação e execução para os convidados.

Para obter mais informações, consulte [Recurso de Termos de Uso do Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "personalizado" criado durante a criação dos termos de uso

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Governança

Durante a criação dos seus termos de uso, um novo modelo de política de acesso condicional “personalizado” também é criado. Esse novo modelo de política permite criar o ToU e ir imediatamente para a folha de criação de política de acesso condicional, sem a necessidade de navegar manualmente pelo portal.

Para obter mais informações, consulte [Recurso de Termos de Uso do Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Diretrizes novas e abrangentes sobre a implantação da autenticação multifator do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Outros **Capacidade do produto:** Segurança de identidade e proteção

Lançamos uma nova orientação passo a passo sobre como implantar a MFA (autenticação multifator) do Azure AD em sua organização.

Para visualizar o guia de implantação do MFA, vá para o repositório [Guias de Implantação de Identidade](./active-directory-deployment-plans.md) no GitHub. Para fornecer feedback sobre os guias de implantação, use o [formulário de Feedback do Plano de Implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>As funções de gerenciamento de aplicativos delegados do Azure AD estão em pré-visualização pública

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Controle de acesso

Os administradores agora podem delegar tarefas de gerenciamento de aplicativos sem atribuir a função de administrador global. As novas funções e capacidades são:

- **Novas funções administrativas padrão do AD do Azure:**

    - **Administrador do Aplicativo.** Concede a capacidade de gerenciar todos os aspectos de todos os aplicativos, incluindo registro, configurações de SSO, atribuições de aplicativos e licenciamento, configurações de proxy de aplicativo e consentimento (exceto para recursos do Azure AD).

    - **Administrador do aplicativo na nuvem.** Concede todas as habilidades de Administrador do Aplicativo, exceto para o proxy de Aplicativo, porque ele não fornece acesso local.

    - **Desenvolvedor de aplicativos.** Concede a capacidade de criar registros de aplicativos, mesmo se a opção **permitir que os usuários registrem aplicativos** seja desativada.

- **Propriedade (configure o registro por aplicativo e o aplicativo por empresa, semelhante ao processo de propriedade do grupo:**

    - **Proprietário do registro do aplicativo.** Concede a capacidade de gerenciar todos os aspectos do registro de aplicativos de propriedade, incluindo o manifesto do aplicativo e a adição de outros proprietários.

    - **Proprietário do aplicativo corporativo.** Concede a capacidade de gerenciar muitos aspectos de aplicativos corporativos próprios, incluindo configurações de SSO, designações de aplicativos e consentimento (exceto para recursos do Azure AD).

Para obter mais informações sobre a visualização pública, consulte as [funções de gerenciamento de aplicativos delegados do Azure AD em pré-visualização pública!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Para obter mais informações sobre funções e permissões, consulte [Atribuindo funções de administrador no Active Directory do Azure](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Maio de 2018

### <a name="expressroute-support-changes"></a>Alterações de suporte do ExpressRoute

**Tipo:** Plano para alteração **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Plataforma

A oferta de Software como Serviço, como Azure AD (Azure Active Directory), foi projetada para funcionar melhor passando diretamente pela Internet, sem a necessidade do ExpressRoute ou de qualquer outro túnel VPN privado. Por isso, em **1º de agosto de 2018**, deixaremos de dar suporte ao ExpressRoute para serviços do Azure AD usando emparelhamento público do Azure e comunidades do Azure no emparelhamento da Microsoft. Quaisquer serviços afetados por essa alteração podem observar o tráfego do Azure AD alternando gradualmente do ExpressRoute para a Internet.

Enquanto estamos alterando nosso suporte, também sabemos que ainda há situações em que você poderá precisar usar um conjunto dedicado de circuitos para o tráfego de autenticação. Por esse motivo, o Azure AD continuará dando suporte a restrições de intervalo de IP por locatário usando o ExpressRoute e serviços já em emparelhamento da Microsoft com a comunidade "Outros serviços online do Office 365". Se seus serviços forem afetados, mas você precisar do ExpressRoute, deverá fazer o seguinte:

- **Se estiver no emparelhamento público do Azure.** Vá para a Microsoft peering e inscreva-se na comunidade **Outros serviços do Office 365 Online (12076: 5100)** . Para obter mais informações sobre como passar do emparelhamento público do Azure para emparelhamento da Microsoft, consulte o artigo [Mover um emparelhamento público para o emparelhamento da Microsoft](../../expressroute/how-to-move-peering.md).

- **Se estiver no emparelhamento da Microsoft.** Inscreva-se na comunidade **Other Office 365 Online service (12076: 5100)** . Para obter mais informações sobre requisitos de roteamento, consulte a seção [Suporte comunidades BGP](../../expressroute/expressroute-routing.md#bgp) do artigo de requisitos de roteamento ExpressRoute.

Se você precisar continuar usando circuitos dedicados, fale com a equipe da sua conta da Microsoft sobre como obter autorização para usar a comunidade **Outros serviços online do Office 365 (12076:5100)** . O quadro de revisão gerenciado do MS Office verificará se você precisará desses circuitos e se reconhece as implicações técnicas de mantê-los. Assinaturas não autorizadas tentando criar filtros de rota para o Office 365 receberão uma mensagem de erro.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>APIs do Microsoft Graph para cenários administrativos para TOU

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Experiência do desenvolvedor

Adicionamos APIs do Microsoft Graph para a operação de administração dos termos de uso do Azure Active Directory. Você pode criar, atualizar e excluir o objeto termos de uso.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Adicionar ponto de extremidade multilocatário do Azure AD como um provedor de identidade no Azure AD B2C

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Agora, usando políticas personalizadas, você pode adicionar o ponto de extremidade comum do Azure AD como um provedor de identidade no Azure AD B2C. Isso permite que você tenha um único ponto de entrada para todos os usuários do Azure AD que estão entrando em seus aplicativos. Para obter mais informações, confira [Azure Active Directory B2C: Permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Use URLs internas para acessar os aplicativos de qualquer lugar com nossa Extensão de Entrada Meus Aplicativos e o Proxy de Aplicativo do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Meus Aplicativos **Capacidade do produto:** SSO

Os usuários agora podem acessar aplicativos por meio de URLs internas, mesmo de fora da rede corporativa, usando a Extensão Logon Protegido dos Meus aplicativos para Azure AD. Isso funcionará com qualquer aplicativo que você tenha publicado usando o Proxy de Aplicativo do Azure AD, em qualquer navegador que também tenha a extensão de navegador do Painel de Acesso instalada. A funcionalidade de redirecionamento de URL é habilitada automaticamente quando um usuário faz logon na extensão. A extensão está disponível para fazer o download no [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Dados na Europa para clientes europeus

**Tipo:** Novo recurso **Categoria de serviço:** Outros **Capacidade do produto:** GoLocal

Os clientes na Europa exigem que seus dados permaneçam na Europa e não sejam replicados fora de datacenters europeus para o cumprimento da legislação europeia sobre privacidade. Este [artigo](./active-directory-data-storage-eu.md) fornece detalhes específicos sobre quais informações de identidade serão armazenadas na Europa e também sobre as informações que serão armazenadas fora dos datacenters europeus.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Novo usuário provisionando integrações de aplicativo SaaS – maio de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Integração de terceiros

O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como Dropbox, Salesforce, ServiceNow e muito mais. Em maio de 2018, adicionamos suporte ao provisionamento de usuários para os seguintes aplicativos na Galeria de aplicativo do Azure AD:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na Galeria do Azure AD, confira [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md).

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>O Azure AD acessa revisões de grupos e o acesso do aplicativo agora fornece revisões recorrentes

**Tipo:** Novo recurso **Categoria de serviço:** Revisões de acesso **Capacidade do produto:** Governança

A revisão de acesso de grupos e aplicativos agora está geralmente disponível como parte do Azure AD Premium P2.  Os administradores poderão configurar revisões de acesso de membros de grupos e atribuições de aplicativo para que ocorram automaticamente em intervalos regulares, como mensal ou trimestralmente.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Os logs de atividades do Azure AD (entradas e auditoria) agora estão disponíveis por meio do MS Graph

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Os logs de atividade do Azure Active Directory, que incluem logs de entradas e de auditoria, agora estão disponíveis por meio da API do Microsoft Graph. Expusemos dois pontos de extremidade por meio da API do Microsoft Graph para acessar esses logs. Confira nossos [documentos](../reports-monitoring/concept-reporting-api.md) para acesso programático às APIs de relatórios do Azure AD para começar.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Aprimoramentos à experiência de resgate B2B e deixar uma organização

**Tipo:** Novo recurso **Categoria de serviço:** B2B **Capacidade do produto:** B2B/B2C

**Resgate Just-In-Time:** Depois que você compartilha um recurso com um usuário convidado usando a API de B2B, não precisa enviar um email de convite especial. Na maioria dos casos, o usuário convidado pode acessar o recurso e será levado até a experiência de resgate a tempo. Chega de consequências devido a emails não recebidos. Chega de perguntar aos convidados: "você clicou no link de resgate que o sistema enviou?". Isso significa que quando o SPO usa o gerenciador de convites, os anexos de nuvem podem ter a mesma URL canônica para todos os usuários, internos e externos, em qualquer estado do resgate.

**Experiência de resgate moderna:** Diga adeus à página de aterrissagem de resgate em tela dividida. Os usuários verão uma experiência moderna de consentimento com a declaração de privacidade da organização convidativa, assim como fazem para aplicativos de terceiros.

**Usuários convidados podem deixar a organização:** Depois que uma relação de usuário com uma organização terminar, eles poderão deixar a organização usando autoatendimento. Não é mais necessário chamar o administrador da organização para “ser removido”; chega de criar tíquetes de suporte.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - maio de 2018

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em maio de 2018, adicionamos esses 18 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty Govern, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [Envi MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md), [Montage Online](../saas-apps/montageonline-tutorial.md), [まなびポケット](../saas-apps/manabipocket-tutorial.md), OpenReel, [Arc Publishing - SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [Riskware](../saas-apps/riskware-tutorial.md), [Flock](../saas-apps/flock-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Novos guias de implantação passo a passo para o Active Directory do Azure

**Tipo:** Novo recurso **Categoria de serviço:** Outros **Capacidade do produto:** Diretório

Novas orientações passo a passo sobre como implantar o Azure Active Directory (AD do Azure), incluindo redefinição de senha por autoatendimento (SSPR), logon único (SSO), acesso condicional, proxy de aplicativo, provisionamento de usuário, Serviços de Federação do Active Directory (AD FS) (ADFS) para autenticação de passagem (PTA) e ADFS para sincronização de hash de senha (PHS).

Para visualizar os guias de implantação, acesse o repositório [Guias de Implantação de Identidade](./active-directory-deployment-plans.md) no GitHub. Para fornecer feedback sobre os guias de implantação, use o [formulário de Feedback do Plano de Implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Pesquisa de Aplicativos Empresariais – Carregar Mais Aplicativos

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Problemas para localizar seus aplicativos/entidades de serviço? Adicionamos a capacidade de carregar mais aplicativos em sua lista Aplicativos corporativos – todos os aplicativos. Por padrão, mostramos 20 aplicativos. Agora você pode clicar em **Carregar mais** para visualizar aplicativos adicionais.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>O lançamento de maio do AADConnect contém uma prévia pública da integração com PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas e ótimas novas ferramentas de solução de problemas.

**Tipo:** Recurso alterado **Categoria de serviço:** AD Connect **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

O lançamento de maio do AADConnect contém uma prévia pública da integração com PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas e ótimas novas ferramentas de solução de problemas. Você pode encontrar as notas de versão [aqui](../hybrid/reference-connect-version-history.md).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Revisões de acesso do Azure AD: aplicação automática

**Tipo:** Recurso alterado **Categoria de serviço:** Revisões de acesso **Capacidade do produto:** Governança

As revisões de acesso de grupos e aplicativos agora estão disponíveis como parte do Azure AD Premium P2. Um administrador pode configurar para aplicar as alterações do revisor automaticamente a esse grupo ou aplicativo quando a revisão de acesso for concluída. O administrador também pode especificar o que acontece com o acesso continuado do usuário se os revisores não responderem, removerem o acesso, manterem o acesso ou receberem recomendações do sistema.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Os tokens de ID não podem ser mais retornados usando a consulta response_mode para novos aplicativos.

**Tipo:** Recurso alterado **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Os aplicativos criados em ou após 25 de abril de 2018 não poderão mais solicitar um **id_token** usando a **consulta** response_mode.  Isso coloca o Azure AD de acordo com as especificações de OIDC e ajuda a reduzir a superfície de ataque dos aplicativos.  Aplicativos criados antes de 25 de abril de 2018 não são impedidos de usar a **consulta** response_mode com um response_type de **id_token**.  O erro retornado ao solicitar uma id_token do Azure AD é **AADSTS70007: ' Query ' não é um valor com suporte de ' response_mode ' ao solicitar um token**.

O **fragmento** e **form_post** response_modes continuam funcionando - ao criar novos objetos de aplicativo (por exemplo, para uso do App Proxy), garanta o uso de um desses response_modes antes que eles criem um novo aplicativo.

---

## <a name="april-2018"></a>Abril de 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Os Tokens de Acesso do Azure AD B2C estão disponíveis ao público

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Agora você pode acessar APIs da Web protegidas pelo Azure AD B2C usando tokens de acesso. O recurso está mudando da versão prévia pública para GA. A experiência da interface do usuário para configurar aplicativos do Azure AD B2C e APIs da Web foi aprimorada e outras pequenas melhorias foram feitas.

Para obter mais informações, confira [Azure AD B2C: Solicitando tokens de acesso](../../active-directory-b2c/access-tokens.md).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testar configuração de logon único para aplicativos baseados em SAML

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Ao configurar aplicativos SSO baseados em SAML, você pode testar a integração na página de configuração. Se você encontrar um erro durante o login, poderá fornecer o erro na experiência de teste e o Azure AD fornecerá etapas de resolução para resolver o problema específico.

Para obter mais informações, consulte:

- [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](../manage-apps/view-applications-portal.md)
- [Como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Os termos de uso do Azure Active Directory agora têm relatórios por usuário

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Conformidade

Agora os administradores podem selecionar um Termo de Uso determinado e ver todos os usuários que consentiram com ele e em que data/hora isso aconteceu.

Para obter mais informações, consulte o [Azure AD terms of use feature](../conditional-access/terms-of-use.md) (Recurso de termos de uso do Azure AD).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP de risco para proteção de bloqueio de extranet do AD FS

**Tipo:** Novo recurso **Categoria de serviço:** Outros **Capacidade do produto:** Monitoramento e relatório

Agora o Connect Health é compatível com a capacidade de detectar endereços IP que excedem um limite de logons U/P com falha por hora ou diariamente. As funcionalidades fornecidas por esse recurso são:

- Relatório completo que mostra o endereço IP e o número de logons com falha gerados por hora/diariamente com limite personalizável.
- Alertas de email que mostram quando um endereço IP específico excedeu o limite de logons U/P com falha por hora/diariamente.
- Uma opção de download para fazer uma análise detalhada dos dados

Para obter mais informações, consulte [Risky IP Report](../hybrid/how-to-connect-health-adfs.md) (Relatório IP arriscado).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Configuração fácil do aplicativo com arquivos ou URL de metadados

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

Na página aplicativos empresariais, os administradores podem carregar um arquivo de metadados SAML para configurar o logon baseado em SAML para a galeria do Azure AD e o aplicativo inexistente na galeria.

Além disso, é possível usar a URL de metadados de federação do aplicativo Azure AD para configurar o SSO com o aplicativo direcionado.

Para obter mais informações, consulte [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Azure Active Directory](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Agora os Termos de uso do Azure AD já estão disponíveis

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Conformidade


Os termos de uso do Azure Active Directory foram movidos da visualização pública para geralmente disponíveis.

Para obter mais informações, consulte o [Azure AD terms of use feature](../conditional-access/terms-of-use.md) (Recurso de termos de uso do Azure AD).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para usuários B2B de organizações específicas

**Tipo:** Novo recurso **Categoria de serviço:** B2B **Capacidade do produto:** B2B/B2C


Agora é possível especificar com quais organizações parceiras você deseja compartilhar e colaborar na Colaboração B2B do Azure AD. Para fazer isso, é possível optar por criar a lista de permissão ou negação de domínios específica. Quando um domínio é bloqueado usando essas funcionalidades, os funcionários não podem mais enviar convites para pessoas nesse domínio.

Isso ajuda a controlar o acesso aos recursos e permite uma experiência perfeita para usuários aprovados.

Esse recuso de Colaboração B2B está disponível para todos os clientes do Azure Active Directory e pode ser usado juntamente com os recursos do Azure AD Premium como acesso condicional e proteção de identidade para oferecer um controle mais granular de quando e como os usuários empresariais externos entram e obtêm acesso.

Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](../external-identities/allow-deny-list.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em abril de 2018, adicionamos esses 13 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

Critério HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (on-premises)](../saas-apps/secretserver-on-premises-tutorial.md), [Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [Organograma Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [appneta performance desempenho monitor](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), prateleira, [SafetyNET](../saas-apps/safetynet-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Permitir acesso a seus aplicativos locais a usuários B2B no Azure AD (versão prévia pública)

**Tipo:** Novo recurso **Categoria de serviço:** B2B **Capacidade do produto:** B2B/B2C

Como uma organização que usa recursos de colaboração B2B do Microsoft Azure AD (Azure Active Directory) para convidar usuários convidados de organizações parceiras para o Microsoft Azure AD, agora é possível fornecer a esses usuários B2B acesso a aplicativos locais. Esses aplicativos locais podem usar a autenticação baseada em SAML ou a IWA (Autenticação Integrada do Windows) com KCD (delegação restrita de Kerberos).

Para obter mais informações, consulte [Conceda aos usuários B2B no acesso do Azure AD aos seus aplicativos locais](../external-identities/hybrid-cloud-to-on-premises.md).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Obter tutoriais de integração de SSO do Azure Marketplace

**Tipo:** Recurso alterado **Categoria de serviço:** Outros **Capacidade do produto:** Integração de terceiros

Se um aplicativo listado no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) for compatível com o logon único baseado em SAML, clicar em **Obter agora** fornecerá o tutorial de integração associado a esse aplicativo.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Desempenho mais rápido do provisionamento automático de usuários do Azure AD para aplicativos SaaS

**Tipo:** Recurso alterado **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Integração de terceiros

Anteriormente, os clientes que usavam os conectores de provisionamento de usuário do Azure Active Directory para aplicativos SaaS (por exemplo, Salesforce, ServiceNow e Box) poderiam ter um desempenho lento se os inquilinos do Azure AD contivessem mais de 100.000 usuários e grupos combinados e estivessem usando atribuições de usuário e grupo determinar quais usuários devem ser provisionados.

Em 2 de abril de 2018, aprimoramentos de desempenho significativos foram implantados no serviço de provisionamento do Azure AD que reduzem muito o tempo necessário para executar as sincronizações iniciais entre o Active Directory do Azure e os aplicativos de SaaS de destino.

Como resultado, muitos clientes que tinham sincronizações iniciais para aplicativos que demoravam dias ou nunca eram concluídas, agora estão sendo concluídas em questão de minutos ou horas.

Para obter mais informações, consulte [What happens during provisioning?](../..//active-directory/app-provisioning/how-provisioning-works.md) (O que acontece durante o provisionamento?)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Redefinição de senha de autoatendimento da tela de bloqueio do Windows 10 para computadores associados ao Azure AD híbrido

**Tipo:** Recurso alterado **Categoria de serviço:** Redefinição de senha de autoatendimento **Capacidade do produto:** Autenticação de usuário

Atualizamos o recurso SSPR do Windows 10 para incluir suporte para computadores ingressados no Azure AD híbridos. Esse recurso está disponível no Windows 10 RS4 e permite que os usuários redefinam sua senha da tela de bloqueio de um computador Windows 10. Os usuários habilitados e registrados para redefinição de senha de autoatendimento podem usar esse recurso.

Para obter mais informações, consulte [Redefinição de senha do Azure AD na tela de logon](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>Março de 2018

### <a name="certificate-expire-notification"></a>Notificação de expiração de certificado

**Tipo:** Fixo **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

O Azure AD envia uma notificação quando um certificado para um aplicativo que está na galeria ou não está prestes a expirar.

Alguns usuários não recebiam as notificações para aplicativos corporativos configurados para SSO baseado em SAML. Esse problema foi resolvido. O Azure AD envia uma notificação sobre certificados que expiram em 7, 30 e 60 dias. Você pode ver esse evento nos logs de auditoria.

Para obter mais informações, consulte:

- [Gerenciar certificados para logon único federado no Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Audit activity reports in the Azure Active Directory portal (Relatórios de atividades de auditoria no portal do Azure Active Directory)](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provedores de identidade do Twitter e do GitHub no Azure AD B2C

**Tipo:** Novo recurso **Categoria de serviço:** B2C - Gerenciamento de identidade do consumidor **Capacidade do produto:** B2B/B2C

Agora você pode adicionar o Twitter ou o GitHub como um provedor de identidade no Azure AD B2C. O Twitter está mudando da visualização pública para GA. O GitHub está sendo lançado em visualização pública.

Para saber mais, confira [O que é a colaboração B2B do Azure AD?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Restringir o acesso do navegador usando Intune Managed Browser com acesso condicional com base no aplicativo do Azure Active Directory para iOS e Android

**Tipo:** Novo recurso **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

**Agora, em visualização pública!**

**SSO do Intune Managed Browser:** Seus funcionários podem usar o logon único em clientes nativos (como o Microsoft Outlook) e o Intune Managed Browser para todos os aplicativos conectados ao Azure AD.

**Suporte ao Acesso Condicional do Intune Managed Browser:** Agora você pode exigir que os funcionários usem o Intune Managed Browser usando políticas de acesso condicional baseadas no aplicativo.

Leia mais sobre isso em nossa [postagem no blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Para obter mais informações, consulte:

- [Configurar acesso condicional com base no aplicativo](../conditional-access/app-based-conditional-access.md)

- [Configurar políticas do navegador gerenciado](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlets do proxy de aplicativo no módulo GA do PowerShell

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Controle de acesso

O suporte para cmdlets de Proxy de Aplicativo agora estão no Módulo GA do PowerShell! Isso exige que você permaneça atualizado nos módulos do PowerShell - se você ficar mais de um ano atrasado, alguns cmdlets poderão parar de funcionar.

Para saber mais, veja [AzureAD](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Os clientes nativos do Office 365 têm suporte do SSO Contínuo usando um protocolo não interativo

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

O usuário que utiliza clientes nativos do Office 365 (versão 16.0.8730.xxxx e acima) obtém uma experiência de logon silencioso usando o SSO Contínuo. Esse suporte é fornecido com a adição de um protocolo não interativo (WS-Trust) ao Azure AD.

Para saber mais, confira [Como entrar em um cliente nativo com o trabalho de SSO contínuo?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Os usuários obtêm uma experiência de logon silenciosa, com o SSO contínuo, se um aplicativo envia solicitações de entrada para os terminais de inquilino do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Os usuários terão uma experiência de logon silenciosa, com SSO contínuo, se um aplicativo (por exemplo, `https://contoso.sharepoint.com`) enviar solicitações de entrada para pontos de extremidade com locatários do Microsoft Azure AD, ou seja, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>`, em vez de enviar para o ponto de extremidade comum do Azure AD (`https://login.microsoftonline.com/common/<...>`).

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>É preciso adicionar apenas uma URL do Azure AD, em vez de duas URLs como anteriormente, às configurações de zona de Intranet dos usuários para implementar o SSO Contínuo

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Para implementar o SSO contínuo para seus usuários, você deve adicionar apenas uma URL do Azure Active Directory às configurações de zona da Intranet dos usuários usando a política de grupo no Active Directory: `https://autologon.microsoftazuread-sso.com`. Anteriormente, era necessário que os clientes adicionassem duas URLs.

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em março de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant by FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), Vtiger CRM, inwink, [Amplitude](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>O PIM para Recursos do Azure está disponível

**Tipo:** Novo recurso **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Se você estiver usando o Azure AD Privileged Identity Management para funções de diretório, agora poderá usar o acesso com limite de tempo do PIM e os recursos de atribuição para funções de recurso do Azure como Assinaturas, Grupos de Recursos, Máquinas Virtuais e qualquer outro recurso com suporte pelo Azure Resource Manager. Imponha a Autenticação Multifator ao ativar funções Just-In-Time e agende as ativações em coordenação com os períodos de alterações aprovadas. Além disso, esta versão adiciona aprimoramentos não disponíveis durante a visualização pública, incluindo uma interface do usuário atualizada, fluxos de trabalho de aprovação e a capacidade de estender funções que expirarão em breve e renovar funções expiradas.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (Versão Prévia)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Adicionar declarações opcionais aos tokens de aplicativos (visualização pública)

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

O aplicativo Azure AD agora pode solicitar declarações personalizadas ou opcionais em tokens SAML ou JWTs.  Essas são as declarações sobre o usuário ou locatário que não são incluídas por padrão no token, devido a restrições de tamanho ou aplicabilidade.  Isso está atualmente em visualização pública para aplicativos Azure AD nos pontos de extremidade v1.0 e v2.0.  Confira a documentação para saber mais sobre quais declarações podem ser adicionadas e como editar o manifesto do aplicativo para solicitá-las.

Para saber mais, confira [Declarações opcionais no Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>O Azure AD dá suporte a PKCE para fluxos de OAuth mais seguros

**Tipo:** Novo recurso **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Os documentos do Azure AD foram atualizados para indicar o suporte para PKCE, que permite a comunicação mais segura durante o fluxo de concessão de Código de Autorização do OAuth 2.0.  Code_challenges S256 e o texto sem formatação têm suporte nos pontos de extremidade v1.0 e v2.0.

Para saber mais, confira [Solicitar um código de autorização](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>O suporte para o provisionamento de todos os valores de atributo de usuário está disponível na API Workday Get_Workers

**Tipo:** Novo recurso **Categoria de serviço:** Provisionamento de aplicativo **Capacidade do produto:** Integração de terceiros

A visualização pública do provisionamento de entrada do Workday para Active Directory e Azure AD agora dá suporte à capacidade de extrair e provisionar todos os valores de atributo disponíveis na API Workday Get_Workers. Isso inclui suporte a centenas de atributos padrão e personalizados adicionais, além daqueles fornecidos na versão inicial do conector de provisionamento de entrada Workday.

Para obter mais informações, consulte: [Personalizando a lista de atributos de usuário do Workday](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Alterar a associação de grupo de dinâmica para estática e vice-versa

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de grupos **Capacidade do produto:** Colaboração

É possível alterar como a associação é gerida em um grupo. Isso é útil quando você quer manter o mesmo ID e nome de grupo no sistema, então, todas as referências existentes no grupo ainda serão válidas; criar um novo grupo exigiria atualizar essas referências.
Atualizamos o centro de administração do Azure AD para oferecer suporte a essa funcionalidade. Agora, os clientes podem converter grupos existentes de associação dinâmica para associação atribuída e vice-versa. Os cmdlets do PowerShell existentes também ainda estão disponíveis.

Para obter mais informações, consulte [Regras de associação dinâmica para grupos no Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Comportamento aprimorado de saída com o SSO contínuo

**Tipo:** Recurso alterado **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

Anteriormente, mesmo que os usuários saíssem explicitamente de um aplicativo protegido pelo Azure AD, seriam conectados automaticamente de novo usando o SSO Contínuo se tentassem acessar um aplicativo Azure AD novamente na rede corporativa por meio de seus dispositivos associados ao domínio. Com essa alteração, há suporte para sair.  Isso permite que os usuários escolham a mesma conta do Azure AD ou outra para entrar novamente, em vez de serem conectados automaticamente usando o SSO contínuo.

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>Versão de conector de proxy de aplicativo 1.5.402.0 lançado

**Tipo:** Recurso alterado **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Segurança de identidade e proteção

Esta versão do conector está sendo distribuída gradualmente até novembro. Essa nova versão do conector inclui as seguintes alterações:

- O conector agora define cookies no nível do domínio em vez do nível do subdomínio. Isso garante uma experiência de SSO sem problemas e evita prompts de autenticação redundantes.
- Suporte para solicitações de codificação em partes
- Monitoramento de integridade de conector aprimorado
- Várias correções de bug e melhorias de estabilidade

Para saber mais, veja [Noções básicas sobre conectores de proxy de aplicativo Azure AD](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Fevereiro de 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Navegação aprimorada para gerenciar usuários e grupos

**Tipo:** Plano para alteração **Categoria de serviço:** Gerenciamento de diretório **Capacidade do produto:** Diretório

A experiência de navegação para gerenciar usuários e grupos foi simplificada. Agora, é possível navegar da visão geral do diretório diretamente para a lista de todos os usuários, com acesso mais fácil à lista dos usuários excluídos. Adicionalmente, você também pode navegar da visão geral do diretório diretamente para a lista de todos os grupos, com acesso mais fácil às configurações de gerenciamento de grupo. Além disso, a partir da página de visão geral do diretório, é possível procurar um usuário, grupo, aplicativo empresarial ou registro do aplicativo.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilidade de inscrições e relatórios de auditoria no Microsoft Azure operado pela 21Vianet (parceiro 21Vianet do Azure na China)

**Tipo:** Novo recurso **Categoria de serviço:** Azure Stack **Capacidade do produto:** Monitoramento e relatório

Relatórios de log de atividades do Azure Active Directory já estão disponíveis no Microsoft Azure operado pelas instâncias 21Vianet (parceiro 21Vianet do Azure na China). Os logs a seguir estão incluídos:

- **Logs de atividades de entradas** - Inclui todos os logs de entradas associados ao locatário.

- **Logs de auditoria de senha de autoatendimento** - Inclui todos os logs de auditoria SSPR.

- **Logs de auditoria de gerenciamento de diretório** - Inclui todos os registros de auditoria relacionados ao gerenciamento de diretório, como gerenciamento de usuários, gerenciamento de aplicativos e outros.

Com esses logs, é possível obter informações sobre como o ambiente está funcionando. Os dados fornecidos permitem a você:

- Determinar como os aplicativos e serviços serão utilizados pelos usuários.

- Solucionar problemas que impedem a conclusão dos trabalhos pelos usuários.

Para obter mais informações sobre como usar esses relatórios, consulte [Relatórios do Azure Active Directory](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Usar a função "Leitor do Relatório" (função não administrador) para exibir relatórios de atividades do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Relatório **Capacidade do produto:** Monitoramento e relatório

Como parte do feedback dos clientes para permitir que funções não administrativas tenham acesso aos logs de atividades do Azure Active Directory, habilitamos a capacidade de usuários que estão na função "Report Reader" acessarem as atividades de Entradas e Auditoria no portal do Azure, bem como usando a API do Microsoft Graph.

Para obter mais informações sobre como usar esses relatórios, consulte [Relatórios do Azure Active Directory](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Declaração EmployeeID disponível como atributo de usuário e identificador de usuário

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** SSO

É possível configurar **EmployeeID** como o identificador de usuário e atributo de usuário para usuários membros e usuários B2B em aplicativos de logon com base em SAML da interface do usuário do aplicativo Enterprise.

Para obter mais informações, consulte [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gerenciamento de aplicativo simplificado usando caracteres curingas no Proxy de Aplicativo do AD

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Autenticação de usuário

Para tornar a implantação de aplicativo mais fácil e reduzir a sobrecarga administrativa, agora podemos dar suporte à capacidade de publicar aplicativos usando caracteres curingas. Para publicar um aplicativo curinga, você pode seguir o fluxo de publicação de aplicativo padrão, mas usar um caractere curinga nas URLs internas e externas.

Para obter mais informações, consulte [Aplicativos curinga no proxy de aplicativo do Azure Active Directory](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Novos cmdlets para dar suporte à configuração do Proxy de Aplicativo

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Plataforma

A versão mais recente do módulo de visualização do AzureAD PowerShell contém novos cmdlets que permitem que os clientes configurem aplicativos de proxy de aplicativo usando o PowerShell.

Os novos cmdlets são:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Novos cmdlets para dar suporte à configuração de grupos

**Tipo:** Novo recurso **Categoria de serviço:** Proxy de aplicativo **Capacidade do produto:** Plataforma

A versão mais recente do módulo AzureAD PowerShell contém cmdlets para gerenciar grupos no Azure Active Directory. Esses cmdlets já estavam disponíveis no módulo AzureADPreview e agora adicionados ao módulo AzureAD

Os cmdlets de Grupo agora liberados para disponibilidade geral são:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Uma nova versão do Azure AD Connect está disponível

**Tipo:** Novo recurso **Categoria de serviço:** AD Sync **Capacidade do produto:** Plataforma

O Azure AD Connect é a ferramenta preferencial para sincronizar dados entre o Azure Active Directory e nas fontes de dados locais, incluindo Windows Server Active Directory e LDAP.

>[!Important]
>Essa compilação apresenta alterações de regras de sincronização e esquema. O Serviço de Sincronização do Azure AD Connect dispara as etapas de Importação Completa e Sincronização Completa após uma atualização. Para obter informações sobre como alterar esse comportamento, consulte [Como adiar a sincronização completa após a atualização](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Essa versão contém as alterações e atualizações a seguir:

**Problemas corrigidos**

- Correção da janela de tempo nas tarefas em segundo plano da página de Filtragem de Partições ao comutar para a próxima página.

- Corrigido um bug que causou violação de Acesso durante a ação personalizada do ConfigDB.

- Corrigido um bug para recuperar do tempo limite de conexão do SQL.

- Corrigido um bug onde os certificados com curingas SAN falharam no teste de pré-requisito.

- Correção de um bug que causa miiserver.exe falha durante a exportação do Azure AD Connector.

- Corrigido um bug em que uma tentativa de senha incorreta entrou no DC ao ser executada fez com que o assistente do Azure AD Connect alterasse a configuração

**Novos recursos e aprimoramentos**

- Telemetria de aplicativos - Os administradores podem ativar/desativar essa classe de dados.

- Dados de Integridade do Azure Active Directory - Os administradores devem visitar o portal de integridade para controlar as configurações de integridade. Quando a política de atendimento for alterada, os agentes farão a leitura e irão impor a política.

- Adicionadas ações de configuração de write-back de dispositivo e uma barra de progresso para inicialização de página.

- Diagnósticos gerais aprimorados com relatório HTML e coleta de dados completa em um relatório HTML/ZIP-Text.

- Aperfeiçoada confiabilidade da atualização automática e adicionada a telemetria adicional para garantir que a integridade do servidor possa ser determinada.

- Restringir permissões disponíveis para contas privilegiadas na conta do AD Connector. Para novas instalações, o assistente restringe as permissões que as contas privilegiadas tenham na conta MSOL, após criar a conta MSOL. As alterações afetam instalações expressas e instalações personalizadas com a conta de Criação Automática.

- Alterado o instalador para não exigir privilégio de SA na instalação limpa do AADConnect.

- Novo utilitário para solucionar problemas de sincronização para um objeto específico. Atualmente, o utilitário verifica os seguintes itens:

    - UserPrincipalName incompatível entre o objeto de usuário sincronizado e a conta de usuário no Locatário do Azure Active Directory.

    - Se o objeto for filtrado da sincronização devido à filtragem de domínio

    - Se o objeto for filtrado da sincronização devido à filtragem da OU (unidade organizacional)

- Novo utilitário para sincronizar o hash de senha atual armazenado no Active Directory local para uma conta de usuário específica. O utilitário não exige uma alteração de senha.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplicativos com suporte a políticas de Proteção de Aplicativo do Intune adicionadas para uso com o acesso condicional baseado em aplicativo do Azure Active Directory

**Tipo:** Recurso alterado **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Adicionamos mais aplicativos com suporte para acesso condicional baseado em aplicativo. Agora, é possível acessar o Office 365 e outros aplicativos de nuvem conectados ao Azure Active Directory usando esses aplicativos clientes aprovados.

As seguintes aplicações serão adicionadas até o final de fevereiro:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Acesso condicional com base no aplicativo do Azure Active Directory](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Termos de uso atualizados para experiência móvel

**Tipo:** Recurso alterado **Categoria de serviço:** Termos de uso **Capacidade do produto:** Conformidade

Quando os termos de uso são exibidos, você pode clicar em **Tendo problemas com o modo de exibição? Clique aqui**. Clicar nesse link, abre os termos de uso nativamente no seu dispositivo. Independentemente do tamanho da fonte no documento ou do tamanho da tela do dispositivo, é possível ampliar e ler o documento, conforme necessário.

---

## <a name="january-2018"></a>Janeiro de 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em janeiro de 2018, os seguintes novos aplicativos com suporte à federação foram adicionados à galeria de aplicativos:

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md), [Software de Gerenciamento de Privacidade OneTrust](../saas-apps/onetrust-tutorial.md), [Dealpath](../saas-apps/dealpath-tutorial.md), [Diretório Federado IriusRisk e [NetBenefits de Fidelidade](../saas-apps/fidelitynetbenefits-tutorial.md).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="sign-in-with-additional-risk-detected"></a>Entre com risco adicional detectado

**Tipo:** Novo recurso **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

O insight obtido para uma detecção de risco está vinculado à sua assinatura do Azure Active Directory. Com a edição Azure AD Premium P2, você obtém as informações mais detalhadas sobre todas as detecções subjacentes.

Com o Azure AD Premium P1 Edition, as detecções que não são cobertas por sua licença aparecem como a detecção de risco Entrada com risco adicional detectado.

Para obter mais informações, confira [Detecções de risco do Azure Active Directory](../identity-protection/overview-identity-protection.md).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ocultar aplicativos do Office 365 dos painéis de acesso do usuário final

**Tipo:** Novo recurso **Categoria de serviço:** Meus Aplicativos **Capacidade do produto:** SSO

Agora você pode gerenciar melhor como os aplicativos do Office 365 são exibidos nos painéis de acesso do usuário por meio de uma nova configuração de usuário. Essa opção é útil para reduzir o número de aplicativos nos painéis de acesso de um usuário, se você preferir mostrar apenas os aplicativos do Office no portal do Office. A configuração está localizada nas **Configurações do usuário** e está marcada como **Os usuários só podem ver os aplicativos do Office 365 no portal do Office 365**.

Para obter mais informações, consulte [Ocultar um aplicativo da experiência de um usuário no Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Entrada direta em aplicativos habilitados para SSO de Senha diretamente na URL do aplicativo

**Tipo:** Novo recurso **Categoria de serviço:** Meus Aplicativos **Capacidade do produto:** SSO

A extensão do navegador Meus Aplicativos agora está disponível por meio de uma ferramenta conveniente que fornece a funcionalidade de logon único de Meus Aplicativos como um atalho no navegador. Após a instalação, o usuário verá um ícone de waffle no navegador que fornece acesso rápido aos aplicativos. Os usuários agora podem usufruir dos seguintes benefícios:

- A capacidade de entrar diretamente em aplicativos baseados em senha com SSO a partir da página de entrada do aplicativo
- Iniciar um aplicativo usando o recurso de pesquisa rápida
- Atalhos para os aplicativos usados recentemente da extensão
- A extensão está disponível para Microsoft Edge, Chrome e Firefox.

Para obter mais informações, consulte [Extensão de Entrada Segura dos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A experiência de administração do Azure AD no Portal Clássico do Azure foi retirada

**Tipo:** Preterido **Categoria de serviço:** Azure Active Directory **Capacidade do produto:** Diretório

A partir de 8 de janeiro de 2018, a experiência de administração do Azure AD no portal clássico do Azure foi desativada. Isso ocorreu em conjunto com a desativação do próprio portal clássico do Azure. No futuro, você deve usar o [centro de administração do Azure AD](https://aad.portal.azure.com) para toda a administração baseada em portal do AD do Azure.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>O portal da Web do PhoneFactor foi desativado

**Tipo:** Preterido **Categoria de serviço:** Azure Active Directory **Capacidade do produto:** Diretório

A partir de 8 de janeiro de 2018, o portal da Web do PhoneFactor foi desativado. Este portal foi usado para a administração do servidor MFA, mas essas funções foram movidas para o portal do Azure em portal.azure.com.

A configuração do MFA está localizada em: **Azure Active Directory \> Servidor do MFA**

---

### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** Preterido **Categoria de serviço:** Relatório **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades


Com a disponibilidade geral do novo Azure Active Directory, um console de Administração e novas APIs agora estão disponíveis para relatórios de atividade e de segurança, as APIs de relatório no ponto de extremidade “/reports” foram desativadas em 31 de dezembro de 2017.

**O que está disponível?**

Como parte da transição para o novo console de administrador, disponibilizamos duas novas APIs para recuperar os Logs de Atividades do Azure AD. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de detecção de risco do Identity Protection do Microsoft Graph.

Para obter mais informações, consulte:

- [Introdução à API de relatórios do Azure Active Directory](../reports-monitoring/concept-reporting-api.md)

- [Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Dezembro de 2017

### <a name="terms-of-use-in-the-access-panel"></a>Termos de uso no Painel de Acesso

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Conformidade

Agora você pode acessar o Painel de Acesso e exibir os termos de uso que aceitou anteriormente.

Siga estas etapas:

1. Acesse o [portal do MyApps](https://myapps.microsoft.com) e entre.

2. No canto superior direito, selecione seu nome e, em seguida, selecione **Perfil** na lista.

3. Em seu **Perfil**, selecione **Examinar termos de uso**.

4. Agora, você pode ler os termos de uso que você aceitou.

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de entrada do Azure AD.

**Tipo:** Novo recurso **Categoria de serviço:** Azure Active Directory **Capacidade do produto:** Autenticação de usuário

As interfaces do usuário do Azure AD e do sistema de identidade da conta da Microsoft foram reformuladas para que tenham uma aparência consistente. Além disso, a página de entrada do Azure AD coleta o nome de usuário primeiro, seguido da credencial em uma segunda tela.

Para obter mais informações, consulte [A nova experiência de entrada do Azure AD agora está em visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos prompts de entrada: Uma nova experiência "Manter-me conectado" para a entrada do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Azure Active Directory **Capacidade do produto:** Autenticação de usuário

A caixa de seleção **Manter-me conectado** na página de entrada do Azure AD foi substituída por um novo prompt exibido depois que você é autenticado com êxito.

Se você responder **Sim** a esse prompt, o serviço fornecerá um token de atualização persistente. Esse comportamento é o mesmo exibido quando você selecionava a caixa de seleção **Manter-me conectado** na experiência antiga. Para locatários federados, esse prompt é mostrado após a autenticação bem-sucedida no serviço federado.

Para obter mais informações, confira [Menos prompts de entrada: A nova experiência "Manter-me conectado" do Azure AD está em versão prévia](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Adicionar uma configuração para exigir que os termos de uso sejam expandidos antes da aceitação

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Conformidade

Uma opção para os administradores exige que seus usuários expandam os termos de uso antes de aceitar os termos.

Selecione **Ativado** ou **Desativado** para solicitar que os usuários expandam os termos de uso. A configuração **Ativado** exige que os usuários visualizem os termos de uso antes de aceitá-los.

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação com escopo para atribuições de função qualificadas

**Tipo:** Novo recurso **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Use a ativação com escopo para ativar atribuições de função qualificadas em recursos do Azure com menos autonomia do que nos padrões de atribuição originais. Um exemplo é quando você está atribuído como o proprietário de uma assinatura em seu locatário. Com a ativação com escopo, é possível ativar a função de proprietário para até cinco recursos contidos na assinatura (como grupos de recursos e máquinas virtuais). A ativação com escopo pode reduzir a possibilidade de executar alterações indesejadas em recursos críticos do Azure.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Novos aplicativos federados na galeria de aplicativos Azure AD

**Tipo:** Novo recurso **Categoria de serviço:** Aplicativos empresariais **Capacidade do produto:** Integração de terceiros

Em dezembro de 2017, adicionamos esses novos aplicativos com suporte de federação à nossa galeria de aplicativos:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [IMAGE WORKS](../saas-apps/imageworks-tutorial.md), [MOBI](../saas-apps/mobi-tutorial.md), [MobileIron Azure AD integration](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO for Bamboo by resolution GmbH](../saas-apps/bamboo-tutorial.md), [SAML SSO for Bitbucket by resolution GmbH](../saas-apps/bitbucket-tutorial.md), [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, Zenegy Azure AD Integration.

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para as funções de diretório do Azure AD

**Tipo:** Recurso alterado **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

O fluxo de trabalho de aprovação para as funções de diretório do Azure AD está geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de função com privilégios podem exigir que membros de função qualificada solicitem a ativação de função antes que possam usar a função com privilégios. Vários usuários e grupos podem receber responsabilidades de aprovação. Os membros de função qualificados recebem notificações quando a aprovação é concluída e suas funções estão ativas.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticação de passagem: Suporte ao Skype for Business

**Tipo:** Recurso alterado **Categoria de serviço:** Autenticações (logons) **Capacidade do produto:** Autenticação de usuário

A autenticação de passagem agora dá suporte a entradas do usuário em aplicativos cliente do Skype for Business que dão suporte à autenticação moderna, que incluem topologias online e híbridas.

Para obter mais informações, consulte [Topologias do Skype for Business compatíveis com a autenticação moderna](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Atualizações no Azure AD Privileged Identity Management para o RBAC do Azure (versão prévia)

**Tipo:** Recurso alterado **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Com a atualização de visualização pública do Azure AD Privileged Identity Management (PIM) para o controle de acesso baseado em função do Azure (RBAC do Azure), agora você pode:

* Usar a Administração Just Enough.
* Exigir aprovação para ativar as funções de recurso.
* Agendar uma ativação futura de uma função que exija aprovação para as funções do Azure AD e do Azure.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (versão prévia)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>Novembro de 2017

### <a name="access-control-service-retirement"></a>Desativação do serviço de Controle de Acesso

**Tipo:** Plano para alteração **Categoria de serviço:** Serviço de Controle de Acesso **Capacidade do produto:** Serviço de Controle de Acesso

O Controle de Acesso do Azure Active Directory (também conhecido como serviço de Controle de Acesso) será desativado no final de 2018. Mais informações que incluem um cronograma detalhado e diretrizes de migração de alto nível serão fornecidas nas próximas semanas. Você pode deixar comentários nesta página com dúvidas sobre o serviço de Controle de Acesso e um membro da equipe responderá a suas dúvidas.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso do navegador ao Intune Managed Browser

**Tipo:** Plano para alteração **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Você pode restringir o acesso do navegador ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD usando o Intune Managed Browser como um aplicativo aprovado.

Agora você pode configurar a seguinte condição para o acesso condicional baseado em aplicativo:

**Aplicativos cliente:** Navegador

**Qual é o efeito da alteração?**

Atualmente, o acesso está bloqueado quando essa condição é usada. Quando a versão prévia estiver disponível, todo o acesso exigirá o uso do aplicativo de navegador gerenciado.

Procure essa funcionalidade e mais informações em notas de versão e blogs futuros.

Para obter mais informações, consulte [Acesso condicional no Azure Active Directory](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

**Tipo:** Plano para alteração **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Acesso condicional com base no aplicativo do Azure Active Directory](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte dos termos de uso para diversos idiomas

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Conformidade

Os administradores agora podem criar novos termos de uso que contêm vários documentos PDF. Você pode marcar esses documentos em PDF com um idioma correspondente. Os usuários recebem o PDF com o idioma correspondente de acordo com suas preferências. Se não houver nenhuma correspondência, o idioma padrão será mostrado.

---

### <a name="real-time-password-writeback-client-status"></a>Status do cliente de write-back de senha em tempo real

**Tipo:** Novo recurso **Categoria de serviço:** Redefinição de senha de self-service **Capacidade do produto:** Autenticação de usuário

Agora você pode examinar o status do cliente de write-back de senha local. Essa opção está disponível na seção **Integração local** da página [Redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset).

Se houver problemas com sua conexão com o cliente de write-back local, você verá uma mensagem de erro que exibirá:

- Informações sobre o motivo pelo qual não é possível se conectar ao cliente de write-back local.
- Um link para a documentação que auxilia na resolução do problema.

Para obter mais informações, consulte [Integração local](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Acesso condicional com base no aplicativo do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Azure Active Directory **Capacidade do produto:** Segurança de identidade e proteção

Agora você pode restringir o acesso ao Office 365 e a outros aplicativos de nuvem conectados ao Azure Active Directory aos [aplicativos cliente aprovados](../conditional-access/concept-conditional-access-conditions.md#client-apps) que dão suporte a políticas de proteção de aplicativo do Intune usando o [acesso condicional baseado no aplicativo do Azure Active Directory](../conditional-access/app-based-conditional-access.md). Políticas da Proteção de Aplicativo do Intune são usadas para configurar e proteger os dados da empresa nesses aplicativos cliente.

Ao combinar as políticas de acesso condicional [baseado em aplicativo](../conditional-access/app-based-conditional-access.md) com [baseado em dispositivo](../conditional-access/require-managed-devices.md), você tem a flexibilidade para proteger dados pessoais e dispositivos da empresa.

As condições e os controles a seguir agora estão disponíveis para uso com acesso condicional com base no aplicativo:

**Condição de plataforma com suporte**

- iOS
- Android

**Condição de aplicativos cliente**

- Aplicativos móveis e clientes de desktop

**Controle de acesso**

- Exigir um aplicativo cliente aprovado

Para obter mais informações, consulte [Acesso condicional baseado em aplicativo do Azure Active Directory](../conditional-access/app-based-conditional-access.md).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gerenciar dispositivos do Azure AD no portal do Azure

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento e registro de dispositivos **Capacidade do produto:** Segurança de identidade e proteção

Agora você pode encontrar todos os dispositivos conectados ao Azure AD e as atividades relacionadas ao dispositivo em um único local. Há uma nova experiência de administração para gerenciar todas as suas identidades e configurações de dispositivos no portal do Azure. Nesta versão, você pode:

- Exibir todos os dispositivos que estão disponíveis para acesso condicional no Azure Active Directory.
- Exibir propriedades, incluindo dispositivos vinculados do Azure AD híbrido.
- Encontrar chaves do BitLocker para os dispositivos ingressados no Azure AD, gerencie seu dispositivo com o Intune e muito mais.
- Gerenciar configurações relacionadas ao dispositivo do Azure AD.

Para obter mais informações, consulte [Gerenciar dispositivos usando o portal do Azure](../devices/device-management-azure-portal.md).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como plataforma de dispositivo para acesso condicional do Azure Active Directory

**Tipo:** Novo recurso **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Agora você pode incluir (ou excluir) o macOS como uma condição de plataforma de dispositivo em sua política de acesso condicional do Azure Active Directory. Com a adição do macOS às plataformas de dispositivos com suporte, você pode:

- **Registrar e gerenciar dispositivos macOS usando o Intune.** Semelhante a outras plataformas como iOS e Android, um aplicativo do portal da empresa está disponível para o macOS para realizar registros unificados. Use o novo aplicativo do portal da empresa para o macOS para registrar um dispositivo no Intune e registrá-lo no Azure AD.
- **Garantir que os dispositivos macOS seguem as políticas de conformidade de sua organização definidas no Intune.** No Intune no portal do Azure, agora você pode configurar políticas de conformidade para dispositivos macOS.
- **Restringir o acesso a aplicativos no Azure AD apenas a dispositivos macOS em conformidade.** A criação de política de acesso condicional traz o macOS como uma opção de plataforma de dispositivo separada. Agora, você pode criar políticas de acesso condicional específicas ao macOS para o conjunto de aplicativos direcionados no Azure.

Para obter mais informações, consulte:

- [Criar uma política de conformidade do dispositivo para dispositivos macOS com o Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Acesso condicional no Azure Active Directory](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Extensão do servidor de políticas de rede para autenticação multifator do Azure AD

**Tipo:** Novo recurso **Categoria de serviço:**  Autenticação multifator **Capacidade do produto:** Autenticação de usuário

A extensão do servidor de políticas de rede para autenticação multifator do Azure AD adiciona recursos de autenticação multifator baseados em nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão Servidor de Políticas de Rede, você pode adicionar uma chamada telefônica, mensagem de texto ou verificação de aplicativo de telefone a seu fluxo de autenticação existente. Você não precisa instalar, configurar nem manter novos servidores.

Essa extensão foi criada para organizações que desejam proteger as conexões de rede virtual privada sem implantar o Servidor de Autenticação Multifator do Azure. A extensão do servidor de políticas de rede atua como um adaptador entre o RADIUS e a autenticação multifator do Azure AD baseada em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.

Para obter mais informações, consulte [integrar sua infraestrutura de servidor de política de rede existente com a autenticação multifator do Azure ad](../authentication/howto-mfa-nps-extension.md).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurar ou remover permanentemente os usuários excluídos

**Tipo:** Novo recurso **Categoria de serviço:** Gerenciamento de usuário **Capacidade do produto:** Diretório

No centro de administração do Azure AD, agora você pode:

- Restaurar um usuário excluído.
- Excluir permanentemente um usuário.

**Para experimentar:**

1. No centro de administração do Azure AD, selecione [Todos os usuários](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) na seção **Gerenciar**.

2. Na lista **Mostrar**, selecione **Usuários excluídos recentemente**.

3. Selecione um ou mais usuários excluídos recentemente e, em seguida, restaure-os ou exclua-os permanentemente.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

**Tipo:** Recurso alterado **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos foram adicionados à lista de [aplicativos cliente aprovados](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft Planner
- Proteção de Informações do Azure

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Acesso condicional com base no aplicativo do Azure Active Directory](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Usar “OR” entre controles em uma política de acesso condicional

**Tipo:** Recurso alterado **Categoria de serviço:** Acesso condicional **Capacidade do produto:** Segurança de identidade e proteção

Agora você pode usar “OR” (exige um dos controles selecionados) para controle de acesso condicional. Use esse recurso para criar políticas com “OR” entre os controles de acesso. Por exemplo, você pode usar esse recurso para criar uma política que exige que um usuário faça login usando a Autenticação Multifator "OU" para estar em um dispositivo compatível.

Para obter mais informações, consulte [Controles no acesso condicional do Azure Active Directory](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregação de detecções de risco em tempo real

**Tipo:** Recurso alterado **Categoria de serviço:** Identity Protection **Capacidade do produto:** Segurança de identidade e proteção

No Azure AD Identity Protection, todas as detecções de risco em tempo real que foram originados no mesmo endereço IP em determinado dia agora são agregados por tipo de detecção de risco. Essa alteração limita o volume de detecções de risco mostrados sem nenhuma alteração na segurança do usuário.

A detecção em tempo real subjacente funciona sempre que o usuário se conecta. Se você tiver uma política de segurança de risco de login configurada para Autenticação de vários fatores ou bloqueio de acesso, ela ainda será acionada durante cada entrada arriscada.

---

## <a name="october-2017"></a>Outubro de 2017

### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** Plano para alteração **Categoria de serviço:** Relatório **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

O Portal do Azure oferece:

- Um novo console de administração do Azure AD.
- Novas APIs para relatórios de atividade e segurança.

Devido a essas novas funcionalidades, as APIs de relatórios no ponto de extremidade /reports foram desativadas em 10 de dezembro de 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Detecção automática de campo de conexão

**Tipo:** Fixo **Categoria de serviço:** Meus Aplicativos **Capacidade do produto:** Logon único

O Azure AD é compatível com a detecção automática de campo de entrada para os aplicativos que renderizam um campo de nome de usuário e senha HTML. Essas etapas são documentadas em [Como capturar automaticamente os campos de entrada para um aplicativo](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app). Você pode encontrar essa funcionalidade adicionando um aplicativo *Inexistente na galeria* na página **Aplicativos empresariais** do [Portal do Azure](https://aad.portal.azure.com). Além disso, você pode configurar o modo **Logon Único** neste novo aplicativo como **Logon Único Baseado em Senha**, inserir uma URL da Web e, em seguida, salvar a página.

Devido a um problema do serviço, essa funcionalidade ficou temporariamente desabilitada. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

### <a name="new-multi-factor-authentication-features"></a>Novos recursos de autenticação multifator

**Tipo:** Novo recurso **Categoria de serviço:** Autenticação multifator **Capacidade do produto:** Segurança de identidade e proteção

O MFA (Autenticação multifator) é uma parte essencial da proteção de sua organização. Para tornar as credenciais mais adaptáveis e a experiência mais direta, os seguintes recursos foram adicionados:

- Os resultados do desafio multifator são diretamente integrados ao relatório de entrada do Azure AD, que inclui o acesso programático aos resultados do MFA.
- A configuração do MFA está mais profundamente integrada à experiência de configuração do Azure AD no portal do Azure.

Com essa versão prévia pública, o gerenciamento e os relatórios de MFA são parte integrante da experiência de configuração central do Azure AD. Agora, você pode gerenciar a funcionalidade do portal de gerenciamento do MFA na experiência do Azure AD.

Para obter mais informações, consulte [Referência de relatórios do MFA no portal do Azure](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Termos de uso

**Tipo:** Novo recurso **Categoria de serviço:** Termos de uso **Capacidade do produto:** Conformidade

Use os termos de uso do Azure AD para apresentar informações como avisos de isenção de responsabilidade relevantes para requisitos legais ou de conformidade para os usuários.

Use os termos de uso do Azure AD nos seguintes cenários:

- Termos de uso gerais para todos os usuários em sua organização
- Termos de uso específicos com base nos atributos de um usuário (por exemplo, médicos versus enfermeiras ou funcionários nacionais vs. internacionais, feito por grupos dinâmicos)
- Termos de uso específicos para acessar aplicativos de negócios de alto impacto, como o Salesforce

Para obter mais informações, consulte [Termos de uso do Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Melhorias no Privileged Identity Management

**Tipo:** Novo recurso **Categoria de serviço:** Privileged Identity Management **Capacidade do produto:** Privileged Identity Management

Com o Azure AD Privileged Identity Management, você pode gerenciar, controlar e monitorar o acesso aos recursos do Azure (versão prévia) em sua organização para:

- Assinaturas
- Grupos de recursos
- Máquinas virtuais

Todos os recursos do portal do Azure que usam a funcionalidade de RBAC do Azure podem usufruir todas as funcionalidades de segurança e gerenciamento do ciclo de vida que o Azure AD Privileged Identity Management tem a oferecer.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="access-reviews"></a>Análises de acesso

**Tipo:** Novo recurso **Categoria de serviço:** Revisões de acesso **Capacidade do produto:** Conformidade

As organizações podem usar revisões de acesso (versão prévia) para gerenciar com eficiência as associações a um grupo e o acesso a aplicativos corporativos:

- Você pode reconfirmar o acesso do usuário convidado usando as revisões do acesso deles a aplicativos e a associações de grupos. Os revisores podem decidir com eficiência se permitirão o acesso contínuo aos convidados de acordo com as informações fornecidas pelas revisões de acesso.
- Você pode reconfirmar o acesso de funcionários a aplicativos e associações de grupo com as revisões de acesso.

Você pode coletar os controles de revisão de acesso em programas relevantes para que sua organização rastreie as análises de conformidade ou aplicativos suscetíveis a riscos.

Para obter mais informações, consulte [Revisões de acesso do Azure AD](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultar aplicativos de terceiros de Meus Aplicativos e do inicializador de aplicativos do Office 365

**Tipo:** Novo recurso **Categoria de serviço:** Meus Aplicativos **Capacidade do produto:** Logon único

Agora é possível gerenciar melhor os aplicativos que são mostrados nos portais dos usuários por meio de uma nova propriedade **ocultar aplicativo**. Você pode ocultar aplicativos para ajudar em casos em que os blocos de aplicativos são mostrados para serviços de back-end ou blocos duplicados e emails secundários de iniciadores de aplicativos dos usuários. A alternância está localizada na seção **Propriedades** do aplicativo de terceiros e é rotulada **Visível para o usuário?** Também é possível ocultar um aplicativo de forma programática por meio do PowerShell.

Para obter mais informações, consulte [Ocultar um aplicativo de terceiros da experiência de um usuário no Azure AD](../manage-apps/hide-application-from-user-portal.md).


**O que está disponível?**

 Como parte da transição para o novo console de administrador, duas novas APIs para recuperar os logs de atividades do Azure AD estão disponíveis. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de Detecção de risco do Identity Protection no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix para o Identity Manager

**Tipo:** Recurso alterado **Categoria de serviço:** Identity Manager **Capacidade do produto:** Gerenciamento do ciclo de vida de identidades

Um pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) está disponível a partir de 25 de setembro de 2017 para o Identity Manager 2016 Service Pack 1. Esse pacote cumulativo:

- Resolve problemas e adiciona melhorias.
- É uma atualização cumulativa que substitui todas as atualizações do Identity Manager 2016 Service Pack 1 até o build 4.4.1459.0 para o Identity Manager 2016.
- Exige o Identity Manager 2016 build 4.4.1302.0.

Para obter mais informações, consulte [Pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) disponível para o Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562).

---
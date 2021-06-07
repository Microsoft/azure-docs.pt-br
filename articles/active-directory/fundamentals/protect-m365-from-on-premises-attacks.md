---
title: Protegendo Microsoft 365 de ataques locais
description: Diretrizes sobre como garantir que um ataque local não afete Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609899"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Protegendo Microsoft 365 de ataques locais

Muitos clientes conectam suas redes corporativas privadas a Microsoft 365 para beneficiar seus usuários, dispositivos e aplicativos. No entanto, essas redes privadas podem ser comprometidas de várias maneiras bem documentadas. Como Microsoft 365 atua como um tipo de sistema preocupados para muitas organizações, é essencial protegê-lo contra a infraestrutura local comprometida.

Este artigo mostra como configurar seus sistemas para proteger seu ambiente de Microsoft 365 Nuvem do comprometimento local. Nos concentramos principalmente em: 

- Definições de configuração de locatário do Azure Active Directory (AD do Azure).
- Como os locatários do Azure AD podem ser conectados com segurança a sistemas locais.
- As compensações necessárias para operar seus sistemas de maneiras que protegem seus sistemas de nuvem contra o comprometimento local.

É altamente recomendável que você implemente essas diretrizes para proteger seu ambiente de Microsoft 365 Nuvem.
> [!NOTE]
> Este artigo foi publicado inicialmente como uma postagem no blog. Ele foi movido para seu local atual para longevidade e manutenção.
>
> Para criar uma versão offline deste artigo, use a funcionalidade de impressão para PDF do navegador. Verifique novamente com frequência para obter atualizações.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Principais vetores de ameaça de ambientes locais comprometidos


Seu ambiente de nuvem Microsoft 365 se beneficia de uma ampla infraestrutura de monitoramento e segurança. Usando o aprendizado de máquina e a inteligência humana, Microsoft 365 procura pelo tráfego mundial. Ele pode detectar ataques rapidamente e permitir que você reconfigure quase em tempo real. 

Em implantações híbridas que conectam a infraestrutura local para Microsoft 365, muitas organizações delegam confiança a componentes locais para as decisões de autenticação crítica e gerenciamento de estado do objeto de diretório.
Infelizmente, se o ambiente local estiver comprometido, essas relações de confiança se tornarão as oportunidades de um invasor para comprometer seu ambiente de Microsoft 365.

Os dois vetores de ameaça principais são *relações de confiança de Federação* e sincronização de *conta.* Ambos os vetores podem conceder a um invasor acesso administrativo à sua nuvem.

* As **relações de confiança federadas**, como a autenticação SAML, são usadas para autenticar para Microsoft 365 por meio de sua infraestrutura de identidade local. Se um certificado de autenticação de tokens SAML for comprometido, a Federação permitirá que qualquer pessoa que tenha esse certificado represente qualquer usuário em sua nuvem. *Recomendamos que você desabilite as relações de confiança de Federação para autenticação para Microsoft 365 quando possível.*

* A **sincronização de conta** pode ser usada para modificar usuários privilegiados (incluindo suas credenciais) ou grupos que têm privilégios administrativos em Microsoft 365. É *recomendável garantir que os objetos sincronizados não contenham nenhum privilégio além de um usuário no Microsoft 365,* seja diretamente ou por meio da inclusão em funções ou grupos confiáveis. Verifique se esses objetos não têm nenhuma atribuição direta ou aninhada em grupos ou funções de nuvem confiáveis.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Protegendo Microsoft 365 do comprometimento local


Para abordar os vetores de ameaça descritos anteriormente, recomendamos que você siga os princípios ilustrados no diagrama a seguir:

![Arquitetura de referência para proteger Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **Isole completamente suas contas de administrador de Microsoft 365.** Eles devem ser:

    * Mestre no Azure AD.

     * Autenticado usando a autenticação multifator.

     *  Protegido pelo acesso condicional do Azure AD.

     *  Acessado somente usando estações de trabalho gerenciadas pelo Azure.

    Essas contas de administrador são contas de uso restrito. *Nenhuma conta local deve ter privilégios administrativos em Microsoft 365.* 

    Para obter mais informações, consulte a [visão geral de Microsoft 365 funções de administrador](/microsoft-365/admin/add-users/about-admin-roles). Consulte também [funções para Microsoft 365 no Azure ad](../roles/m365-workload-docs.md).

1. **Gerenciar dispositivos de Microsoft 365.** Use o Azure AD Join e o MDM (gerenciamento de dispositivo móvel) baseado em nuvem para eliminar dependências em sua infraestrutura de gerenciamento de dispositivo local. Essas dependências podem comprometer o dispositivo e os controles de segurança.

1. **Certifique-se de que nenhuma conta local tenha privilégios elevados para Microsoft 365.**
    Algumas contas acessam aplicativos locais que exigem autenticação NTLM, LDAP ou Kerberos. Essas contas devem estar na infraestrutura de identidade local da organização. Verifique se essas contas, incluindo contas de serviço, não estão incluídas em grupos ou funções de nuvem privilegiadas. Além disso, certifique-se de que as alterações nessas contas não afetem a integridade do ambiente de nuvem. O software local privilegiado não deve ser capaz de afetar Microsoft 365 contas com privilégios ou funções.

1. **Use a autenticação de nuvem do AD do Azure** para eliminar dependências em suas credenciais locais. Sempre use autenticação forte, como Windows Hello, FIDO, Microsoft Authenticator ou autenticação multifator do Azure AD.

## <a name="specific-security-recommendations"></a>Recomendações de segurança específicas


As seções a seguir fornecem diretrizes específicas sobre como implementar os princípios descritos anteriormente.

### <a name="isolate-privileged-identities"></a>Isolar identidades privilegiadas


No Azure AD, os usuários que têm funções privilegiadas, como administradores, são a raiz de confiança para criar e gerenciar o restante do ambiente. Implemente as práticas a seguir para minimizar os efeitos de um comprometimento.

* Use contas somente em nuvem para o Azure AD e Microsoft 365 funções com privilégios.

* Implantar [dispositivos com acesso privilegiado](/security/compass/privileged-access-devices#device-roles-and-profiles) para acesso privilegiado para gerenciar o Microsoft 365 e o Azure AD.

*  Implantar [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) para acesso just-in-time (JIT) a todas as contas humanas que têm funções privilegiadas. Exigir autenticação forte para ativar funções.

* Forneça funções administrativas que permitam o [privilégio mínimo necessário para executar as tarefas necessárias](../roles/delegate-by-task.md).

* Para habilitar uma experiência de atribuição de função rica que inclui delegação e várias funções ao mesmo tempo, considere o uso de grupos de segurança do Azure AD ou grupos de Microsoft 365. Esses grupos são chamados coletivamente de *grupos de nuvem*. [Habilite também o controle de acesso baseado em função](../roles/groups-assign-role.md). Você pode usar [unidades administrativas](../roles/administrative-units.md) para restringir o escopo de funções a uma parte da organização.

* Implantar [contas de acesso de emergência](../roles/security-emergency-access.md). *Não* use cofres de senha locais para armazenar credenciais.

Para obter mais informações, consulte [protegendo o acesso privilegiado](/security/compass/overview). Consulte também [práticas de acesso seguro para administradores no Azure ad](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Usar autenticação de nuvem 

As credenciais são um vetor de ataque primário. Implemente as seguintes práticas para tornar as credenciais mais seguras:

* [Implante a autenticação com senha](../authentication/howto-authentication-passwordless-deployment.md). Reduza o uso de senhas tanto quanto possível implantando credenciais com senha. Essas credenciais são gerenciadas e validadas nativamente na nuvem. Escolha um destes métodos de autenticação:

   * [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [O aplicativo Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Chaves de segurança do FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Implante a autenticação multifator](../authentication/howto-mfa-getstarted.md). Provisione [várias credenciais fortes usando a autenticação multifator do Azure ad](../fundamentals/resilience-in-credentials.md). Dessa forma, o acesso aos recursos de nuvem exigirá uma credencial gerenciada no Azure AD, além de uma senha local que possa ser manipulada. Para obter mais informações, consulte [criar uma estratégia de gerenciamento de controle de acesso resiliente usando o Azure ad](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Limitações e compensações

* O gerenciamento de senha de conta híbrida requer componentes híbridos, como agentes de proteção de senha e agentes de write-back de senha. Se sua infraestrutura local estiver comprometida, os invasores poderão controlar os computadores nos quais esses agentes residem. Essa vulnerabilidade não comprometerá sua infraestrutura de nuvem. Mas suas contas de nuvem não protegem esses componentes contra o comprometimento local.

*  As contas locais sincronizadas de Active Directory são marcadas para nunca expirarem no Azure AD. Essa configuração geralmente é minimizada pelo local Active Directory configurações de senha. No entanto, se sua instância local do Active Directory estiver comprometida e a sincronização estiver desabilitada, você deverá definir a opção [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) para forçar as alterações de senha.

## <a name="provision-user-access-from-the-cloud"></a>Provisionar o acesso do usuário na nuvem

O *provisionamento* refere-se à criação de contas de usuário e grupos em aplicativos ou provedores de identidade.

![Diagrama de arquitetura de provisionamento.](media/protect-m365/protect-m365-provision.png)

Recomendamos os seguintes métodos de provisionamento:

* **Provisionar de aplicativos de RH de nuvem para o Azure ad**: esse provisionamento permite que um comprometimento local seja isolado, sem interromper o ciclo de movimentação do seu associador de seus aplicativos de RH na nuvem para o Azure AD.

* **Aplicativos de nuvem**: sempre que possível, implante o [provisionamento de aplicativo do Azure ad](../app-provisioning/user-provisioning.md) em oposição às soluções de provisionamento local. Esse método protege alguns dos seus aplicativos SaaS (software como serviço) de serem afetados por perfis de hacker mal-intencionados em violações locais. 

* **Identidades externas**: Use a [colaboração B2B do Azure ad](../external-identities/what-is-b2b.md).
    Esse método reduz a dependência de contas locais para colaboração externa com parceiros, clientes e fornecedores. Avalie cuidadosamente qualquer Federação direta com outros provedores de identidade. É recomendável limitar as contas de convidado B2B das seguintes maneiras:

   *  Limite o acesso de convidado a grupos de navegação e outras propriedades no diretório. Use as configurações de colaboração externas para restringir a capacidade dos convidados de ler grupos dos quais eles não são membros. 

    *   Bloquear o acesso ao portal do Azure. Você pode fazer exceções raras necessárias.  Crie uma política de acesso condicional que inclua todos os convidados e usuários externos. Em seguida, [implemente uma política para bloquear o acesso](../../role-based-access-control/conditional-access-azure-management.md). 

* **Florestas desconectadas**: Use o [provisionamento de nuvem do Azure ad](../cloud-sync/what-is-cloud-sync.md). Esse método permite que você se conecte a florestas desconectadas, eliminando a necessidade de estabelecer confiança ou conectividade entre florestas, o que pode ampliar o efeito de uma violação local. 
 
### <a name="limitations-and-tradeoffs"></a>Limitações e compensações

Quando usado para provisionar contas híbridas, o sistema Azure-AD-from-Cloud-HR depende da sincronização local para concluir o fluxo de dados do Active Directory para o Azure AD. Se a sincronização for interrompida, os novos registros de funcionários não estarão disponíveis no Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Usar grupos de nuvem para colaboração e acesso

Os grupos de nuvem permitem dissociar sua colaboração e acesso de sua infraestrutura local.

* **Colaboração**: Use grupos de Microsoft 365 e Microsoft Teams para colaboração moderna. Descomissionar listas de distribuição locais e [atualizar listas de distribuição para Microsoft 365 grupos no Outlook](/office365/admin/manage/upgrade-distribution-lists).

* **Acesso**: Use grupos de segurança do Azure ad ou grupos de Microsoft 365 para autorizar o acesso a aplicativos no Azure AD.
* **Licenciamento do office 365**: Use o licenciamento baseado em grupo para provisionar o Office 365 usando grupos somente de nuvem. Esse método separa o controle de associação de grupo da infraestrutura local.

Os proprietários de grupos que são usados para acesso devem ser considerados identidades privilegiadas para evitar a associação de tomada em um comprometimento local.
Um tomada inclui A manipulação direta da Associação de grupo no local ou a manipulação de atributos locais que podem afetar a associação de grupo dinâmico no Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gerenciar dispositivos de nuvem


Use os recursos do Azure AD para gerenciar dispositivos com segurança.

-   **Usar estações de trabalho do Windows 10**: [implantar dispositivos ingressados no Azure ad](../devices/azureadjoin-plan.md) com as políticas de MDM. Habilite o [piloto automático do Windows](/mem/autopilot/windows-autopilot) para uma experiência de provisionamento totalmente automatizada.

    -   Substitua as máquinas que executam o Windows 8.1 e versões anteriores.

    -   Não implante computadores do sistema operacional como estações de trabalho.

    -   Use [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) como a origem de autoridade para todas as cargas de trabalho de gerenciamento de dispositivos.

-   [**Implantar dispositivos com acesso privilegiado**](/security/compass/privileged-access-devices#device-roles-and-profiles): Use o acesso privilegiado para gerenciar o Microsoft 365 e o Azure AD.

## <a name="workloads-applications-and-resources"></a>Cargas de trabalho, aplicativos e recursos 

-   **Sistemas de logon único (SSO) locais** 

    Substitua qualquer infraestrutura de gerenciamento de acesso à Web e federação local. Configurar aplicativos para usar o Azure AD.  

-   **Aplicativos SaaS e LOB (linha de negócios) que dão suporte a protocolos de autenticação modernos** 

    [Use o Azure ad para SSO](../manage-apps/what-is-single-sign-on.md). Quanto mais aplicativos você configurar para usar o Azure AD para autenticação, menor será o risco em um comprometimento local.


* **Aplicativos herdados** 

   * Você pode habilitar a autenticação, a autorização e o acesso remoto a aplicativos herdados que não dão suporte à autenticação moderna. Use o [proxy de aplicativo do AD do Azure](../manage-apps/application-proxy.md). Você também pode habilitá-los por meio de uma solução de rede ou de controlador de entrega de aplicativos usando [integrações de parceiros de acesso híbrido seguro](../manage-apps/secure-hybrid-access.md).   

   * Escolha um fornecedor de VPN que ofereça suporte à autenticação moderna. Integre sua autenticação com o Azure AD. Em um comprometimento local, você pode usar o Azure AD para desabilitar ou bloquear o acesso desabilitando a VPN.

*  **Aplicativos e servidores de carga de trabalho**

   * Os aplicativos ou recursos que precisam de servidores podem ser migrados para a infraestrutura como um serviço (IaaS) do Azure. Use [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (AD DS do Azure) para dissociar a confiança e a dependência em instâncias locais do Active Directory. Para obter essa desassociação, verifique se as redes virtuais usadas para o Azure AD DS não têm uma conexão com redes corporativas.

   * Siga as diretrizes para a [camada de credencial](/security/compass/privileged-access-access-model#ADATM_BM). Os servidores de aplicativos normalmente são considerados ativos de camada 1.

## <a name="conditional-access-policies"></a>Políticas de acesso condicional

Use o acesso condicional do Azure AD para interpretar sinais e usá-los para tomar decisões de autenticação. Para obter mais informações, consulte o [plano de implantação de acesso condicional](../conditional-access/plan-conditional-access.md).

* Use o acesso condicional para [Bloquear protocolos de autenticação herdados](../conditional-access/howto-conditional-access-policy-block-legacy.md) sempre que possível. Além disso, desabilite os protocolos de autenticação herdados no nível do aplicativo usando uma configuração específica do aplicativo.

   Para obter mais informações, consulte [protocolos de autenticação herdados](../fundamentals/auth-sync-overview.md). Ou consulte detalhes específicos para o [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) e o [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant).

* Implemente as configurações recomendadas de [acesso de dispositivo e identidade](/microsoft-365/security/office-365-security/identity-access-policies).

* Se você estiver usando uma versão do Azure AD que não inclui acesso condicional, verifique se você está usando os [padrões de segurança do Azure ad](../fundamentals/concept-fundamentals-security-defaults.md).

   Para obter mais informações sobre o licenciamento de recursos do Azure AD, consulte o [Guia de preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Monitoramento 

Depois de configurar seu ambiente para proteger seus Microsoft 365 de um comprometimento local, [monitore proativamente](../reports-monitoring/overview-monitoring.md) o ambiente.
### <a name="scenarios-to-monitor"></a>Cenários a serem monitorados

Monitore os seguintes cenários principais, além de quaisquer cenários específicos para sua organização. Por exemplo, você deve monitorar proativamente o acesso aos seus aplicativos e recursos críticos para os negócios.

* **Atividade suspeita** 

    Monitore todos os [eventos de risco do Azure ad](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) para atividades suspeitas. O [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md) é nativamente integrado à central de segurança do Azure.

    Defina os [locais nomeados](../reports-monitoring/quickstart-configure-named-locations.md) da rede para evitar detecções com ruídos em sinais baseados no local. 
*  **Alertas de UEBA (análise comportamental de entidade e usuário)** 

    Use UEBA para obter informações sobre a detecção de anomalias.
    * Microsoft Cloud App Security (MCAS) fornece [Ueba na nuvem](/cloud-app-security/tutorial-ueba).

    * Você pode [integrar o Ueba local da ATP (proteção avançada contra ameaças) do Azure](/defender-for-identity/install-step2). MCAS lê sinais de Azure AD Identity Protection. 

* **Atividade de contas de acesso de emergência** 

    Monitore qualquer acesso que use [contas de acesso de emergência](../roles/security-emergency-access.md). Crie alertas para investigações. Esse monitoramento deve incluir: 

   * Entradas. 

   * Gerenciamento de credenciais. 

   * Quaisquer atualizações em associações de grupo. 

   * Atribuições de aplicativo. 
* **Atividade de função com privilégios**

    Configurar e examinar alertas de segurança [gerados pelo Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Monitore a atribuição direta de funções privilegiadas fora do PIM, gerando alertas sempre que um usuário é atribuído diretamente.

* **Configurações de todo o locatário do Azure AD**

    Qualquer alteração nas configurações de todo o locatário deve gerar alertas no sistema. Essas alterações incluem, mas não estão limitadas a:

  *  Domínios personalizados atualizados.  

  * O Azure AD B2B muda para AllowLists e bloqueio.

  * As alterações B2B do Azure AD para provedores de identidade permitidos (provedores de identidade SAML por meio de Federação direta ou entradas sociais).  

  * Alterações de política de risco ou de acesso condicional. 

* **Objetos de aplicativo e entidade de serviço**
    
   * Novos aplicativos ou entidades de serviço que podem exigir políticas de acesso condicional. 

   * Credenciais adicionadas às entidades de serviço.
   * Atividade de consentimento do aplicativo. 

* **Funções personalizadas**
   * Atualizações para as definições de função personalizadas. 

   * Funções personalizadas criadas recentemente. 

### <a name="log-management"></a>Gerenciamento de Log

Defina um armazenamento de log e uma estratégia de retenção, design e implementação para facilitar um conjunto de ferramentas consistente. Por exemplo, você pode considerar sistemas SIEM (gerenciamento de eventos e informações de segurança) como sentinela do Azure, consultas comuns e guias estratégicos de investigação e análise.

* **Logs do Azure ad**: ingerir logs e sinais gerados seguindo consistentemente as práticas recomendadas para configurações como diagnóstico, retenção de log e ingestão de Siem. 

    A estratégia de log deve incluir os seguintes logs do Azure AD:
   * Atividade de entrada 

   * Logs de auditoria 

   * Eventos de risco 

    O Azure AD fornece [Azure monitor integração](../reports-monitoring/concept-activity-logs-azure-monitor.md) para o log de atividades de entrada e logs de auditoria. Os eventos de risco podem ser ingeridos por meio da [API Microsoft Graph](/graph/api/resources/identityriskevent). Você pode [transmitir logs do Azure ad para Azure monitor logs](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Logs de segurança do sistema operacional de infraestrutura híbrida**: todos os logs de sistema operacional de infraestrutura de identidade híbrida devem ser arquivados e cuidadosamente monitorados como um sistema de camada 0, devido às implicações da área de superfície. Inclua os seguintes elementos: 

   *  o Azure AD Connect. [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md) deve ser implantado para monitorar a sincronização de identidades.

   *  Agentes de proxy de aplicativo 


   * Agentes de write-back de senha 

   * Computadores do gateway de proteção de senha  

   * NPSs (servidores de diretivas de rede) que têm a extensão RADIUS de autenticação multifator do Azure AD 

## <a name="next-steps"></a>Próximas etapas
* [Crie resiliência no gerenciamento de identidade e acesso usando o Azure AD](resilience-overview.md)

* [Proteger o acesso externo a recursos](secure-external-access-resources.md) 
* [Integre todos os seus aplicativos com o Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)
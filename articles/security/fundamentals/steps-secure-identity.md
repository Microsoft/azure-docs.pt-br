---
title: Proteger a infraestrutura da sua identidade do Azure AD
titleSuffix: Azure Active Directory
description: Este documento descreve uma lista de ações importantes que os administradores devem implementar para ajudá-los a proteger sua organização usando recursos do Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: ffc5bafca305086b0c524c76eb91b8aec2e2655d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602445"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinco etapas para proteger sua infraestrutura de identidade

Se você está lendo esse documento, está ciente da significância da segurança. Você provavelmente já tem a responsabilidade de proteger sua organização. Se precisar convencer outras pessoas sobre a importância da segurança, envie-as para que leiam o [Relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/business/security-intelligence-report) mais recente.

Esse documento ajudará você a obter uma postura mais segura usando os recursos do Azure Active Directory utilizando uma lista de verificação de cinco etapas para imunizar sua organização contra ataques cibernéticos.

Esta lista de verificação ajudará você a implantar rapidamente ações recomendadas críticas para proteger sua organização imediatamente explicando como:

* Fortaleça suas credenciais.
* Reduza a área de superfície de ataque.
* Automatize a resposta a ameaças.
* Use a inteligência de nuvem.
* Habilite o autoatendimento do usuário final.

Certifique-se de manter o controle de quais recursos e etapas estão completos durante a leitura desta lista de verificação.

> [!NOTE]
> Muitas das recomendações contidas neste documento se aplicam apenas a aplicativos, que são configurados para usar o Azure Active Directory como seu provedor de identidade. Configurar os aplicativos para utilizar o logon único garante os benefícios de políticas de credenciais, detecção de ameaças, auditoria, registro em log e outros recursos são adicionados a esses aplicativos. O [Gerenciamento de aplicativos do Azure ad](../../active-directory/manage-apps/what-is-application-management.md) é a base em que todas essas recomendações se baseiam.

As recomendações neste documento estão alinhadas com a [Classificação de Segurança de Identidade](../../active-directory/fundamentals/identity-secure-score.md), uma avaliação automatizada da configuração de segurança de identidade do locatário do Azure AD. As organizações podem usar a página de Classificação de Segurança de Identidade no portal do Azure AD para localizar lacunas na configuração de segurança atual e assegurar que sigam as [melhores práticas](identity-management-best-practices.md) atuais da Microsoft para segurança. Implementar cada recomendação na página de Classificação de Segurança aumentará sua classificação e permitirá que você acompanhe o progresso, além de ajudá-lo a comparar a implementação com outras organizações de porte semelhante ou do setor.

![Classificação de segurança de identidade](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Muitos dos recursos descritos aqui exigem uma assinatura Azure AD Premium, enquanto outros são gratuitos. Examine nossos [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) e a [lista de verificação de implantação do Azure AD](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md) para obter mais informações.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Antes de começar: Proteja contas com privilégios com MFA

Antes de iniciar esta lista de verificação, certifique-se de que não seja comprometido enquanto lê essa lista. Você precisa primeiro proteger suas contas com privilégios.

Os invasores que obtêm o controle de contas com privilégios podem causar danos tremendos, portanto, é essencial proteger essas contas primeiro. Habilite e exija a MFA ( [autenticação multifator) do Azure ad](../../active-directory/authentication/concept-mfa-howitworks.md) para todos os administradores em sua organização usando os [padrões de segurança do Azure ad](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) ou o [acesso condicional](../../active-directory/conditional-access/plan-conditional-access.md). Se você ainda não implementou a MFA, faça isso agora! Isso é realmente importante.

Tudo pronto? Vamos começar a trabalhar na lista de verificação.

## <a name="step-1---strengthen-your-credentials"></a>Etapa 1: fortalecer suas credenciais

A maioria das violações de segurança empresariais são originadas com uma conta comprometida com um de uma série de métodos como pulverização de senha, reprodução de violação ou phishing. Saiba mais sobre esses ataques neste vídeo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Verifique se sua organização usa autenticação forte

Dada a frequência de adivinhação de senhas, phishinged, furtos com malware ou reutilizado, é essencial voltar à senha com alguma forma de credencial forte – saiba mais sobre a [autenticação multifator do Azure ad](../../active-directory/authentication/concept-mfa-howitworks.md).

Para habilitar facilmente o nível básico de segurança de identidade, você pode usar a habilitação em um clique com os [Padrões de Segurança do Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Os padrões de segurança impõem o Azure AD MFA para todos os usuários em um locatário e bloqueia as entradas de protocolos herdados em todo o locatário.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Comece a proibir senhas comumente atacadas e desative a complexidade tradicional e as regras de expiração.

Muitas organizações usam a complexidade tradicional (exigindo caracteres especiais, números, letras maiúsculas e minúsculas) e regras de expiração de senha. A [pesquisa da Microsoft](https://aka.ms/passwordguidance) mostrou que essas políticas fazem com que os usuários escolham senhas que sejam mais fáceis de adivinhar.

O recurso de [senha proibida dinamicamente](../../active-directory/authentication/concept-password-ban-bad.md) do Azure AD usa o comportamento atual do invasor para impedir que os usuários definam senhas que possam ser facilmente adivinhadas. Esse recurso sempre é ativado quando os usuários são criados na nuvem, mas agora também está disponível para organizações híbridas ao implantarem a [Proteção por senha do Azure AD para Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). A proteção por senha do Azure AD bloqueará usuários a partir das escolhas dessas senhas comuns e poderá ser estendida para bloquear a senha contendo as palavras-chave personalizadas que você especificar. Por exemplo, é possível impedir que os usuários escolham senhas que contenham nomes de produtos da empresa ou de uma equipe esportiva local.

A Microsoft recomenda adotar a seguinte política de senha moderna com base nas [diretrizes NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Exigir senhas com pelo menos 8 caracteres. Senhas mais longas não são necessariamente melhores, uma vez que podem fazer com que os usuários escolham senhas previsíveis, salvem senhas em arquivos ou as anotem.
2. Desabilitar regras de expiração, que motivam os usuários a criarem senhas fáceis de descobrir como, por exemplo, **Primavera2019!**
3. Desabilitar os requisitos de composição de caracteres e impedir que os usuários escolham senhas atacadas mais comumente, pois eles fazem com que os usuários escolham substituições de caracteres previsíveis nas senhas.

Você poderá usar o [PowerShell para impedir que as senhas expirem](../../active-directory/authentication/concept-sspr-policy.md) para os usuários, se você criar identidades diretamente no Azure AD. As organizações híbridas devem implementar essas políticas usando as [configurações de política de grupo do domínio](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) ou [Windows PowerShell](/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteger contra credenciais vazadas e adicionar resiliência contra falhas

Se a organização usar uma solução de identidade híbrida com autenticação de passagem ou federação, então, você deverá habilitar a sincronização de hash de senha pelos dois motivos a seguir:

* O relatório [Usuários com credenciais vazadas](../../active-directory/identity-protection/overview-identity-protection.md) no gerenciamento do Azure AD alerta sobre pares de nome de usuário e senha que foram expostos na “dark Web”. Um volume incrível de senhas vazou por meio de phishing, malware e reutilização de senhas em sites de terceiros que posteriormente são violados. A Microsoft encontra muitas dessas credenciais vazadas e informará, neste relatório, se elas corresponderem às credenciais em sua organização – mas somente se você [habilitar a sincronização de hash de senha](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) ou se tiver identidades somente de nuvem!
* No caso de uma interrupção local (por exemplo, em um ataque de ransomware), você pode alternar para usar a autenticação de [nuvem usando a sincronização de hash de senha](../../active-directory/hybrid/choose-ad-authn.md). Esse método de autenticação de backup permitirá que você continue acessando aplicativos configurados para autenticação com Azure Active Directory, incluindo Microsoft 365. Nesse caso, a equipe de TI não precisará recorrer a contas de email pessoal para compartilhar dados até que a interrupção local seja resolvida.

Saiba mais sobre como a [sincronização de hash de senha](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) funciona.

> [!NOTE]
> Se você habilitar a sincronização de hash de senha e estiver usando os serviços de Domínio do Azure AD, hashes Kerberos (AES 256) e, opcionalmente, hashes NTLM (RC4, sem salt) também serão criptografados e sincronizados com o Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementar bloqueio inteligente de extranet do AD FS

As organizações, que configuram aplicativos para que se autentiquem diretamente no Azure AD se beneficiam do [bloqueio inteligente do Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Se você usar o AD FS no Windows Server 2012 R2, implementar o AD FS [proteção de bloqueio de extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Se você usar o AD FS no Windows Server 2016 R2, implemente a [proteção de bloqueio de extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). O bloqueio de extranet AD FS protege contra ataques de força bruta, que tem como alvo o AD FS enquanto evita que os usuários sejam bloqueados no Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Aproveitar credenciais intrinsecamente seguras fáceis de usar

Usando [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification), você pode substituir as senhas com forte autenticação de dois fatores em computadores e dispositivos móveis. Essa autenticação consiste em um novo tipo de credencial de usuário que é vinculado com segurança a um dispositivo e usa um PIN ou biométrico.

## <a name="step-2---reduce-your-attack-surface"></a>Etapa 2: reduzir a superfície de ataque

Considerando a propagação do comprometimento de senha, minimizar a superfície de ataque em sua organização é essencial. Eliminar o uso de protocolos mais antigos e menos segura, limitar os pontos de entrada de acesso e exercer um controle mais significativo do acesso administrativo aos recursos pode ajudar a reduzir a área de superfície de ataque.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada

Os aplicativos que usam seus próprios métodos herdados para realizar a autenticação com o Azure AD e acessar dados da empresa representam outro risco para as organizações. Exemplos de aplicativos que usam autenticação herdada são clientes POP3, IMAP4 ou SMTP. Os aplicativos de autenticação herdados realizam a autenticação em nome do usuário e impedem que o Azure AD realize avaliações de segurança avançadas. A autenticação moderna alternativa reduzirá seu risco de segurança, pois é compatível com o Acesso Condicional e com a autenticação multifator. Recomendamos as três ações a seguir:

1. Bloquear a [autenticação herdada se você usar o AD FS](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurar o [SharePoint Online e o Exchange Online para usar a autenticação moderna](../../active-directory/conditional-access/block-legacy-authentication.md).
3. Se você tiver Azure AD Premium, use políticas de acesso condicional para [bloquear a autenticação herdada](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md); caso contrário, use os [Padrões de Segurança do Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Bloquear pontos de entrada de autenticação inválidos

Usando a mentalidade de pressuposição de violação, você deve reduzir o impacto de credenciais de usuário comprometidas quando elas ocorrem. Para cada aplicativo em seu ambiente, considere os casos de uso válidos: quais grupos, quais redes, quais dispositivos e outros elementos são autorizados e bloqueie o restante. Com o [acesso condicional do Azure AD](../../active-directory/conditional-access/overview.md), você pode controlar como os usuários autorizados acessam os aplicativos e os recursos com base em condições especificas definidas por você.

### <a name="restrict-user-consent-operations"></a>Restringir operações de consentimento do usuário

É importante entender as diversas [experiências de consentimento de aplicativo do Azure AD](../../active-directory/develop/application-consent-experience.md), os [tipos de permissões e de consentimento](../../active-directory/develop/v2-permissions-and-consent.md) e as respectivas implicações na postura de segurança de sua organização. Por padrão, todos os usuários no Azure AD podem conceder acesso aos dados da sua organização para aplicativos que aproveitam a plataforma de identidade da Microsoft. Embora o consentimento dos usuários por si só permita que eles adquiram facilmente aplicativos úteis que se integram ao Microsoft 365, ao Azure e a outros serviços, isso poderá representar um risco se não for usado e monitorado com cuidado.

A Microsoft recomenda restringir o consentimento do usuário para ajudar a reduzir a área de superfície e atenuar esse risco. Você também pode usar [as políticas de consentimento do aplicativo (versão prévia)](../../active-directory/manage-apps/configure-user-consent.md) para restringir o consentimento do usuário final somente para os Publicadores verificados e apenas para as permissões que você selecionar. Se o consentimento do usuário final for restrito, as concessões de consentimento anteriores ainda serão respeitadas, mas todas as operações de consentimento futuras deverão ser executadas por um administrador. Para casos restritos, o consentimento do administrador pode ser solicitado pelos usuários por meio de um [fluxo de trabalho de solicitação de consentimento de administrador](../../active-directory/manage-apps/configure-admin-consent-workflow.md) integrado ou por meio de seus próprios processos de suporte Antes de restringir o consentimento do usuário final, use nossas [recomendações](../../active-directory/manage-apps/manage-consent-requests.md) para planejar essa alteração em sua organização. Para aplicativos que você deseja permitir que todos os usuários acessem, considere [conceder consentimento em nome de todos os usuários](../../active-directory/develop/v2-admin-consent.md), assegurando que os usuários que ainda não consentiram individualmente possam acessar o aplicativo. Se você não quiser que esses aplicativos estejam disponíveis para todos os usuários em todos os cenários, use a [atribuição de aplicativo](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) e o acesso condicional para restringir o acesso do usuário a [aplicativos específicos](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md).

Verifique se os usuários podem solicitar aprovação de administrador para novos aplicativos a fim de reduzir o conflito do usuário, minimizar o volume de suporte e impedir que os usuários se inscrevam em aplicativos que usam credenciais não Azure AD. Depois de você regulamentar suas operações de consentimento, os administradores devem auditar as permissões do aplicativo e consentidas regularmente.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementar o Azure AD Privileged Identity Management

Outro impacto da "pressuposição de violação" é a necessidade de minimizar a probabilidade de que uma conta comprometida possa operar com uma função com privilégios.

O [Azure AD PIM (Privileged Identity Management)](../../active-directory/privileged-identity-management/pim-configure.md) ajuda você a minimizar os privilégios de conta ajudando a:

* Identificar e gerenciar os usuários atribuídos a funções administrativas.
* Compreender funções com privilégios não usados ou excessivos que devem ser removidos.
* Estabelecer regras para garantir que as funções com privilégios sejam protegidas pela autenticação multifator.
* Estabelecer regras para garantir que as funções com privilégios sejam concedidas apenas pelo tempo suficiente para realizar a tarefa com privilégios.

Habilite o Azure AD PIM e exiba os usuários que têm funções administrativas atribuídas e remova as contas desnecessárias nessas funções. Para os usuários com privilégios restantes, mova-os de permanentes para qualificados. Por fim, estabeleça políticas apropriadas para garantir que ao necessitarem do acesso a essas funções privilegiadas, possam fazer isso com segurança e controle de alterações necessários.

Como parte da implantação do seu processo de conta com privilégios, siga as [melhores práticas para criar pelo menos duas contas de emergência](../../active-directory/roles/security-planning.md) para garantir que você ainda tenha acesso ao Azure AD caso fique bloqueado.

## <a name="step-3---automate-threat-response"></a>Etapa 3: automatizar a resposta a ameaças

O Azure Active Directory tem muitos recursos que interceptam automaticamente ataques, para remover a latência entre a detecção e a resposta. Você pode reduzir os custos e os riscos quando reduz o tempo que os criminosos usam para se inserir em seu ambiente. Aqui estão as etapas de concretas que podem ser adotadas.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementar a política de segurança de risco do usuário usando o Azure AD Identity Protection

O risco do usuário indica a probabilidade de a identidade de um usuário ter sido comprometida, que é calculada com base nas [detecções de risco do usuário](../../active-directory/identity-protection/overview-identity-protection.md) associadas à identidade de um determinado usuário. Uma política de risco do usuário é uma política de acesso condicional que avalia o nível de risco para um grupo ou usuário específico. Com base no nível de risco Baixo, Médio ou Alto, é possível configurar uma política para bloquear o acesso ou exigir uma mudança de senha segura usando a autenticação multifator. A recomendação da Microsoft é a de exigir uma mudança de senha segura para usuários com risco alto.

![Captura de tela mostra os usuários sinalizados para risco, com um usuário selecionado.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementar a política de risco de entrada usando o Azure AD Identity Protection

O risco de entrada é a probabilidade de alguém que não o proprietário da conta estar tentando entrar usando a identidade. Uma [política de risco de entrada](../../active-directory/identity-protection/overview-identity-protection.md) é uma política de acesso condicional que avalia o nível de risco para um grupo ou usuário específico. Com base no nível de risco (baixo/médio/alto), é possível configurar uma política para bloquear o acesso ou forçar a autenticação multifator. Certifique-se de forçar a autenticação multifator em entradas de risco Médio ou acima.

![Entrada de IPs anônimos](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Etapa 4 – Usar a inteligência de nuvem

A auditoria e o log de eventos relacionados à segurança e alertas relacionados são componentes fundamentais de uma estratégia de proteção eficiente. Os logs e os relatórios de segurança fornecem um registro eletrônico das atividades suspeitas e ajudam a detectar padrões que podem indicar uma tentativa de penetração externa ou uma penetração externa com êxito da rede, bem como ataques internos. Você pode usar a auditoria para monitorar as atividades do usuário, a conformidade regulatória de documentos, realizar análise forense e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="monitor-azure-ad"></a>Monitorar o Azure AD

Os serviços e recursos do Microsoft Azure fornecem opções configuráveis de auditoria e log para ajudá-lo a identificar lacunas em suas políticas e seus mecanismos de segurança e corrigi-las, a fim de ajudar a prevenir violações de segurança. Você pode usar a [Auditoria e os Logs do Azure](log-audit.md) e os [Relatórios de atividades de auditoria no portal do Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorar o Azure AD Connect Health em ambientes híbridos

O [Monitoramento do AD FS com Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) fornece um insight maior sobre os possíveis problemas e a visibilidade de ataques na infraestrutura do AD FS. O Azure AD Connect Health fornece alertas com detalhes, etapas de resolução e links para a documentação relacionada, análise de uso para várias métricas relacionadas ao tráfego de autenticação, o monitoramento de desempenho e relatórios.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorar eventos do Azure AD Identity Protection

O [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) é uma ferramenta de notificação, monitoramento e relatórios que você pode usar para detectar possíveis vulnerabilidades que afetam as identidades da organização. Ele detecta detecções de risco, como credenciais vazadas, viagem impossível e entradas de dispositivos infectados, endereços IP anônimos, endereços IP associados à atividade suspeita e localizações desconhecidas. Habilite os alertas de notificação para receber email de usuários em risco e/ou um email de resumo semanal.

O Azure AD Identity Protection fornece dois relatórios importantes que devem ser monitorados diariamente:
1. Os relatórios de entradas arriscadas exibirão as atividades de entrada do usuário que você deve investigar, pois o proprietário legítimo pode não ter sido verificado na entrada.
2. Os relatórios de usuário arriscados exibirão as contas de usuários que poderão ter sido comprometidas, como as credenciais perdidas detectadas ou usuário que entrou a partir de localizações diferentes causando um evento de viagem impossível.

![Captura de tela mostra o painel de proteção de identidade do Azure a D com usuários e seus níveis de risco.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Auditar aplicativos e permissões de consentimento

Os usuários podem ser induzidos a navegar para um site ou aplicativos comprometidos que terão acesso às informações do perfil e aos dados dos usuários, como o email deles. Um ator mal-intencionado pode usar as permissões consentidas que recebeu para criptografar o conteúdo da caixa de correio e exigir um resgate para recuperar os dados da caixa de correio. [Os administradores devem examinar e auditar](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) as permissões fornecidas pelos usuários ou desabilitar a capacidade dos usuários de dar consentimento por padrão.

Além de auditar as permissões fornecidas pelos usuários, você pode [localizar aplicativos OAuth arriscados ou indesejados](/cloud-app-security/investigate-risky-oauth) em ambientes premium.

## <a name="step-5---enable-end-user-self-service"></a>Etapa 5 – Habilitar o autoatendimento do usuário final

Você vai querer balancear a segurança com a produtividade o máximo possível. Na mesma perspectiva de abordar sua jornada com a mentalidade de que você está estabelecendo uma base para segurança em longo prazo, é possível remover o atrito da sua organização capacitando seus usuários enquanto permanece vigilante.

### <a name="implement-self-service-password-reset"></a>Implementar a redefinição de senha por autoatendimento

A [SSPR (redefinição de senha por autoatendimento)](../../active-directory/authentication/tutorial-enable-sspr.md) do Azure AD oferece um meio simples para os administradores de TI permitirem que os usuários redefinam ou desbloqueiem suas senhas ou contas sem suporte técnico nem intervenção do administrador. O sistema inclui relatórios detalhados que acompanham quando os usuários redefinem as respectivas senhas, junto com notificações para alertá-lo sobre uso indevido ou abuso.

### <a name="implement-self-service-group-and-application-access"></a>Implementar acesso self-service a aplicativos e grupos

O Azure AD fornece a capacidade de não administradores gerenciarem o acesso a recursos, usando grupos de segurança, grupos de Microsoft 365, funções de aplicativo e catálogos de pacotes de acesso.  O [gerenciamento de grupos de autoatendimento](../../active-directory/enterprise-users/groups-self-service-management.md) permite que os proprietários de grupos gerenciem os respectivos grupos sem a necessidade de uma função administrativa ser atribuída a eles. Os usuários também podem criar e gerenciar grupos de Microsoft 365 sem depender de administradores para lidar com suas solicitações e os grupos não utilizados expiram automaticamente.  O [gerenciamento de direitos do Azure AD](../../active-directory/governance/entitlement-management-overview.md) permite ainda mais delegação e visibilidade, com fluxos de trabalho de solicitação de acesso abrangentes e expiração automática.  Você pode delegar a não administradores a capacidade de configurar os respectivos pacotes de acesso para grupos, equipes, aplicativos e sites do SharePoint Online que eles têm, com políticas personalizadas para quem precisa aprovar o acesso, incluindo a configuração de patrocinadores de parceiros de negócios e gerentes de funcionários como aprovadores.

### <a name="implement-azure-ad-access-reviews"></a>Implementar revisões de acesso do Azure AD

Com as [revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md), é possível gerenciar o pacote de acesso e as associações a grupos, o acesso a aplicativos empresariais e as atribuições de funções com privilégios para garantir que você mantenha um padrão de segurança.  Uma supervisão regular pelos próprios usuários, proprietários de recursos e outros revisores garante que os usuários não mantenham o acesso por períodos de tempo longos quando não precisarem mais dele.

## <a name="summary"></a>Resumo

Há muitos aspectos para uma infraestrutura de identidade segura, mas essa lista de verificação de cinco etapas ajudará você a obter rapidamente uma infraestrutura de identidade mais segura e protegida:

* Fortaleça suas credenciais.
* Reduza a área de superfície de ataque.
* Automatize a resposta a ameaças.
* Use a inteligência de nuvem.
* Habilite a segurança do usuário final mais previsível e completa com autoajuda.

Apreciamos a seriedade com que você encara a Segurança de Identidade e esperamos que esse documento seja um roteiro útil para uma postura mais segura para sua organização.

## <a name="next-steps"></a>Próximas etapas

Se precisar de ajuda para planejar e implantar as recomendações, confira os [planos de implantação de projeto do Azure AD](../../active-directory/fundamentals/active-directory-deployment-plans.md) para obter ajuda.

Se você tiver certeza de que todas essas etapas estão concluídas, use a [classificação de segurança de identidade da Microsoft](../../active-directory/fundamentals/identity-secure-score.md), que manterá você atualizado com as ameaças de segurança e [melhores práticas mais recentes](identity-management-best-practices.md).

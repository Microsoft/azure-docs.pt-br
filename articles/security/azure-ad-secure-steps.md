---
title: Cinco etapas para proteger sua infraestrutura de identidade no Azure Active Directory
description: Este documento descreve uma lista de ações importantes que os administradores devem implementar para ajudá-los a proteger sua organização usando recursos do Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: 92546e6aabdf43c2f9cb0339fb21dd2dfc641d44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587782"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinco etapas para proteger sua infraestrutura de identidade

Se você está lendo esse documento, está ciente da significância da segurança. Você provavelmente já tem a responsabilidade de proteger sua organização. Se precisar convencer outras pessoas sobre a importância da segurança, envie-as para que leiam o [Relatório de inteligência de segurança da Microsoft](https://go.microsoft.com/fwlink/p/?linkid=2073747) mais recente.

Esse documento ajudará você a obter uma postura mais segura usando os recursos do Azure Active Directory utilizando uma lista de verificação de cinco etapas para imunizar sua organização contra ataques cibernéticos.

Esta lista de verificação ajudará você a implantar rapidamente ações recomendadas críticas para proteger sua organização imediatamente explicando como:

* Fortaleça suas credenciais.
* Reduza a área de superfície de ataque.
* Automatize a resposta a ameaças.
* Aumente o reconhecimento de auditoria e monitoramento.
* Habilite a segurança do usuário final mais previsível e completa com autoajuda.

> [!NOTE]
> Muitas das recomendações contidas neste documento se aplicam apenas a aplicativos, que são configurados para usar o Azure Active Directory como seu provedor de identidade. Configurar os aplicativos para utilizar o logon único garante os benefícios de políticas de credenciais, detecção de ameaças, auditoria, registro em log e outros recursos são adicionados a esses aplicativos. O [logon único por meio do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) é a base na qual todas essas recomendações se baseiam.

As recomendações neste documento estão alinhadas com a [Classificação de Segurança de Identidade](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score), uma avaliação automatizada da configuração de segurança de identidade do locatário do Azure AD. As organizações podem usar a página de Classificação de Segurança de Identidade no portal do Azure AD para localizar lacunas na configuração de segurança atual e assegurar que sigam as melhores práticas atuais da Microsoft para segurança. Implementar cada recomendação na página de Classificação de Segurança aumentará sua classificação e permitirá que você acompanhe o progresso, além de ajudá-lo a comparar a implementação com outras organizações de porte semelhante ou do setor.

![Classificação de segurança de identidade](media/azure-ad/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Antes de começar: Proteja contas com privilégios com MFA

Antes de iniciar esta lista de verificação, certifique-se de que não seja comprometido enquanto lê essa lista. Você precisa primeiro proteger suas contas com privilégios.

Os invasores que obtêm o controle de contas com privilégios podem causar danos tremendos, portanto, é essencial proteger essas contas primeiro. Habilite e exija a [MFA (Autenticação Multifator) do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) para todos os administradores na organização usando a [Proteção de Linha de Base](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Se você ainda não implementou a MFA, faça isso agora! Isso é realmente importante.

Tudo pronto? Vamos começar a trabalhar na lista de verificação.

## <a name="step-1---strengthen-your-credentials"></a>Etapa 1: fortalecer suas credenciais 

A maioria das violações de segurança empresariais são originadas com uma conta comprometida com um de uma série de métodos como pulverização de senha, reprodução de violação ou phishing. Saiba mais sobre esses ataques neste vídeo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Certificar-se de que sua organização use a autenticação forte

Considerando a frequência com que as senhas são adivinhadas, obtidas por phishing, roubadas com malware ou reutilizadas, é fundamental auxiliar a senha com alguma forma de credencial forte, saiba mais sobre a [Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Comece a proibir senhas comumente atacadas e desative a complexidade tradicional e as regras de expiração.

Muitas organizações usam a complexidade tradicional (exigindo caracteres especiais, números, letras maiúsculas e minúsculas) e regras de expiração de senha. A [pesquisa da Microsoft](https://aka.ms/passwordguidance) mostrou que essas políticas fazem com que os usuários escolham senhas que sejam mais fáceis de adivinhar.

O recurso de [senha proibida dinamicamente](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) do Azure AD usa o comportamento atual do invasor para impedir que os usuários definam senhas que possam ser facilmente adivinhadas. Esse recurso sempre é ativado quando os usuários são criados na nuvem, mas agora também está disponível para organizações híbridas ao implantarem a [Proteção por senha do Azure AD para Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). A proteção por senha do Azure AD bloqueará usuários a partir das escolhas dessas senhas comuns e poderá ser estendida para bloquear a senha contendo as palavras-chave personalizadas que você especificar. Por exemplo, é possível impedir que os usuários escolham senhas que contenham nomes de produtos da empresa ou de uma equipe esportiva local.

A Microsoft recomenda adotar a seguinte política de senha moderna com base nas [diretrizes NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Exigir senhas com pelo menos 8 caracteres. Senhas mais longas não são necessariamente melhores, uma vez que podem fazer com que os usuários escolham senhas previsíveis, salvem senhas em arquivos ou as anotem.
2. Desabilitar regras de expiração, as quais motivam os usuários a criarem senhas fáceis de descobrir como, por exemplo, **Verão2018!**.
3. Desabilitar os requisitos de composição de caracteres e impedir que os usuários escolham senhas atacadas mais comumente, pois eles fazem com que os usuários escolham substituições de caracteres previsíveis nas senhas.

Você poderá usar o [PowerShell para impedir que as senhas expirem](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) para os usuários, se você criar identidades diretamente no Azure AD. As organizações híbridas devem implementar essas políticas usando as [configurações de política de grupo do domínio](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) ou [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteger contra credenciais vazadas e adicionar resiliência contra falhas

Se a organização usar uma solução de identidade híbrida com autenticação de passagem ou federação, então, você deverá habilitar a sincronização de hash de senha pelos dois motivos a seguir:

* O relatório [Usuários com credenciais vazadas](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) no gerenciamento do Azure AD alerta sobre pares de nome de usuário e senha que foram expostos na “dark Web”. Um volume incrível de senhas vazou por meio de phishing, malware e reutilização de senhas em sites de terceiros que posteriormente são violados. A Microsoft localizará muitas dessas credenciais vazadas e indicará, nesse relatório, se elas correspondem às credenciais da sua organização, mas somente se você [habilitar a sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* No caso de uma interrupção local (por exemplo, em um ataque de ransomware), você poderá usar a [autenticação em nuvem usando a sincronização de hash de senha](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Esse método de autenticação de backup permitirá que você continue a acessar os aplicativos configurados para autenticação com o Azure Active Directory, incluindo o Office 365. Nesse caso, a equipe de TI não precisará recorrer a contas de email pessoal para compartilhar dados até que a interrupção local seja resolvida.

Saiba mais sobre como a [sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) funciona.

> [!NOTE]
> Se você habilitar a sincronização de hash de senha e estiver usando os serviços de Domínio do Azure AD, hashes Kerberos (AES 256) e, opcionalmente, hashes NTLM (RC4, sem salt) também serão criptografados e sincronizados com o Azure AD. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementar bloqueio inteligente de extranet do AD FS

As organizações, que configuram aplicativos para que se autentiquem diretamente no Azure AD se beneficiam do [bloqueio inteligente do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Se você usar o AD FS no Windows Server 2012 R2, implementar o AD FS [proteção de bloqueio de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Se você usar o AD FS no Windows Server 2016 R2, implemente a [proteção de bloqueio de extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). O bloqueio de extranet AD FS protege contra ataques de força bruta, que tem como alvo o AD FS enquanto evita que os usuários sejam bloqueados no Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Aproveitar credenciais intrinsecamente seguras fáceis de usar

Usando [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), você pode substituir as senhas com forte autenticação de dois fatores em computadores e dispositivos móveis. Essa autenticação consiste em um novo tipo de credencial de usuário que é vinculado com segurança a um dispositivo e usa um PIN ou biométrico.

## <a name="step-2---reduce-your-attack-surface"></a>Etapa 2: reduzir a superfície de ataque

Considerando a propagação do comprometimento de senha, minimizar a superfície de ataque em sua organização é essencial. Eliminar o uso de protocolos mais antigos e menos segura, limitar os pontos de entrada de acesso e exercer um controle mais significativo do acesso administrativo aos recursos pode ajudar a reduzir a área de superfície de ataque.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada

Os aplicativos que usam seus próprios métodos herdados para realizar a autenticação com o Azure AD e acessar dados da empresa representam outro risco para as organizações. Exemplos de aplicativos que usam autenticação herdada são clientes POP3, IMAP4 ou SMTP. Os aplicativos de autenticação herdados realizam a autenticação em nome do usuário e impedem que o Azure AD realize avaliações de segurança avançadas. A autenticação moderna alternativa reduzirá seu risco de segurança, pois é compatível com o acesso condicional e com a autenticação multifator. Recomendamos as três ações a seguir:

1. Bloquear a [autenticação herdada se você usar o AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurar o [SharePoint Online e o Exchange Online para usar a autenticação moderna](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Usar [Políticas de acesso condicional para bloquear a autenticação herdada](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions).

### <a name="block-invalid-authentication-entry-points"></a>Bloquear pontos de entrada de autenticação inválidos

Usando a mentalidade de pressuposição de violação, você deve reduzir o impacto de credenciais de usuário comprometidas quando elas ocorrem. Para cada aplicativo em seu ambiente, considere os casos de uso válidos: quais grupos, quais redes, quais dispositivos e outros elementos são autorizados e bloqueie o restante. Com o [acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), você pode controlar como os usuários autorizados acessam os aplicativos e os recursos com base em condições especificas definidas por você.

### <a name="block-end-user-consent"></a>Bloquear o consentimento do usuário final

Por padrão, todos os usuários no Azure AD têm permissão para conceder aplicativos que aproveitam as permissões de [estrutura de consentimento](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) de identidade da Microsoft e do OAuth 2.0 para acessar os dados da empresa. Embora o consentimento permita que os usuários adquiram facilmente aplicativos úteis que integram-se ao Microsoft 365 e ao Azure, isso poderá representar um risco se não for usado e monitorado com cuidado. [Desabilitar todas as operações de consentimento futuras do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access) poderá ajudar a reduzir a área de superfície e atenuar esse risco. Se o consentimento do usuário final estiver desabilitado, os subsídios de consentimento anteriores ainda serão honrados, mas todas as operações de consentimento futuras deverão ser executadas por um administrador. Antes de desabilitar essa funcionalidade, é recomendável garantir que os usuários compreendam como solicitar a aprovação do administrador para novos aplicativos, pois isso deverá ajudar a reduzir o atrito do usuário, minimizar o volume de suporte e garantir que os usuários não inscrevam-se em aplicativos que usam credenciais não Azure AD.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementar o Azure AD Privileged Identity Management

Outro impacto da "pressuposição de violação" é a necessidade de minimizar a probabilidade de que uma conta comprometida possa operar com uma função com privilégios. 

O [Azure AD PIM (Privileged Identity Management)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ajuda você a minimizar os privilégios de conta ajudando a:

* Identificar e gerenciar os usuários atribuídos a funções administrativas.
* Compreender funções com privilégios não usados ou excessivos que devem ser removidos.
* Estabelecer regras para garantir que as funções com privilégios sejam protegidas pela autenticação multifator.
* Estabelecer regras para garantir que as funções com privilégios sejam concedidas apenas pelo tempo suficiente para realizar a tarefa com privilégios.

Habilite o Azure AD PIM e exiba os usuários que têm funções administrativas atribuídas e remova as contas desnecessárias nessas funções. Para os usuários com privilégios restantes, mova-os de permanentes para qualificados. Por fim, estabeleça políticas apropriadas para garantir que ao necessitarem do acesso a essas funções privilegiadas, possam fazer isso com segurança e controle de alterações necessários.

Como parte da implantação do seu processo de conta com privilégios, siga as [melhores práticas para criar pelo menos duas contas de emergência](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) para garantir que você tenha acesso ao Azure AD caso fique bloqueado.

## <a name="step-3---automate-threat-response"></a>Etapa 3: automatizar a resposta a ameaças

O Azure Active Directory tem muitos recursos que interceptam automaticamente ataques, para remover a latência entre a detecção e a resposta. Você pode reduzir os custos e os riscos quando reduz o tempo que os criminosos usam para se inserir em seu ambiente. Aqui estão as etapas de concretas que podem ser adotadas.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementar a política de segurança de risco do usuário usando o Azure AD Identity Protection

O risco do usuário indica a probabilidade de a identidade de um usuário ter sido comprometida e é calculada com base nos [eventos de risco do usuário](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) associados à identidade de um usuário. Uma política de risco de usuário é uma política de acesso condicional que avalia o nível de risco para um grupo ou usuário específico. Com base no nível de risco Baixo, Médio ou Alto, é possível configurar uma política para bloquear o acesso ou exigir uma mudança de senha segura usando a autenticação multifator. A recomendação da Microsoft é a de exigir uma mudança de senha segura para usuários com risco alto.

![Usuários sinalizados por risco](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementar a política de risco de entrada usando o Azure AD Identity Protection

O risco de entrada é a probabilidade de alguém que não o proprietário da conta estar tentando entrar usando a identidade. Uma [política de risco de entrada](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é uma política de acesso condicional que avalia o nível de risco para um grupo ou usuário específico. Com base no nível de risco (baixo/médio/alto), é possível configurar uma política para bloquear o acesso ou forçar a autenticação multifator. Certifique-se de forçar a autenticação multifator em entradas de risco Médio ou acima.

![Entrada de IPs anônimos](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Etapa 4: aumentar o reconhecimento

A auditoria e o log de eventos relacionados à segurança e alertas relacionados são componentes fundamentais de uma estratégia de proteção eficiente. Os logs e os relatórios de segurança fornecem um registro eletrônico das atividades suspeitas e ajudam a detectar padrões que podem indicar uma tentativa de penetração externa ou uma penetração externa com êxito da rede, bem como ataques internos. Você pode usar a auditoria para monitorar as atividades do usuário, a conformidade regulatória de documentos, executar análise forense e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="monitor-azure-ad"></a>Monitorar o Azure AD

Os serviços e recursos do Microsoft Azure fornecem opções configuráveis de auditoria e log para ajudá-lo a identificar lacunas em suas políticas e seus mecanismos de segurança e corrigi-las, a fim de ajudar a prevenir violações de segurança. Você pode usar a [Auditoria e os Logs do Azure](https://docs.microsoft.com/azure/security/azure-log-audit) e os [Relatórios de atividades de auditoria no portal do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorar o Azure AD Connect Health em ambientes híbridos

O [Monitoramento do AD FS com Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) fornece um insight maior sobre os possíveis problemas e a visibilidade de ataques na infraestrutura do AD FS. O Azure AD Connect Health fornece alertas com detalhes, etapas de resolução e links para a documentação relacionada, análise de uso para várias métricas relacionadas ao tráfego de autenticação, o monitoramento de desempenho e relatórios.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorar eventos do Azure AD Identity Protection

O [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é uma ferramenta de notificação, monitoramento e relatórios que você pode usar para detectar possíveis vulnerabilidades que afetam as identidades da organização. Ele detecta eventos de risco, como credenciais vazadas, viagem impossível e entradas de dispositivos infectados, endereços IP anônimos, endereços IP associados à atividade suspeita e localizações desconhecidas. Habilite os alertas de notificação para receber email de usuários em risco e/ou um email de resumo semanal.

O Azure AD Identity Protection fornece dois relatórios importantes que devem ser monitorados diariamente:
1. Os relatórios de entradas arriscadas exibirão as atividades de entrada do usuário que você deve investigar, pois o proprietário legítimo pode não ter sido verificado na entrada.
2. Os relatórios de usuário arriscados exibirão as contas de usuários que poderão ter sido comprometidas, como as credenciais perdidas detectadas ou usuário que entrou a partir de localizações diferentes causando um evento de viagem impossível. 

![Usuários sinalizados por risco](media/azure-ad/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Auditar aplicativos e permissões de consentimento

Os usuários podem ser induzidos a navegar para um site ou aplicativos comprometidos que terão acesso às informações do perfil e aos dados do usuário, como o email. Um ator mal-intencionado pode usar as permissões consentidas que recebeu para criptografar o conteúdo da caixa de correio e exigir um resgate para recuperar os dados da caixa de correio. [Os administradores devem revisar e auditar](https://blogs.technet.microsoft.com/office365security/defending-against-illicit-consent-grants/) as permissões dadas pelos usuários.

## <a name="step-5---enable-end-user-self-help"></a>Etapa 5: habilitar a autoajuda do usuário final

Você vai querer balancear a segurança com a produtividade o máximo possível. Na mesma perspectiva de abordar sua jornada com a mentalidade de que você está estabelecendo uma base para segurança em longo prazo, é possível remover o atrito da sua organização capacitando seus usuários enquanto permanece vigilante. 

### <a name="implement-self-service-password-reset"></a>Implementar a redefinição de senha por autoatendimento

A [SSPR (redefinição da senha de autoatendimento)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) do Azure oferece um meio simples para os administradores de TI permitirem que os usuários redefinam ou desbloqueiem suas senhas ou contas sem a intervenção do administrador. O sistema inclui relatórios detalhados que acompanham quando os usuários acessam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso. 

### <a name="implement-self-service-group-management"></a>Implementar o gerenciamento de grupos de autoatendimento

O Azure AD fornece a capacidade de gerenciar o acesso aos recursos usando grupos de segurança e Grupos do Office 365. Esses grupos podem ser gerenciados por proprietários de grupo em vez de administradores de TI. Conhecido como [gerenciamento de grupos de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), esse recurso permite que os proprietários de grupos que não têm uma função administrativa criem e gerenciem grupos sem depender dos administradores para tratar de suas solicitações.

### <a name="implement-azure-ad-access-reviews"></a>Implementar revisões de acesso do Azure AD

Com as [revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), é possível gerenciar as associações a grupos, o acesso a aplicativos empresariais e as atribuições de funções com privilégios para garantir que você mantenha um padrão de segurança que não concede aos usuários acesso por períodos estendidos quando não é necessário.

## <a name="summary"></a>Resumo

Há muitos aspectos para uma infraestrutura de identidade segura, mas essa lista de verificação de cinco etapas ajudará você a obter rapidamente uma infraestrutura de identidade mais segura e protegida:

* Fortaleça suas credenciais.
* Reduza a área de superfície de ataque.
* Automatize a resposta a ameaças.
* Aumente o reconhecimento de auditoria e monitoramento.
* Habilite a segurança do usuário final mais previsível e completa com autoajuda.

Apreciamos a seriedade com que você encara a Segurança de Identidade e esperamos que esse documento seja um roteiro útil para uma postura mais segura para sua organização.

## <a name="next-steps"></a>Próximas etapas
Se precisar de ajuda para planejar e implantar as recomendações, confira os [planos de implantação de projeto do Azure AD](https://aka.ms/deploymentplans) para obter ajuda.

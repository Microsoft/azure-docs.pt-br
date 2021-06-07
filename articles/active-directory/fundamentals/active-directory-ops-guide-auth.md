---
title: Guia de referência de operações de gerenciamento de autenticação Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve seguir para proteger o gerenciamento de autenticação
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 0d425111e151d60ab38a60e38cd1805cef39f606
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641770"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Guia de referência de operações de gerenciamento de autenticação Azure Active Directory

Esta seção do [Guia de referência de operações do Azure ad](active-directory-ops-guide-intro.md) descreve as verificações e as ações que você deve executar para proteger e gerenciar credenciais, definir a experiência de autenticação, delegar a atribuição, o uso de medidas e definir políticas de acesso com base na postura de segurança corporativa.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas principais

O gerenciamento de Azure Active Directory requer a execução contínua das principais tarefas e processos operacionais, que podem não fazer parte de um projeto de distribuição. Ainda é importante que você configure essas tarefas para otimizar seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Gerenciar o ciclo de vida da configuração de SSO (logon único) no Azure AD | Equipe de operações IAM |
| Criar políticas de acesso condicional para aplicativos do Azure AD | Equipe de arquitetura do batalha |
| Arquivar atividade de entrada em um sistema SIEM | Equipe de operações do batalha |
| Arquivar eventos de risco em um sistema SIEM | Equipe de operações do batalha |
| Fazer triagem e investigar relatórios de segurança | Equipe de operações do batalha |
| Fazer triagem e investigar eventos de risco | Equipe de operações do batalha |
| Fazer triagem e investigar os usuários sinalizados para os relatórios de risco e vulnerabilidade de Azure AD Identity Protection | Equipe de operações do batalha |

> [!NOTE]
> Azure AD Identity Protection requer uma licença do Azure AD Premium P2. Para encontrar a licença certa para seus requisitos, consulte comparando [recursos disponíveis de forma geral das edições Azure ad gratuito e Azure ad Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Ao examinar sua lista, você pode achar necessário atribuir um proprietário para tarefas que não têm um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhadas com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada pelo proprietário

- [Atribuindo funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governança no Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Gerenciamento de credenciais

### <a name="password-policies"></a>Políticas de senha

O gerenciamento de senhas com segurança é uma das partes mais importantes do gerenciamento de identidade e acesso e, muitas vezes, da maior meta de ataques. O Azure AD dá suporte a vários recursos que podem ajudar a impedir que um ataque seja bem-sucedido.

Use a tabela a seguir para encontrar a solução recomendada para atenuar o problema que precisa ser resolvido:

| Problema | Recomendação |
| :- | :- |
| Nenhum mecanismo para proteção contra senhas fracas | Habilitar [SSPR (redefinição de senha de autoatendimento)](../authentication/concept-sspr-howitworks.md) do Azure AD e [proteção por senha](../authentication/concept-password-ban-bad-on-premises.md) |
| Nenhum mecanismo para detectar senhas vazadas | Habilitar a [sincronização de hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) para obter informações |
| Usando AD FS e não é possível mover para a autenticação gerenciada | Habilitar [AD FS bloqueio inteligente de extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) e/ou [bloqueio inteligente do Azure ad](../authentication/howto-password-smart-lockout.md) |
| A política de senha usa regras baseadas em complexidade, como comprimento, vários conjuntos de caracteres ou expiração | Reconsidere em favor das [práticas recomendadas da Microsoft](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) e mude sua abordagem para o gerenciamento de senhas e implante a [proteção de senha do Azure ad](../authentication/concept-password-ban-bad.md). |
| Os usuários não estão registrados para usar a MFA (autenticação multifator) | [Registrar as informações de segurança de todos os usuários](../identity-protection/howto-identity-protection-configure-mfa-policy.md) para que ele possa ser usado como um mecanismo para verificar a identidade do usuário junto com sua senha |
| Não há revogação de senhas com base no risco do usuário | Implantar [as políticas de risco do usuário](../identity-protection/howto-identity-protection-configure-risk-policies.md) do Azure ad Identity Protection para forçar alterações de senha em credenciais vazadas usando o SSPR |
| Não há nenhum mecanismo de bloqueio inteligente para proteger a autenticação mal-intencionada de atores ruins provenientes de endereços IP identificados | Implantar a autenticação gerenciada por nuvem com a sincronização de hash de senha ou [autenticação de passagem](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Leitura recomendada de políticas de senha

- [Práticas recomendadas do Azure AD e AD FS: defesa contra ataques de irrigação de senha-Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Habilitar redefinição de senha de autoatendimento e proteção por senha

Os usuários que precisam alterar ou redefinir suas senhas são uma das maiores fontes de volume e custo de chamadas de assistência técnica. Além do custo, alterar a senha como uma ferramenta para mitigar um risco de usuário é uma etapa fundamental para melhorar a postura de segurança de sua organização.

No mínimo, é recomendável implantar a SSPR ( [redefinição de senha de autoatendimento](../authentication/concept-sspr-howitworks.md) ) do Azure AD e a [proteção de senha](../authentication/howto-password-ban-bad-on-premises-deploy.md) local para realizar:

- Chamadas de suporte técnico de deflexão.
- Substitua o uso de senhas temporárias.
- Substitua qualquer solução de gerenciamento de senha de autoatendimento existente que dependa de uma solução local.
- [Elimine senhas fracas](../authentication/concept-password-ban-bad.md) em sua organização.

> [!NOTE]
> Para organizações com uma assinatura do Azure AD Premium P2, é recomendável implantar o SSPR e usá-lo como parte de uma [política de risco do usuário de proteção de identidade](../identity-protection/howto-identity-protection-configure-risk-policies.md).

### <a name="strong-credential-management"></a>Gerenciamento de credenciais forte

As senhas por si só não são seguras o suficiente para evitar que atores ruins obtenham acesso ao seu ambiente. No mínimo, qualquer usuário com uma conta privilegiada deve ser habilitado para a autenticação multifator (MFA). O ideal é que você habilite o [registro combinado](../authentication/concept-registration-mfa-sspr-combined.md) e exija que todos os usuários se registrem para MFA e SSPR usando a [experiência de registro combinada](../user-help/security-info-setup-signin.md). Por fim, recomendamos que você adote uma estratégia para [fornecer resiliência](../authentication/concept-resilient-controls.md) para reduzir o risco de bloqueio devido a circunstâncias imprevistas.

![Fluxo de experiência do usuário combinado](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Resiliência local de autenticação de falha

Além dos benefícios da simplicidade e da habilitação da detecção de credenciais vazadas, a sincronização de hash de senha do Azure AD (PHS) e a MFA do Azure AD permitem que os usuários acessem aplicativos SaaS e Microsoft 365 apesar de interrupções locais devido a ataques cibernéticos como [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Também é possível habilitar o PHS enquanto estiver em conjunto com a Federação. Habilitar o PHS permite o fallback de autenticação quando os serviços de Federação não estão disponíveis.

Se sua organização local não estiver em uma estratégia de resiliência de interrupção ou se tiver uma que não esteja integrada ao Azure AD, você deverá implantar o Azure AD PHS e definir um plano de recuperação de desastres que inclua o PHS. Habilitar o Azure AD PHS permitirá que os usuários se autentiquem no Azure AD se seu local Active Directory estiver indisponível.

![fluxo de sincronização de hash de senha](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Para entender melhor suas opções de autenticação, consulte [escolher o método de autenticação correto para sua solução de identidade híbrida Azure Active Directory](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Uso programático de credenciais

Os scripts do Azure AD usando o PowerShell ou aplicativos que usam a API Microsoft Graph exigem autenticação segura. O gerenciamento de credenciais deficiente executando esses scripts e ferramentas aumenta o risco de roubo de credenciais. Se você estiver usando scripts ou aplicativos que dependem de Senhas embutidas em código ou de senha, você deve primeiro examinar as senhas em arquivos de configuração ou código-fonte e, em seguida, substituir essas dependências e usar identidades gerenciadas do Azure, Integrated-Windows autenticação ou [certificados](../reports-monitoring/tutorial-access-api-with-certificates.md) sempre que possível. Para aplicativos em que as soluções anteriores não são possíveis, considere o uso de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Se você determinar que há entidades de serviço com credenciais de senha e não tiver certeza de como essas credenciais de senha são protegidas por scripts ou aplicativos, entre em contato com o proprietário do aplicativo para entender melhor os padrões de uso.

A Microsoft também recomenda que você entre em contato com os proprietários do aplicativo para entender os padrões de uso se houver entidades de serviço com credenciais de senha.

## <a name="authentication-experience"></a>Experiência de autenticação

### <a name="on-premises-authentication"></a>Autenticação local

A autenticação federada com autenticação integrada do Windows (IWA) ou autenticação gerenciada simples de Sign-On único (SSO) com sincronização de hash de senha ou autenticação de passagem é a melhor experiência de usuário quando dentro da rede corporativa com a linha de visão para controladores de domínio locais. Ele minimiza a solicitação de credencial fadiga e reduz o risco de os usuários se depararem com ataques de phishing. Se você já estiver usando a autenticação gerenciada por nuvem com PHS ou PTA, mas os usuários ainda precisarem digitar sua senha ao autenticar localmente, você deverá implantar imediatamente o [SSO contínuo](../hybrid/how-to-connect-sso.md). Por outro lado, se você estiver atualmente federado com planos para eventualmente migrar para a autenticação gerenciada pela nuvem, deverá implementar o SSO contínuo como parte do projeto de migração.

### <a name="device-trust-access-policies"></a>Políticas de acesso de confiança do dispositivo

Como um usuário na sua organização, um dispositivo é uma identidade importante que você quer proteger. É possível usar uma identidade do dispositivo para proteger seus recursos a qualquer momento e de qualquer local. A autenticação do dispositivo e a contabilização de seu tipo de confiança aumenta sua postura e usabilidade de segurança:

- Evitando o conflito, por exemplo, com a MFA, quando o dispositivo é confiável
- Bloqueando o acesso de dispositivos não confiáveis
- Para dispositivos Windows 10, forneça [logon único para recursos locais diretamente](../devices/azuread-join-sso.md).

Você pode realizar essa meta colocando as identidades do dispositivo e gerenciando-as no Azure AD usando um dos seguintes métodos:

- As organizações podem usar [Microsoft Intune](/intune/what-is-intune) para gerenciar o dispositivo e impor políticas de conformidade, a integridade do dispositivo atestar e definir políticas de acesso condicional com base no fato de o dispositivo estar em conformidade. Microsoft Intune pode gerenciar dispositivos iOS, desktops Mac (via integração do JAMF), áreas de trabalho do Windows (usando nativamente o gerenciamento de dispositivos móveis para Windows 10 e o cogerenciamento com o Microsoft Endpoint Configuration Manager) e dispositivos móveis Android.
- O [ingresso no Azure ad híbrido](../devices/hybrid-azuread-join-managed-domains.md) fornece gerenciamento com políticas de grupo ou Configuration Manager de ponto de extremidade da Microsoft em um ambiente com Active Directory dispositivos de computadores ingressados no domínio. As organizações podem implantar um ambiente gerenciado por meio de PHS ou PTA com SSO contínuo. Trazer seus dispositivos para o Azure AD maximiza a produtividade do usuário por meio de SSO em seus recursos de nuvem e locais, permitindo que você proteja o acesso aos recursos de nuvem e locais com [acesso condicional](../conditional-access/overview.md) ao mesmo tempo.

Se você tiver dispositivos Windows ingressados no domínio que não estão registrados na nuvem ou dispositivos Windows ingressados no domínio que são registrados na nuvem, mas sem políticas de acesso condicional, você deve registrar os dispositivos não registrados e, em ambos os casos, [usar o ingresso do Azure ad híbrido como um controle](../conditional-access/require-managed-devices.md) em suas políticas de acesso condicional.

![Uma captura de tela de concessão na política de acesso condicional que requer dispositivo híbrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se você estiver gerenciando dispositivos com MDM ou Microsoft Intune, mas não usando controles de dispositivo em suas políticas de acesso condicional, é recomendável usar o [dispositivo exigir que ele seja marcado como em conformidade](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) como um controle nessas políticas.

![Uma captura de tela de concessão na política de acesso condicional que requer conformidade do dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Leitura recomendada de políticas de acesso de confiança de dispositivo

- [Como planejar sua implementação de junção do Azure Active Directory híbrido](../devices/hybrid-azuread-join-plan.md)
- [Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello para Empresas

No Windows 10, o [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification) substitui senhas com autenticação forte de dois fatores em computadores. O Windows Hello para empresas permite uma experiência de MFA mais simplificada para os usuários e reduz sua dependência de senhas. Se você não começou a distribuir dispositivos com Windows 10 ou os implantou parcialmente, recomendamos que você atualize para o Windows 10 e [habilite o Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) em todos os dispositivos.

Se você quiser saber mais sobre autenticação sem senha, consulte [um mundo sem senhas com Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Autenticação e atribuição de aplicativos

### <a name="single-sign-on-for-apps"></a>Logon único para aplicativos

Fornecer um mecanismo de logon único padronizado para toda a empresa é crucial para melhor experiência do usuário, redução de risco, capacidade de relatório e governança. Se você estiver usando aplicativos que dão suporte ao SSO com o Azure AD, mas estiverem atualmente configurados para usar contas locais, você deverá reconfigurar esses aplicativos para usar o SSO com o Azure AD. Da mesma forma, se você estiver usando qualquer aplicativo que ofereça suporte a SSO com o Azure AD, mas estiver usando outro provedor de identidade, deverá reconfigurar esses aplicativos para usar o SSO com o Azure AD também. Para aplicativos que não dão suporte a protocolos de Federação, mas oferecem suporte à autenticação baseada em formulários, recomendamos que você configure o aplicativo para usar a [compartimentação de senha](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) com o Azure proxy de aplicativo do AD.

![Logon baseado em senha do AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se você não tiver um mecanismo para descobrir aplicativos não gerenciados em sua organização, é recomendável implementar um processo de descoberta usando uma solução de agente de segurança de acesso à nuvem (CASB), como [Microsoft Cloud app Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Por fim, se você tiver uma galeria de aplicativo do Azure AD e usar aplicativos que dão suporte a SSO com o Azure AD, é recomendável [listar o aplicativo na Galeria de aplicativos](../develop/v2-howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Leitura recomendada de logon único

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migração de aplicativos de AD FS para o Azure AD

[A migração de aplicativos do AD FS para o Azure ad](../manage-apps/migrate-adfs-apps-to-azure.md) permite recursos adicionais de segurança, capacidade de gerenciamento mais consistente e uma melhor experiência de colaboração. Se você tiver aplicativos configurados em AD FS que dão suporte a SSO com o Azure AD, você deverá reconfigurar esses aplicativos para usar o SSO com o Azure AD. Se você tiver aplicativos configurados em AD FS com configurações incomuns sem suporte pelo Azure AD, entre em contato com os proprietários do aplicativo para entender se a configuração especial é um requisito absoluto do aplicativo. Se não for necessário, você deverá reconfigurar o aplicativo para usar o SSO com o Azure AD.

![Azure AD como o provedor de identidade principal](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure ad Connect Health para ADFS](../hybrid/how-to-connect-health-adfs.md) pode ser usado para coletar detalhes de configuração sobre cada aplicativo que pode ser migrado para o Azure AD.

### <a name="assign-users-to-applications"></a>Atribuir usuários a aplicativos

[A atribuição de usuários aos aplicativos](../manage-apps/assign-user-or-group-access-portal.md) é melhor mapeada usando grupos porque eles permitem maior flexibilidade e capacidade de gerenciar em escala. Os benefícios de usar grupos incluem [Associação de grupo dinâmico baseada em atributo](../enterprise-users/groups-dynamic-membership.md) e [delegação a proprietários de aplicativo](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Portanto, se você já estiver usando e gerenciando grupos, recomendamos que execute as seguintes ações para melhorar o gerenciamento em escala:

- Delegue gerenciamento e governança de grupo para proprietários de aplicativos.
- Permitir acesso de autoatendimento ao aplicativo.
- Defina grupos dinâmicos se os atributos de usuário puderem determinar o acesso aos aplicativos de forma consistente.
- Implemente atestado para grupos usados para acesso de aplicativo usando [revisões de acesso do Azure ad](../governance/access-reviews-overview.md).

Por outro lado, se você encontrar aplicativos que têm atribuição a usuários individuais, certifique-se de implementar a [governança](../governance/index.yml) em relação a esses aplicativos.

#### <a name="assign-users-to-applications-recommended-reading"></a>Atribuir usuários a aplicativos de leitura recomendada

- [Atribuir usuários e grupos a um aplicativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegar permissões de registro de aplicativo no Azure Active Directory](../roles/delegate-app-roles.md)
- [Associação dinâmica do Azure Active Directory para grupos](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Políticas de acesso

### <a name="named-locations"></a>Localizações nomeadas

Com os [locais nomeados](../reports-monitoring/quickstart-configure-named-locations.md) no Azure AD, você pode rotular intervalos de endereços IP confiáveis em sua organização. O Azure AD usa localizações nomeadas para:

- Evite falsos positivos em eventos de risco. Entrar de um local de rede confiável reduz o risco de entrada de um usuário.
- Configure o [acesso condicional com base em localização](../reports-monitoring/quickstart-configure-named-locations.md).

![Local nomeado](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Com base na prioridade, use a tabela abaixo para encontrar a solução recomendada que melhor atenda às necessidades da sua organização:

| **Prioridade** | **Cenário** | **Recomendação** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se você usar PHS ou PTA e locais nomeados não tiverem sido definidos | Definir locais nomeados para melhorar a detecção de eventos de risco |
| 2 | Se você for federado e não usar a declaração "insideCorporateNetwork" e os locais nomeados não tiverem sido definidos | Definir locais nomeados para melhorar a detecção de eventos de risco |
| 3 | Se você não usar locais nomeados em políticas de acesso condicional e não houver nenhum controle de risco ou de dispositivo nas políticas de acesso condicional | Configurar a política de acesso condicional para incluir locais nomeados |
| 4 | Se você for federado e usar a declaração "insideCorporateNetwork" e os locais nomeados não tiverem sido definidos | Definir locais nomeados para melhorar a detecção de eventos de risco |
| 5 | Se você estiver usando endereços IP confiáveis com MFA em vez de locais nomeados e marcá-los como confiáveis | Definir locais nomeados e marcá-los como confiáveis para melhorar a detecção de eventos de risco |

### <a name="risk-based-access-policies"></a>Políticas de acesso com base em risco

O Azure AD pode calcular o risco para cada entrada e todos os usuários. O uso de riscos como um critério nas políticas de acesso pode proporcionar uma melhor experiência do usuário, por exemplo, menos prompts de autenticação e maior segurança, por exemplo, apenas solicitações de usuários quando forem necessárias e automatizar a resposta e a correção.

![Política de risco de entrada](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se você já tem Azure AD Premium licenças P2 que dão suporte ao uso de risco em políticas de acesso, mas elas não estão sendo usadas, é altamente recomendável adicionar risco à sua postura de segurança.

#### <a name="risk-based-access-policies-recommended-reading"></a>Leitura recomendada de políticas de acesso baseado em risco

- [Como: configurar a política de risco de entrada](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Como configurar a política de risco do usuário](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Políticas de acesso do aplicativo cliente

O gerenciamento de aplicativo Microsoft Intune (MAM) fornece a capacidade de enviar por push controles de proteção de dados, como criptografia de armazenamento, PIN, limpeza de armazenamento remoto, etc., para aplicativos móveis de cliente compatíveis, como o Outlook Mobile. Além disso, as políticas de acesso condicional podem ser criadas para [restringir o acesso](../conditional-access/app-based-conditional-access.md) a serviços de nuvem, como o Exchange Online, de aplicativos aprovados ou compatíveis.

Se seus funcionários instalarem aplicativos compatíveis com MAM, como aplicativos móveis do Office, para acessar recursos corporativos, como o Exchange Online ou o SharePoint Online, e você também oferecer suporte a BYOD (Traga seu próprio dispositivo), recomendamos implantar políticas de MAM de aplicativo para gerenciar a configuração de aplicativos em dispositivos de propriedade pessoal sem registro de MDM e, em seguida, atualizar suas políticas de acesso condicional para

![Controle de concessão de acesso condicional](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Os funcionários devem instalar aplicativos compatíveis com MAM com recursos corporativos e o acesso é restrito em dispositivos gerenciados pelo Intune. em seguida, você deve considerar a implantação de políticas de MAM do aplicativo para gerenciar a configuração do aplicativo para dispositivos pessoais e atualizar as políticas de acesso condicional para permitir o acesso somente de clientes compatíveis com MAM.

### <a name="conditional-access-implementation"></a>Implementação de acesso condicional

O acesso condicional é uma ferramenta essencial para melhorar a postura de segurança de sua organização. Portanto, é importante seguir estas práticas recomendadas:

- Verifique se todos os aplicativos SaaS têm pelo menos uma política aplicada
- Evite combinar o filtro **todos os aplicativos** com o controle **Bloquear** para evitar o risco de bloqueio
- Evite usar **todos os usuários** como um filtro e adicionar **convidados** inadvertidamente
- **Migrar todas as políticas "herdadas" para o portal do Azure**
- Capturar todos os critérios para usuários, dispositivos e aplicativos
- Usar políticas de acesso condicional para [implementar a MFA](../conditional-access/plan-conditional-access.md), em vez de usar uma **MFA por usuário**
- Ter um pequeno conjunto de políticas de núcleo que podem ser aplicadas a vários aplicativos
- Definir grupos de exceções vazios e adicioná-los às políticas para ter uma estratégia de exceção
- Planejar contas de [vidro](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) sem controles de MFA
- Garanta uma experiência consistente em Microsoft 365 aplicativos cliente, por exemplo, equipes, OneDrive, Outlook, etc.) Implementando o mesmo conjunto de controles para serviços como o Exchange Online e o SharePoint Online
- A atribuição às políticas deve ser implementada por meio de grupos, não por indivíduos
- Faça revisões regulares dos grupos de exceções usados em políticas para limitar o tempo que os usuários estão fora da postura de segurança. Se você tiver o Azure AD P2, poderá usar as revisões de acesso para automatizar o processo

#### <a name="conditional-access-recommended-reading"></a>Leitura recomendada de acesso condicional

- [Práticas recomendadas para acesso condicional no Azure Active Directory](../conditional-access/overview.md)
- [Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referência de configurações de acesso condicional Azure Active Directory](../conditional-access/concept-conditional-access-conditions.md)
- [Políticas de acesso condicional comuns](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Área de superfície de acesso

### <a name="legacy-authentication"></a>Autenticação herdada

Credenciais fortes, como a MFA, não podem proteger aplicativos usando protocolos de autenticação herdados, que o tornam o vetor de ataque preferencial por atores mal-intencionados. O bloqueio da autenticação herdada é crucial para melhorar a postura de segurança de acesso.

A autenticação herdada é um termo que se refere aos protocolos de autenticação usados por aplicativos como:

- Clientes mais antigos do Office que não usam autenticação moderna (por exemplo, cliente do Office 2010)
- Clientes que usam protocolos de email, como IMAP/SMTP/POP

Os invasores preferem fortemente esses protocolos – na verdade, quase [100% dos ataques de irrigação de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) usam protocolos de autenticação herdados! Os hackers usam protocolos de autenticação herdados, pois não dão suporte a entrada interativa, o que é necessário para desafios de segurança adicionais, como autenticação multifator e autenticação de dispositivo.

Se a autenticação herdada for amplamente usada em seu ambiente, você deverá planejar a migração de clientes herdados para clientes que dão suporte à [autenticação moderna](/office365/enterprise/modern-auth-for-office-2013-and-2016) assim que possível. No mesmo token, se você tiver alguns usuários que já usam autenticação moderna, mas outros que ainda usam autenticação herdada, execute as seguintes etapas para bloquear clientes de autenticação herdados:

1. Use [relatórios de atividade de entrada](../reports-monitoring/concept-sign-ins.md) para identificar os usuários que ainda estão usando a autenticação herdada e a correção de plano:

   a. Atualize para clientes compatíveis com a autenticação moderna para usuários afetados.
   
   b. Planeje um período de transição para bloquear por etapas abaixo.
   
   c. Identifique quais aplicativos herdados têm uma dependência rígida na autenticação herdada. Consulte a etapa 3 abaixo.

2. Desabilite os protocolos herdados na origem (por exemplo, caixa de correio do Exchange) para usuários que não estão usando autenticação herdada para evitar mais exposição.
3. Para as contas restantes (as identidades ideais não humanas, como contas de serviço), use o [acesso condicional para restringir a pós-autenticação de protocolos herdados](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) .

#### <a name="legacy-authentication-recommended-reading"></a>Leitura recomendada de autenticação herdada

- [Habilitar ou desabilitar o acesso POP3 ou IMAP4 às caixas de correio no Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Concessões de consentimento

Em um ataque de concessão de consentimento ilícito, o invasor cria um aplicativo registrado no Azure AD que solicita acesso a dados, como informações de contato, email ou documentos. Os usuários podem estar concedendo consentimento a aplicativos mal-intencionados por meio de ataques de phishing durante a aterrissagem em sites mal-intencionados.

Veja abaixo uma lista de aplicativos com permissões que você pode querer analisar para os serviços de nuvem da Microsoft:

- Aplicativos com aplicativo ou delegado \* . Permissões ReadWrite
- Aplicativos com permissões delegadas podem ler, enviar ou gerenciar email em nome do usuário
- Aplicativos que recebem o usando as seguintes permissões:

| Recurso | Permissão |
| :- | :- |
| Exchange Online | Estendi. AccessAsUser. All |
| | EWS. AccessAsUser. All |
| | Mail.Read |
| API do Microsoft Graph | Mail.Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Os aplicativos receberam a representação completa do usuário do usuário conectado. Por exemplo:

|Recurso | Permissão |
| :- | :- |
| API do Microsoft Graph| Directory.AccessAsUser.All |
| API REST do Azure | user_impersonation |

Para evitar esse cenário, você deve se referir a [detectar e corrigir concessões de consentimento ilícitos no Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) para identificar e consertar quaisquer aplicativos com concessões ilícitos ou aplicativos que tenham mais concessões do que o necessário. Em seguida, [remova o autoatendimento completamente](../manage-apps/configure-user-consent.md) e [estabeleça procedimentos de governança](../manage-apps/configure-admin-consent-workflow.md). Por fim, agende revisões regulares de permissões de aplicativo e remova-as quando elas não forem necessárias.

#### <a name="consent-grants-recommended-reading"></a>O consentimento concede a leitura recomendada

- [Microsoft Graph permissões de API](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Configurações de usuário e grupo

Abaixo estão as configurações de usuário e grupo que podem ser bloqueadas se não houver uma necessidade comercial explícita:

#### <a name="user-settings"></a>Configurações do usuário

- **Usuários externos** -colaboração externa pode ocorrer de forma orgânica na empresa com serviços como equipes, Power bi, SharePoint Online e proteção de informações do Azure. Se você tiver restrições explícitas para controlar a colaboração externa iniciada pelo usuário, é recomendável habilitar usuários externos usando o [Gerenciamento de direitos do Azure ad](../governance/entitlement-management-overview.md) ou uma operação controlada, como por meio de seu suporte técnico. Se você não quiser permitir a colaboração externa orgânica para serviços, poderá [Bloquear completamente os membros de convidar usuários externos](../external-identities/delegate-invitations.md). Como alternativa, você também pode [permitir ou bloquear domínios específicos](../external-identities/allow-deny-list.md) em convites de usuários externos.
- **Registros de aplicativo** – quando registros de aplicativo estão habilitados, os usuários finais podem integrar aplicativos e conceder acesso aos seus dados. Um exemplo típico de registro de aplicativo são os usuários que habilitam plug-ins do Outlook, ou assistentes de voz, como Alexa e Siri para ler seu email e calendário ou enviar emails em seu nome. Se o cliente decidir desativar o registro do aplicativo, as equipes batalha e IAM deverão estar envolvidas no gerenciamento de exceções (registros de aplicativo que são necessários com base nos requisitos de negócios), pois eles precisariam registrar os aplicativos com uma conta de administrador e, provavelmente, exigirem a criação de um processo para colocar o processo em operação.
- **Portal de administração** – as organizações podem bloquear a folha do Azure ad na portal do Azure de forma que os não administradores não possam acessar o gerenciamento do AD do azure no portal do Azure e ficar confuso. Vá para as configurações de usuário no portal de gerenciamento do Azure AD para restringir o acesso:

![Acesso restrito ao portal de administração](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Os não administradores ainda podem acessar as interfaces de gerenciamento do Azure AD por meio de linha de comando e outras interfaces programáticas.

#### <a name="group-settings"></a>Configurações de grupo

**Gerenciamento de grupo de autoatendimento/os usuários podem criar grupos de segurança/grupos de Microsoft 365.** Se não houver uma iniciativa de autoatendimento atual para grupos na nuvem, os clientes poderão decidir desativá-lo até que estejam prontos para usar esse recurso.

#### <a name="groups-recommended-reading"></a>Leitura recomendada de grupos

- [O que é a colaboração B2B do Azure Active Directory?](../external-identities/what-is-b2b.md)
- [Integrar aplicativos com Azure Active Directory](../develop/quickstart-register-app.md)
- [Aplicativos, permissões e consentimento no Azure Active Directory.](../develop/quickstart-register-app.md)
- [Use grupos para gerenciar o acesso a recursos no Azure Active Directory](./active-directory-manage-groups.md)
- [Configurando o gerenciamento de acesso de aplicativo de autoatendimento no Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Tráfego de locais inesperados

Os invasores se originam de várias partes do mundo. Gerencie esse risco usando políticas de acesso condicional com o local como a condição. A [condição de local](../conditional-access/location-condition.md) de uma política de acesso condicional permite bloquear o acesso para locais de onde não há nenhum motivo comercial para se conectar.

![Criar um novo local nomeado](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponível, use uma solução SIEM (gerenciamento de informações e eventos de segurança) para analisar e localizar padrões de acesso entre regiões. Se você não usar um produto SIEM ou não estiver ingerindo informações de autenticação do Azure AD, recomendamos que você use [Azure monitor](../../azure-monitor/overview.md) para identificar padrões de acesso entre regiões.

## <a name="access-usage"></a>Uso de acesso

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Logs do Azure AD arquivados e integrados com planos de resposta a incidentes

Ter acesso a atividades de entrada, auditorias e eventos de risco do Azure AD é crucial para solução de problemas, análise de uso e investigações forenses. O Azure AD fornece acesso a essas fontes por meio de APIs REST que têm um período de retenção limitado. Um sistema SIEM (gerenciamento de informações e eventos de segurança) ou uma tecnologia de arquivamento equivalente é a chave para o armazenamento de longo prazo de auditorias e suporte. Para habilitar o armazenamento de longo prazo de logs do Azure AD, você deve adicioná-los à sua solução SIEM existente ou usar [Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Arquive logs que podem ser usados como parte de seus planos de resposta a incidentes e investigações.

#### <a name="logs-recommended-reading"></a>Logs de leitura recomendados

- [Referência à API de auditoria do Azure Active Directory](/graph/api/resources/directoryaudit?view=graph-rest-beta)
- [Referência da API de relatório de atividade de entrada do Azure Active Directory](/graph/api/resources/signin?view=graph-rest-beta)
- [Obtenha dados usando a API de relatórios do Microsoft Azure Active Directory com certificados](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph para Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Referência de API de atividade de gerenciamento do Office 365](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Como usar o Pacote de Conteúdo do Power BI do Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Resumo

Há 12 aspectos para uma infraestrutura de identidade segura. Esta lista ajudará você a proteger e gerenciar credenciais, definir a experiência de autenticação, delegar a atribuição, usar medidas e definir políticas de acesso com base na postura de segurança corporativa.

- Atribuir proprietários a tarefas principais.
- Implemente soluções para detectar senhas fracas ou vazadas, melhorar o gerenciamento e a proteção de senhas e proteger ainda mais o acesso dos usuários aos recursos.
- Gerencie a identidade dos dispositivos para proteger seus recursos a qualquer momento e de qualquer local.
- Implemente a autenticação com senha.
- Forneça um mecanismo de logon único padronizado em toda a organização.
- Migre aplicativos do AD FS para o Azure AD para permitir maior segurança e capacidade de gerenciamento mais consistente.
- Atribua usuários a aplicativos usando grupos para permitir maior flexibilidade e capacidade de gerenciar em escala.
- Configure políticas de acesso com base em risco.
- Bloquear protocolos de autenticação herdados.
- Detectar e corrigir concessões de consentimento ilícitos.
- Bloquear configurações de usuário e de grupo.
- Habilite o armazenamento de longo prazo de logs do Azure AD para solução de problemas, análise de uso e investigações forenses.

## <a name="next-steps"></a>Próximas etapas

Introdução às [verificações e ações operacionais de governança de identidade](active-directory-ops-guide-govern.md).

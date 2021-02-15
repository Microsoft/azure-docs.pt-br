---
title: Migrar a autenticação de aplicativo para Azure Active Directory
description: Este White Paper detalha o planejamento e os benefícios da migração da autenticação de seu aplicativo para o Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd33f9e0b249db6b7c6bd0a0a556d0bb4cf79312
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101116"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrar a autenticação de aplicativo para Azure Active Directory

## <a name="about-this-paper"></a>Sobre este documento

Este White Paper detalha o planejamento e os benefícios da migração da autenticação de seu aplicativo para o Azure AD. Ele foi projetado para administradores do Azure e profissionais de identidade.

Dividindo o processo em quatro fases, cada uma com critérios detalhados de planejamento e saída, ela foi projetada para ajudá-lo a planejar sua estratégia de migração e entender como a autenticação do Azure AD dá suporte a suas metas organizacionais.

## <a name="introduction"></a>Introdução

Hoje em dia, sua organização requer uma grande quantidade de aplicativos (aplicativos) para que os usuários realizem suas tarefas. Provavelmente, você continuará a adicionar, desenvolver ou desativar aplicativos todos os dias. Os usuários acessam esses aplicativos de uma vasta gama de dispositivos corporativos e pessoais e locais. Eles abrem aplicativos de várias maneiras, incluindo:

- por meio de uma home page ou portal da empresa

- por meio de marcadores em seus navegadores

- por meio da URL de um fornecedor para aplicativos SaaS (software como serviço)

- links enviados diretamente para desktops ou dispositivos móveis do usuário por meio de uma solução de MDM/MAM (gerenciamento de aplicativo/dispositivo móvel)

Seus aplicativos provavelmente estão usando os seguintes tipos de autenticação:

- Soluções de federação local (como Serviços de Federação do Active Directory (AD FS) (ADFS) e ping)

- Active Directory (como autenticação Kerberos e autenticação integrada do Windows)

- Outras soluções de IAM (gerenciamento de acesso e identidade) baseadas em nuvem (como Okta ou Oracle)

- Infraestrutura da Web local (como IIS e Apache)

- Infraestrutura hospedada na nuvem (como Azure e AWS)

**Para garantir que os usuários possam acessar aplicativos com facilidade e segurança, seu objetivo é ter um único conjunto de controles de acesso e políticas em seus ambientes locais e na nuvem.**

O [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis) oferece uma plataforma de identidade universal que fornece a suas pessoas, parceiros e clientes uma única identidade para acessar os aplicativos que desejam e colaboram de qualquer plataforma e dispositivo.

![Um diagrama de conectividade de Azure Active Directory](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

O Azure AD tem um [pacote completo de recursos de gerenciamento de identidade](/azure/active-directory/fundamentals/active-directory-whatis#which-features-work-in-azure-ad). Padronizar a autenticação e a autorização de seu aplicativo para o Azure AD permite que você obtenha os benefícios que esses recursos fornecem.

Consulte recursos de migração adicionais em [https://aka.ms/migrateapps](https://aka.ms/migrateapps)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Benefícios da migração de autenticação de aplicativo para o Azure AD

Mover a autenticação de aplicativo para o Azure AD ajudará você a gerenciar riscos e custos, aumentar a produtividade e atender aos requisitos de conformidade e governança.

### <a name="manage-risk"></a>gerenciar riscos

Proteger seus aplicativos exige que você tenha uma visão completa de todos os fatores de risco. Migrar seus aplicativos para o Azure AD consolida suas soluções de segurança. Com ele, você pode:

- Melhore o acesso de usuário seguro a aplicativos e dados corporativos associados usando [políticas de acesso condicional](/azure/active-directory/active-directory-conditional-access-azure-portal), [autenticação multifator](/azure/active-directory/authentication/concept-mfa-howitworks)e tecnologias de [proteção de identidade](/azure/active-directory/active-directory-identityprotection) baseadas em risco em tempo real.

- Proteja o acesso do usuário privilegiado ao seu ambiente com acesso de administrador [just-in-time](/azure/managed-applications/request-just-in-time-access) .

- Use o [design multilocatário, distribuído geograficamente e de alta disponibilidade do Azure ad](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)para suas necessidades comerciais mais críticas.

- Proteja seus aplicativos herdados com uma das nossas [integrações de parceiros de acesso híbrido seguro](https://aka.ms/secure-hybrid-access) que talvez você já tenha implantado.

### <a name="manage-cost"></a>Gerenciar custos

Sua organização pode ter várias soluções de IAM (gerenciamento de acesso de identidade) em vigor. A migração para uma infraestrutura do Azure AD é uma oportunidade de reduzir as dependências em licenças IAM (no local ou na nuvem) e nos custos de infraestrutura. Nos casos em que você já deve ter pago pelo Azure AD por meio de licenças do M365, não há motivo para pagar o custo adicional de outra solução IAM.

**Com o Azure AD, você pode reduzir os custos de infraestrutura:**

- Fornecendo acesso remoto seguro a aplicativos locais usando o [Azure proxy de aplicativo do AD](/azure/active-directory/manage-apps/application-proxy).

- Desacoplando aplicativos da abordagem de credencial local em seu locatário [Configurando o Azure ad como o provedor de identidade universal confiável](/azure/active-directory/hybrid/plan-connect-user-signin#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Aumentar a produtividade

A economia e os benefícios de segurança orientam as organizações a adotar o Azure AD, mas a adoção e a conformidade totais são mais prováveis se os usuários se beneficiarem também. Com o Azure AD, você pode:

- Melhore a experiência de [SSO (Sign-On único)](/azure/active-directory/manage-apps/what-is-single-sign-on) do usuário final por meio de acesso contínuo e seguro a qualquer aplicativo, de qualquer dispositivo e qualquer local.

- Aproveite os recursos IAM de autoatendimento, como [redefinições de senha de autoatendimento](/azure/active-directory/authentication/concept-sspr-howitworks) e [Gerenciamento de grupo autoatendimento](/azure/active-directory/users-groups-roles/groups-self-service-management).

- Reduza a sobrecarga administrativa Gerenciando apenas uma única identidade para cada usuário em ambientes de nuvem e locais:

  - [Automatizar o provisionamento](/azure/active-directory/active-directory-saas-app-provisioning) de contas de usuário (na [Galeria do Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) com base nas IDENTIDADEs do Azure AD
  - Acessar todos os seus aplicativos do painel myapps no [portal do Azure ](https://portal.azure.com/)

- Permita que os desenvolvedores protejam o acesso aos seus aplicativos e aprimorem a experiência do usuário final usando a [plataforma de identidade da Microsoft](/azure/active-directory/develop/about-microsoft-identity-platform) com a MSAL (biblioteca de autenticação da Microsoft).

- Capacite seus parceiros com acesso a recursos de nuvem usando a [colaboração B2B do Azure ad](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Isso remove a sobrecarga de configurar a Federação ponto a ponto com seus parceiros.

### <a name="address-compliance-and-governance"></a>Atender à conformidade e governança

Garanta a conformidade com os requisitos regulatórios impondo políticas de acesso corporativo e monitorando o acesso do usuário a aplicativos e dados associados usando ferramentas e APIs de auditoria integradas. Com o Azure AD, você pode monitorar entradas de aplicativo por meio de relatórios que aproveitam as [ferramentas de Siem (monitoramento de eventos e incidentes de segurança)](/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting). Você pode acessar os relatórios do portal ou das APIs e auditar programaticamente quem tem acesso aos seus aplicativos e remover o acesso a usuários inativos por meio de revisões de acesso.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planejar suas fases de migração e a estratégia do projeto

Quando os projetos de tecnologia falham, geralmente se deve a expectativas incompatíveis, os participantes certos não estão envolvidos nem a falta de comunicação. Verifique seu sucesso planejando o projeto em si.

### <a name="the-phases-of-migration"></a>As fases de migração

Antes de entrarmos nas ferramentas, você deve entender como considerar o processo de migração. Por meio de vários workshops diretos para clientes, recomendamos as quatro fases a seguir:

![Um diagrama das fases de migração](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Montar a equipe do projeto

A migração de aplicativos é um esforço de equipe, e você precisa garantir que todas as posições vitais sejam preenchidas. O suporte de líderes de negócios sênior é importante. Certifique-se de que você envolva o conjunto certo de patrocinadores executivos, tomadores de decisões de negócios e especialistas no assunto (SMEs).

Durante o projeto de migração, uma pessoa pode atender a várias funções ou várias pessoas atendem a cada função, dependendo do tamanho e da estrutura da sua organização. Você também pode ter uma dependência em outras equipes que desempenham um papel fundamental em seu panorama de segurança.

A tabela a seguir inclui as principais funções e suas contribuições:

| Função          | Contribuições                                              |
| ------------- | ---------------------------------------------------------- |
| **Gerente de projeto** | Projeto de contas a considerar para a orientação do projeto, incluindo:<br /> -obter suporte executivo<br /> -Traga os participantes<br /> -gerenciar agendas, documentação e comunicações |
| **Arquiteto de identidade/administrador de Aplicativo Azure AD** | Eles são responsáveis pelo seguinte:<br /> -Projete a solução em cooperação com os participantes<br /> -documentar o design da solução e os procedimentos operacionais para a entrega à equipe de operações<br /> -gerenciar os ambientes de pré-produção e produção |
| **Equipe de operações do AD local** | A organização que gerencia as diferentes fontes de identidade locais, como florestas do AD, diretórios LDAP, sistemas de RH, etc.<br /> -executar todas as tarefas de correção necessárias antes de sincronizar<br /> -Fornecer as contas de serviço necessárias para a sincronização<br /> -fornecer acesso para configurar a Federação para o Azure AD |
| **Gerente de suporte de ti** | Um representante da organização de suporte de TI que pode fornecer informações sobre a capacidade de suporte dessa mudança a partir da perspectiva da assistência técnica. |
| **Proprietário da segurança**  | Um representante da equipe de segurança que pode garantir que o plano atenda aos requisitos de segurança de sua organização. |
| **Proprietários técnicos de aplicativos** | Inclui os proprietários técnicos dos aplicativos e serviços que serão integrados ao Azure AD. Eles fornecem os atributos de identidade dos aplicativos que devem incluir no processo de sincronização. Normalmente, eles têm uma relação com representantes de CSV. |
| **Proprietários de negócios do aplicativo** | Colegas representativos que podem fornecer informações sobre a experiência do usuário e a utilidade dessa mudança da perspectiva de um usuário e possui o aspecto comercial geral do aplicativo, que pode incluir o gerenciamento de acesso. |
| **Grupo de usuários piloto** | Os usuários que testarão como parte de seu trabalho diário, a experiência piloto e fornecerão comentários para orientar o restante das implantações. |

### <a name="plan-communications"></a>Planejar a comunicação

O envolvimento e a comunicação de negócios efetivos são a chave para o sucesso. É importante dar aos participantes e usuários finais uma avenida para obter informações e manter-se informado sobre atualizações de agenda. Instrua todos sobre o valor da migração, quais são os cronogramas esperados e como planejar qualquer interrupção de negócios temporária. Use vários caminhos como sessões de resumo, emails, reuniões um-para-um, faixas e townhalls.

Com base na estratégia de comunicação que você escolheu para o aplicativo, talvez você queira lembrar os usuários do tempo de inatividade pendente. Você também deve verificar se não há alterações recentes ou impactos comerciais que exijam adiar a implantação.

Na tabela a seguir, você encontrará a comunicação mínima sugerida para manter seus participantes informados:

**Fases do plano e estratégia do projeto**:

| Comunicação      | Público                                          |
| ------------------ | ------------------------------------------------- |
| Reconhecimento e valor técnico/comercial do projeto | Todos, exceto usuários finais |
| Solicitação de aplicativos piloto | -Proprietários de negócios do aplicativo<br />-Proprietários técnicos do aplicativo<br />-Arquitetos e equipe de identidade |

**Fase 1-descoberta e escopo**:

| Comunicação      | Público                                          |
| ------------------ | ------------------------------------------------- |
| -Solicitação de informações do aplicativo<br />-Resultado do escopo do exercício | -Proprietários técnicos do aplicativo<br />-Proprietários de negócios do aplicativo |

**Fase 2-classificar aplicativos e o piloto do plano**:

| Comunicação      | Público                                          |
| ------------------ | ------------------------------------------------- |
| -Resultado de classificações e o que isso significa para a agenda de migração<br />-Agendamento de migração preliminar | -Proprietários técnicos do aplicativo<br /> -Proprietários de negócios do aplicativo |

**Fase 3 – planejar a migração e os testes**:

| Comunicação      | Público                                          |
| ------------------ | ------------------------------------------------- |
| -Resultado do teste de migração de aplicativos | -Proprietários técnicos do aplicativo<br />-Proprietários de negócios do aplicativo |
| -Notificação de que a migração é proveniente e a explicação das experiências resultantes do usuário final.<br />-Tempo de inatividade e comunicação completa, incluindo o que eles devem fazer agora, comentários e como obter ajuda | -Usuários finais (e todos os outros) |

**Fase 4 – gerenciar e obter informações**:

| Comunicação      | Público                                          |
| ------------------ | ------------------------------------------------- |
| Análise disponível e como acessar | -Proprietários técnicos do aplicativo<br />-Proprietários de negócios do aplicativo |

### <a name="migration-states-communication-dashboard"></a>Painel de comunicação de Estados de migração

A comunicação do estado geral do projeto de migração é crucial, pois mostra o progresso e ajuda os proprietários de aplicativos cujos aplicativos estão surgindo para a migração para se preparar para a mudança. Você pode reunir um painel simples usando Power BI ou outras ferramentas de relatório para fornecer visibilidade do status dos aplicativos durante a migração.

Os Estados de migração que você pode considerar usar são os seguintes:

| Estados de migração       | Plano de ação                                   |
| ---------------------- | --------------------------------------------- |
| **Solicitação inicial** | Localize o aplicativo e contate o proprietário para obter mais informações |
| **Avaliação concluída** | O proprietário do aplicativo avalia os requisitos do aplicativo e retorna o questionário do aplicativo</td>
| **Configuração em andamento** | Desenvolver as alterações necessárias para gerenciar a autenticação no Azure AD |
| **Configuração de teste bem-sucedida** | Avaliar as alterações e autenticar o aplicativo em relação ao locatário do Azure AD de teste no ambiente de teste |
| **Configuração de produção bem-sucedida** | Alterar as configurações para trabalhar com o locatário do AD de produção e avaliar a autenticação do aplicativo no ambiente de teste |
| **Concluir/aprovar** | Implantar as alterações do aplicativo no ambiente de produção e executar o no locatário do Azure AD de produção |

Isso garantirá que os proprietários do aplicativo saibam o que é a migração de aplicativo e o agendamento de teste quando seus aplicativos estão para migração e quais são os resultados de outros aplicativos que já foram migrados. Você também pode considerar o fornecimento de links para o seu banco de dados do rastreador de bugs para que os proprietários possam arquivar e exibir problemas de aplicativos que estão sendo migrados.

### <a name="best-practices"></a>Práticas recomendadas

A seguir estão as histórias de sucesso de nossos clientes e parceiros e as melhores práticas sugeridas:

- [Cinco dicas para melhorar o processo de migração para Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) pela consultoria Patriot, um membro da nossa rede de parceiros que se concentra em ajudar os clientes a implantar soluções de nuvem da Microsoft com segurança.

- [Desenvolva uma estratégia de gerenciamento de riscos para a migração de aplicativos do Azure ad](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) por Edgile, um parceiro que se concentra em soluções de gerenciamento de risco e iam.

## <a name="phase-1-discover-and-scope-apps"></a>Fase 1: descobrir e delimitar aplicativos

**A descoberta e a análise de aplicativos são um exercício fundamental para dar um bom começo.** Talvez você não conheça tudo, portanto esteja preparado para acomodar os aplicativos desconhecidos.

### <a name="find-your-apps"></a>Encontre seus aplicativos

O primeiro ponto de decisão em uma migração de aplicativo é quais aplicativos devem ser migrados, o que, se houver, deve permanecer e quais aplicativos serão substituídos. Sempre há uma oportunidade de substituir os aplicativos que você não usará em sua organização. Há várias maneiras de localizar aplicativos em sua organização. **Ao descobrir aplicativos, verifique se você está incluindo aplicativos em desenvolvimento e planejados. Use o Azure AD para autenticação em todos os aplicativos futuros.**

**Usando o Serviços de Federação do Active Directory (AD FS) (AD FS) para coletar um inventário de aplicativo correto:**

- **Usar o Azure AD Connect Health.** Se você tiver uma licença de Azure AD Premium, é recomendável implantar [Azure ad Connect Health](/azure/active-directory/hybrid/how-to-connect-health-adfs) para analisar o uso do aplicativo em seu ambiente local. Você pode usar o [relatório de aplicativo do ADFS](/azure/active-directory/manage-apps/migrate-adfs-application-activity) (versão prévia) para descobrir aplicativos ADFS que podem ser migrados e avaliar a prontidão do aplicativo a ser migrado. Depois de concluir a migração, implante [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) que permite que você monitore continuamente a ti de sombra em sua organização quando estiver na nuvem.

- **AD FS análise de log**. Se você não tiver licenças Azure AD Premium, é recomendável usar o ADFS para as ferramentas de migração de aplicativo do Azure AD com base no [PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Consulte o [Guia de solução](https://aka.ms/migrateapps/adfssolutionguide):

[Migrar aplicativos de Serviços de Federação do Active Directory (AD FS) (AD FS) para o Azure AD.](https://aka.ms/migrateapps/adfssolutionguide)

### <a name="using-other-identity-providers-idps"></a>Usando outros provedores de identidade (IdPs)

Para outros provedores de identidade (como Okta ou ping), você pode usar suas ferramentas para exportar o inventário de aplicativos. Você pode considerar a análise dos princípios de serviço registrados em Active Directory que correspondem aos aplicativos Web em sua organização.

### <a name="using-cloud-discovery-tools"></a>Usando ferramentas de descoberta de nuvem

No ambiente de nuvem, você precisa de visibilidade avançada, controle sobre a viagem de dados e análises sofisticadas para localizar e combater ameaças cibernéticos em todos os seus serviços de nuvem. Você pode reunir seu inventário de aplicativos de nuvem usando as seguintes ferramentas:

- **CASB (agente de segurança de acesso de nuvem**) – um [CASB](/cloud-app-security/) normalmente funciona junto com seu firewall para fornecer visibilidade do uso do aplicativo de nuvem de seus funcionários e ajuda a proteger seus dados corporativos contra ameaças de segurança cibernética. O relatório CASB pode ajudá-lo a determinar os aplicativos mais usados em sua organização e os destinos iniciais para migrar para o Azure AD.

- **Cloud Discovery** -configurando [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery), você obterá visibilidade do uso do aplicativo de nuvem e poderá descobrir aplicativos de ti não aprovados ou de sombra.

- **APIs** – para aplicativos conectados à infraestrutura de nuvem, você pode usar as APIs e ferramentas nesses sistemas para começar a fazer um inventário dos aplicativos hospedados. No ambiente do Azure:

  - Use o cmdlet [Get-AzureWebsite](/powershell/module/servicemanagement/azure/get-azurewebsite?view=azuresmps-4.0.0&redirectedfrom=MSDN&preserve-view=true)para obter informações sobre os sites do Azure.

  - Use o cmdlet [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.2.0&preserve-view=true)para obter informações sobre seus aplicativos Web do Azure.

  - Você pode encontrar todos os aplicativos em execução no Microsoft IIS na linha de comando do Windows usando [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Use [aplicativos](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) e [entidades de serviço](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) para obter informações sobre uma instância de aplicativo e aplicativo em um diretório no Azure AD.

### <a name="using-manual-processes"></a>Usando processos manuais

Depois de tomar as abordagens automatizadas descritas acima, você terá uma boa alça em seus aplicativos. No entanto, você pode considerar fazer o seguinte para garantir que tem uma boa cobertura em todas as áreas de acesso do usuário:

- Entre em contato com os vários proprietários da empresa em sua organização para encontrar os aplicativos em uso na sua organização.

- Execute uma ferramenta de inspeção HTTP no servidor proxy ou analise os logs de proxy para ver onde o tráfego é normalmente roteado.

- Examine os weblogs de sites populares do portal da empresa para ver quais links os usuários acessam mais.

- Entre em contato com os executivos ou outros membros importantes da empresa para garantir que você já tenha coberto os aplicativos críticos para os negócios.

### <a name="type-of-apps-to-migrate"></a>Tipo de aplicativos a serem migrados

Depois de encontrar seus aplicativos, você identificará esses tipos de aplicativos em sua organização:

- Aplicativos que usam protocolos de autenticação modernos já

- Aplicativos que usam protocolos de autenticação herdados que você escolhe para modernizar

- Aplicativos que usam protocolos de autenticação herdados que você opta por não modernizar

- Novos aplicativos LoB (linha de negócios)

### <a name="apps-that-use-modern-authentication-already"></a>Aplicativos que usam autenticação moderna já

Os aplicativos já modernizados são os mais prováveis de serem movidos para o Azure AD. Esses aplicativos já usam protocolos de autenticação modernos (como SAML ou OpenID Connect) e podem ser reconfigurados para autenticar com o Azure AD.

Além das opções na [Galeria de aplicativos do Azure AD,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) eles podem ser aplicativos que já existem em sua organização ou qualquer aplicativo de terceiros de um fornecedor que não faz parte da galeria do Azure AD ([aplicativos que não são da Galeria)](/azure/active-directory/manage-apps/add-non-gallery-app).

Aplicativos herdados que você escolhe para modernizar

Para aplicativos herdados que você deseja modernizar, migrar para o Azure AD para autenticação e autorização de núcleo desbloqueia toda a energia e a riqueza de dados que o [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) e [gráfico de segurança inteligente](https://www.microsoft.com/security/operations/intelligence?rtc=1) precisam oferecer.

É recomendável **atualizar o código de pilha de autenticação** para esses aplicativos do protocolo herdado (como a autenticação integrada do Windows, a delegação restrita de Kerberos, a autenticação baseada em cabeçalhos HTTP) para um protocolo moderno (como SAML ou OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Aplicativos herdados que você opta por não modernizar

Para determinados aplicativos que usam protocolos de autenticação herdados, às vezes, a modernização de sua autenticação não é a coisa certa a fazer por motivos comerciais. Eles incluem os seguintes tipos de aplicativos:

- Aplicativos mantidos localmente para fins de conformidade ou controle.

- Aplicativos conectados a uma identidade local ou a um provedor de Federação que você não deseja alterar.

- Aplicativos desenvolvidos usando padrões de autenticação locais que você não tem planos para mover

O Azure AD pode trazer grandes benefícios para esses aplicativos herdados, pois você pode habilitar recursos modernos de segurança e governança do Azure AD, como [autenticação multifator](/azure/active-directory/authentication/concept-mfa-howitworks), [acesso condicional](/azure/active-directory/conditional-access/overview), [proteção de identidade](/azure/active-directory/identity-protection/), [acesso de aplicativo delegado](/azure/active-directory/manage-apps/access-panel-manage-self-service-access)e [revisões de acesso](https://docs.microsoft.com/azure/active-directory/governance/manage-user-access-with-access-reviews#create-and-perform-an-access-review) nesses aplicativos sem tocar no aplicativo!

Comece **estendendo esses aplicativos para a nuvem** com o [proxy de aplicativo](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) do Azure ad usando meios simples de autenticação (como o cofre de senhas) para que seus usuários sejam migrados rapidamente ou por meio de nossas [integrações de parceiros](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) com controladores de entrega de aplicativos que você já tenha implantado.

### <a name="new-line-of-business-lob-apps"></a>Novos aplicativos LoB (linha de negócios)

Normalmente, você desenvolve aplicativos LoB para o uso interno de sua organização. Se você tiver novos aplicativos no pipeline, é recomendável usar a [plataforma de identidade da Microsoft](/azure/active-directory/develop/about-microsoft-identity-platform) para implementar o OpenID Connect.

### <a name="apps-to-deprecate"></a>Aplicativos para substituição

Os aplicativos sem desmarcar os proprietários e limpar a manutenção e o monitoramento apresentam um risco de segurança para sua organização. Considere a substituição de aplicativos quando:

- sua **funcionalidade é altamente redundante** com outros sistemas • não há **nenhum proprietário comercial**

- Não há **nenhum uso** evidente.

É claro que **não preterim aplicativos críticos para os negócios de alto impacto**. Nesses casos, trabalhe com proprietários de negócios para determinar a estratégia certa.

### <a name="exit-criteria"></a>Critérios de saída

Você tem êxito nesta fase com:

- Uma boa compreensão dos sistemas no escopo da sua migração (que você pode desativar depois de ter movido para o Azure AD)

- Uma lista de aplicativos que inclui:

  - Em quais sistemas esses aplicativos se conectam, de onde e em quais dispositivos os usuários os acessam

  - Se eles serão migrados, preteridos ou conectados com [Azure ad Connect](/azure/active-directory/hybrid/whatis-azure-ad-connect).

> [!NOTE]
> Você pode baixar a [planilha de descoberta de aplicativos](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) para registrar os aplicativos que deseja migrar para a autenticação do Azure AD e aqueles que você deseja deixar, mas gerenciá-los usando [Azure ad Connect](/azure/active-directory/hybrid/whatis-azure-ad-connect).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fase 2: classificar aplicativos e planejar o piloto

A classificação da migração de seus aplicativos é um exercício importante. Nem todo aplicativo precisa ser migrado e transicionado ao mesmo tempo. Depois de coletar informações sobre cada um dos aplicativos, você pode racionalizar quais aplicativos devem ser migrados primeiro e o que pode levar tempo adicional.

### <a name="classify-in-scope-apps"></a>Classificar aplicativos no escopo

Uma maneira de pensar nisso é ao longo dos eixos de importância, uso e vida útil dos negócios, cada um dos quais depende de vários fatores.

### <a name="business-criticality"></a>Nível de importância de negócios

A criticalidade dos negócios assumirá dimensões diferentes para cada empresa, mas as duas medidas que você deve considerar são **recursos e funções** e **perfis de usuário**. Atribua aplicativos com funcionalidade exclusiva um valor de ponto mais alto do que aqueles com funcionalidade redundante ou obsoleta.

![Um diagrama dos espectros de recursos & funcionalidade e perfis de usuário](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Uso

Os aplicativos com **altos números de uso** devem receber um valor maior do que os aplicativos com baixo uso. Atribua um valor mais alto a aplicativos com usuários de equipe externa, executiva ou de segurança. Para cada aplicativo em seu portfólio de migração, conclua essas avaliações.

![Um diagrama dos espectros do volume do usuário e da amplitude do usuário](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Depois de determinar os valores para a criticalidade e o uso dos negócios, você pode determinar o tempo de **vida do aplicativo** e criar uma matriz de prioridade. Veja uma dessas matrizes abaixo:

![Um diagrama de triângulo mostrando as relações entre o uso, o ciclo de vida esperado e a criticalidade dos negócios](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Priorizar aplicativos para migração

Você pode optar por iniciar a migração do aplicativo com os aplicativos de prioridade mais baixa ou com os aplicativos de prioridade mais alta com base nas necessidades da sua organização.

Em um cenário em que você pode não ter experiência com o Azure AD e o Identity Services, considere mover seus **aplicativos de prioridade mais baixa** para o Azure ad primeiro. Isso minimizará o impacto nos negócios e você poderá criar iniciativas. Depois de mover esses aplicativos com êxito e ter obtido a confiança do stakeholder, você pode continuar migrando os outros aplicativos.

Se não houver nenhuma prioridade clara, você deve considerar mover os aplicativos que estão na [Galeria do Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) primeiro e dar suporte a vários provedores de identidade (ADFS ou Okta), pois eles são mais fáceis de integrar. É provável que esses aplicativos sejam os **aplicativos de prioridade mais alta** em sua organização. Para ajudar a integrar seus aplicativos SaaS com o Azure AD, desenvolvemos uma coleção de [tutoriais](/azure/active-directory/saas-apps/tutorial-list) que o orientam pela configuração.

Quando você tiver um prazo para migrar os aplicativos, esse Bucket de aplicativos de prioridade mais alta usará a carga de trabalho principal. Eventualmente, você pode selecionar os aplicativos de prioridade mais baixa, pois eles não alterarão o custo mesmo que você tenha movido o prazo. Mesmo que você precise renovar a licença, ela será de uma pequena quantidade.

Além dessa classificação e, dependendo da urgência da sua migração, você também pode considerar a possibilidade de colocar um **agendamento de migração** no qual os proprietários dos aplicativos devem se envolver para que seus aplicativos sejam migrados. No final desse processo, você deve ter uma lista de todos os aplicativos em buckets priorizados para migração.

### <a name="document-your-apps"></a>Documente seus aplicativos

Primeiro, comece coletando os principais detalhes sobre seus aplicativos. A [planilha de descoberta de aplicativos](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)ajudará você a tomar suas decisões de migração rapidamente e a obter uma recomendação para seu grupo de negócios sem nenhum tempo.

As informações que são importantes para fazer sua decisão de migração incluem:

- **Nome do aplicativo** – o que é esse aplicativo conhecido como negócio?

- **Tipo de aplicativo** – é um aplicativo SaaS de terceiros? Um aplicativo Web personalizado de linha de negócios? Uma API?

- A **criticalidade dos negócios** – é sua alta importância? Pequena? Ou em algum lugar entre eles?

- **Volume de acesso do usuário** – faz com que todos acessem este aplicativo ou apenas algumas pessoas?

- **Vida útil planejada** – quanto tempo esse aplicativo terá? Menos de 6 meses? Mais de 2 anos?

- **Provedor de identidade atual** – qual é o IDP primário para este aplicativo? Ou ele depende do armazenamento local?

- **Método de autenticação** – o aplicativo é autenticado usando padrões abertos?

- **Se você planeja atualizar o código do aplicativo** – é o aplicativo em desenvolvimento planejado ou ativo?

- **Se você planeja manter o aplicativo local** – deseja manter o aplicativo em seu datacenter de longo prazo?

- **Se o aplicativo depende de outros aplicativos ou APIs** – o aplicativo atualmente chama outros aplicativos ou APIs?

- **Se o aplicativo está na galeria do Azure ad** – o aplicativo já está atualmente integrado à [Galeria do Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Outros dados que irão ajudá-lo mais tarde, mas que você não precisa fazer uma decisão de migração imediata incluem:

- **URL do aplicativo** – onde os usuários vão acessar o aplicativo?

- **Descrição do aplicativo** – qual é uma breve descrição do que o aplicativo faz?

- **Proprietário do aplicativo** – que, na empresa, é a POC principal do aplicativo?

- **Comentários gerais ou observações** – quaisquer outras informações gerais sobre o aplicativo ou a propriedade corporativa

Depois de classificar seu aplicativo e documentar os detalhes, certifique-se de obter o proprietário da empresa comprar para sua estratégia de migração planejada.

### <a name="plan-a-pilot"></a>Planejar um piloto

Os aplicativos selecionados para o piloto devem representar os principais requisitos de identidade e segurança de sua organização, e você deve ter uma compra clara dos proprietários do aplicativo. Os pilotos normalmente são executados em um ambiente de teste separado. Consulte [práticas recomendadas para pilotos](/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) na página planos de implantação.

**Não se esqueça de seus parceiros externos.** Verifique se eles participam de agendas e testes de migração. Por fim, verifique se eles têm uma maneira de acessar a assistência técnica em caso de problemas de interrupção.

### <a name="plan-for-limitations"></a>Planejar limitações

Embora alguns aplicativos sejam fáceis de migrar, outros podem levar mais tempo devido a vários servidores ou instâncias. Por exemplo, a migração do SharePoint pode levar mais tempo devido a páginas de entrada personalizadas.

Muitos fornecedores de aplicativos SaaS cobram pela alteração da conexão SSO. Verifique com eles e planeje isso.

O Azure AD também tem [limites de serviço e restrições](/azure/active-directory/users-groups-roles/directory-service-limits-restrictions) que você deve estar atento.

### <a name="app-owner-sign-off"></a>Aprovação do proprietário do aplicativo

Aplicativos críticos para os negócios e de uso universal podem precisar de um grupo de usuários piloto para testar o aplicativo no estágio piloto. Depois de testar um aplicativo no ambiente de pré-produção ou piloto, verifique se os proprietários de negócios de aplicativo desligam o desempenho antes da migração do aplicativo e de todos os usuários para o uso de produção do Azure AD para autenticação.

### <a name="plan-the-security-posture"></a>Planejar a postura de segurança

Antes de iniciar o processo de migração, Reserve um tempo para considerar totalmente a postura de segurança que você deseja desenvolver para o seu sistema de identidade corporativa. Isso se baseia na coleta desses conjuntos valiosos de informações: **identidades e dados, quem está acessando seus dados, dispositivos e locais**.

### <a name="identities-and-data"></a>Identidades e dados

A maioria das organizações tem requisitos específicos sobre identidades e proteção de dados que variam de acordo com o segmento do setor e por funções de trabalho dentro das organizações. Consulte [configurações de acesso de dispositivo e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations) para nossas recomendações, incluindo um conjunto prescrito de [políticas de acesso condicional](/azure/active-directory/active-directory-conditional-access-azure-portal) e recursos relacionados.

Você pode usar essas informações para proteger o acesso a todos os serviços integrados ao Azure AD. Essas recomendações são alinhadas com a pontuação de segurança da Microsoft, bem como a [Pontuação de identidade no Azure ad](/azure/active-directory/fundamentals/identity-secure-score). A classificação ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade

- Planejar aprimoramentos de segurança de identidade

- Examinar o sucesso das suas melhorias

Isso também ajudará a implementar as [cinco etapas para proteger sua infraestrutura de identidade](/azure/security/azure-ad-secure-steps). Use as diretrizes como um ponto de partida para sua organização e ajuste as políticas para atender aos requisitos específicos da sua organização.

### <a name="who-is-accessing-your-data"></a>Quem está acessando seus dados?

Há duas categorias principais de usuários de seus aplicativos e recursos aos quais o Azure AD dá suporte:

- **Interno:** Funcionários, contratados e fornecedores que têm contas em seu provedor de identidade. Isso pode precisar de tabelas dinâmicas adicionais com regras diferentes para gerentes ou liderança em relação a outros funcionários.

- **Externo:** Fornecedores, fornecedores, distribuidores ou outros parceiros de negócios que interagem com sua organização no curso regular de negócios com a [colaboração B2B do Azure AD.](/azure/active-directory/b2b/what-is-b2b)

Você pode definir grupos para esses usuários e preencher esses grupos de diversas maneiras. Você pode escolher que um administrador deve adicionar membros manualmente a um grupo ou pode habilitar a associação de grupo autoatendimento. As regras podem ser estabelecidas para adicionar membros automaticamente em grupos com base nos critérios especificados usando [grupos dinâmicos](/azure/active-directory/users-groups-roles/groups-dynamic-membership).

Os usuários externos também podem consultar clientes que exigem uma consideração especial. [Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-overview), um produto separado dá suporte à autenticação do cliente. No entanto, ele está fora do escopo deste documento.

### <a name="devicelocation-used-to-access-data"></a>Dispositivo/local usado para acessar dados

O dispositivo e o local que um usuário usa para acessar um aplicativo também são importantes. Os dispositivos conectados fisicamente à sua rede corporativa são mais seguros. Conexões de fora da rede por VPN podem precisar de análise.

![Um diagrama que mostra a relação entre o local do usuário e o acesso a dados](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Com esses aspectos do recurso, do usuário e do dispositivo em mente, você pode optar por usar os recursos de [acesso condicional do Azure ad](/azure/active-directory/active-directory-conditional-access-azure-portal) . O acesso condicional vai além das permissões de usuário: ele se baseia em uma combinação de fatores, como a identidade de um usuário ou grupo, a rede à qual o usuário está conectado, o dispositivo e o aplicativo que estão usando e o tipo de dados que eles estão tentando acessar. O acesso concedido ao usuário se adapta a esse conjunto mais amplo de condições.

### <a name="exit-criteria"></a>Critérios de saída

Você é bem-sucedido nesta fase quando você:

- Conheça seus aplicativos
  - Ter documentado totalmente os aplicativos que você pretende migrar
  - Ter aplicativos priorizados com base na criticalidade, no volume de uso e na vida útil dos negócios

- Selecionou aplicativos que representam seus requisitos para um piloto

- Negócios-proprietário comprar para sua priorização e estratégia

- Entenda suas necessidades de postura de segurança e como implementá-las

## <a name="phase-3-plan-migration-and-testing"></a>Fase 3: planejar a migração e os testes

Depois que você obtiver compra comercial, a próxima etapa será começar a migrar esses aplicativos para a autenticação do Azure AD.

### <a name="migration-tools-and-guidance"></a>Ferramentas e diretrizes de migração

Use as ferramentas e as diretrizes abaixo para seguir as etapas exatas necessárias para migrar seus aplicativos para o Azure AD:

- **Diretrizes de migração geral** – use o questionário de White Paper, ferramentas, modelos de email e aplicativos no [Kit de ferramentas de migração de aplicativos do Azure ad](https://aka.ms/migrateapps) para descobrir, classificar e migrar seus aplicativos.

- **Aplicativos SaaS** – consulte nossa lista de [centenas de tutoriais de aplicativos SaaS](/azure/active-directory/active-directory-saas-tutorial-list) e o [plano de implantação de SSO do Azure ad](https://aka.ms/ssodeploymentplan) completo para percorrer o processo de ponta a ponta.

- **Aplicativos em execução local** – saiba tudo [sobre o proxy de aplicativo do AD do Azure](/azure/active-directory/manage-apps/application-proxy) e use o [plano de implantação completo do Azure proxy de aplicativo do AD](https://aka.ms/AppProxyDPDownload) para começar a usar rapidamente.

- **Aplicativos que você está desenvolvendo** – leia nossas diretrizes de [integração](/azure/active-directory/develop/active-directory-integrating-applications) e [registro](/azure/active-directory/develop/active-directory-v2-app-registration) passo a passo.

Após a migração, você pode optar por enviar comunicação informando os usuários da implantação bem-sucedida e lembrá-los de quaisquer novas etapas que precisam ser executadas.

### <a name="plan-testing"></a>Teste de plano

Durante o processo de migração, seu aplicativo pode já ter um ambiente de teste usado durante implantações regulares. Você pode continuar a usar esse ambiente para testes de migração. Se um ambiente de teste não estiver disponível no momento, você poderá configurar um usando Azure App serviço ou máquinas virtuais do Azure, dependendo da arquitetura do aplicativo. Você pode optar por configurar um locatário do Azure AD de teste separado para usar ao desenvolver suas configurações de aplicativo. Esse locatário será iniciado em um estado limpo e não será configurado para sincronização com nenhum sistema.

Você pode testar cada aplicativo fazendo logon com um usuário de teste e certificar-se de que toda a funcionalidade seja a mesma do que antes da migração. Se você determinar durante os testes que os usuários precisarão atualizar suas configurações de [MFA](/active-directory/authentication/howto-mfa-userstates) ou [SSPR](/azure/active-directory/authentication/quickstart-sspr), ou se estiver adicionando essa funcionalidade durante a migração, certifique-se de adicioná-la ao seu plano de comunicação do usuário final. Consulte os modelos de comunicação do usuário final [MFA](https://aka.ms/mfatemplates) e [SSPR](https://aka.ms/ssprtemplates) .

Depois de migrar os aplicativos, acesse o [portal do Azure](https://aad.portal.azure.com/) para testar se a migração foi bem-sucedida. Siga as instruções abaixo:

- Selecione **aplicativos empresariais &gt; todos os aplicativos** e localize seu aplicativo na lista.

- Selecione **Gerenciar &gt; usuários e grupos** para atribuir pelo menos um usuário ou grupo ao aplicativo.

- Selecione **Gerenciar &gt; acesso condicional**. Examine sua lista de políticas e certifique-se de que você não está bloqueando o acesso ao aplicativo com uma [política de acesso condicional](/azure/active-directory/active-directory-conditional-access-azure-portal).

Dependendo de como você configura seu aplicativo, verifique se o SSO funciona corretamente.

| Tipo de autenticação      | Testando                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Selecione **&gt; permissões de aplicativos empresariais** e verifique se você consentiu com o aplicativo a ser usado em sua organização nas configurações de usuário para seu aplicativo. |
| **SSO baseado em SAML** | Use o botão [testar configurações de SAML](/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) encontrado em **logon único.** |
| **SSO baseado em senha** | Baixe e instale a [extensão de entrada segura do myapps](/azure/active-directory/user-help/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension). Essa extensão ajuda a iniciar qualquer um dos aplicativos de nuvem da sua organização que exigem que você use um processo de SSO. |
| **[Proxy do Aplicativo](/azure/active-directory/manage-apps/application-proxy)** | Verifique se o conector está em execução e atribuído ao seu aplicativo. Visite o [Guia de solução de problemas de proxy de aplicativo](/azure/active-directory/manage-apps/application-proxy-troubleshoot) para obter mais assistência. |

### <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas, confira nosso [Guia de solução de problemas de aplicativos](https://aka.ms/troubleshoot-apps) para obter ajuda. Consulte também [problemas ao entrar em um aplicativo personalizado](/azure/active-directory/manage-apps/application-sign-in-problem-custom-dev).

### <a name="plan-rollback"></a>Reversão do plano

Se a sua migração falhar, a melhor estratégia é reverter e testar. Aqui estão as etapas que você pode executar para mitigar problemas de migração:

- **Tirar capturas de tela** da configuração existente do seu aplicativo. Você pode verificar novamente se deve reconfigurar o aplicativo mais uma vez.

- Você também pode considerar **o fornecimento de links para a autenticação herdada**, em caso de problemas com a autenticação na nuvem.

- Antes de concluir a migração, não **altere sua configuração existente** com o provedor de identidade anterior.

- Comece migrando **os aplicativos que dão suporte a vários IDPs**. Se algo der errado, você sempre poderá mudar para a configuração do IdP preferencial.

- Certifique-se de que sua experiência de aplicativo tenha um **botão de comentários** ou ponteiros para a **assistência técnica** em caso de problemas.

### <a name="exit-criteria"></a>Critérios de saída

Você terá êxito nesta fase quando tiver:

- Determinado como cada aplicativo será migrado

- Revisar as ferramentas de migração

- Planejou seu teste, incluindo ambientes de teste e grupos

- Reversão planejada

## <a name="phase-4-plan-management-and-insights"></a>Fase 4: gerenciamento e informações do plano

Depois que os aplicativos são migrados, você deve garantir que:

- Os usuários podem acessar e gerenciar com segurança

- Você pode obter as informações apropriadas sobre uso e integridade do aplicativo

Recomendamos executar as seguintes ações conforme apropriado para sua organização.

### <a name="manage-your-users-app-access"></a>Gerenciar o acesso do aplicativo de seus usuários

Depois de migrar os aplicativos, você pode enriquecer a experiência do usuário de várias maneiras

**Tornar aplicativos detectáveis**

**Aponte seu usuário** para a experiência do portal do [myapps](/azure/active-directory/user-help/my-apps-portal-end-user-access#my-apps-secure-sign-in-extension). Aqui, eles podem acessar todos os aplicativos baseados em nuvem, os aplicativos que você disponibiliza usando [Azure ad Connect](/azure/active-directory/hybrid/whatis-azure-ad-connect)e os aplicativos que usam o [proxy de aplicativo](/azure/active-directory/manage-apps/application-proxy) forneciam permissões para acessar esses aplicativos.

Você pode orientar seus usuários sobre como descobrir seus aplicativos:

- Usar o recurso de [logon único existente](/azure/active-directory/active-directory-saas-custom-apps#existing-single-sign-on) para **vincular seus usuários a qualquer aplicativo**

- Habilitar o [acesso de aplicativo de autoatendimento](/azure/active-directory/application-access-self-service-how-to)a um aplicativo e **permitir que os usuários adicionem aplicativos que você** organizar

- [Ocultar aplicativos de usuários finais](/azure/active-directory/manage-apps/hide-application-from-user-portal) (aplicativos padrão da Microsoft ou outros aplicativos) para **tornar os aplicativos que eles precisam mais detectáveis**

### <a name="make-apps-accessible"></a>Tornar aplicativos acessíveis

**Permitir que os usuários acessem aplicativos de seus dispositivos móveis**. Os usuários podem acessar o portal do myapps com o navegador gerenciado pelo Intune em seus dispositivos [iOS](/azure/active-directory/manage-apps/hide-application-from-user-portal) 7,0 ou posteriores ou [Android](/azure/active-directory/manage-apps/hide-application-from-user-portal) .

Os usuários podem baixar um **navegador gerenciado pelo Intune**:

- **Para dispositivos Android**, na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)

- **Para dispositivos da Apple**, da [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) ou eles podem baixar o [aplicativo móvel meus aplicativos para IOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Permitir que os usuários abram seus aplicativos a partir de uma extensão de navegador.**

Os usuários podem [baixar a extensão de entrada segura do myapps](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) no [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) no [Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) ou no [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) e podem iniciar aplicativos diretamente da barra do navegador para:

- **Pesquisar seus aplicativos e fazer com que seus aplicativos usados mais recentemente sejam exibidos**

- **Converta automaticamente as URLs internas** que você configurou no [proxy de aplicativo](/azure/active-directory/manage-apps/application-proxy) para as URLs externas apropriadas. Agora, os usuários podem trabalhar com os links que estão familiarizados, independentemente de onde estiverem.

**Permita que os usuários abram seus aplicativos do Office.com.**

Os usuários podem acessar o [Office.com](https://www.office.com/) para **Pesquisar seus aplicativos e fazer com que seus aplicativos usados mais recentemente sejam exibidos** para eles diretamente de onde funcionam.

### <a name="secure-app-access"></a>Proteger o acesso do aplicativo

O Azure AD fornece um local de acesso centralizado para gerenciar seus aplicativos migrados. Vá para a [portal do Azure](https://portal.azure.com/) e habilite os seguintes recursos:

- **Proteger o acesso do usuário aos aplicativos.** Habilite [as políticas de acesso condicional](/azure/active-directory/active-directory-conditional-access-azure-portal)ou a [proteção de identidade](/azure/active-directory/active-directory-identityprotection)para proteger o acesso do usuário a aplicativos com base no estado do dispositivo, local e muito mais.

- **Provisionamento automático.** Configure o [provisionamento automático de usuários](/azure/active-directory/manage-apps/user-provisioning) com uma variedade de aplicativos SaaS de terceiros que os usuários precisam acessar. Além de criar identidades de usuário, ele inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam.

- **Delegar** o **Gerenciamento** de acesso do usuário. Conforme apropriado, habilite o acesso de aplicativo de autoatendimento para seus aplicativos e *atribua um Aprovador de negócios para aprovar o acesso a esses aplicativos*. Use o [Gerenciamento de grupo de autoatendimento](/azure/active-directory/users-groups-roles/groups-self-service-management)para grupos atribuídos a coleções de aplicativos.

- **Delegar acesso de administrador.** usando a **função de diretório** para atribuir uma função de administrador (como administrador de aplicativos, administrador de aplicativos de nuvem ou desenvolvedor de aplicativos) ao seu usuário.

### <a name="audit-and-gain-insights-of-your-apps"></a>Auditar e obter informações sobre seus aplicativos

Você também pode usar o [portal do Azure](https://portal.azure.com/) para auditar todos os seus aplicativos de um local centralizado,

- **Faça auditoria de seu aplicativo** usando **aplicativos empresariais, audite** ou acesse as mesmas informações da [API de relatórios do Azure ad](/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) para integrar suas ferramentas favoritas.

- **Exiba as permissões para um aplicativo** usando **aplicativos empresariais, permissões** para aplicativos que usam OAuth/OpenID Connect.

- **Obtenha informações de entrada** usando **aplicativos empresariais e entradas**. Acesse as mesmas informações da [API de relatórios do Azure AD.](/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- **Visualizar o uso do aplicativo** no [pacote de conteúdo do Azure ad PowerBI](/azure/active-directory/active-directory-reporting-power-bi-content-pack-how-to)

### <a name="exit-criteria"></a>Critérios de saída

Você é bem-sucedido nesta fase quando você:

- Fornecer acesso de aplicativo seguro aos seus usuários

- Gerenciar para auditar e obter informações sobre os aplicativos migrados

### <a name="do-even-more-with-deployment-plans"></a>Faça ainda mais com planos de implantação

Os planos de implantação orientam você pelo valor de negócios, planejamento, etapas de implementação e gerenciamento de soluções do Azure AD, incluindo cenários de migração de aplicativo. Eles reúnem tudo o que você precisa para começar a implantar e obter o valor dos recursos do Azure AD. Os guias de implantação incluem conteúdo como práticas recomendadas da Microsoft, comunicações do usuário final, guias de planejamento, etapas de implementação, casos de teste e muito mais.

Muitos [planos de implantação](https://aka.ms/deploymentplans) estão disponíveis para seu uso e estamos sempre fazendo mais!

### <a name="contact-support"></a>Contate o suporte

Visite os links de suporte a seguir para criar ou acompanhar o tíquete de suporte e monitorar a integridade.

- **Suporte do Azure:** Você pode chamar [suporte da Microsoft](https://azure.microsoft.com/support) e abrir um tíquete para qualquer Azure

Problema de implantação de identidade, dependendo do seu Contrato Enterprise com a Microsoft.

- **FastTrack**: se você comprou as licenças do EMS (Enterprise Mobility and Security) ou Azure ad Premium, você está qualificado para receber assistência de implantação do [programa FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Participe da equipe de engenharia de produtos:** Se estiver trabalhando em uma implantação de cliente importante com milhões de usuários, você terá direito a dar suporte à equipe de conta Microsoft ou ao arquiteto de soluções de nuvem. Com base na complexidade da implantação do projeto, você pode trabalhar diretamente com a [equipe de engenharia de produtos do Azure Identity.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Blog de identidade do Azure AD:** Assine o [blog de identidade do Azure ad](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) para se manter atualizado com todos os comunicados mais recentes sobre produtos, aprofundamento e informações de roteiros fornecidos diretamente pela equipe de engenharia de identidade.

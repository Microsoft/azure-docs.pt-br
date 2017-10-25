---
title: "Implementação da cartilha do PoC do Azure Active Directory | Microsoft Docs"
description: "Explorar e implementar rapidamente os cenários de Identidade e Gerenciamento de Acesso"
services: active-directory
keywords: azure active directory, cartilha, Prova de Conceito, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 10877ee33ec04cf0d350417af59d598eab249aa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Guia estratégico de prova de conceito do Azure Active Directory: Implementação

## <a name="foundation---syncing-ad-to-azure-ad"></a>Foundation - sincronizando o AD com o Azure AD 

Uma identidade híbrida é a base para a maioria dos clientes empresariais que já tem um diretório local. A meta aqui é gastar intencionalmente o menor tempo possível para mostrar o valor dos cenários de identidade e acesso reais. 

| Cenário | Blocos de construção| 
| --- | --- |  
| [Estendendo sua identidade local para a nuvem](#extending-your-on-premises-identity-to-the-cloud) | [Sincronização de Diretórios - PHS (sincronização de hash de senha)](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Observação**: se você já tiver o DirSync/ADSync ou versões anteriores do Azure AD Connect, esta etapa será opcional. Alguns cenários neste guia podem exigir uma versão mais recente do Azure AD Connect.  <br/>[Identidade visual](active-directory-playbook-building-blocks.md#branding) | 
| [Atribuindo licenças do Azure AD usando grupos](#assigning-azure-ad-licenses-using-groups) | [Licenciamento baseado em grupo](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Estender sua identidade local para a nuvem 

1. Carlos é o administrador do Active Directory da Contoso. Ele recebe o pedido para habilitar a identidade como um serviço para um conjunto de usuários. Após a execução do assistente do Azure AD Connect, a identidade dos usuários de destino fica disponível na nuvem. 
2. Carlos pede a Susana, uma das usuárias de destino, que acesse o painel de acesso do Azure Active Directory e confirme se ela consegue autenticar. Susana vê uma página de logon com marca e um painel de acesso vazio, que está pronto para habilitar o acesso futuro a aplicativos.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Atribuição de licenças do Azure AD usando Grupos 

1. Carlos é o Administrador Global do Azure AD e quer alocar licenças do Azure AD a um conjunto específico de usuários como parte da distribuição inicial do Azure AD.
2. Carlos cria um grupo de usuários piloto. 
3. Carlos atribui as licenças ao grupo
4. Susana, uma das operadoras de informações, é adicionada ao grupo de segurança como parte de suas funções de trabalho
5. Após algum tempo, Susana tem acesso à licença premium do Azure AD. Isso permitirá mais cenários de POC posteriormente.

## <a name="theme---lots-of-apps-one-identity"></a>Tema - muitos aplicativos, uma identidade

| Cenário | Blocos de construção| 
| --- | --- |  
| [Integrar aplicativos SaaS - SSO federado](#integrate-saas-applications---federated-sso) | [Configuração de SSO federado SaaS](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Grupos - propriedade delegada](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integrar aplicativos SaaS - SSO de senha](#integrate-saas-applications---password-sso) | [Configuração do SSO de senha de SaaS](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [SSO e eventos de ciclo de vida de identidade](#sso-and-identity-lifecycle-events) | [Ciclo de vida de identidade e SaaS](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Proteger o acesso às Contas Compartilhadas](#secure-access-to-shared-accounts) | [Configuração de Contas Compartilhadas SaaS](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Acesso remoto seguro a aplicativos locais](#secure-remote-access-to-on-premises-applications) | [Configuração de proxy de aplicativo](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Sincronizar identidades LDAP para o Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Configuração do Conector LDAP Genérico](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integrar aplicativos SaaS - SSO federado 

1. Carlos é o Administrador Global do Azure AD e recebe uma solicitação do departamento de marketing para habilitar o acesso à instância deles do ServiceNow. Carlos localiza o tutorial passo a passo na documentação do Azure AD e o segue, e delega a atribuição de usuários ao aplicativo para Kevin, o chefe da equipe de marketing. 
2. Kevin faz logon como o proprietário de direitos do ServiceNow e atribui Susana ao aplicativo. Kevin também observa que o perfil de Susana foi criado automaticamente no ServiceNow
3. Susana é uma operadora de informações do departamento de marketing. Ela faz logon no Azure AD e encontra todos os aplicativos SaaS aos quais foi atribuída no portal myapps. A partir daí, ela obtém acesso ao ServiceNow.
4. O departamento de marketing quer fazer a auditoria de quem acessou o ServiceNow. Carlos baixa um relatório de atividades e o compartilha com Kevin por email.  

### <a name="sso-and-identity-lifecycle-events"></a>SSO e eventos de ciclo de vida de identidade

1. Susana precisa se ausentar e, de acordo com a política corporativa, a conta do AD local é temporariamente desabilitada. Agora, Susana não consegue fazer logon no Azure AD e, portanto, não consegue acessar o ServiceNow. 
2. Susana faz uma mudança lateral, da equipe de marketing para vendas. Kevin remove o acesso dela do ServiceNow. Susana faz logon no myapps do Azure AD e não vê mais o bloco do ServiceNow. Após 10 minutos, Kevin confirma que a conta de Susana foi desabilitada no Console de gerenciamento do ServiceNow.

### <a name="integrate-saas-applications---password-sso"></a>Integrar aplicativos SaaS - SSO de senha

1. Carlos configura o acesso ao Atlassian HipChat. O HipChat tem integração de SSO de senha e concede acesso a Susana
2. Susana faz logon no portal do myapps e vê um link para baixar a extensão de navegador para IE do Azure AD, e baixa a extensão
3. Ao clicar, ela recebe a solicitação para fornecer suas credenciais de usuário e senha do HipChat. Essa é uma operação única, e depois de conclui-la, ela recebe o acesso ao HipChat
4. Alguns dias mais tarde, Susana abre o portal myapps e clica no HipChat novamente. Desta vez, ele obtém o acesso
5. Kevin, o proprietário do aplicativo HipChat, deseja auditar quem acessou o aplicativo. Carlos baixa um relatório de auditoria e o compartilha com Kevin por email. 

### <a name="secure-access-to-shared-accounts"></a>Proteger o acesso às Contas Compartilhadas 

1. Bob recebe a tarefa de proteger o identificador do Twitter compartilhado para membros da equipe de vendas. Ele adiciona o Twitter como um aplicativo de SSO e o atribui ao grupo de segurança da equipe de vendas. Ele recebeu as credenciais para a conta compartilhada e as fornece no sistema. 
2. Compartilhar credenciais do Twitter não é algo confiável, pois várias pessoas ficam sabendo. Carlos habilita a substituição automática de senha do Twitter.
3. Susana, que é membro da equipe de vendas, faz logon no portal do myapps e vê um link para baixar a extensão de navegador para IE do Azure AD Ela instala a extensão.
4. Ao clicar, ela obtém acesso diretamente ao Twitter. Ela não sabe a senha.
5. Arnaldo também faz parte da equipe de vendas. Ele tem a mesma experiência que Susana nas etapas 3 e 4
6. O departamento de vendas quer fazer a auditoria de quem acessou o Twitter. Carlos baixa um relatório de atividades e o compartilha com Kevin por email. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Acesso remoto seguro a aplicativos locais

1. Carlos, o Administrador Global do Azure AD, recebeu várias solicitações para habilitar o acesso dos funcionários remotos a vários recursos locais úteis, como o aplicativo de despesas. Ele segue a [Documentação do Proxy de Aplicativo](active-directory-application-proxy-enable.md) para instalar um conector e publicar as Despesas como um aplicativo de Proxy de Aplicativo. 
2. Carlos compartilha a URL externa do aplicativo de Despesas com Susana, uma das funcionárias que precisa do acesso remoto. Ela acessa o link e, depois de autenticar no AAD, é capaz de acessar o aplicativo de Despesas e continuar produzindo mesmo remotamente. 
3. Em seguida, Carlos continua publicando outros aplicativos locais usando o mesmo processo e concede acesso aos usuários conforme o necessário. Ele adiciona o acesso condicional e a autenticação multifator aos aplicativos mais importantes que ele publica, a fim de garantir mais segurança.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Sincronizar identidades LDAP para o Azure AD

1. A empresa de Carlos tem uma infraestrutura complexa de identidade. A maioria dos usuários é mantida dentro do ADDS (Active Directory Domain Services) do Windows Server. Alguns deles são gerenciadas pelo sistema de RH dentro do ADLDS (Active Directory Lightweight Directory Services).
2. Carlos recebe a tarefa de habilitar o acesso a aplicativos SaaS para todos os usuários (também aos não presentes no ADDS).
3. Carlos configura o conector LDAP Genérico para extrair dados do ADLDS no Azure AD Connect.
4. Carlos cria regras de sincronização para que os usuários do LDAP sejam incluídos no Metaverse e no Azure AD
5. Susana, como usuário do LDAP, acessa seu aplicativo SaaS usando a identidade sincronizada



> [!IMPORTANT] 
> Esta é uma configuração avançada que exige alguma familiaridade com o FIM/MIM. Se usada em produção, aconselhamos consulgar as perguntas sobre essa configuração através do [Suporte Premier](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Tema - aumentar sua segurança 

| Cenário | Blocos de construção| 
| --- | --- |  
| [Acesso à conta de administrador protegida](#secure-administrator-account-access) | [Azure MFA com chamadas telefônicas](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Acesso seguro para aplicativos](#secure-access-to-applications) | [Acesso condicional para aplicativos SaaS](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Habilitar administração Just in Time](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Proteger identidades com base em riscos](#protect-identities-based-on-risk) | [Descoberta de eventos de risco](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Implantação de políticas de risco de logon](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [Autenticar sem senhas usando autenticação baseada em certificado](#authenticate-without-passwords-using-certificate-based-authentication) | [Configurando autenticação baseada em certificado](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Acesso à conta de administrador protegida

1. Carlos é o Administrador Global do Azure AD. Ele identificou Stuart como coadministrador do serviço. 
2. Carlos configura a conta de Stuart para sempre exigir MFA a fim de melhorar a postura de segurança
3. Stuart faz logon no Portal do Azure e percebe que ele precisa registrar seu número de telefone para continuar o logon
4. Agora, os próximos logons de Stuart são protegidos com a Autenticação Multifator, e ele recebe um telefonema para verificar sua identidade.

### <a name="secure-access-to-applications"></a>Acesso seguro a aplicativos

1. Kevin é o proprietário da empresa ServiceNow. Agora, a empresa quer que esses usuários façam logon com MFA ao acessar fora da rede corporativa.
2. Carlos, nosso Administrador Global do Azure AD, adiciona uma política de acesso condicional para o aplicativo ServiceNow a fim de habilitar o MFA para acesso externo
3. Susana, a operadora de informações, faz logon no portal myapps e clica no bloco do ServiceNow. Agora ela recebe o desafio da MFA.

### <a name="enable-just-in-time-jit-administration"></a>Habilitar administração JIT (Just in Time)

1. Carlos e Stuart são Administradores Globais do Azure AD. Eles querem habilitar o acesso JIT para as funções de gerenciamento e também manter os registros de uso das funções privilegiadas.
2. Carlos habilita o PIM no locatário do Azure AD e se torna o administrador de segurança. Ele altera a associação de função de administrador global de si mesmo e de Stuart, mudando de permanente para qualificado.
3. Carlos e Stuart agora precisam ativar sua função no Portal do Azure antes de fazer quaisquer alterações na configuração do Azure AD. 

### <a name="protect-identities-based-on-risk"></a>Proteger identidades com base em riscos 

1. Susana, uma operadora de informações, tenta fazer logon de um navegador Tor. 
2. Carlos verifica o painel de proteção do Azure AD Identity Protection e vê o logon de Susana de um endereço IP anônimo. A equipe de segurança quer desafiar esses acessos com MFA
3. Carlos habilita a Política do Azure AD Identity Protection para usar MFA para eventos de risco médio ou alto
4. O tempo passa e Susana faz logon pelo navegador Tor novamente. Desta vez, ele verá o desafio da MFA

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>Autenticar sem senhas usando autenticação baseada em certificado

1. Carlos é o Administrador Global da instituição financeira, que proíbe o uso de senhas como um fator de autenticação para seus aplicativos.
2. Carlos habilita e impõe a autenticação de certificado no ADFS e no Azure AD
3. Susana, ao acessar o aplicativo, recebe a solicitação para autenticar usando um certificado

## <a name="theme---scale-with-self-service"></a>Tema - escala com serviço de autoatendimento

| Cenário | Blocos de construção| 
| --- | --- |  
| [Redefinição de senha por autoatendimento](#self-service-password-reset) | [Redefinição de senha por autoatendimento](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Acesso a aplicativos de autoatendimento](#self-service-access-to-applications) | [Acesso a aplicativos de autoatendimento](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Redefinição de senha por autoatendimento 

1. Carlos é o Administrador Global do Azure AD e permite o Gerenciamento de Senha de Autoatendimento a um subconjunto de usuários, incluindo Susana. 
2. Susana faz logon no portal myapps e vê uma mensagem para registrar suas informações de segurança para eventos de redefinição de senha futuros.
3. Após alguns dias, Susana esquece sua senha e a redefine por meio do Portal do Azure AD

### <a name="self-service-access-to-applications"></a>Acesso a aplicativos de autoatendimento 

1. Kevin é o proprietário da empresa ServiceNow. Ele quer que os usuários se "registrem" sob demanda, em vez de adicioná-los todos de uma vez
2. Carlos, nosso Administrador Global do Azure AD, modifica o aplicativo ServiceNow para habilitar solicitações de autoatendimento
3. Susana, a operadora de informações, faz logon no portal myapps, clica no botão "Adicionar mais aplicativos" e vê ServiceNow como um dos aplicativos recomendados. Em seguida, ela navega de volta ao portal myapps e vê o aplicativo ServiceNow.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
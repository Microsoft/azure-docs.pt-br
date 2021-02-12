---
title: 'Azure AD Connect: Autenticação de Passagem – Perguntas frequentes | Microsoft Docs'
description: Obtenha respostas para perguntas frequentes sobre a Autenticação de Passagem do Azure Active Directory
services: active-directory
keywords: Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f797c40a276323cea9983c5340d2d854160c83
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368470"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticação de passagem do Azure Active Directory: perguntas frequentes

Este artigo aborda perguntas frequentes sobre a Autenticação de Passagem do Azure AD (Azure Active Directory). Continue verificando conteúdo atualizado.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Qual dos métodos para entrar no Azure AD, Autenticação de Passagem, sincronização de hash de senha e Serviços de Federação do Active Directory (AD FS), devo escolher?

Leia [este guia](./choose-ad-authn.md) para uma comparação entre os vários métodos de conexão do Azure AD e como escolher o método de entrada certo para a sua organização.

## <a name="is-pass-through-authentication-a-free-feature"></a>A Autenticação de Passagem é um recurso gratuito?

A Autenticação de Passagem é um recurso gratuito. Você não precisa de nenhuma edição paga do Azure AD para usá-lo.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>O [Acesso Condicional](../conditional-access/overview.md) funciona com a Autenticação de Passagem?

Sim. Todos os recursos de acesso condicional, incluindo a autenticação multifator do Azure AD, funcionam com a autenticação de passagem.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>A Autenticação de Passagem dá suporte à "ID alternativa" como nome de usuário, em vez de "userPrincipalName"?
Sim, a entrada usando um valor não-UPN, como um email alternativo, tem suporte para PTA (autenticação de passagem) e PHS (sincronização de hash de senha). Para obter mais informações sobre a [ID de logon alternativa](../authentication/howto-authentication-use-email-signin.md).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>A sincronização de hash de senha funciona como um fallback da Autenticação de Passagem?

Não. A Autenticação de Passagem _não_ realiza o failover automaticamente para a sincronização de hash de senha. Para evitar falhas de entrada do usuário, você deve configurar a Autenticação de Passagem para [alta disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>O que acontece quando eu faço para alternar da sincronização de hash de senha para autenticação de passagem?

Quando você usa Azure AD Connect para alternar o método de entrada da sincronização de hash de senha para autenticação de passagem, a autenticação de passagem torna-se o método de entrada primário para seus usuários em domínios gerenciados. Observe que os hashes de senha de todos os usuários que foram sincronizados anteriormente pela sincronização de hash de senha permanecem armazenados no Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Eu posso instalar um conector de [Proxy de Aplicativo Azure AD](../manage-apps/application-proxy.md) no mesmo servidor que um Agente de Autenticação de Passagem?

Sim. As versões remodeladas do Agente de Autenticação de passagem, versões 1.5.193.0 ou posteriores, dão suporte a esta configuração.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>De quais versões do Azure AD Connect e do Agente de Autenticação de Passagem você precisa?

Para que esse recurso funcione, você precisa da versão 1.1.750.0 ou posterior para o Azure AD Connect e 1.5.193.0 ou posterior para o Agente de Autenticação de Passagem. Instale o software em servidores com Windows Server 2012 R2 ou posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>O que acontece se minha senha de usuário tiver expirado e eu tentar entrar usando a Autenticação de Passagem?

Caso você tenha configurado [write-back de senha](../authentication/concept-sspr-writeback.md) para um usuário específico e o usuário entrar usando a Autenticação de Passagem, ele poderá alterar ou redefinir sua senha. As senhas serão gravadas de volta no Active Directory local conforme o esperado.

Se você não tiver configurado o write-back de senha para um usuário específico ou se o usuário não tiver uma licença válida do Azure AD atribuída a ele, o usuário não poderá atualizar sua senha na nuvem. Ele não poderá atualizar a senha mesmo que ela tenha expirado. Em vez disso, o usuário verá essa mensagem: “Sua organização não permite que você atualize sua senha neste site. Atualize-a de acordo com o método recomendado pela sua organização ou peça ajuda ao seu administrador se necessário". O usuário ou o administrador precisa redefinir a senha no Active Directory local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como a Autenticação de Passagem protege você contra ataques de senha de força bruta?

[Ler as informações sobre o bloqueio inteligente](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que os Agentes de Autenticação de Passagem comunicam pelas portas 80 e 443?

- Os Agentes de Autenticação fazem solicitações HTTPS pela porta 443 para todas as operações de recurso.
- Os agentes de autenticação fazem solicitações HTTP pela porta 80 para baixar as CRLs (listas de certificados revogados) TLS/SSL.

     >[!NOTE]
     >As atualizações recentes reduziram o número de portas exigidas pelo recurso. Se você tiver versões mais antigas do Azure AD Connect ou do Agente de Autenticação, mantenha estas portas abertas também: 5671, 8080, 9090, 9091, 9350, 9352 e 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os Agentes de Autenticação de Passagem podem se comunicar através de um servidor proxy da Web de saída?

Sim. Se a Descoberta Automática de Proxy da Web (WPAD) estiver habilitado em seu ambiente local, os Agentes de Autenticação tentarão automaticamente localizar e usar um servidor proxy da Web na rede.

Se você não tiver o WPAD em seu ambiente, você pode adicionar informações de proxy (conforme mostrado abaixo) para permitir que um agente de autenticação de passagem para se comunicar com o Azure AD:
- Configure informações de proxy no Internet Explorer antes de instalar o agente de autenticação de passagem no servidor. Isso permitirá que você conclua a instalação do Agente de Autenticação, mas ele ainda será exibido como **Inativo** no portal do Administrador.
- No servidor, navegue até "C: \ Arquivos de Programas \ Microsoft Azure AD Connect Authentication Agent".
- Edite o arquivo de configuração "AzureADConnectAuthenticationAgentService" e adicione as seguintes linhas (substitua "\://contosoproxy.com:8080" pelo seu endereço de proxy real):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Eu posso instalar dois ou mais Agentes de Autenticação de Passagem no mesmo servidor?

Não, você só pode instalar um Agente de Autenticação de Passagem em um único servidor. Se você quiser configurar a autenticação de passagem para alta disponibilidade, [siga as instruções aqui](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Preciso renovar manualmente os certificados usados pelos Agentes de Autenticação de Passagem?

A comunicação entre cada Agente de Autenticação de Passagem e o Azure AD é protegida com a autenticação baseada em certificado. Esses [certificados são renovados automaticamente a cada poucos meses pelo Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Não é necessário renová-los manualmente. Você pode limpar os certificados expirados mais antigos, conforme o necessário.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Como fazer para remover um Agente de Autenticação de Passagem?

Enquanto um Agente de Autenticação de Passagem estiver em execução, ele permanecerá ativo e continuamente lidará com solicitações de entrada de usuários. Se você quiser desinstalar um Agente de Autenticação, acesse **Painel de Controle -> Programas -> Programas e Recursos** e desinstale os programas **Agente de Autenticação do Microsoft Azure AD Connect** e o **Atualizador do Agente do Microsoft Azure AD Connect**.

Se você verificar a folha de Autenticação de Passagem no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) depois de concluir a etapa anterior, verá o agente de autenticação exibido como **Inativo**. Isso é _esperado_. O agente de autenticação é descartado automaticamente da lista após 10 dias.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Já uso o AD FS para entrar no Azure AD. Como fazer para passar dele para a Autenticação de Passagem?

Se você estiver migrando do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem, é altamente recomendável seguir nosso guia detalhado de implantação publicado [aqui](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Eu posso usar a Autenticação de Passagem em um ambiente de várias floresta do Active Directory?

Sim. Ambientes de várias florestas têm suporte se houver relações de confiança de floresta (duas vias) entre suas florestas de Active Directory e se o roteamento de sufixo de nome estiver configurado corretamente.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>A autenticação de passagem fornece balanceamento de carga entre vários Agentes de Autenticação?

Não, a instalação de vários Agentes de Autenticação de Passagem garante a [alta disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Não fornece o balanceamento de carga determinístico entre os Agentes de Autenticação. Qualquer agente de autenticação (aleatório) pode processar uma solicitação de entrada do usuário específico.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos Agentes de Autenticação de Passagem preciso instalar?

A instalação de vários Agentes de Autenticação de Passagem garante a [alta disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Mas não fornece o balanceamento de carga determinístico entre os Agentes de Autenticação.

Considere o horário de pico e a carga média de solicitações de entrada que você espera ver no seu locatário. Como um parâmetro de comparação, um único Agente de autenticação pode manipular de 300 a 400 autenticações por segundo em um servidor padrão com CPU de 4 núcleos e 16 GB de RAM.

Para estimar o tráfego de rede, use as seguintes diretrizes de tamanho:
- Cada solicitação tem um tamanho de payload (0,5 K + 1 K * num_of_agents) bytes. Ou seja, dados do Azure AD para o Agente de autenticação. Aqui, "num_of_agents" indica o número de Agentes de autenticação registrado no seu locatário.
- Cada resposta tem um tamanho de payload de 1 K de bytes. Ou seja, dados do Agente de autenticação do Azure AD.

Para a maioria dos clientes, um total de dois ou três agentes de autenticação é o suficiente para alta disponibilidade e capacidade. Você deve instalar os Agentes de Autenticação perto de seus controladores de domínio para melhorar a latência de entrada.

>[!NOTE]
>Há um limite do sistema de 40 Agentes de Autenticação por locatário.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Por que eu preciso de uma conta de Administrador Global somente em nuvem para habilitar a autenticação de passagem?

É recomendável que você habilitar ou desabilitar a autenticação de passagem usando uma conta de Administrador Global somente em nuvem. Saiba mais sobre como [Adicionar uma conta de administrador global somente em nuvem](../fundamentals/add-users-azure-active-directory.md). Fazendo dessa maneira garante que você não seja bloqueado de seu locatário.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como posso desabilitar a Autenticação de Passagem?

Execute novamente o assistente Azure AD Connect e altere o método de entrada do usuário de Autenticação de Passagem para outro método. Essa alteração desabilita a Autenticação de Passagem no locatário e desinstala o Agente de Autenticação do servidor. Você deve desinstalar manualmente os Agentes de Autenticação dos outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando eu desinstalo um Agente de Autenticação de Passagem?

Se você desinstalar um Agente de Autenticação de Passagem de um servidor, ele faz o servidor parar de aceitar solicitações de entrada. Para evitar desativar o recurso de entrada do usuário em seu locatário, tenha outro Agente de Autenticação em execução antes de desinstalar um Agente de Autenticação de Passagem.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Eu tenho um locatário mais antigo que originalmente foi configurada usando o AD FS.  Recentemente migrado para PTA, mas agora não está vendo nossas alterações UPN sincronizando com o Azure Active Directory.  Por que as nossas mudanças UPN não estão sendo sincronizadas?

R: nas seguintes circunstâncias, as alterações do UPN local não podem sincronizar se:

- Seu locatário do Azure Active Directory foi criado antes de 15 de junho de 2015
- Você inicialmente foi federado com seu locatário do Azure Active Directory usando o AD FS para autenticação
- Você alternou de ter usuários gerenciados para PTA como autenticação

Isso ocorre porque o comportamento padrão de locatários criados antes de 15 de junho de 2015 era para bloquear alterações UPN.  Se você precisar de alterações UPN de desbloqueio, você precisará executar o seguinte cmdlet do PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Locatários criados depois de 15 de junho de 2015 têm o comportamento padrão da sincronização de alterações UPN.   



## <a name="next-steps"></a>Próximas etapas
- [Limitações atuais](how-to-connect-pta-current-limitations.md): saiba quais cenários têm suporte e quais não têm.
- [Início rápido](how-to-connect-pta-quick-start.md): instale e execute a Autenticação de Passagem do Azure AD.
- [Migrar do AD FS para Autenticação de Passagem](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – um guia detalhado para migrar do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem.
- [Bloqueio Inteligente](../authentication/howto-password-smart-lockout.md): saiba como configurar a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Análise técnica aprofundada](how-to-connect-pta-how-it-works.md): entenda como funciona o recurso de Autenticação de passagem.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de Autenticação de Passagem.
- [Aprofundamento em segurança](how-to-connect-pta-security-deep-dive.md): obtenha informações técnicas avançadas sobre o recurso de Autenticação de passagem.
- [SSO contínuo do Azure AD](how-to-connect-sso.md): saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.

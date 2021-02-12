---
title: Federação direta com um provedor de identidade para B2B – Azure AD
description: Federar diretamente com um provedor de identidade SAML ou WS-Fed para que os convidados possam entrar nos seus aplicativos do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9afb5a078d5359ed236b44c0a6712985bf8c305
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257178"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federação direta com AD FS e provedores de terceiros para usuários convidados (versão prévia)

> [!NOTE]
>  A federação direta é uma versão prévia pública de um recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve como configurar a Federação direta com outra organização para colaboração B2B. Você pode configurar a Federação direta com qualquer organização cujo IdP (provedor de identidade) dê suporte ao protocolo SAML 2.0 ou WS-Fed.
Quando você configura a federação direta com o IdP de um parceiro, novos usuários convidados desse domínio podem usar as respectivas contas institucionais gerenciada pelo IdP para entrar no seu locatário do Azure AD e começar a colaborar com você. Não é necessário que o usuário convidado crie uma conta do Azure AD separada.
> [!NOTE]
> Os usuários convidados da federação direta precisam entrar usando um link que inclua o contexto do locatário (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>`, `https://portal.azure.com/<tenant id>` ou, no caso de um domínio padrão verificado, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). Links diretos para aplicativos e recursos também funcionam desde que incluam o contexto do locatário. No momento, os usuários convidados da federação direta não conseguem entrar usando pontos de extremidade comuns sem contexto do locatário. Por exemplo, usar `https://myapps.microsoft.com`, `https://portal.azure.com` ou `https://teams.microsoft.com` resultará em um erro.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando um usuário convidado é autenticado com a federação direta?
Após você configurar a federação direta com uma organização, todos os novos usuários convidados que você convidar serão autenticados usando a federação direta. É importante observar que a configuração da federação direta não altera o método de autenticação para usuários convidados que já resgataram um convite de você. Estes são alguns exemplos:
 - Se os usuários convidados já tiverem resgatado convites de você e você configurar posteriormente a federação direta com a organização deles, esses usuários convidados continuarão a usar o mesmo método de autenticação usado antes de você configurar a federação direta.
 - Se você configurar a federação direta com uma organização parceira e convidar usuários convidados e a organização parceira posteriormente mudar para o Azure AD, os usuários convidados que já tiverem resgatado convites continuarão a usar a federação direta, desde que a política de federação direta em seu locatário exista.
 - Se você excluir a federação direta com uma organização parceira, os usuários convidados que estiverem usando a federação direta não poderão entrar.

Em qualquer um desses cenários, você pode atualizar o método de autenticação de um usuário convidado excluindo a conta de usuário convidado do seu diretório e convidando-o novamente.

A federação direta está vinculada a namespaces de domínio, como contoso.com e fabrikam.com. Ao estabelecer uma configuração de Federação direta com AD FS ou um IdP de terceiros, as organizações associam um ou mais namespaces de domínio a esses IdPs. 

## <a name="end-user-experience"></a>Experiência do usuário final 
Com a federação direta, os usuários convidados usam as respectivas contas organizacionais para entrar em seu locatário do Azure AD. Quando eles acessam recursos compartilhados e recebem uma solicitação para entrar, cada um dos usuários de federação direta é redirecionado para o respectivo IdP. Após a entrada bem-sucedida, eles são retornados ao Azure AD para acessar recursos. Os tokens de atualização dos usuários de federação direta são válidos por 12 horas, o [comprimento padrão para o token de atualização de passagem](../develop/active-directory-configurable-token-lifetimes.md#exceptions) no Azure AD. Se o IdP federado tiver o SSO habilitado, o usuário vivenciará o SSO e não verá nenhum prompt de entrada após a autenticação inicial.

## <a name="limitations"></a>Limitações

### <a name="dns-verified-domains-in-azure-ad"></a>Domínios verificados por DNS no Azure AD
O domínio com o qual você deseja federar deve ***não** _ ser verificado pelo DNS no Azure AD. Você tem permissão para configurar a federação direta com locatários do Azure AD não gerenciados (com verificação por email ou "viral") porque eles não são verificados por DNS.

### <a name="authentication-url"></a>URL de autenticação
A federação direta só é permitida para políticas em que o domínio da URL de autenticação corresponde ao domínio de destino ou onde a URL de autenticação é um desses provedores de identidade permitidos (essa lista está sujeita a alterações):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Por exemplo, ao configurar a Federação direta para _ * fabrikam. com * *, a URL de autenticação `https://fabrikam.com/adfs` passará a validação. Um host no mesmo domínio também passará, por exemplo `https://sts.fabrikam.com/adfs`. No entanto, a URL de autenticação `https://fabrikamconglomerate.com/adfs` ou `https://fabrikam.com.uk/adfs` para o mesmo domínio não passará.

### <a name="signing-certificate-renewal"></a>Renovação de certificado de autenticação
Se você especificar a URL de metadados nas configurações do provedor de identidade, o Azure AD renovará automaticamente o certificado de autenticação quando ele expirar. No entanto, se a rotação do certificado for realizada por qualquer motivo antes da hora de expiração ou se você não fornecer uma URL de metadados, o Azure AD não poderá renová-lo. Nesse caso, você precisará atualizar o certificado de autenticação manualmente.

### <a name="limit-on-federation-relationships"></a>Limite em relações de federação
Atualmente, o limite máximo de 1.000 relações de federação é compatível. Esse limite inclui [federações internas](/powershell/module/msonline/set-msoldomainfederationsettings) e federações diretas.

### <a name="limit-on-multiple-domains"></a>Limite em vários domínios
Atualmente, a federação direta com vários domínios do mesmo locatário não é compatível.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Posso configurar a federação direta com um domínio para o qual existe um locatário não gerenciado (verificado por email)? 
Sim. Se o domínio não tiver sido verificado e o locatário não tiver passado por uma [tomada de controle do administrador](../enterprise-users/domains-admin-takeover.md), você poderá configurar a federação direta com esse domínio. Os locatários não gerenciados, ou verificados por email, são criados quando um usuário resgata um convite B2B ou executa uma inscrição por autoatendimento para o Azure AD usando um domínio que não existe atualmente. Você pode configurar a federação direta com esses domínios. Se você tentar configurar a federação direta com um domínio verificado por DNS, seja no portal do Azure ou por meio do PowerShell, você verá um erro.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se a federação direta e a autenticação de senha de uso único por email estiverem habilitadas, qual método terá precedência?
Quando a federação direta é estabelecida com uma organização parceira, ela tem precedência sobre a autenticação de senha de uso único por email para novos usuários convidados dessa organização. Se um usuário convidado resgatar um convite usando a autenticação de senha de uso único antes de configurar a federação direta, ele continuará a usar a autenticação de senha de uso único. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>A federação direta resolve problemas de entrada devido a uma locação parcialmente sincronizada?
Não, o recurso de [senha de uso único por email](one-time-passcode.md) deve ser usado neste cenário. Um "aluguel parcialmente sincronizado" refere-se a um locatário do Azure AD de parceiro no qual as identidades de usuário local não estão totalmente sincronizadas com a nuvem. Um convidado cuja identidade ainda não existir na nuvem, mas que tentar resgatar seu convite B2B, não conseguirá entrar. O recurso de senha de uso único permitiria que esse convidado entrasse. O recurso de federação direta aborda os cenários em que o convidado tem sua própria conta institucional gerenciada por IdP, mas não há nenhuma presença do Azure AD na organização.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Quando a Federação direta é configurada com uma organização, cada convidado precisa ser enviado e resgatar um convite individual?
A configuração da Federação direta não altera o método de autenticação para usuários convidados que já resgataram um convite de você. Você pode atualizar o método de autenticação de um usuário convidado excluindo a conta de usuário convidado do seu diretório e convidando-as novamente.
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Etapa 1: Configurar o provedor de identidade da organização parceira
Primeiro, sua organização parceira precisa configurar o respectivo provedor de identidade com as declarações e relações de confiança de terceira parte confiável necessárias. 

> [!NOTE]
> Para ilustrar como configurar um provedor de identidade para federação direta, usaremos o AD FS (Serviços de Federação do Active Directory) como um exemplo. Confira o artigo [Configurar a federação direta com o AD FS](direct-federation-adfs.md), que fornece exemplos de como configurar o AD FS como um provedor de identidade SAML 2.0 ou WS-Fed em preparação para a federação direta.

### <a name="saml-20-configuration"></a>Configuração do SAML 2.0

O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo SAML com os requisitos específicos listados abaixo. Para obter mais informações sobre como configurar uma relação de confiança entre seu provedor de identidade SAML e o Azure AD, confira [Usar um provedor de identidade SAML 2.0 (IdP) para logon único](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> O domínio de destino para a federação direta não pode ser verificado por DNS no Azure AD. O domínio da URL de autenticação precisa corresponder ao domínio de destino ou precisa ser o domínio de um provedor de identidade permitido. Confira a seção [Limitações](#limitations) para obter mais detalhes. 

#### <a name="required-saml-20-attributes-and-claims"></a>Atributos e declarações SAML 2.0 necessários
As tabelas a seguir mostram os requisitos para declarações e atributos específicos que devem ser configurados no provedor de identidade de terceiros. Para configurar a federação direta, os atributos a seguir precisam ser recebidos na resposta SAML 2.0 do provedor de identidade. Esses atributos podem ser configurados vinculando ao arquivo XML do serviço de token de segurança online ou inserindo-os manualmente.

Atributos necessários para a resposta SAML 2.0 do IdP:

|Atributo  |Valor  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do emissor do IdP do parceiro, por exemplo, `http://www.example.com/exk10l6w90DHM0yi...`         |


Declarações necessárias para o token SAML 2.0 emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configuração de WS-Fed 
O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo WS-Fed com alguns requisitos específicos, conforme listado abaixo. Atualmente, os dois provedores WS-Fed que foram testados quanto à compatibilidade com o Azure AD incluem o AD FS e o Shibboleth. Para obter mais informações sobre como estabelecer um objeto de confiança de terceira parte confiável entre o Azure AD e um provedor em conformidade com o WS-Fed, confira o "Documento de integração do STS usando protocolos WS", disponível nos [Documentos de compatibilidade do provedor de identidade do Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> O domínio de destino para a federação direta não pode ser verificado por DNS no Azure AD. O domínio da URL de autenticação precisa corresponder ao domínio de destino ou então ao domínio de um provedor de identidade permitido. Confira a seção [Limitações](#limitations) para obter mais detalhes. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Atributos e declarações WS-Fed necessários

As tabelas a seguir mostram os requisitos para declarações e atributos específicos que devem ser configurados no provedor de identidade WS-Fed de terceiros. Para configurar a federação direta, os atributos a seguir precisam ser recebidos na mensagem WS-Fed do provedor de identidade. Esses atributos podem ser configurados vinculando ao arquivo XML do serviço de token de segurança online ou inserindo-os manualmente.

Atributos necessários na mensagem WS-Fed do IdP:
 
|Atributo  |Valor  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do emissor do IdP do parceiro, por exemplo, `http://www.example.com/exk10l6w90DHM0yi...`         |

Declarações necessárias para o token WS-Fed emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Etapa 2: Configurar a federação direta no Azure AD 
Em seguida, você vai configurar a federação com o provedor de identidade configurado na etapa 1 no Azure AD. Você pode usar o portal do Azure AD ou então o PowerShell. Pode levar 5-10 minutos para a política de federação direta entrar em vigor. Durante esse tempo, não tente resgatar um convite para o domínio de federação direta. Os atributos a seguir são obrigatórios:
- URI do emissor do parceiro IdP
- Ponto de extremidade de autenticação passiva do parceiro IdP (apenas https é compatível)
- Certificado

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Como configurar a federação direta no portal do Azure AD

1. Vá para o [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Confira **Identidades Externas** > **Todos os provedores de identidade**.
3. Selecione e, em seguida, selecione **novo IDP SAML/WS-Enalimentado**.

    ![Captura de tela mostrando o botão para adicionar um novo IdP de SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Na página **Novo IdP de SAML/WS-Fed**, em **Protocolo de provedor de identidade**, selecione **SAML** ou **WS-Fed**.

    ![Captura de tela mostrando o botão de análise na página do IdP de SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Insira o nome de domínio da sua organização parceira, que será o nome de domínio de destino para a federação direta
6. Você pode carregar um arquivo de metadados para popular os detalhes de metadados. Se você optar por inserir metadados manualmente, insira as seguintes informações:
   - Nome de domínio do IdP do parceiro
   - ID da entidade do IdP do parceiro
   - Ponto de extremidade de solicitante passivo do IdP do parceiro
   - Certificado
   > [!NOTE]
   > A URL de metadados é opcional, no entanto, é altamente recomendável. Se você fornecer a URL de metadados, o Azure AD poderá renovar automaticamente o certificado de autenticação quando ele expirar. Se a rotação do certificado for realizada por qualquer motivo antes da hora de expiração ou se você não fornecer uma URL de metadados, o Azure AD não poderá renová-lo. Nesse caso, você precisará atualizar o certificado de autenticação manualmente.

7. Clique em **Salvar**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Como configurar a federação direta no Azure AD usando o PowerShell

1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD. (Se você precisa de etapas detalhadas, o início rápido para adicionar um usuário convidado inclui a seção [Instalar o módulo AzureADPreview mais recente](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Execute o comando a seguir: 
   ```powershell
   Connect-AzureAD
   ```
1. No prompt de entrada, entre com a conta de Administrador Global gerenciada. 
2. Execute os comandos a seguir, substituindo os valores do arquivo de metadados de federação. Para o servidor do AD FS e o Okta, o arquivo de federação é federationmetadata.xml, por exemplo: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Etapa 3: Testar a federação direta no Azure AD
Agora, teste a configuração da federação direta convidando um novo usuário convidado B2B. Para obter detalhes, confira [Adicionar usuários de colaboração B2B do Azure AD no portal do Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Como fazer para editar uma relação de federação direta?

1. Vá para o [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os provedores de identidade**
4. Em **Provedores de identidade SAML/WS-Fed**, selecione o provedor.
5. No painel de detalhes do provedor de identidade, atualize os valores.
6. Clique em **Salvar**.


## <a name="how-do-i-remove-direct-federation"></a>Como fazer a remoção da federação direta?
Você pode remover a configuração de federação direta. Se você fizer isso, os usuários convidados de federação direta que já tiverem resgatado os respectivos convites não conseguirão entrar. Mas você pode permitir a eles acesso a seus recursos novamente, excluindo-os do diretório e convidando-os novamente. Para remover a federação direta com um provedor de identidade no portal do Azure AD:

1. Vá para o [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os provedores de identidade**.
4. Selecione o provedor de identidade e, em seguida, selecione **Excluir**. 
5. Clique em **Sim** para confirmar a exclusão. 

Para remover a federação direta com um provedor de identidade usando o PowerShell:
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute o comando a seguir: 
   ```powershell
   Connect-AzureAD
   ```
3. No prompt de entrada, entre com a conta de Administrador Global gerenciada. 
4. Insira o seguinte comando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [experiência de resgate de convite](redemption-experience.md) quando usuários externos entram com vários provedores de identidade.

---
title: Configurar a Federação direta com um provedor de identidade para B2B-Azure Active Directory | Microsoft Docs
description: Federar diretamente com um provedor de identidade SAML ou WS-informativo para que os convidados possam entrar em seus aplicativos do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 113e178d39ec776b63a0b38c55035f3493586ea2
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233864"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federação direta com AD FS e provedores de terceiros para usuários convidados (visualização)
|     |
| --- |
| A Federação direta é um recurso de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como configurar a Federação direta com outra organização para colaboração B2B. Você pode configurar a Federação direta com qualquer organização cujo IdP (provedor de identidade) dê suporte ao protocolo SAML 2,0 ou WS-encaminhar.
Quando você configura a Federação direta com o IdP de um parceiro, novos usuários convidados desse domínio podem usar sua própria conta institucional gerenciada pelo IdP para entrar no seu locatário do Azure AD e começar a colaborar com você. Não é necessário que o usuário convidado crie uma conta do Azure AD separada.
> [!NOTE]
> Os usuários convidados de Federação direta devem entrar usando um link que inclui o contexto do locatário (por `https://myapps.microsoft.com/?tenantid=<tenant id>` exemplo `https://portal.azure.com/<tenant id>`, ou, ou no caso `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`de um domínio verificado). Links diretos para aplicativos e recursos também funcionam desde que incluam o contexto do locatário. Atualmente, os usuários de Federação direta não podem entrar usando pontos de extremidade comuns que não têm nenhum contexto de locatário. Por exemplo, usar `https://myapps.microsoft.com`, `https://portal.azure.com`, ou `https://teams.microsoft.com` resultará em um erro.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando um usuário convidado é autenticado com a Federação direta?
Depois de configurar a Federação direta com uma organização, todos os novos usuários convidados que você convidar serão autenticados usando a Federação direta. É importante observar que a configuração da Federação direta não altera o método de autenticação para usuários convidados que já resgataram um convite de você. Estes são alguns exemplos:
 - Se os usuários convidados já tiverem resgatado convites de você e você configurar posteriormente a Federação direta com sua organização, esses usuários convidados continuarão a usar o mesmo método de autenticação usado antes de configurar a Federação direta.
 - Se você configurar a Federação direta com uma organização parceira e convidar usuários convidados e a organização parceira posteriormente mudar para o Azure AD, os usuários convidados que já tiverem reconsiderado convites continuarão a usar a Federação direta, desde que o Direct a política de Federação no seu locatário existe.
 - Se você excluir a Federação direta com uma organização parceira, os usuários convidados que estiverem usando a Federação direta não poderão entrar.

Em qualquer um desses cenários, você pode atualizar o método de autenticação de um usuário convidado excluindo a conta de usuário convidado do seu diretório e convidando-as novamente.

A Federação direta está vinculada a namespaces de domínio, como contoso.com e fabrikam.com. Ao estabelecer uma configuração de Federação direta com AD FS ou um IdP de terceiros, as organizações associam um ou mais namespaces de domínio a esses IdPs. 

## <a name="end-user-experience"></a>Experiência do usuário final 
Com a Federação direta, os usuários convidados entram em seu locatário do Azure AD usando sua própria conta institucional. Quando eles acessam recursos compartilhados e são solicitados a entrar, os usuários de Federação direta são redirecionados para o IdP. Após a entrada bem-sucedida, eles são retornados ao Azure AD para acessar recursos. Os tokens de atualização dos usuários de Federação direta são válidos por 12 horas, o [comprimento padrão para o token de atualização de passagem](../develop/active-directory-configurable-token-lifetimes.md#exceptions) no Azure AD. Se o IdP federado tiver o SSO habilitado, o usuário experimentará o SSO e não verá nenhum prompt de entrada após a autenticação inicial.

## <a name="limitations"></a>Limitações

### <a name="dns-verified-domains-in-azure-ad"></a>Domínios verificados pelo DNS no Azure AD
A Federação direta é permitida somente para domínios que ***não*** são verificados pelo DNS no Azure AD. A Federação direta é permitida para locatários não gerenciados (por email ou "viral") do Azure AD porque eles não são verificados pelo DNS.
### <a name="authentication-url"></a>URL de autenticação
A Federação direta só é permitida para políticas em que o domínio da URL de autenticação corresponde ao domínio de destino ou onde a URL de autenticação é um desses provedores de identidade permitidos (essa lista está sujeita a alterações):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Por exemplo, ao configurar a Federação direta para **fabrikam.com**, a URL `https://fabrikam.com/adfs` de autenticação passará a validação. Um host no mesmo domínio também será aprovado, por exemplo `https://sts.fabrikam.com/adfs`. No entanto, a `https://fabrikamconglomerate.com/adfs` URL `https://fabrikam.com.uk/adfs` de autenticação ou o mesmo domínio não passará.

### <a name="signing-certificate-renewal"></a>Renovação do certificado de assinatura
Se você especificar a URL de metadados nas configurações do provedor de identidade, o Azure AD renovará automaticamente o certificado de autenticação quando ele expirar. No entanto, se o certificado for girado por qualquer motivo antes da hora de expiração ou se você não fornecer uma URL de metadados, o Azure AD não poderá renová-la. Nesse caso, você precisará atualizar o certificado de autenticação manualmente.

### <a name="limit-on-federation-relationships"></a>Limite em relações de Federação
Atualmente, há suporte para um máximo de 1.000 relações de Federação. Esse limite inclui [federações internas](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) e federações diretas.
## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Posso configurar a Federação direta com um domínio para o qual existe um locatário não gerenciado (verificado por email)? 
Sim. Se o domínio não tiver sido verificado e o locatário não passou por um [tomada de administrador](../users-groups-roles/domains-admin-takeover.md), você poderá configurar a Federação direta. Os locatários não gerenciados, ou verificados por email, são criados quando um usuário resgata um convite B2B ou executa uma inscrição de autoatendimento para o Azure AD usando um domínio que atualmente não existe. Você pode configurar a Federação direta com esses domínios. Se você tentar configurar a Federação direta com um domínio verificado pelo DNS, seja na portal do Azure ou por meio do PowerShell, você verá um erro.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se a Federação direta e a autenticação de senha de email único estiverem habilitadas, o método terá precedência?
Quando a Federação direta é estabelecida com uma organização parceira, ela tem precedência sobre a autenticação de senha de uso único de email para novos usuários convidados dessa organização. Se um usuário convidado resgatasse um convite usando a autenticação de senha única antes de configurar a Federação direta, ele continuará a usar a autenticação de senha única. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>A Federação direta resolve problemas de entrada devido a uma locação parcialmente sincronizada?
Não, o recurso de [senha de email único](one-time-passcode.md) deve ser usado neste cenário. Um "aluguel parcialmente sincronizado" refere-se a um locatário do Azure AD de parceiro no qual as identidades de usuário local não são totalmente sincronizadas com a nuvem. Um convidado cuja identidade ainda não existe na nuvem, mas que tenta resgatar seu convite B2B não conseguirá entrar. O recurso de senha de uso único permitiria que este convidado entrasse. O recurso de Federação direta aborda os cenários em que o convidado tem sua própria conta institucional gerenciada pelo IdP, mas a organização não tem nenhuma presença do Azure AD.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Etapa 1: Configurar o provedor de identidade da organização do parceiro
Primeiro, sua organização parceira precisa configurar seu provedor de identidade com as declarações necessárias e as relações de confiança de terceira parte confiável. 

> [!NOTE]
> Para ilustrar como configurar um provedor de identidade para Federação direta, usaremos Serviços de Federação do Active Directory (AD FS) (AD FS) como um exemplo. Consulte o artigo [Configurar a Federação direta com o AD FS](direct-federation-adfs.md), que fornece exemplos de como configurar AD FS como um provedor de identidade SAML 2,0 ou WS-enparativo em preparação para Federação direta.

### <a name="saml-20-configuration"></a>Configuração do SAML 2,0

O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo SAML com requisitos específicos listados abaixo. Para obter mais informações sobre como configurar uma relação de confiança entre seu provedor de identidade SAML e o Azure AD, consulte [usar um IDP (SAML 2,0 Identity Provider) para logon único](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Observe que o domínio de destino para a Federação direta não deve ser verificado pelo DNS no Azure AD. O domínio da URL de autenticação deve corresponder ao domínio de destino ou deve ser o domínio de um provedor de identidade permitido. Consulte a seção [limitações](#limitations) para obter detalhes. 

#### <a name="required-saml-20-attributes-and-claims"></a>Atributos e declarações SAML 2,0 necessários
As tabelas a seguir mostram os requisitos para atributos específicos e declarações que devem ser configuradas no provedor de identidade de terceiros. Para configurar a Federação direta, os atributos a seguir devem ser recebidos na resposta SAML 2,0 do provedor de identidade. Esses atributos podem ser configurados vinculando-se ao arquivo XML do serviço de token de segurança online ou inserindo-os manualmente.

Atributos necessários para a resposta SAML 2,0 do IdP:

|Atributo  |Valor  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Público-alvo     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do emissor do IdP do parceiro, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |


Declarações necessárias para o token 2,0 do SAML emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configuração do WS-alimentado 
O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo WS-Redirecionado com alguns requisitos específicos, conforme listado abaixo. Atualmente, os dois provedores de WS-alimentados foram testados quanto à compatibilidade com o Azure AD incluem AD FS e Shibboleth. Para obter mais informações sobre como estabelecer uma relação de confiança de terceira parte confiável entre um provedor compatível com WS-alimentado com o Azure AD, consulte o "documento de integração do STS usando protocolos WS", disponível nos [documentos de compatibilidade do provedor de identidade do Azure ad](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> O domínio de destino para a Federação direta não deve ser verificado pelo DNS no Azure AD. O domínio da URL de autenticação deve corresponder ao domínio de destino ou ao domínio de um provedor de identidade permitido. Consulte a seção [limitações](#limitations) para obter detalhes. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Atributos e declarações necessários para WS-Alimentated

As tabelas a seguir mostram os requisitos para atributos específicos e declarações que devem ser configuradas no provedor de identidade WS-enmissão de terceiros. Para configurar a Federação direta, os atributos a seguir devem ser recebidos na mensagem WS-enfileirada do provedor de identidade. Esses atributos podem ser configurados vinculando-se ao arquivo XML do serviço de token de segurança online ou inserindo-os manualmente.

Atributos necessários na mensagem WS-enfileirada do IdP:
 
|Atributo  |Valor  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Público-alvo     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do emissor do IdP do parceiro, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |

Declarações necessárias para o token de WS-reportado emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Etapa 2: Configurar a Federação direta no Azure AD 
Em seguida, você configurará a Federação com o provedor de identidade configurado na etapa 1 no Azure AD. Você pode usar o portal do Azure AD ou o PowerShell. Pode levar de 5-10 minutos para a política de Federação direta entrar em vigor. Durante esse tempo, não tente resgatar um convite para o domínio de Federação direta. Os atributos a seguir são obrigatórios:
- URI do emissor do parceiro IdP
- Ponto de extremidade de autenticação passiva do parceiro IdP (há suporte apenas para https)
- Certificado

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Para configurar a Federação direta no portal do Azure AD

1. Vá para o [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecione **provedores de identidade**e selecione **novo IDP SAML/WS-** enalimentado.

    ![Captura de tela mostrando o botão para adicionar um novo IdP SAML ou WS-enalimentado](media/direct-federation/new-saml-wsfed-idp.png)

4. Na **nova página IDP de SAML/WS-** enalimentada, em **protocolo do provedor de identidade**, selecione **SAML** ou **WS-** enalimentado.

    ![Captura de tela mostrando o botão de análise na página IdP SAML ou WS-enalimentado](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Insira o nome de domínio da sua organização parceira, que será o nome de domínio de destino para a Federação direta
6. Você pode carregar um arquivo de metadados para preencher os detalhes de metadados. Se você optar por inserir metadados manualmente, insira as seguintes informações:
   - Nome de domínio do parceiro IdP
   - ID da entidade do parceiro IdP
   - Ponto de extremidade do solicitante passivo do parceiro IdP
   - Certificado
   > [!NOTE]
   > A URL de metadados é opcional, no entanto, é altamente recomendável. Se você fornecer a URL de metadados, o Azure AD poderá renovar automaticamente o certificado de autenticação quando ele expirar. Se o certificado for girado por qualquer motivo antes da hora de expiração ou se você não fornecer uma URL de metadados, o Azure AD não poderá renová-la. Nesse caso, você precisará atualizar o certificado de autenticação manualmente.

7. Clique em **Salvar**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Para configurar a Federação direta no Azure AD usando o PowerShell

1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD. (Se você precisar de etapas detalhadas, o início rápido para adicionar um usuário convidado incluirá a seção [instalar o módulo AzureADPreview mais recente](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
1. No prompt de entrada, entre com a conta de Administrador Global gerenciada. 
2. Execute os comandos a seguir, substituindo os valores do arquivo de metadados de Federação. Para AD FS Server e Okta, o arquivo de Federação é FederationMetadata. xml, por exemplo `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`:. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Etapa 3: Testar a Federação direta no Azure AD
Agora, teste a configuração da Federação direta convidando um novo usuário convidado B2B. Para obter detalhes, consulte [Adicionar usuários de colaboração B2B do Azure AD no portal do Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Como fazer editar uma relação de Federação direta?

1. Vá para o [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecionar **provedores de identidade**
4. Em **provedores de identidade SAML/WS-** enalimentados, selecione o provedor.
5. No painel de detalhes do provedor de identidade, atualize os valores.
6. Clique em **Salvar**.


## <a name="how-do-i-remove-direct-federation"></a>Como fazer remover a Federação direta?
Você pode remover a configuração de Federação direta. Se você fizer isso, os usuários convidados de Federação direta que já tiverem resgatado seus convites não poderão entrar. Mas você pode conceder a eles acesso a seus recursos novamente, excluindo-os do diretório e convidando-os novamente. Para remover a Federação direta com um provedor de identidade no portal do AD do Azure:

1. Vá para o [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecione **provedores de identidade**.
4. Selecione o provedor de identidade e, em seguida, selecione **excluir**. 
5. Clique em **Sim** para confirmar a exclusão. 

Para remover a Federação direta com um provedor de identidade usando o PowerShell:
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
3. No prompt de entrada, entre com a conta de Administrador Global gerenciada. 
4. Digite o seguinte comando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

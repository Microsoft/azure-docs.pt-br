---
title: "Azure AD Connect: Usar um Provedor de Identidade SAML 2.0 para Logon Único | Microsoft Docs"
description: "Este tópico descreve o uso de um provedor de identidade em conformidade com SAML 2.0 para logon único."
services: active-directory
author: billmath
manager: femila
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 048697f87383662506fb851bb3ea510c2cddf043
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Usar um IdP (provedor de identidade) SAML 2.0 para logon único

Este tópico contém informações sobre como usar um provedor de identidade baseado em um perfil de SP-Lite em conformidade com SAML 2.0 como o provedor de identidade/STS (serviço de token de segurança) preferencial. Isso é útil quando você já tem um diretório de usuários e um repositório de senhas locais que podem ser acessados usando SAML 2.0. Esse diretório de usuários existente pode ser usado para fazer logon no Office 365 e em outros recursos protegidos pelo Azure AD. O perfil de SP-Lite compatível com SAML 2.0 baseia-se no padrão de identidade federada amplamente utilizado SAML (Security Assertion Markup Language) para fornecer uma estrutura de logon e troca de atributos.

>[!NOTE]
>Para obter uma lista de IdPs de terceiros que foram testados para uso com o Azure AD, consulte a [Lista de compatibilidade de federação do Azure AD](active-directory-aadconnect-federation-compatibility.md)

A Microsoft dá suporte a esta experiência de logon como a integração de um serviço de nuvem da Microsoft, como o Office 365, com seu IdP baseado em um perfil SAML 2.0 configurado corretamente. Provedores de identidade SAML 2.0 são produtos de terceiros e, portanto, a Microsoft não dá suporte às melhores práticas de implantação, configuração e solução de problemas relacionadas a eles. Depois de configurada corretamente, a integração com o provedor de identidade SAML 2.0 pode ser testada quanto à configuração correta usando a Ferramenta Analisador de Conectividade da Microsoft, que é descrita em mais detalhes abaixo. Para obter mais informações sobre seu provedor de identidade baseado em um perfil de SP-Lite compatível com SAML 2.0, solicite essas informações à organização que o forneceu.

>[!IMPORTANT]
>Somente um conjunto limitado de clientes está disponível neste cenário de logon com provedores de identidade SAML 2.0, entre os quais:

>- Clientes baseados na Web, como o Outlook Web Access e o SharePoint Online
- Clientes avançados de email que usam autenticação básica e um método de acesso ao Exchange com suporte, como IMAP, POP, Active Sync, MAPI etc. (o ponto de extremidade do Protocolo de Cliente Avançado precisa estar implantado), incluindo:
    - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (diversas versões do iOS)
    - Diversos dispositivos Google Android
    - Windows Phone 7, Windows Phone 7.8 e Windows Phone 8.0
    - Cliente de email do Windows 8 e Cliente de email do Windows 8.1
    - Cliente de email do Windows 10

Todos os outros clientes estão indisponíveis neste cenário de logon com seu provedor de identidade SAML 2.0. Por exemplo, o cliente de desktop do Lync 2010 não pode fazer logon no serviço com seu provedor de identidade SAML 2.0 configurado para logon único.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Requisitos de protocolo do SAML 2.0 no Azure AD
Este tópico contém os requisitos detalhados para o protocolo e para a formatação de mensagens que seu provedor de identidade SAML 2.0 precisa implementar para estabelecer uma federação com o Azure AD e habilitar o logon em um ou mais serviços em nuvem da Microsoft (como o Office 365). A terceira parte confiável do SAML 2.0 (SP-STS) para o serviço de nuvem da Microsoft neste cenário é o Azure AD.

É recomendável que as mensagens de saída de seu provedor de identidade SAML 2.0 sejam o mais semelhantes possível aos rastreamentos de exemplo fornecidos. Além disso, use os valores de atributo específicos dos metadados do Azure AD fornecidos sempre que possível. Quando estiver satisfeito com suas mensagens de saída, você pode fazer o teste com o Analisador de Conectividade da Microsoft, conforme descrito abaixo.

Os metadados do Azure AD podem ser baixados nesta URL: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](http://https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Para clientes na China que estão usando a instância do Office 365 específica da China, o seguinte ponto de extremidade de federação deverá ser usado: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Requisitos do protocolo SAML
Esta seção detalha como os pares de mensagens de solicitação e resposta são reunidos para ajudá-lo a formatar suas mensagens corretamente.

O Azure AD pode ser configurado para funcionar com provedores de identidade que usam o perfil SP Lite SAML 2.0 com alguns requisitos específicos, conforme listado abaixo. Usando as mensagens de solicitação e resposta SAML de exemplo em conjunto com os testes automáticos e manuais, você pode trabalhar para obter a interoperabilidade com o Azure AD.

## <a name="signature-block-requirements"></a>Requisitos do bloco de assinatura
Dentro da mensagem de resposta SAML, o nó de Assinatura contém informações sobre a assinatura digital da mensagem em si. O bloco de assinatura tem os seguintes requisitos:

1. O próprio nó de declaração precisa ser assinado
2.  O algoritmo RSA-sha1 precisa ser usado como o DigestMethod. Outros algoritmos de assinatura digital não são aceitos.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Você também pode assinar o documento XML. 
4.  O algoritmo de transformação deve corresponder aos valores no exemplo a seguir: `<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  O algoritmo SignatureMethod deve coincidir com o exemplo a seguir: `<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Associações com suporte
Associações são os parâmetros de comunicação relacionados ao transporte que são necessários. Os requisitos a seguir se aplicam às associações

1. HTTPS é o transporte obrigatório.
2.  O Azure AD exigirá o HTTP POST para envio do token durante o logon
3.  O Azure AD usará HTTP POST para a solicitação de autenticação para o provedor de identidade e REDIRECT para a mensagem de logoff para o provedor de identidade.

## <a name="required-attributes"></a>Atributos obrigatórios
Esta tabela mostra os requisitos para atributos específicos na mensagem SAML 2.0.
 
|Atributo|Descrição|
| ----- | ----- |
|NameID|O valor dessa declaração deve ser o mesmo que o ImmutableID do usuário do Azure AD. Ela pode ter até 64 caracteres alfanuméricos. Qualquer caractere seguro não HTML deve ser codificado, por exemplo, um caractere "+" é mostrado como ".2B".|
|IDPEmail|O nome UPN é listado na resposta SAML como um elemento com o nome IDPEmail. Este é o UPN (UserPrincipalName) do usuário no Azure AD/Office 365. O UPN tem o formato de endereço de email. Valor do UPN no Windows Office 365 (Azure Active Directory).|
|Emissor|Ele precisa ser um URI do provedor de identidade. Você não deve reutilizar o Emissor das mensagens de exemplo. Se você tiver vários domínios de nível superior em seus locatários do Azure AD, o Emissor deverá corresponder à configuração de URI especificada configurada por domínio.|

>[!IMPORTANT]
>Atualmente, o Azure AD dá suporte ao seguinte URI de Formato de NameID para SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>Mensagens de exemplo de solicitação e resposta SAML
Um par de mensagens de solicitação e resposta é mostrado para a troca de mensagens de logon.
Este é um exemplo de mensagem de solicitação que é enviada do Azure AD para um provedor de identidade SAML 2.0 de exemplo. O provedor de identidade SAML 2.0 de exemplo é o Serviços de Federação do Active Directory (AD FS), configurado para usar o protocolo SAML-P. Testes de interoperabilidade também foram concluídos com outros provedores de identidade SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Este é um exemplo de mensagem de resposta que é enviada do provedor de identidade em conformidade com SAML 2.0 de exemplo para o Azure AD/Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Configurar seu provedor de identidade em conformidade com SAML 2.0
Este tópico contém diretrizes de como configurar seu provedor de identidade SAML 2.0 para estabelecer uma federação com o Azure AD e habilitar o acesso de logon único para um ou mais serviços em nuvem da Microsoft (como o Office 365) usando o protocolo SAML 2.0. A terceira parte confiável SAML 2.0 para o serviço de nuvem da Microsoft neste cenário é o Azure AD.

## <a name="add-azure-ad-metadata"></a>Adicionar metadados do Azure AD
Seu provedor de identidade SAML 2.0 precisa aderir a informações sobre a terceira parte confiável do Azure AD. O Azure AD publica metadados em https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

É recomendável que você sempre importe os metadados mais recentes do Azure AD quando configurar seu provedor de identidade SAML 2.0. Observe que o Azure AD não lê metadados do provedor de identidade.

## <a name="add-azure-ad-as-a-relying-party"></a>Adicionar o Azure AD como uma terceira parte confiável
Você precisa habilitar a comunicação entre seu provedor de identidade SAML 2.0 e o Azure AD. Essa configuração dependerá de seu provedor de identidade específico e você deve consultar a documentação dele. Normalmente, você configuraria a ID da terceira parte confiável como o mesmo que o entityID dos metadados do Azure AD.

>[!NOTE]
>Verifique se o relógio no servidor de seu provedor de identidade SAML 2.0 está sincronizado com uma fonte de horário precisa. Um relógio com horário impreciso pode fazer com que os logons federados falhem.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalar o Windows PowerShell para fazer logon no provedor de identidade SAML 2.0
Após ter configurado seu provedor de identidade SAML 2.0 para uso com o logon do Azure AD, a próxima etapa é baixar e instalar o módulo do Azure Active Directory para o Windows PowerShell. Uma vez instalado, você usará os cmdlets para configurar seus domínios do Azure AD como domínios federados.

O módulo do Azure Active Directory para Windows PowerShell é um download para gerenciar os dados de sua organização no Azure AD. Esse módulo instala um conjunto de cmdlets no Windows PowerShell; você executa esses cmdlets para configurar o acesso de logon único ao Azure AD e, por sua vez, em todos os serviços de nuvem em que você se inscreveu. Para obter instruções sobre como baixar e instalar os cmdlets, consulte [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Configurar uma relação de confiança entre seu provedor de identidade SAML e o Azure AD
Antes de configurar a federação em um domínio do Azure AD, ele precisa ter um domínio personalizado configurado. Você não pode federar o domínio padrão fornecido pela Microsoft. O domínio padrão da Microsoft termina com "onmicrosoft.com".
Você executará uma série de cmdlets na interface de linha de comando do Windows PowerShell para adicionar ou converter domínios para logon único.

Cada domínio do Azure Active Directory que você quiser federar usando seu provedor de identidade SAML 2.0 deve ser adicionado como um domínio de logon único ou convertido para um domínio de logon único de um domínio padrão. Adicionar ou converter um domínio configura uma relação de confiança entre seu provedor de identidade SAML 2.0 e Azure AD.

O procedimento a seguir descreve a conversão de um domínio padrão existente em um domínio federado usando SP-Lite compatível com o SAML 2.0. Observe que seu domínio pode passar por uma interrupção que afetará usuários até duas horas após você executar esta etapa.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Configurar um domínio em seu diretório do Azure AD para federação


1. Conecte-se ao seu diretório do Azure AD como um administrador de locatário: Connect-MsolService.
2.  Configure seu domínio do Office 365 desejado para usar a federação com SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Você pode obter a cadeia de caracteres codificada em Base64 do certificado de autenticação em seu arquivo de metadados do IDP. Um exemplo desse local foi fornecido, mas ele pode diferir ligeiramente dependendo de sua implementação.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Para obter mais informações sobre "Set-MsolDomainAuthentication", consulte: [http://technet.microsoft.com/library/dn194112.aspx](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Você precisa executar “$ecpUrl = “https://WS2012R2-0.contoso.com/PAOS“” somente se configurar uma extensão ECP para seu provedor de identidade. Clientes do Exchange Online, exceto pelo aplicativo OWA (Outlook Web Application), dependem de ponto de extremidade ativa baseado em POST. Se o seu STS SAML 2.0 implementar um ponto de extremidade ativo semelhante à implementação de ECP do Shibboleth de um ponto de extremidade ativo, é possível que esses clientes avançados interajam com o serviço do Exchange Online.

Quando a federação estiver federada, você poderá voltar para "não federado" (ou "gerenciado"). No entanto, essa alteração leva até duas horas para ser concluída e requer que novas senhas aleatórias para logon na nuvem sejam atribuídas a cada usuário. Em alguns cenários, pode ser necessário voltar para a configuração "gerenciado" para redefinir um erro em suas configurações. Para obter mais informações sobre a conversão de domínio, consulte: [http://msdn.microsoft.com/library/windowsazure/dn194122.aspx](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Provisionar entidades de usuários para o Azure AD/Office 365
Antes que possa autenticar seus usuários no Office 365, você precisa provisionar o Azure AD com entidades de usuários que correspondam à declaração SAML 2.0. Se essas entidades de usuário não foram conhecidas pelo Azure AD com antecedência, elas não poderão ser usadas para logon federado. Tanto o Azure AD Connect quanto o Windows PowerShell pode ser usado para provisionar as entidades de usuário.

O Azure AD Connect pode ser usado para provisionar entidades para seus domínios em seu diretório do Azure AD no Active Directory local. Para obter informações mais detalhadas, consulte [Integrar seus diretórios locais com o Azure Active Directory](active-directory-aadconnect.md).

O Windows PowerShell também pode ser usado para automatizar a adição de novos usuários ao Azure AD e para sincronizar alterações no diretório local. Para usar os cmdlets do Windows PowerShell, você precisa baixar os [Módulos do Azure Active Directory](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Este procedimento mostra como adicionar um único usuário ao Azure AD.


1. Conectar-se ao seu diretório do Azure AD como um administrador de locatário: Connect-MsolService.
2.  Crie uma nova entidade de usuário: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Para obter mais informações sobre "New-MsolUser", consulte [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>O valor de "UserPrinciplName" deve corresponder ao valor que você enviará para "IDPEmail" em sua declaração de SAML 2.0 e o valor de "ImmutableID" deve corresponder ao valor enviado na sua declaração de "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Verificar o logon único com seu IDP SAML 2.0
Como administrador, antes de verificar e gerenciar o logon único (também chamado de federação de identidades), revise as informações e execute as etapas nos artigos a seguir para configurar o logon único com seu provedor de identidade baseado no SP-Lite compatível com SAML 2.0:


1.  Você revisou os requisitos de protocolo SAML 2.0 do Azure AD
2.  Você configurou seu provedor de identidade SAML 2.0
3.  Instalar o Windows PowerShell para logon único no provedor de identidade SAML 2.0
4.  Configure uma relação de confiança entre seu provedor de identidade SAML 2.0 e o Azure AD
5.  Provisione uma entidade de usuário de teste conhecida para o Azure Active Directory (Office 365) por meio do Windows PowerShell ou do Azure AD Connect.
6.  Configure a sincronização de diretório usando o [Azure AD Connect](active-directory-aadconnect.md).

Após configurar o logon único com seu provedor de identidade baseado em SP-Lite compatível com SAML 2.0, verifique se ele está funcionando corretamente.

>[!NOTE]
>Se você tiver convertido um domínio em vez de adicionar um, poderá levar até 24 horas para configurar o logon único.
Antes de verificar o logon único, termine de configurar a sincronização do Active Directory, sincronize seus diretórios e ative seus usuários sincronizados.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Use a ferramenta para verificar se o logon único foi configurado corretamente
Para verificar se o logon único foi configurado corretamente, você pode executar o procedimento a seguir para confirmar que você consegue entrar no serviço de nuvem com suas credenciais corporativas.

A Microsoft fornece uma ferramenta que você pode usar para testar seu provedor de identidade baseado em SAML 2.0. Antes de executar a ferramenta de teste, você precisa ter configurado um locatário do Azure AD para estabelecer a federação com seu provedor de identidade.

>[!NOTE]
>O Analisador de Conectividade requer o Internet Explorer 10 ou posterior.



1. Baixe o Analisador de Conectividade em [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Clique em Instalar Agora para começar a baixar e instalar a ferramenta.
3.  Selecione "Não é possível configurar a federação com o Office 365, o Azure ou outros serviços que usam o Azure Active Directory".
4.  Quando a ferramenta estiver baixada e em execução, você verá a janela Diagnóstico de Conectividade. A ferramenta orientará você ao longo do teste de sua conexão de federação.
5.  O Analisador de Conectividade abrirá seu IDP SAML 2.0 para você fazer logon; insira as credenciais da entidade de usuário que você está testando: ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  Na janela de entrada do teste de Federação, você deve inserir um nome de conta e uma senha para o locatário do Azure AD que está configurado para ser federado com seu provedor de identidade SAML 2.0. A ferramenta tentará entrar usando essas credenciais e os resultados detalhados dos testes realizados durante a tentativa de logon serão fornecidos como saída.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. Esta janela mostra o resultado de um teste com falha. Clicar em “Examinar resultados detalhados” mostrará informações sobre os resultados de cada teste executado. Você também pode salvar os resultados em disco para compartilhá-los.
 
>[!NOTE]
>O Analisador de Conectividade também testa a federação ativa usando os protocolos ECP/PAOS e baseados em WS*. Se não estiver usando esses protocolos, você poderá ignorar o erro a seguir: Testando o fluxo de entrada ativo usando o ponto de extremidade de federação ativa de seu provedor de identidade.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verifique manualmente se o logon único foi configurado corretamente
A verificação manual fornece etapas adicionais que você pode adotar para garantir que seu provedor de identidade SAML 2.0 esteja funcionando corretamente em diversos cenários.
Para confirmar que o logon único foi configurado corretamente, execute as etapas a seguir:


1. Em um computador ingressado no domínio, entre em seu serviço de nuvem usando o mesmo nome de logon que você usa para suas credenciais corporativas.
2.  Clique dentro da caixa de senha. Se o logon único estiver configurado, a caixa de senha estará sombreada e você verá a seguinte mensagem: "Agora você precisa fazer entrar em <your company>."
3.  Clique no link Entrar no <your company>. Se você conseguir entrar, isso significa que o logon único foi configurado.

## <a name="next-steps"></a>Próximas etapas


- [Gerenciamento e personalização dos Serviços de Federação do Active Directory (AD FS) com o Azure AD Connect](active-directory-aadconnect-federation-management.md)
- [Lista de compatibilidade de federação do AD do Azure](active-directory-aadconnect-federation-compatibility.md)
- [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)

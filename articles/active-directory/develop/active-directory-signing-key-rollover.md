---
title: "Substituição da chave de assinatura no Azure AD | Microsoft Docs"
description: "Este artigo descreve as práticas recomendadas de substituição de chave de assinatura para o Azure Active Directory"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: krassk
editor: 
ms.assetid: ed964056-0723-42fe-bb69-e57323b9407f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 228bb9058537af1e4eb38207c376c2eb86aee68c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Substituição de chave de assinatura no Azure Active Directory
Este tópico aborda o que você precisa saber sobre as chaves públicas que são usadas no Azure AD (Azure Active Directory) para assinar tokens de segurança. É importante observar que essas chaves são substituídas em intervalos periódicos e, em caso de emergência, podem ser substituídas imediatamente. Todos os aplicativos que usam o Azure AD devem ser capazes de manipular programaticamente o processo de substituição de chave ou estabelecer um processo de substituição manual periódica. Continue lendo para entender como funcionam as chaves, como avaliar o impacto de substituição no seu aplicativo e como atualizar seu aplicativo ou estabelecer um processo de substituição manual periódica para tratar a substituição de chave, se necessário.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Visão geral das chaves de assinatura no Azure AD
O Azure AD usa criptografia de chave pública criada nos padrões da indústria para estabelecer a confiança entre ela e os aplicativos que a utilizam. Em termos práticos, isso funciona da seguinte maneira: o Azure AD usa uma chave de assinatura que consiste em um par de chaves público e privado. Quando um usuário entra em um aplicativo que usa o Azure AD para autenticação, este cria um token de segurança que contém informações sobre o usuário. Esse token é assinado pelo Azure AD usando sua chave privada antes de ser enviado para o aplicativo. Para verificar se o token é válido e se realmente se origina do Azure AD, o aplicativo deverá validar a assinatura do token usando a chave pública exposta pelo Azure AD que está contida no [documento de descoberta do OpenID Connect](http://openid.net/specs/openid-connect-discovery-1_0.html) do locatário ou o [documento de metadados federados do locatário SAML/WS-Fed](active-directory-federation-metadata.md).

Para fins de segurança, a chave de assinatura do Azure AD é substituída periodicamente e, em caso de emergência, pode ser substituída imediatamente. Um aplicativo que se integra ao Azure AD deve estar preparado para lidar com um evento de substituição de chave, independentemente da frequência em que pode ocorrer. Se não tiver, e o aplicativo tentar usar uma chave expirada para verificar a assinatura em um token, a solicitação falhará.

Sempre há mais de uma chave pública válida disponível no documento de descoberta do OpenID Connect e no documento de metadados de federação. O aplicativo deve estar preparado para usar qualquer uma das chaves especificadas no documento, já que uma das chaves pode ser substituída em breve, a outra pode ser a sua substituta e assim por diante.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Como avaliar se o aplicativo será afetado e o que fazer com ele
Como o seu aplicativo lida com a substituição de chave depende de variáveis como o tipo de aplicativo ou que protocolo de identidade e biblioteca foi usado. As seções a seguir avaliam se os tipos mais comuns de aplicativos são afetados pela substituição de chave e fornecem diretrizes sobre como atualizar o aplicativo para dar suporte à substituição automática ou à atualização manual da chave.

* [Aplicativos cliente nativos que acessam recursos](#nativeclient)
* [Aplicativos/APIs Web que acessam recursos](#webclient)
* [Aplicativos/APIs Web que protegem recursos e criam usando os Serviços de Aplicativo do Azure](#appservices)
* [Aplicativos/APIs Web que protegem recursos usando .NET OWIN OpenID Connect, WS-Fed ou o middleware WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Aplicativos/APIs Web protegendo recursos usando .NET Core OpenID Connect ou o middleware JwtBearerAuthentication](#owincore)
* [Aplicativos/APIs Web que protegem recursos usando o módulo passport-azure-ad do Node.js](#passport)
* [Aplicativos/APIs Web que protegem recursos e que foram criados com o Visual Studio 2015 ou o Visual Studio 2017](#vs2015)
* [Aplicativos/APIs Web que protegem recursos e que foram criados com o Visual Studio 2013](#vs2013)
* [APIs Web que protegem recursos e que foram criados com o Visual Studio 2013](#vs2013_webapi)
* [Aplicativos Web que protegem recursos e que foram criados com o Visual Studio 2012](#vs2012)
* [Aplicativos Web que protegem recursos e que foram criados com o Visual Studio 2010, 2008 ou usando o Windows Identity Foundation](#vs2010)
* [Aplicativos/APIs Web que protegem recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte](#other)

Esta diretriz **não** é aplicável:

* Os aplicativos adicionados da Galeria de Aplicativos do Azure AD (incluindo os Personalizados) têm diretrizes separadas em relação às chaves de assinatura. [Mais informações.](../active-directory-sso-certs.md)
* Os aplicativos locais publicados por meio do proxy de aplicativo não precisam se preocupar com as chaves de assinatura.

### <a name="nativeclient"></a>Aplicativos cliente nativos que acessam recursos
Aplicativos que só acessam recursos (ou seja, Microsoft Graph, KeyVault, API do Outlook e outras APIs Microsoft), geralmente só obtêm um token e o passam para o proprietário do recurso. Já que eles não estão protegendo recursos, não inspecionam o token e, portanto, não é necessário garantir que ele esteja adequadamente assinado.

Os aplicativos cliente nativos, tanto da área de trabalho como móveis, entram nessa categoria e, portanto, não são afetados pela substituição.

### <a name="webclient"></a>Aplicativos/APIs Web que acessam recursos
Aplicativos que só acessam recursos (ou seja, Microsoft Graph, KeyVault, API do Outlook e outras APIs Microsoft), geralmente só obtêm um token e o passam para o proprietário do recurso. Já que eles não estão protegendo recursos, não inspecionam o token e, portanto, não é necessário garantir que ele esteja adequadamente assinado.

Os aplicativos Web e as APIs Web que estão usando o fluxo somente para aplicativo (credenciais de cliente/certificado de cliente), entram nessa categoria e, portanto, não são afetados pela substituição.

### <a name="appservices"></a>Aplicativos/APIs Web que protegem recursos e criam usando os Serviços de Aplicativo do Azure
A funcionalidade de Autenticação/Autorização (EasyAuth) dos Serviços de Aplicativo do Azure já tem a lógica necessária para tratar a substituição de chave automaticamente.

### <a name="owin"></a>Aplicativos/APIs Web que protegem recursos usando .NET OWIN OpenID Connect, WS-Fed ou o middleware WindowsAzureActiveDirectoryBearerAuthentication
Se seu aplicativo estiver usando o .NET OWIN OpenID Connect, WS-Fed ou o middleware WindowsAzureActiveDirectoryBearerAuthentication, ele já terá a lógica necessária para tratar a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo está usando qualquer um desses ao procurar qualquer um dos trechos no Startup.cs ou Startup.Auth.cs do seu aplicativo

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Aplicativos/APIs Web protegendo recursos usando .NET Core OpenID Connect ou o middleware JwtBearerAuthentication
Se seu aplicativo estiver usando o .NET Core OWIN OpenID Connect ou o middleware JwtBearerAuthentication, ele já terá a lógica necessária para tratar a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo está usando qualquer um desses ao procurar qualquer um dos trechos no Startup.cs ou Startup.Auth.cs do seu aplicativo

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="passport"></a>Aplicativos/APIs Web que protegem recursos usando o módulo passport-azure-ad do Node.js
Se seu aplicativo estiver usando o módulo passport-ad do Node.js, ele já terá a lógica necessária para tratar a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo tem passport-ad pesquisando o trecho a seguir no app.js do seu aplicativo

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplicativos/APIs Web que protegem recursos e que foram criados com o Visual Studio 2015 ou o Visual Studio 2017
Se o aplicativo tiver sido criado usando um modelo de aplicativo Web no Visual Studio 2015 ou no Visual Studio 2017 e você tiver selecionado **Contas Corporativas e de Estudante** no menu **Alterar Autenticação**, ele já terá a lógica necessária para manipular a substituição de chave automaticamente. Essa lógica, incorporada ao middleware OWIN OpenID Connect, recupera e armazena em cache as chaves de documento de descoberta OpenID Connect e as atualiza periodicamente.

Se você tiver adicionado a autenticação à sua solução manualmente, talvez seu aplicativo não tenha a lógica de substituição de chave necessária. Você precisará escrevê-la por conta própria ou seguir as etapas em [Aplicativos/APIs Web que usam quaisquer outras bibliotecas ou que implementam manualmente qualquer um dos protocolos com suporte](#other).

### <a name="vs2013"></a>Aplicativos/APIs Web que protegem recursos e que foram criados com o Visual Studio 2013
Se seu aplicativo tiver sido criado usando um modelo de aplicativo Web no Visual Studio 2013 e você selecionou **Contas Organizacionais** no menu **Alterar Autenticação**, ele já tem a lógica necessária para tratar da substituição de chave automaticamente. Essa lógica armazena o identificador exclusivo da sua organização e as informações de chave de autenticação em duas tabelas de banco de dados associadas ao projeto. Você pode encontrar a cadeia de conexão para o banco de dados no arquivo Web.config do projeto.

Se você tiver adicionado a autenticação à sua solução manualmente, talvez seu aplicativo não tenha a lógica de substituição de chave necessária. Você precisará escrevê-la por conta própria ou seguir as etapas em [Aplicativos/APIs Web que usam quaisquer outras bibliotecas ou que implementam manualmente qualquer um dos protocolos com suporte](#other).

As etapas a seguir o ajudarão a verificar se a lógica está funcionando corretamente em seu aplicativo.

1. No Visual Studio 2013, abra a solução e clique na guia **Gerenciador de Servidores** na janela à direita.
2. Expanda **Conexões de Dados**, **DefaultConnection** e **Tabelas**. Localize a tabela **IssuingAuthorityKeys**, clique com o botão direito do mouse e clique em **Mostrar Dados da Tabela**.
3. Na tabela **IssuingAuthorityKeys** , haverá pelo menos uma linha, que corresponde ao valor de impressão digital da chave. Exclua todas as linhas na tabela.
4. Clique com botão direito do mouse na tabela **Locatários** e clique em **Mostrar Dados da Tabela**.
5. Na tabela **Locatários** , haverá pelo menos uma linha, que corresponde a um identificador de locatário de diretório exclusivo. Exclua todas as linhas na tabela. Se você não excluir as linhas em ambas as tabelas **Locatários** e **IssuingAuthorityKeys**, obterá um erro no tempo de execução.
6. Compile e execute o aplicativo. Depois de entrar na sua conta, você poderá interromper o aplicativo.
7. Volte para o **Gerenciador de Servidores** e examine os valores das tabelas **IssuingAuthorityKeys** e **Locatários**. Você notará que eles foram repopulados automaticamente com as informações apropriadas do documento de metadados federados.

### <a name="vs2013"></a>APIs Web que protegem recursos e que foram criados com o Visual Studio 2013
Se você tiver criado um aplicativo de API Web usando um modelo de aplicativo de API Web no Visual Studio 2013 e selecionou **Contas Organizacionais** no menu **Alterar Autenticação**, já terá a lógica necessária no seu aplicativo.

Se você configurou a autenticação manualmente, siga as instruções abaixo para saber como configurar sua API Web para atualizar as informações de chave automaticamente.

O trecho de código a seguir demonstra como obter as últimas chaves de documento de metadados federados e usar o [Manipulador de Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar o token. O trecho de código pressupõe que você usará seu próprio mecanismo de cache para persistir a chave e validar futuros tokens do Azure AD, seja em um banco de dados, em um arquivo de configuração ou em outro lugar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Aplicativos Web que protegem recursos e que foram criados com o Visual Studio 2012
Se seu aplicativo foi criado no Visual Studio 2012, você provavelmente usou a Ferramenta de Identidade e Acesso para configurar seu aplicativo. Também é provável que você esteja usando o [VINR (registro de nome de emissor validador)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável por manter informações sobre os provedores de identidade confiável (Azure AD) e as chaves usadas para validar tokens emitidos por eles. O VINR também facilita a atualização automática das informações fundamentais armazenadas em um arquivo Web.config baixando o documento de metadados federados mais recente associado ao seu diretório, verificando se a configuração está desatualizada em relação ao documento mais recente e atualizando o aplicativo para usar a nova chave conforme necessário.

Se você criou seu aplicativo usando um dos exemplos de código ou documentação passo a passo fornecidos pela Microsoft, a lógica de substituição de chave já estará incluída no seu projeto. Você notará que o código a seguir já existe em seu projeto. Se seu aplicativo não tiver essa lógica, siga as etapas abaixo para adicioná-la e verificar se ela está funcionando corretamente.

1. No **Gerenciador de Soluções**, adicione uma referência ao assembly **System.IdentityModel** ao projeto apropriado.
2. Abra o arquivo **Global.asax.cs** e adicione o seguinte usando diretivas:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adicione o seguinte método ao arquivo **Global.asax.cs** :
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Invoque o método **RefreshValidationSettings()** no método **Application_Start()** em **Global.asax.cs** conforme mostrado abaixo:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Depois que você tiver seguido as etapas, o Web.config do seu aplicativo será atualizado com as últimas informações do documento de metadados federados, incluindo as últimas chaves. Essa atualização ocorrerá sempre que o pool de aplicativos for reciclado no IIS. Por padrão, o IIS é definido para reciclar aplicativos a cada 29 horas.

Siga as etapas abaixo para verificar se a lógica de substituição de chave está funcionando.

1. Após ter verificado que seu aplicativo está usando o código acima, abra o arquivo **Web.config** e navegue até o bloco **<issuerNameRegistry>** procurando especificamente as poucas linhas abaixo:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Na tabela **<add thumbprint=””>** , altere o valor de impressão digital, substituindo um caractere por um diferente. Salve o arquivo **Web.config** .
3. Crie o aplicativo e o execute. Se você consegue completar o processo de logon, seu aplicativo está atualizando a chave com êxito baixando as informações necessárias do documento de metadados federados do diretório. Se você estiver tendo problemas ao entrar, verifique se as alterações em seu aplicativo estão corretas lendo o tópico [Adicionando logon ao seu aplicativo Web usando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) ou baixando e inspecionando o seguinte exemplo de código: [Aplicativo de nuvem multilocatário do Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Os aplicativos Web que protegem recursos e que foram criados com o Visual Studio 2008 ou 2010 e o Windows Identity Foundation (WIF) v1.0 para .NET 3.5
Se você criou um aplicativo no WIF v 1.0, nenhum mecanismo foi fornecido para atualizar automaticamente a configuração do seu aplicativo e usar uma nova chave.

* *Maneira mais fácil* Use a ferramenta FedUtil incluída no SDK do WIF, que pode recuperar o documento de metadados mais recente e atualizar sua configuração.
* Atualize seu aplicativo para .NET 4.5, que inclui a versão mais recente do WIF localizado no namespace System. Você pode usar o [VINR (registro de nome de emissor validador)](https://msdn.microsoft.com/library/dn205067.aspx) para executar as atualizações automáticas da configuração do aplicativo.
* Execute uma substituição manual de acordo com as instruções ao final deste documento de diretrizes.

Instruções para usar o FedUtil para atualizar sua configuração:

1. Verifique se você tem o SDK do WIF v 1.0 instalado na máquina de desenvolvimento para o Visual Studio 2008 ou 2010. Você pode [baixá-lo daqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se ainda não estiver instalado.
2. No Visual Studio, abra a solução, clique com o botão direito do mouse no projeto aplicável e selecione **Atualizar metadados federados**. Se essa opção não estiver disponível, o FedUtil e/ou o WIF v 1.0 SDK não foram instalados.
3. No aviso, selecione **Atualizar** para começar a atualizar os metadados federados. Se você tiver acesso ao ambiente de servidor onde o aplicativo está hospedado, você pode opcionalmente usar o [agendador de atualização automática de metadados](https://msdn.microsoft.com/library/ee517272.aspx)do FedUtil.
4. Clique em **Concluir** para concluir o processo de atualização.

### <a name="other"></a>Aplicativos/APIs Web que protegem recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte
Se você estiver usando alguma outra biblioteca ou se tiver implementado manualmente qualquer um dos protocolos com suporte, precisará examinar a biblioteca ou a implementação para garantir que a chave esteja sendo recuperada do documento de descoberta OpenID Connect ou do documento de metadados de federação. Uma maneira de verificar isso é fazer uma pesquisa no seu código ou no código da biblioteca de quaisquer chamadas para o documento de descoberta OpenID ou para o documento de metadados de federação.

Se a chave estiver sendo armazenada em algum lugar ou codificada em seu aplicativo, você poderá recuperar manualmente a chave e atualizá-la de forma adequada ao executar uma substituição manual de acordo com as instruções ao final deste documento de diretrizes. **É altamente incentivado que você aprimore seu aplicativo para dar suporte à substituição automática** usando qualquer um dos tópicos de abordagens neste artigo para evitar transtornos futuros e sobrecarga caso o Azure AD aumente a cadência de sua substituição ou tenha uma substituição fora de banda de emergência.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como testar o aplicativo para determinar se ele será afetado
Você pode validar se o aplicativo oferece suporte à substituição automática da chave baixando os scripts e seguindo as instruções [neste repositório GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Como executar uma substituição manual se seu aplicativo não oferecer suporte à substituição automática
Se seu aplicativo **não** der suporte à substituição automática, será necessário estabelecer um processo que monitore periodicamente as chaves de assinatura do Azure AD e execute uma substituição manual de forma adequada. [Este repositório GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e instruções sobre como fazer isso.


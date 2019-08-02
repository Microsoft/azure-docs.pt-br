---
title: Proteger seu serviço RESTful usando certificados do cliente no Azure Active Directory B2C | Microsoft Docs
description: Proteja suas trocas de declarações da API REST personalizadas no Azure AD B2C usando certificados do cliente
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1690adfe5336ea85328e16755c5e3bc82b6d240a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835621"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Proteger seu serviço RESTful usando certificados do cliente

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Em um artigo relacionado, [crie um serviço RESTful](active-directory-b2c-custom-rest-api-netfw.md) que interage com o Azure Active Directory B2C (Azure AD B2C).

Neste artigo, você aprenderá a restringir o acesso ao seu aplicativo Web do Azure (API RESTful) usando um certificado do cliente. Esse mecanismo é chamado de autenticação mútua de TLS ou *autenticação de certificado do cliente*. Somente os serviços que têm certificados adequados, como o Azure AD B2C, poderão acessar o serviço.

>[!NOTE]
>Também é possível proteger seu serviço RESTful usando a [autenticação básica HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). No entanto, a autenticação básica HTTP é considerada menos segura que um certificado do cliente. Nossa recomendação é proteger o serviço RESTful usando a autenticação de certificado do cliente conforme descrito neste artigo.

Este artigo detalha como:
* Configure seu aplicativo Web para usar a autenticação de certificado do cliente.
* Carregue o certificado para as chaves de política do Azure AD B2C.
* Configure sua política personalizada para usar o certificado do cliente.

## <a name="prerequisites"></a>Pré-requisitos
* Execute as etapas descritas no artigo [Integrar trocas de declarações da API REST](active-directory-b2c-custom-rest-api-netfw.md).
* Obtenha um certificado válido (um arquivo .pfx com uma chave privada).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Etapa 1: Configurar o aplicativo Web para autenticação de certificado do cliente
Para configurar o **Serviço de Aplicativo do Azure** para exigir certificados do cliente, defina a configuração do site `clientCertEnabled` do aplicativo Web como *true*. Para fazer esta mudança, no Portal do Azure, abra a página do aplicativo Web. Na navegação esquerda, em **Configurações** selecione **Configurações SSL**. No **certificados de cliente** seção, ative o **certificado de cliente de entrada** opção.

>[!NOTE]
>Verifique se seu plano de Serviço de Aplicativo do Azure é Standard ou superior. Para obter mais informações, consulte [Visão geral detalhada de planos de serviço de aplicativo do Azure](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Para obter mais informações sobre como definir a propriedade **clientCertEnabled**, consulte [Como configurar a autenticação mútua TLS para Aplicativo Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Etapa 2: Carregar seu certificado para as chaves de política do Azure AD B2C
Depois de definir `clientCertEnabled` como *true*, a comunicação com a API RESTful agora requer um certificado do cliente. Para obter, carregar e armazenar o certificado do cliente no locatário do Azure AD B2C, faça o seguinte:
1. No seu locatário do Azure AD B2C, selecione **Configurações de B2C** > **Estrutura de Experiência de Identidade**.

2. Para exibir as chaves disponíveis no seu locatário, selecione **Chaves de Política**.

3. Selecione **Adicionar**.
    A janela **Criar uma chave** é aberta.

4. Na caixa **Opções**, selecione **Carregar**.

5. Na caixa **Nome**, digite **B2cRestClientCertificate**.
    O prefixo *B2C_1A_* é adicionado automaticamente.

6. Na caixa **Carregar arquivo**, selecione o arquivo de certificado .pfx com uma chave privada.

7. Na caixa **Senha**, digite a senha do certificado.

    ![Carregar chave de política no criar uma página de chave no portal do Azure](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Selecione **Criar**.

8. Para exibir as chaves disponíveis no seu locatário e confirmar que você criou a chave `B2C_1A_B2cRestClientCertificate`, selecione **Chaves de política**.

## <a name="step-3-change-the-technical-profile"></a>Etapa 3: Alterar o perfil técnico
Para dar suporte à autenticação de certificado do cliente na sua política personalizada, altere o perfil técnico fazendo o seguinte:

1. No diretório de trabalho, abra o arquivo de política de extensão *TrustFrameworkExtensions.xml*.

2. Pesquise o nó `<TechnicalProfile>` que inclui `Id="REST-API-SignUp"`.

3. Localize o elemento `<Metadata>`.

4. Altere o *AuthenticationType* para *ClientCertificate* da seguinte maneira:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Adicione o seguinte snippet de código XML logo após fechar o elemento `<Metadata>`:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Depois de adicionar o snippet de código, seu perfil técnico será semelhante ao seguinte código XML:

    ![Defina os elementos XML de autenticação ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Etapa 4: Carregar a política ao seu locatário

1. No [Portal do Azure](https://portal.azure.com), mude para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e, em seguida, selecione **Azure AD B2C**.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Selecione **Todas as Políticas**.

4. Selecione **Carregar Política**.

5. Marque a caixa de seleção **Substituir a política caso ela exista**.

6. Carregue o arquivo *TrustFrameworkExtensions.xml* e verifique se a validação dele é aprovada.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Etapa 5: Testar a política personalizada usando a opção Executar Agora
1. Abra **Configurações do Azure AD B2C** e selecione **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >Executar Agora exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) carregada e selecione **Executar agora**.

3. Teste o processo digitando **Teste** na caixa **Nome**.
    O Azure AD B2C exibirá uma mensagem de erro na parte superior da janela.

    ![Caixa de texto do nome fornecido realçado e mostrado de erro de validação de entrada](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Na caixa **Nome**, digite um nome (diferente de "Teste").
    O Azure AD B2C inscreve o usuário e envia um número de fidelidade para o aplicativo. Observe o número neste exemplo JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Se você receber a mensagem de erro *O nome não é válido, forneça um nome válido*, isso significará que o Azure AD B2C chamou com êxito seu serviço RESTful quando enquanto ele apresentou o certificado do cliente. A próxima etapa é validar o certificado.

## <a name="step-6-add-certificate-validation"></a>Etapa 6: Adicionar validação do certificado
O certificado do cliente que o Azure AD B2C envia para seu serviço RESTful não realiza nenhuma validação na plataforma de Serviço de Aplicativo do Azure, exceto para verificar se o certificado existe. Validar o certificado é de responsabilidade do aplicativo Web.

Nesta seção, você adicionará o código ASP.NET que valida as propriedades do certificado para fins de autenticação.

> [!NOTE]
>Para obter mais informações sobre como configurar o Serviço de Aplicativo do Azure para autenticação de certificado do cliente, consulte [Como configurar a autenticação mútua TLS para Aplicativo Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Adicionar configurações de aplicativo ao arquivo web.config do projeto
No projeto do Visual Studio que você criou anteriormente, adicione as seguintes configurações de aplicativo ao arquivo *web.config* no elemento `appSettings`:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Substitua os valores **Nome da entidade**, **Nome do emissor** e **Impressão digital do certificado** pelos valores do certificado.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Adicionar a função IsValidClientCertificate
Abra o arquivo *Controllers\IdentityController.cs* e adicione a função a seguir à classe do controlador `Identity`:

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

No exemplo de código anterior, o certificado só será aceito como uma opção válida se todas as condições a seguir forem atendidas:
* O certificado não expirou e está ativo no momento atual no servidor.
* O nome `Subject` do certificado é igual ao valor da configuração de aplicativo `ClientCertificate:Subject`.
* O nome `Issuer` do certificado é igual ao valor da configuração de aplicativo `ClientCertificate:Issuer`.
* O `thumbprint` do certificado é igual ao valor da configuração de aplicativo `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>Dependendo da confidencialidade do seu serviço, pode ser necessário adicionar mais validações. Por exemplo, pode ser necessário testar se há cadeias confiáveis para uma autoridade raiz confiável, a validação do nome da organização emissora e assim por diante.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 Chamar a função IsValidClientCertificate
Abra o arquivo *Controllers\IdentityController.cs* e adicione o snippet de código a seguir no início da função `SignUp()`:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Depois de adicionar o snippet de código, seu controlador `Identity` se parecerá com seguinte código:

![Adicionar código de validação do certificado](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Etapa 7: Publicar o projeto no Azure e testá-lo
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Contoso.AADB2C.API** e selecione **Publicar**.

2. Repita a “Etapa 6” e teste novamente a política personalizada com a validação do certificado. Tente executar a política e verifique se tudo funciona depois de adicionar a validação.

3. No arquivo *web.config*, altere o valor de `ClientCertificate:Subject` para o **inválido**. Execute a política novamente e você verá uma mensagem de erro.

4. Altere o valor de volta para **válido** e verifique se a política consegue chamar sua API REST.

Se for preciso solucionar problemas nesta etapa, consulte [Coleta de logs usando o Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Baixar os arquivos e o código da política completos
* Depois de concluir o passo a passo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), recomendamos que você crie o cenário usando seus próprios arquivos de política personalizados. Fornecemos os [Arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert) como referência.
* É possível baixar o código completo em [Solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

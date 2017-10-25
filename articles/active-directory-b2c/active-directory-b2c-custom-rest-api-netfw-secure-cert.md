---
title: "Azure Active Directory B2C: proteja seus serviços RESTful usando certificados de cliente"
description: "Exemplo de como proteger suas trocas de declarações da API REST personalizadas no Azure AD B2C usando certificados do cliente"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C: proteja seus serviços RESTful usando certificados de cliente
Depois de [criar um serviço RESTful](active-directory-b2c-custom-rest-api-netfw.md) para interagir com o Azure AD B2C, mostraremos como restringir o acesso ao seu aplicativo Web do Azure (API RESTful) usando um certificado de cliente. Esse mecanismo é chamado de autenticação mútua de TLS ou **autenticação de certificado do cliente**.  Apenas serviços como o Azure AD B2C com o certificado apropriado poderão acessar seu serviço.

> [!NOTE]
>
>Também é possível proteger seu serviço RESTful [usando a autenticação básica HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). No entanto, você deve saber que a Autenticação Básica HTTP é considerada menos segura com relação a certificado do cliente. Nossa recomendação é proteger o serviço RESTful usando autenticação de certificado do cliente conforme descrito neste artigo.

Este artigo detalha como:
1. Saiba como configurar o aplicativo Web para usar a autenticação de certificado do cliente
1. Carregar o certificado para as Chaves de Política do Azure AD B2C
1. Configurar a política personalizada para usar o certificado do cliente

## <a name="prerequisites"></a>Pré-requisitos
* Conclua as etapas anteriores no artigo [Integrar trocas de declarações da API REST](active-directory-b2c-custom-rest-api-netfw.md)
* Você deve ter um certificado válido (arquivo .pfx com chave privada)

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>Etapa 1: Configurar o aplicativo Web para autenticação de certificado do cliente
Para configurar o **Serviço de Aplicativo do Azure** para exigir certificados do cliente, a configuração do site `clientCertEnabled` do aplicativo Web deve ser true. No momento, essa configuração não está disponível por meio da experiência de gerenciamento no portal do Azure e será necessário usar a API REST para essa alteração.

> [!NOTE]
>
>Verifique se seu plano de Serviço de Aplicativo do Azure é Standard ou superior. Para obter mais informações, consulte [Visão geral aprofundada de planos do Serviço de Aplicativo do Azure](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)


Use o [Azure Resource Explorer (Versão Prévia)](https://resources.azure.com) para inverter a propriedade clientCertEnabled para true. A captura de tela abaixo demonstra como definir o valor de clientCertEnabled por meio do Azure Resource Explorer ![Configurando clientCertEnabled por meio do Azure Resource Explorer](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>Para obter mais informações como definir o atributo clientCertEnabled, consulte: [Como configurar a autenticação mútua TLS para o aplicativo Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)


> [!TIP]
>
>Como alternativa, você pode usar a ferramenta [ARMClient](https://github.com/projectkudu/ARMClient) para facilitar a chamada à API REST.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Etapa 2: Carregar seu certificado para as chaves de política do Azure AD B2C
Depois de definir `clientCertEnabled` como `true`, a comunicação com a API RESTful agora requer um certificado do cliente. Para fazer isso, você precisa carregar e armazenar o certificado do cliente em seu locatário do Azure AD B2C.   
1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Upload**.
5.  Para o **Nome**, use `B2cRestClientCertificate`.  
    O prefixo `B2C_1A_` é adicionado automaticamente.
6.  No **Upload do arquivo**, selecione seu arquivo de certificado .pfx com chave privada. E forneça a **senha** do certificado.

    ![Carregar chave de política](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  Clique em **Criar**
8.  Para confirmar que você criou a chave `B2C_1A_B2cRestClientCertificate`, selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>Etapa 3: Altere `TechnicalProfile` para dar suporte à autenticação de certificado do cliente na sua política de extensão
1.  Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho.
2.  Localize o nó `<TechnicalProfile>` que é incluído com `Id="REST-API-SignUp"`
3.  Localize o elemento `<Metadata>`
4.  Altere `AuthenticationType` para `ClientCertificate`

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  Adicione o seguinte trecho XML imediatamente após o fechamento do elemento `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

Depois de adicionar trechos XML, seu `TechnicalProfile` deve se parecer com:

![Defina os elementos XML de autenticação ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Etapa 4: Carregar a política para o seu locatário

1.  No [Portal do Azure](https://portal.azure.com), mude para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e clique em **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Clique em **Todas as Políticas**.
4.  Selecione **Carregar Política**
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Etapa 5: Testar a política personalizada usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >
    >    A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. 
    >    Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.

3.  Tente digitar "Teste" no campo **Nome**; o B2C exibe a mensagem de erro na parte superior da página

    ![Testar sua API de identidade](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Tente digitar um nome (diferente de "teste") no campo **Nome**. O B2C inscreve o usuário e, em seguida, envia loyaltyNumber para seu aplicativo. Observe o número neste JWT no exemplo.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

   > [!NOTE]
   >Se você receber a mensagem de erro acima, isso significa que Azure AD B2C chamou com êxito seu serviço RESTful ao apresentar o certificado de cliente. A próxima etapa é validar o certificado.

## <a name="step-6-adding-certificate-validation"></a>Etapa 6: Adicionando validação de certificado
O certificado de cliente do Azure AD B2C envia para seu serviço RESTful não realiza nenhuma validação na plataforma de Aplicativos Web do Azure (exceto para verificar se o certificado existe). Validar o certificado é de responsabilidade do aplicativo Web. Veja o exemplo de código ASP.NET que valida as propriedades do certificado para fins de autenticação.

> [!NOTE]
>Para obter mais informações sobre como configurar o Aplicativo de Serviço do Azure para Autenticação de Certificado de Cliente, consulte: [Como para configurar a autenticação mútua TLS para Aplicativo Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1 Adicionar configurações do aplicativo ao arquivo web.config do projeto
Abra o projeto do Visual Studio que você criou anteriormente e adicione as seguintes configurações do aplicativo ao arquivo web.config no elemento `appSettings`

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Substitua os valores do certificado:
* Nome da entidade
* Nome do emissor
* Impressão digital do certificado

### <a name="62-add-isvalidclientcertificate-function"></a>6.2 Adicionar a função IsValidClientCertificate
Abra Controllers\IdentityController.cs e adicione a função a seguir à classe do controlador `Identity`. 

```C#
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

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
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
    
    // 3. Check issuer name of certificate
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

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
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

No **exemplo** anterior, só será possível aceitar o certificado como uma opção válida se todas as condições forem atendidas:
1. O certificado não expirou e está ativo no momento atual no servidor.
2. O nome do certificado `Subject` que tem o nome comum é igual ao valor de configuração de aplicativo `ClientCertificate:Subject`
3. O nome do certificado `Issuer` que tem o nome comum é igual ao valor de configuração de aplicativo `ClientCertificate:Issuer`
4. O `thumbprint` do certificado é igual ao valor de configuração de aplicativo `ClientCertificate:Thumbprint`

> [!IMPORTANT]
>
>Dependendo da sensibilidade de seu serviço, talvez seja necessário adicionar mais validações. Por exemplo: teste se há uma cadeia confiável para a Autoridade Raiz Confiável, validação de nome da organização emissora e assim por diante.

### <a name="63-add-isvalidclientcertificate-function"></a>6.3 Adicionar a função IsValidClientCertificate
Abra Controllers\IdentityController.cs no início da função `SignUp()` e adicione linhas de código a seguir. 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Depois de adicionar trechos de código, seu controlador `Identity` deve se parecer com:

![Adicionar código de validação do certificado](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>Etapa 7: Publicar no Azure e testar
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Contoso.AADB2C.API** e selecione **Publicar**.
2. Repita a etapa 6 e teste a política personalizada (novamente) com a validação do certificado. Tente executar a política e verifique se que tudo funciona depois de adicionar a validação
3. Agora, no arquivo web.config, altere o valor de `ClientCertificate:Subject` para o valor **inválido**. Executar a política novamente e você verá uma mensagem de erro.
4. Altere o valor de volta para o valor **válido** e verifique se a política consegue chamar sua API REST
5. Solucione problemas [coletando logs usando o Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Baixar os arquivos e código da política completa
* Recomendamos a criação de seu cenário usando seus próprios arquivos de política Personalizada após a conclusão das instruções passo a passo Introdução às políticas personalizadas, em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* Você pode baixar o código completo aqui [Amostra de solução do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)
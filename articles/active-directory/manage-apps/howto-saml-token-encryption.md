---
title: Criptografia de token SAML no Azure Active Directory
description: Saiba como configurar a criptografia de token SAML no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5329f33cc4cbaa3309450165e657fc829c828b
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709490"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Como configurar a criptografia de token SAML do Azure AD

> [!NOTE]
> A criptografia de token é um recurso premium do Azure Active Directory (Azure AD). Para saber mais sobre as edições, os recursos e os preços do Azure AD, confira [Preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

A criptografia de token SAML permite o uso de declarações SAML criptografadas com um aplicativo compatível. Quando configurado para um aplicativo, o Azure AD criptografará as declarações SAML que ele emite para esse aplicativo usando a chave pública obtida de um certificado armazenado no Azure AD. O aplicativo deve usar a chave privada correspondente para descriptografar o token antes que ele possa ser usado como evidência de autenticação para o usuário conectado.

Criptografar as declarações SAML entre o Azure AD e o aplicativo fornece uma garantia adicional de que o conteúdo do token não pode ser interceptado e os dados pessoais ou corporativos comprometidos.

Mesmo sem criptografia de tokens, os tokens SAML do Azure AD nunca são transmitidos na rede. O Azure AD requer que as trocas de solicitações/respostas de tokens ocorram em canais HTTPS/TLS criptografados para que as comunicações entre o IDP, o navegador e o aplicativo ocorram em links criptografados. Considere o valor da criptografia de tokens para sua situação em comparação com a sobrecarga de gerenciamento de certificados adicionais.   

Para configurar a criptografia de tokens, você precisará carregar um arquivo de certificado X.509 que contém a chave pública do objeto de aplicativo do Azure AD que representa o aplicativo. Para obter o certificado X.509, você poderá baixá-lo do próprio aplicativo ou obtê-lo do fornecedor do aplicativo nos casos em que o fornecedor do aplicativo fornece as chaves de criptografia ou nos casos em que o aplicativo espera que você forneça uma chave privada. Ele pode ser criado usando ferramentas de criptografia, sendo a parte da chave privada carregada no repositório de chaves do aplicativo e o certificado de chave pública correspondente carregado no Azure AD.

O Azure AD usa o AES-256 para criptografar os dados de declaração SAML.

## <a name="configure-saml-token-encryption"></a>Configurar a criptografia de token SAML

Para configurar a criptografia de token SAML, siga as etapas abaixo:

1. Obtenha um certificado de chave pública que corresponda a uma chave privada configurada no aplicativo.

    Crie um par de chaves assimétricas para usar na criptografia. Ou, se o aplicativo fornece uma chave pública a ser usada para criptografia, siga as instruções do aplicativo para baixar do certificado X.509.

    A chave pública deve ser armazenada em um arquivo de certificado X.509 no formato .cer.

    Se o aplicativo usa uma chave criada para sua instância, siga as instruções fornecidas pelo seu aplicativo para instalar a chave privada que o aplicativo usará para descriptografar os tokens do seu locatário do Azure AD.

1. Adicione o certificado à configuração do aplicativo no Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Configurar a criptografia de token no portal do Azure

Você pode adicionar o certificado público à sua configuração de aplicativo no portal do Azure.

1. Vá para o [Portal do Azure](https://portal.azure.com).

1. Acesse a folha **Azure Active Directory > Aplicativos empresariais** e selecione o aplicativo para o qual você deseja configurar a criptografia de token.

1. Na página do aplicativo, selecione **Criptografia de yoken**.

    ![Opção de criptografia de token no portal do Azure](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > A opção **Criptografia de Token** está disponível somente para aplicativos SAML que foram configurados a partir da folha **Aplicativos Empresariais** no portal do Azure, do aplicativo Galeria de Aplicativos ou de um aplicativo inexistente na galeria. Para outros aplicativos, esta opção de menu não está disponível. Para aplicativos registrados por meio da experiência de **Registros de aplicativo** no portal do Azure, você pode configurar a criptografia para tokens SAML usando o manifesto do aplicativo, o Microsoft Graph ou o PowerShell.

1. Na página **criptografia de token** , selecione **importar certificado** para importar o arquivo. cer que contém seu certificado X. 509 público.

    ![Importar o arquivo .cer que contém o certificado X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Depois que o certificado é importado e a chave privada configurada para uso no lado do aplicativo, ative a criptografia selecionando **...** ao lado do status da impressão digital e, em seguida, selecione **Ativar Criptografia de Tokens** nas opções no menu suspenso.

1. Selecione **Sim** para confirmar a ativação do certificado de criptografia de tokens.

1. Confirme se as declarações SAML emitidas para o aplicativo estão criptografadas.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Desativar a criptografia do token no portal do Azure

1. No portal do Azure, acesse **Azure Active Directory > Aplicativos Empresariais** e selecione o aplicativo com a criptografia de token SAML disponível.

1. Na página do aplicativo, selecione **Criptografia de Token**, encontre o certificado e, em seguida, selecione a opção **...** para exibir o menu suspenso.

1. Selecione **Desativar criptografia de tokens**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Configurar a criptografia de token SAML usando a API do Graph, o PowerShell ou o manifesto do aplicativo

Certificados de criptografia são armazenados no objeto de aplicativo no Azure AD com uma marca de uso `encrypt`. Você pode configurar vários certificados de criptografia e aquele que está ativo para criptografar tokens é identificado pelo atributo `tokenEncryptionKeyID`.

Você precisará do ID de objeto do aplicativo para configurar a criptografia de token usando a API do Microsoft Graph ou o PowerShell. Você pode encontrar esse valor programaticamente ou indo até a página **Propriedades** do aplicativo no portal do Azure e observando o valor **ID do Objeto**.

Ao configurar uma keyCredential usando o Graph, o PowerShell ou o manifesto do aplicativo, você deve gerar um GUID para usar o keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Configurar a criptografia de token usando o Microsoft Graph

1. Atualize as `keyCredentials` do aplicativo com um certificado X.509 para criptografia. O exemplo a seguir mostra como fazer isso.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identifique o certificado de criptografia que está ativo para criptografar tokens. O exemplo a seguir mostra como fazer isso.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Configurar a criptografia de token usando o PowerShell

1. Use o módulo mais recente do PowerShell do Azure AD para se conectar ao seu locatário.

1. Defina as configurações de criptografia de token usando o comando **[set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview&preserve-view=true)** .

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Leia as configurações de criptografia de token usando os comandos a seguir.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Configurar a criptografia de token usando o manifesto do aplicativo

1. No portal do Azure, acesse **Azure Active Directory > Registros de aplicativo**.

1. Selecione **Todos os aplicativos** no menu suspenso para exibir todos os aplicativos e, em seguida, selecione o aplicativo empresarial que você deseja configurar.

1. Na página do aplicativo, selecione **Manifesto** para editar o [manifesto do aplicativo](../develop/reference-app-manifest.md).

1. Defina o valor para o atributo `tokenEncryptionKeyId`.

    O exemplo a seguir mostra um manifesto de aplicativo configurado com dois certificados de criptografia, com o segundo selecionado como ativo usando o tokenEnryptionKeyId.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Próximas etapas

* Descubra [Como o Azure AD usa o protocolo SAML](../develop/active-directory-saml-protocol-reference.md)
* Conheça o formato, as características de segurança e o conteúdo de [Tokens SAML no Azure AD](../develop/reference-saml-tokens.md)
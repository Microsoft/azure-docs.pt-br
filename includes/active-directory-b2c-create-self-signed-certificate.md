---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095282"
---
Se você ainda não tiver um certificado, poderá usar um certificado autoassinado. Um certificado autoassinado é um certificado de segurança que não é assinado por uma autoridade de certificação (CA) e não fornece as garantias de segurança de um certificado assinado por uma autoridade de certificação. 

# <a name="windows"></a>[Windows](#tab/windows)

No Windows, use o cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) do PowerShell para gerar um certificado.

1. Execute esse comando do PowerShell para gerar um certificado autoassinado. Modifique o argumento `-Subject` conforme apropriado para o aplicativo e nome de locatário do Azure AD B2C. Você também pode ajustar a data `-NotAfter` para especificar uma expiração diferente para o certificado.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Abra **Gerenciar certificados de usuário** > **Usuário atual** > **Pessoal** > **Certificados** > *yourappname.yourtenant.onmicrosoft.com*.
1. Selecione o certificado e, em seguida, selecione **ação**  >  **todas as tarefas**  >  **Exportar**.
1. Selecione **Sim** > **Próximo** > **Sim, exportar a chave privada** > **Próximo** .
1. Aceite os padrões para **Exportar formato de arquivo**.
1. Forneça a senha do certificado.

Para Azure AD B2C aceitar a senha do arquivo. pfx, a senha deve ser criptografada com a opção TripleDES-SHA1 no utilitário de exportação do repositório de certificados do Windows, em oposição ao AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

No macOS, use o [Assistente de certificado](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) no acesso do conjunto de chaves para gerar um certificado.

1. Siga as instruções sobre como [criar certificados autoassinados no acesso ao conjunto de chaves no Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. No aplicativo acesso do conjunto de chaves no seu Mac, selecione o certificado que você criou.
1. Escolha **arquivo**  >  **exportar itens**.
1. Selecione um nome de arquivo para salvar o certificado. Por exemplo, **autoassinado-Certificate. p12**.
1. Para o **formato de arquivo**, selecione **troca de informações pessoais (. p12)**.
1. Clique em **Salvar**.
1. Insira uma **senha** e, em seguida, **Verifique** a senha.
1. Substitua a extensão de arquivo para `.pfx` . Por exemplo, **Self-signed-Certificate. pfx**.

---
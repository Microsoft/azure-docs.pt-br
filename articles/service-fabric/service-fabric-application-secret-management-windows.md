---
title: Configurar um certificado de criptografia em clusters do Windows
description: Saiba como configurar um certificado de criptografia e criptografar segredos em clusters Windows.
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82583294"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Configurar um certificado de criptografia e criptografar segredos em clusters Windows
Este artigo mostra como configurar um certificado de criptografia e criptografar segredos em clusters Windows. Para clusters Linux, consulte [Configurar um certificado de criptografia e criptografar segredos em clusters Linux.][secret-management-linux-specific-link]

O [Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como uma maneira de obter certificados instalados em clusters do Service Fabric no Azure. Se não estiver implantando no Azure, você não precisará usar o cofre de chaves para gerenciar segredos em aplicativos do Service Fabric. No entanto, o *uso* de segredos em um aplicativo é independente da plataforma de nuvem para permitir que os aplicativos sejam implantados em um cluster hospedado em qualquer lugar. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de codificação de dados
Um certificado de codificação de dados é usado estritamente para criptografia e descriptografia de [parâmetros][parameters-link] no arquivo Settings.xml de um serviço e nas [variáveis de ambiente][environment-variables-link] do ServiceManifest.xml do serviço. Ele não é usado para autenticação ou assinatura do texto codificado. O certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O uso da chave de certificado deve incluir a Codificação de Dados (10) e não deve incluir a Autenticação de Servidor ou de Cliente. 
  
  Por exemplo, ao criar um certificado autoassinado usando o PowerShell, o sinalizador `KeyUsage` deverá ser definido como `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado em seu cluster
Esse certificado deve ser instalado em cada nó no cluster. Veja [como criar um cluster usando o Azure Resource Manager][service-fabric-cluster-creation-via-arm] para obter instruções de instalação. 

## <a name="encrypt-application-secrets"></a>Criptografar segredos do aplicativo
O comando do PowerShell a seguir é usado para criptografar um segredo. Esse comando só criptografa o valor; ele **não** assina o texto da criptografia. Você deve usar o mesmo certificado de codificação instalado no seu cluster para produzir texto cifrado para valores do segredo:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A cadeia de caracteres codificada de base 64 resultante contém tanto o texto cifrado secreto como informações sobre o certificado usado para criptografá-lo.

## <a name="next-steps"></a>Próximas etapas
Saiba como [Especificar segredos criptografados em um aplicativo.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application

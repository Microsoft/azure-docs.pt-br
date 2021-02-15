---
title: Configurar um certificado de criptografia em clusters do Linux
description: Saiba como configurar um certificado de criptografia e criptografar segredos em clusters Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78969027"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Configurar um certificado de criptografia e criptografar segredos em clusters Linux
Este artigo mostra como configurar um certificado de criptografia e criptografar segredos em clusters Linux. Para clusters Windows, consulte [Configurar um certificado de criptografia e criptografar segredos em clusters Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de codificação de dados
Um certificado de codificação de dados é usado estritamente para criptografia e descriptografia de [parâmetros][parameters-link] no arquivo Settings.xml de um serviço e nas [variáveis de ambiente][environment-variables-link] do ServiceManifest.xml do serviço. Ele não é usado para autenticação ou assinatura do texto codificado. O certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada.
* O uso da chave de certificado deve incluir a Codificação de Dados (10) e não deve incluir a Autenticação de Servidor ou de Cliente.

  Por exemplo, os comandos a seguir podem ser usados para gerar o certificado necessário usando OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado em seu cluster
O certificado precisa ser instalado em cada nó no cluster em `/var/lib/sfcerts`. A conta de usuário na qual o serviço está em execução (sfuser por padrão) **deve ter acesso de leitura** ao certificado instalado (ou seja, `/var/lib/sfcerts/TestCert.pem` no exemplo atual).

## <a name="encrypt-secrets"></a>Criptografar segredos
O snippet a seguir pode ser usado para criptografar um segredo. Esse snippet só criptografa o valor; ele **não** assina o texto da codificação. **Você precisa usar** o mesmo certificado de codificação instalado no seu cluster para produzir texto codificado para valores do segredo.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
A cadeia de caracteres codificada de base 64 resultante que é gerada para encrypted.txt contém tanto o texto cifrado secreto como informações sobre o certificado usado para criptografá-lo. Você pode verificar sua validade descriptografando-a com OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Próximas etapas
Saiba como [Especificar segredos criptografados em um aplicativo.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application

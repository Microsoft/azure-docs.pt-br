---
title: Configurar um certificado de criptografia e criptografar segredos em clusters Linux do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar um certificado de criptografia e criptografar segredos em clusters Linux.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126980"
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
O trecho a seguir pode ser usado para criptografar um segredo. Esse trecho só criptografa o valor; ele **não** assina o texto da codificação. **Você precisa usar** o mesmo certificado de codificação instalado no seu cluster para produzir texto codificado para valores do segredo.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
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

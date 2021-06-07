---
title: Como configurar o OpenSSL para Linux
titleSuffix: Azure Cognitive Services
description: Aprenda a configurar o OpenSSL para Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577437"
---
# <a name="configure-openssl-for-linux"></a>Configurar o OpenSSL para Linux

Quando é usada qualquer versão do SDK de Fala anterior à 1.9.0, o [OpenSSL](https://www.openssl.org) é configurado dinamicamente para a versão do sistema host. Nas versões posteriores do SDK de Fala, o OpenSSL (versão [1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) é vinculado estaticamente à biblioteca principal do SDK de Fala.

Para garantir a conectividade, verifique se os certificados OpenSSL foram instalados no sistema. Execute um comando:
```bash
openssl version -d
```

A saída em sistemas baseados no Ubuntu/Debian deve ser:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Verifique se há um subdiretório `certs` em OPENSSLDIR. No exemplo acima, seria `/usr/lib/ssl/certs`.

* Se houver um `/usr/lib/ssl/certs` e ele contiver vários arquivos de certificado individuais (com a extensão `.crt` ou `.pem`), não haverá necessidade de realizar mais ações.

* Se o OPENSSLDIR for diferente de `/usr/lib/ssl` e/ou houver só um arquivo de pacote de certificado em vez de vários arquivos individuais, você precisará definir uma variável de ambiente SSL apropriada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- O OPENSSLDIR é `/opt/ssl`. Há um subdiretório `certs` com vários arquivos `.crt` ou `.pem`.
Defina a variável de ambiente `SSL_CERT_DIR` para apontar para `/opt/ssl/certs` antes de executar um programa que usa o SDK de Fala. Por exemplo:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR é `/etc/pki/tls` (como em sistemas baseados no RHEL/CentOS). Há um subdiretório `certs` com um arquivo de pacote de certificado, por exemplo `ca-bundle.crt`.
Defina a variável de ambiente `SSL_CERT_FILE` para apontar para esse arquivo antes de executar um programa que use o SDK de Fala. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Verificações de Revogação de Certificado
Quando você se conectar com o serviço de Fala, o SDK de Fala verificará se o certificado TLS usado pelo serviço de Fala não foi revogado. Para realizar essa verificação, o SDK de Fala precisará de acesso aos pontos de distribuição da CRL para as autoridades de certificação usadas pelo Azure. Encontre uma lista de possíveis locais de download da CRL [neste documento](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes). Se um certificado tiver sido revogado ou a CRL não puder ser baixada, o SDK de Fala anulará a conexão e criará o evento Cancelado.

Se a rede na qual o SDK de Fala está sendo usado estiver configurada de um modo que não permita acesso aos locais de download da CRL, a verificação da CRL poderá ser desabilitada ou definida para não falhar se a CRL não puder ser recuperada. Essa configuração é feita por meio do objeto de configuração usado para criar um objeto Reconhecedor.

Para continuar a conexão quando uma CRL não puder ser recuperada, defina a propriedade OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Quando ela for definida como "true", será feita uma tentativa de recuperar a CRL. Se a recuperação for bem-sucedida, o certificado será verificado quanto à revogação. Se a recuperação falhar, a conexão terá permissão para continuar.

Para desabilitar completamente as verificações de revogação de certificado, defina a propriedade OPENSSL_DISABLE_CRL_CHECK como "true".
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Também vale observar que algumas distribuições do Linux não têm uma variável de ambiente TMP ou TMPDIR definida. Isso fará com que o SDK de Fala sempre baixe a CRL (lista de certificados revogados), em vez de armazenar a CRL em cache no disco para reutilização até que ela expire. Para aprimorar o desempenho da conexão inicial, você pode [criar uma variável de ambiente chamada TMPDIR e defini-la como o caminho do diretório temporário escolhido.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sobre o SDK de Fala](speech-sdk.md)

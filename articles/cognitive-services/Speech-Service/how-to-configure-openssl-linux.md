---
title: Como configurar o OpenSSL para Linux
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o OpenSSL para Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683158"
---
# <a name="configure-openssl-for-linux"></a>Configurar o OpenSSL para Linux

Ao usar qualquer versão do SDK de fala anterior ao 1.9.0, o [OpenSSL](https://www.openssl.org) é configurado dinamicamente para a versão do sistema host. Em versões posteriores do SDK do Speech, o OpenSSL (versão [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) é vinculado estaticamente à biblioteca principal do SDK de fala.

Para garantir a conectividade, verifique se os certificados OpenSSL foram instalados no sistema. Execute um comando:
```bash
openssl version -d
```

A saída em sistemas baseados no Ubuntu/Debian deve ser:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Verifique se há `certs` subdiretório em OPENSSLDIR. No exemplo acima, seria `/usr/lib/ssl/certs` .

* Se houver `/usr/lib/ssl/certs` e ele contiver muitos arquivos de certificado individuais ( `.crt` com `.pem` extensão ou), não haverá necessidade de mais ações.

* Se OPENSSLDIR for algo mais que `/usr/lib/ssl` e/ou houver um único arquivo de pacote de certificado em vez de vários arquivos individuais, você precisará definir uma variável de ambiente SSL apropriada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- OPENSSLDIR é `/opt/ssl` . Há um `certs` subdiretório com muitos `.crt` arquivos ou `.pem` .
Defina a variável `SSL_CERT_DIR` de ambiente para apontar para `/opt/ssl/certs` antes de executar um programa que usa o SDK de fala. Por exemplo:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR é `/etc/pki/tls` (como em sistemas baseados em RHEL/CentOS). Há `certs` subdiretório com um arquivo de pacote de certificado, por exemplo `ca-bundle.crt` .
Defina a variável `SSL_CERT_FILE` de ambiente para apontar para esse arquivo antes de executar um programa que usa o SDK de fala. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Também vale a pena observar que algumas distribuições do Linux não têm uma variável de ambiente TMP ou TMPDIR definida. Isso fará com que o SDK de fala Baixe a CRL (lista de certificados revogados) toda vez, em vez de armazenar em cache a CRL em disco para reutilização até que ela expire. Para melhorar o desempenho de conexão inicial, você pode [criar uma variável de ambiente chamada TMPDIR e defini-la como o caminho do diretório temporário escolhido.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sobre o SDK de Fala](speech-sdk.md)

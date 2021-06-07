---
title: Configurar a autenticação de cliente de chamadas de entrada-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Saiba mais sobre as possíveis configurações de autenticação de cliente para o módulo de grade de eventos.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91290765"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurar a autenticação de cliente de chamadas de entrada

Este guia fornece exemplos das possíveis configurações de autenticação de cliente para o módulo de grade de eventos. O módulo de grade de eventos dá suporte a dois tipos de autenticação de cliente:

* Baseada em chave de SAS (assinatura de acesso compartilhado)
* Baseado em certificados

Consulte o guia de [segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Habilitar a autenticação de cliente baseada em certificado, não há certificados autoassinados

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Habilitar a autenticação de cliente baseada em certificado, permitir certificados autoassinados

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Defina a propriedade **inbound__clientAuth__clientCert__allowUnknownCA** como **true** somente em ambientes de teste como você normalmente pode usar certificados autoassinados. Para cargas de trabalho de produção, recomendamos que você defina essa propriedade como **false** e certificados de uma autoridade de certificação (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Habilitar a autenticação de cliente baseada em certificado e na chave SAS

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>A autenticação de cliente baseada em chave SAS permite que um módulo sem IoT Edge faça gerenciamento e operações de tempo de execução supondo que as portas de API estejam acessíveis fora da rede IoT Edge.

---
title: Configurar protocolos de API-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Saiba mais sobre as possíveis configurações de protocolo de um módulo de grade de eventos.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a9cf6088201ffeaed76d99a9b211e5bcd1ea139a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322573"
---
# <a name="configure-event-grid-api-protocols"></a>Configurar protocolos de API da grade de eventos

Este guia fornece exemplos das possíveis configurações de protocolo de um módulo de grade de eventos. O módulo de grade de eventos expõe a API para suas operações de gerenciamento e tempo de execução. A tabela a seguir captura os protocolos e as portas.

| Protocolo | Porta | Descrição |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Desativado por padrão. Útil somente durante o teste. Não é adequado para cargas de trabalho de produção.
| HTTPS | 4438 | Padrão

Consulte o guia de [segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Expor HTTPS para módulos IoT na mesma rede de borda

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Habilitar HTTPS para outros módulos IoT e cargas de trabalho não IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> A seção **PortBindings** permite mapear portas internas para portas do host do contêiner. Esse recurso possibilita acessar o módulo de grade de eventos de fora da rede de contêineres IoT Edge, se o dispositivo do IoT Edge estiver acessível publicamente.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Expor HTTP e HTTPS para módulos IoT na mesma rede de borda

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Habilitar HTTP e HTTPS para outros módulos IoT e cargas de trabalho não IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Por padrão, cada módulo IoT faz parte do tempo de execução de IoT Edge criado pela rede de ponte. Ele permite que diferentes módulos de IoT na mesma rede se comuniquem entre si. O **PortBindings** permite mapear uma porta interna de contêiner para o computador host, permitindo que qualquer pessoa possa acessar a porta do módulo de grade de eventos de fora.

>[!IMPORTANT]
> Embora as portas possam se tornar acessíveis fora da rede IoT Edge, a autenticação de cliente impõe quem realmente tem permissão para fazer chamadas no módulo.

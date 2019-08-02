---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712566"
---
O Fluentd é um coletor de dados de software livre para registro em log unificado. As configurações do `Fluentd` gerenciam a conexão do contêiner com um servidor [Fluentd](https://www.fluentd.org). O contêiner inclui um provedor de registro em log do Fluentd, que permite que seu contêiner grave logs e, opcionalmente, dados de métrica para um servidor do Fluentd.

A tabela a seguir descreve as definições de configuração com suporte sob o `Fluentd` seção.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Host` | string | O endereço IP ou o nome do host DNS do servidor Fluentd. |
| `Port` | Inteiro | A porta do servidor Fluentd.<br/> O valor padrão é 24224. |
| `HeartbeatMs` | Inteiro | O intervalo de pulsação, em milissegundos. Se nenhum tráfego de evento tiver sido enviado antes que esse intervalo expire, uma pulsação será enviada ao servidor Fluentd. O valor padrão é 60.000 milissegundos (1 minuto). |
| `SendBufferSize` | Inteiro | O espaço de buffer de rede, em bytes, alocado para operações de envio. O valor padrão é 32768 bytes (32 kilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Inteiro | O tempo de limite, em milissegundos, para estabelecer uma conexão SSL/TLS com o servidor do Fluentd. O valor padrão é 10.000 milissegundos (10 segundos).<br/> Se `UseTLS` for definido como false, esse valor é ignorado. |
| `UseTLS` | Boolean | Indica se o contêiner deve usar SSL / TLS para se comunicar com o servidor Fluentd. O valor padrão é false. |
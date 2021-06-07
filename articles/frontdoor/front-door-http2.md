---
title: Azure front door-suporte do HTTP2 | Microsoft Docs
description: Este artigo ajuda você a aprender sobre o suporte a HTTP/2 na porta frontal do Azure
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91448691"
---
# <a name="http2-support-in-azure-front-door"></a>Suporte a HTTP/2 na porta frontal do Azure

Atualmente, o suporte a HTTP/2 está ativo para todas as configurações de porta frontal do Azure. Nenhuma ação adicional dos clientes é necessária.

O HTTP/2 é uma revisão importante do HTTP/1.1 que fornece um desempenho na Web mais rápido, reduzindo o tempo de resposta. O HTTP/2 é feito mantendo os métodos de HTTP, códigos de status e semânticas conhecidos para melhorar a experiência do usuário. Embora o HTTP/2 seja projetado para funcionar com HTTP e HTTPS, muitos navegadores da Web do cliente dão suporte apenas a HTTP/2 pela TLS (segurança da camada de transporte).

> [!NOTE]
> O suporte ao protocolo HTTP/2 está disponível somente para solicitações de clientes à porta frontal. A comunicação da porta frontal com os back-ends no pool de back-end ocorre por HTTP/1.1. 

### <a name="http2-benefits"></a>Benefícios do HTTP/2

Os benefícios do HTTP/2 incluem:

*   **Multiplexação e simultaneidade**

    Usando o HTTP 1.1, fazer solicitações de recursos requer várias conexões TCP, sendo que cada conexão tem uma sobrecarga de desempenho associada a ela. O HTTP/2 permite que vários recursos sejam solicitados em uma única conexão TCP.

*   **Compactação de cabeçalho**

    Compactando cabeçalhos HTTP para recursos fornecidos, o tempo de transmissão é reduzido significativamente.

*   **Dependências de fluxo**

    Dependências de fluxo permitem que o cliente indique ao servidor quais recursos têm prioridade.


## <a name="http2-browser-support"></a>Suporte do navegador a HTTP/2

Todos os principais navegadores implementaram o suporte a HTTP/2 em suas versões atuais. Os navegadores sem suporte automaticamente retornam para HTTP/1.1.

|Navegador|Versão Mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o HTTP/2, visite os seguintes recursos:

- [Home page de especificação HTTP/2](https://http2.github.io/)
- [Perguntas Frequentes Oficiais do HTTP/2](https://http2.github.io/faq/)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

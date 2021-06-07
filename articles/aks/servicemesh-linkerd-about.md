---
title: Visão geral do Linkerd
description: Obtenha uma visão geral do Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593760"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Visão geral

O [Linkerd][linkerd] é uma malha de serviço leve e fácil de usar.

## <a name="architecture"></a>Arquitetura

O Linkerd fornece um plano de dados composto por UltraLight [Linkerd][linkerd-proxy] de proxy especializado. Esses proxies inteligentes controlam todo o tráfego de rede dentro e fora de seus aplicativos e cargas de trabalho em malha. Os proxies também expõem métricas por meio de pontos de extremidade de métricas do [Prometheus][prometheus] .

O plano de controle gerencia a configuração e a telemetria agregada por meio dos seguintes [componentes][linkerd-architecture]:

- **Controlador** -fornece a API que orienta a CLI e o painel do Linkerd. Fornece configuração para proxies.

- **Toque** – estabelecer inspeções em tempo real em solicitações e respostas.

- **Identidade** -fornece recursos de identidade e segurança que permitem a MTLS entre serviços.

- **Web** -fornece o painel do Linkerd.


O diagrama de arquitetura a seguir demonstra como os vários componentes dentro do plano de dados e do plano de controle interagem.


![Visão geral dos componentes e da arquitetura do Linkerd.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante entender e considerar as seguintes áreas ao avaliar Linkerd para suas cargas de trabalho:

- [Princípios de design](#design-principles)
- [Funcionalidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-principles"></a>Princípios de design

Os princípios de design a seguir [guiam][design-principles] o projeto Linkerd:

- **Mantenha a simplicidade** – deve ser fácil de usar e entender.

- **Minimize os requisitos de recursos** -imponha o mínimo de desempenho e custo de recursos.

- **Basta trabalhar** – não interrompa os aplicativos existentes e não exija configuração complexa.


### <a name="capabilities"></a>Funcionalidades

O Linkerd fornece o seguinte conjunto de recursos:

- **Malha** – opção de depuração interna

- **Gerenciamento de tráfego** – divisão, tempos limite, novas tentativas, entrada

- **Segurança** – criptografia (MTLS), certificados autogirados a cada 24 horas

- **Observação** : métricas de ouro, toque, rastreamento, perfis de serviço e métricas por rota, painel da Web com grafos de topologia, Prometheus, grafana


### <a name="scenarios"></a>Cenários

O Linkerd é adequado e sugerido para os seguintes cenários:

- Simples de usar com apenas o conjunto essencial de requisitos de capacidade

- Baixa latência, baixa sobrecarga, com foco na observação e gerenciamento de tráfego simples


## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode instalar o Linkerd no AKS (serviço kubernetes do Azure):

> [!div class="nextstepaction"]
> [Instalar o Linkerd no serviço kubernetes do Azure (AKS)][linkerd-install]

Você também pode explorar ainda mais os recursos e a arquitetura do Linkerd:

- [Recursos do Linkerd][linkerd-features]
- [Arquitetura do Linkerd][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md
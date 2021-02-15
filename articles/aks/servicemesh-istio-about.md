---
title: Visão geral do İSTİO
description: Obtenha uma visão geral do İSTİO
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593893"
---
# <a name="istio"></a>İSTİO

## <a name="overview"></a>Visão geral

O [İSTİO][istio] é uma malha de serviço completa em destaque, personalizável e extensível.

## <a name="architecture"></a>Arquitetura

O İSTİO fornece um plano de dados composto por sidecars baseados em [Envoy][envoy-proxy]. Esses proxies inteligentes controlam todo o tráfego de rede dentro e fora de seus aplicativos e cargas de trabalho em malha.

O plano de controle gerencia a configuração, a política e a telemetria por meio dos seguintes [componentes][what-is-istio]:

- **Mixer** -impõe políticas de uso e controle de acesso. Coleta telemetria dos proxies que são enviados para o [Prometheus][prometheus].

- **Piloto** -fornece a política/configuração de descoberta de serviço e gerenciamento de tráfego para os proxies.

- **Citadel** -fornece recursos de identidade e segurança que permitem a MTLS entre serviços.

- **Galé** -abstrai e fornece configuração aos componentes.

O diagrama de arquitetura a seguir demonstra como os vários componentes dentro do plano de dados e do plano de controle interagem.


![Visão geral dos componentes e da arquitetura do İSTİO.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante entender e considerar as seguintes áreas ao avaliar İSTİO para suas cargas de trabalho:

- [Metas de design](#design-goals)
- [Funcionalidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-goals"></a>Metas de design

As metas de design a seguir [guiam][design-goals] o projeto İSTİO:

- **Maximizar transparência** – permitir a adoção com a quantidade mínima de trabalho para obter o valor real do sistema.

- **Extensibilidade** -deve ser capaz de crescer e adaptar-se às necessidades em constante mudança.

- **Portabilidade** -execute facilmente em diferentes tipos de ambientes – nuvem, local.

- **Uniformidade de política** -consistência na definição de política em vários recursos.


### <a name="capabilities"></a>Funcionalidades

O İSTİO fornece o seguinte conjunto de recursos:

- **Malha** – gateways (vários clusters), máquinas virtuais (expansão de malha)

- **Gerenciamento de tráfego** – roteamento, divisão, tempos limite, separadores de circuito, repetições, entrada, saída

- **Política** – controle de acesso, limite de taxa, cota, adaptadores de política personalizada

- **Segurança** – autenticação (JWT), autorização, criptografia (MTLS), CA externa (cofre HashiCorp)

- **Observação** – métricas Golden, espelho, rastreamento, adaptadores personalizados, Prometheus, grafana

### <a name="scenarios"></a>Cenários

O İSTİO é adequado e sugerido para os seguintes cenários:

- Exigir extensibilidade e conjunto avançado de recursos

- Expansão de malha para incluir cargas de trabalho baseadas em VM

- Malha de serviço de vários clusters

## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode instalar o İSTİO no AKS (serviço kubernetes do Azure):

> [!div class="nextstepaction"]
> [Instalar Istio no AKS (Serviço de Kubernetes do Azure)][istio-install]

Você também pode explorar ainda mais conceitos de İSTİO e modelos de implantação adicionais:

- [Conceitos de İSTİO][what-is-istio]
- [Modelos de implantação do İSTİO][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md

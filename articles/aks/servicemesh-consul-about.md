---
title: Visão geral do Consul
description: Obter uma visão geral do Consul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83773996"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Visão geral

[Consul][consul] é uma solução de rede de serviço com reconhecimento de vários data centers para conectar e proteger serviços em plataformas de tempo de execução. [Connect][consul-features] é o componente que fornece recursos de malha de serviço.

## <a name="architecture"></a>Arquitetura

O Consul fornece um plano de dados composto por [sidecars][consul-sidecar] baseados em [Envoy][envoy-proxy] por padrão. O Consul tem uma arquitetura de proxy conectável. Esses proxies inteligentes controlam todo o tráfego de rede dentro e fora de seus aplicativos e cargas de trabalho em malha.

O plano de controle gerencia a configuração e a política por meio dos seguintes [componentes][consul-architecture]:

- **Servidor** - um agente do Consul executado no modo de Servidor que mantém o estado do cluster do Consul.

- **Cliente** - um agente do Consul executado no modo de Cliente leve. Cada nó de computação deve ter um agente Cliente em execução. Esse cliente negocia a configuração e a política entre as cargas de trabalho e a configuração do Consul. 

O diagrama de arquitetura a seguir demonstra como os vários componentes dentro do plano de dados e do plano de controle interagem.

![Visão geral dos componentes e da arquitetura do Consul.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante entender e considerar as seguintes áreas ao avaliar o Consul para suas cargas de trabalho:

- [Princípios do Consul](#consul-principles)
- [Funcionalidades](#capabilities)
- [Cenários](#scenarios)


### <a name="consul-principles"></a>Princípios do Consul

Os princípios a seguir [orientam][consul-principles] o projeto do Consul:

- **Controlado por API** - codifique todas as configurações e políticas.

- **Executar e conectar-se em qualquer lugar** - conecte cargas de trabalho em plataformas de tempo de execução (Kubernetes, VMs, computação sem servidor).

- **Estender e integrar**- conecte cargas de trabalho com segurança na infraestrutura.


### <a name="capabilities"></a>Funcionalidades

O Consul fornece os seguintes recursos:

- **Malha** – gateway (vários data centers), máquinas virtuais (fora dos nós de cluster), sincronização de serviço, opção de depuração incorporada

- **Proxies** – representante, proxy interno, conectável, proxy I4 disponível para cargas de trabalho do Windows

- **Gerenciamento de tráfego** – roteamento, divisão, resolução

- **Política** – intenções, ACLs

- **Segurança** – autorização, autenticação, criptografia, identidades baseadas em SPIFFE, CA externo (cofre), gerenciamento de certificados e rotação

- **Observabilidade** – métricas, painel de interface do usuário, prometheus, grafana


### <a name="scenarios"></a>Cenários

O Consul é adequado e sugerido para os seguintes cenários:

- Estender cargas de trabalho existentes conectadas pelo Consul

- Requisitos de conformidade em relação ao gerenciamento de certificados

- Malha de serviço de vários clusters

- Cargas de trabalho baseadas em VM a serem incluídas na malha de serviço



## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode instalar o Consul no Serviço de Kubernetes do Azure (AKS):

> [!div class="nextstepaction"]
> [Instalar o Consul no Serviço Kubernetes do Azure (AKS)][consul-install]

Você também pode explorar ainda mais os recursos e a arquitetura do Consul:

- [Tutoriais de introdução do Consul][consul-getting-started]
- [Recursos do Consul][consul-features]
- [Arquitetura do Consul][consul-architecture]
- [Consul - como funciona a conexão][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md

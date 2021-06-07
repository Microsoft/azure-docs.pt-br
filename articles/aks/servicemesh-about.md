---
title: Sobre malhas de serviço
description: Obtenha uma visão geral das malhas de serviço, sua arquitetura e seus recursos e quais critérios você deve considerar ao selecionar um para implantar.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77594304"
---
# <a name="about-service-meshes"></a>Sobre malhas de serviço

Uma malha de serviço fornece recursos como gerenciamento de tráfego, resiliência, política, segurança, identidade forte e capacidade de observação para suas cargas de trabalho. Seu aplicativo é dissociado desses recursos operacionais e a malha de serviço os move para fora da camada de aplicativo e para a camada de infraestrutura.

## <a name="scenarios"></a>Cenários

Estes são alguns dos cenários que podem ser habilitados para suas cargas de trabalho quando você usa uma malha de serviço:

- **Criptografar todo o tráfego no cluster** – habilite o TLS mútuo entre os serviços especificados no cluster. Isso pode ser estendido para entrada e saída no perímetro da rede. Fornece uma opção segura por padrão sem alterações necessárias para o código do aplicativo e a infraestrutura.

- **Canário e distribuições em fases** – especifique as condições para um subconjunto de tráfego a ser roteado para um conjunto de novos serviços no cluster. No teste bem-sucedido da versão do canário, remova o roteamento condicional e a fase aumentando gradualmente o% de todo o tráfego para o novo serviço. Eventualmente, todo o tráfego será direcionado para o novo serviço.

- **Gerenciamento e manipulação de tráfego** – crie uma política em um serviço que classificará o limite de todo o tráfego para uma versão de um serviço de uma origem específica. Ou uma política que aplica uma estratégia de repetição a classes de falhas entre os serviços especificados. Espelhe o tráfego ao vivo para novas versões de serviços durante uma migração ou para depurar problemas. Injetar falhas entre serviços em um ambiente de teste para testar a resiliência.

- **Observação** -tenha informações sobre como os serviços estão conectados ao tráfego que flui entre eles. Obtenha métricas, logs e rastreamentos para todo o tráfego no cluster e entrada/saída. Adicione recursos de rastreamento distribuído aos seus aplicativos.

## <a name="architecture"></a>Arquitetura

Uma malha de serviço normalmente é composta por um plano de controle e o plano de dados.

O **plano de controle** tem vários componentes que dão suporte ao gerenciamento da malha de serviço. Normalmente, isso incluirá uma interface de gerenciamento que pode ser uma interface do usuário ou uma API. Normalmente, também há componentes que gerenciam as definições de regra e política que definem como a malha de serviço deve implementar recursos específicos. Também há componentes que gerenciam aspectos de segurança, como identidade forte e certificados para mTLS. As malhas de serviço normalmente também terão uma métrica ou um componente de observação que coleta e agrega métricas e telemetria das cargas de trabalho.

O **plano de dados** normalmente consiste em um proxy que é injetado de forma transparente como um sidecar para suas cargas de trabalho. Esse proxy é configurado para controlar todo o tráfego de rede dentro e fora do pod que contém sua carga de trabalho. Isso permite que o proxy seja configurado para proteger o tráfego via mTLS, rotear o tráfego dinamicamente, aplicar políticas ao tráfego e coletar métricas e informações de rastreamento. 

![Arquitetura de malha de serviço típica](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Funcionalidades

Cada uma das malhas de serviço tem um ajuste natural e se concentra no suporte a cenários específicos, mas normalmente você descobrirá que a maioria irá implementar um número de, se não todos, dos seguintes recursos.

### <a name="traffic-management"></a>Gerenciamento de tráfego 

- **Protocolo** – camada 7 (http, grpc)
- **Roteamento dinâmico** – condicional, ponderação, espelhamento
- **Resiliência** – tempos limite, repetições, separadores de circuito
- **Política** – controle de acesso, limites de taxa, cotas
- **Teste** -injeção de falha

### <a name="security"></a>Segurança

- **Criptografia** – MTLS, gerenciamento de certificados, AC externa
- **Identidade forte** – SPIFFE ou semelhante
- **Auth** – autenticação, autorização

### <a name="observability"></a>Observabilidade

- **Métricas** – métricas de ouro, Prometheus, grafana
- **Rastreamento** -rastreamentos entre cargas de trabalho
- **Tráfego** – cluster, entrada/saída

### <a name="mesh"></a>Malha

- **Computação com suporte** -kubernetes, máquinas virtuais
- Gateways de **vários clusters** , Federação

## <a name="selection-criteria"></a>Critérios de seleção

Antes de selecionar uma malha de serviço, verifique se você entendeu seus requisitos e os motivos para instalar uma malha de serviço. Tente fazer as perguntas a seguir.

- **Um controlador de entrada é suficiente para minhas necessidades?** -Às vezes, ter um recurso como teste a/b ou divisão de tráfego na entrada é suficiente para dar suporte ao cenário necessário. Não adicione complexidade ao seu ambiente sem nenhuma vantagem.

- **Minhas cargas de trabalho e o ambiente podem tolerar sobrecargas adicionais?** -Todos os componentes adicionais necessários para dar suporte à malha de serviço exigem recursos adicionais, como CPU e memória. Além disso, todos os proxies e suas verificações de política associadas adicionam latência ao tráfego. Se você tiver cargas de trabalho muito sensíveis à latência ou não puder fornecer os recursos adicionais para cobrir os componentes de malha de serviço e, em seguida, considere novamente.

- **Isso está adicionando mais complexidade desnecessariamente?** -Se o motivo da instalação de uma malha de serviço é obter um recurso que não seja necessariamente crítico para as equipes de negócios ou operacionais, considere se a complexidade adicional de instalação, manutenção e configuração vale a pena.

- **Isso pode ser adotado em uma abordagem incremental?** -Algumas das malhas de serviço que fornecem muitos recursos podem ser adotadas em uma abordagem mais incremental. Instale apenas os componentes necessários para garantir seu sucesso. Quando você tiver mais confiança e recursos adicionais forem necessários, explore-os. Resista ao desejo de instalar *tudo* desde o início.

Se, após uma consideração cuidadosa, você decidir que precisa de uma malha de serviço para fornecer os recursos necessários, sua próxima decisão será qual é a *malha de serviço?*

Considere as seguintes áreas e quais delas estão mais alinhadas com seus requisitos. Isso vai orientá-lo em direção ao melhor ajuste para seu ambiente e cargas de trabalho. A seção [próximas etapas](#next-steps) levará você a obter informações detalhadas sobre malhas de serviço específicas e como elas são mapeadas para essas áreas.

- Gerenciamento de tráfego **técnico** , política, segurança, observação

- **Business** -suporte comercial, Fundação (CNCF), licença do OSS, governança

- **Operacional** – instalação/atualizações, requisitos de recursos, requisitos de desempenho, integrações (métricas, telemetria, painéis, ferramentas, SMI), cargas de trabalho mistas (pools de nós do Linux e do Windows), computação (kubernetes, máquinas virtuais), vários clusters

- **Segurança** -autenticação, identidade, gerenciamento de certificados e rotação, AC externa conectável


## <a name="next-steps"></a>Próximas etapas

A documentação a seguir fornece mais informações sobre as malhas de serviço que você pode experimentar no serviço de kubernetes do Azure (AKS):

> [!div class="nextstepaction"]
> [Saiba mais sobre o İSTİO...][istio-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre o Linkerd...][linkerd-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre o Consul...][consul-about]

Talvez você também queira explorar a interface de malha do serviço (SMI), uma interface padrão para malhas de serviço no kubernetes:

- [SMI (interface de malha de serviço)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md
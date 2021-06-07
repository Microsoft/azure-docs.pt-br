---
title: Visão geral do gateway auto-hospedado | Microsoft Docs
description: Saiba como o recurso de gateway auto-hospedado do gerenciamento de API do Azure ajuda as organizações a gerenciar APIs em ambientes híbridos e de nuvem.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99989095"
---
# <a name="self-hosted-gateway-overview"></a>Visão geral do gateway auto-hospedado

Este artigo explica como o recurso de gateway auto-hospedado do gerenciamento de API do Azure permite o gerenciamento de API híbrida e de várias nuvens, apresenta sua arquitetura de alto nível e realça seus recursos.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gerenciamento de API híbrida e de várias nuvens

O recurso de gateway auto-hospedado expande o suporte ao gerenciamento de API para ambientes híbridos e de várias nuvens e permite que as organizações gerenciem com eficiência e segurança as APIs hospedadas localmente e entre nuvens de um único serviço de gerenciamento de API no Azure.

Com o gateway auto-hospedado, os clientes têm a flexibilidade de implantar uma versão em contêiner do componente do gateway de gerenciamento de API nos mesmos ambientes em que hospedam suas APIs. Todos os gateways de hospedagem interna são gerenciados do serviço de gerenciamento de API com o qual são federados, fornecendo aos clientes a experiência de gerenciamento unificada e de visibilidade em todas as APIs internas e externas. Colocar os gateways próximos às APIs permite que os clientes otimizem fluxos de tráfego de API e atendam aos requisitos de segurança e conformidade.

Cada serviço de gerenciamento de API é composto pelos seguintes componentes principais:

-   Plano de gerenciamento, exposto como uma API, usado para configurar o serviço por meio do portal do Azure, do PowerShell e de outros mecanismos com suporte.
-   O gateway (ou o plano de dados) é responsável por solicitações de proxy de API, aplicação de políticas e coleta de telemetria
-   Portal do desenvolvedor usado por desenvolvedores para descobrir, aprender e carregar para usar as APIs

Por padrão, todos esses componentes são implantados no Azure, causando todo o tráfego da API (mostrado como setas pretas sólidas na imagem abaixo) para fluir pelo Azure, independentemente de onde os back-ends implementando as APIs estejam hospedados. A simplicidade operacional desse modelo é o custo de maior latência, problemas de conformidade e, em alguns casos, taxas de transferência de dados adicionais.

![Fluxo de tráfego de API sem gateways hospedados internamente](media/self-hosted-gateway-overview/without-gateways.png)

A implantação de gateways de hospedagem interna nos mesmos ambientes em que as implementações de API de back-end são hospedadas permite que o tráfego de API flua diretamente para as APIs de back-end, o que melhora a latência, otimiza os custos de transferência de dados e permite a conformidade, mantendo os benefícios de ter um único ponto de gerenciamento, a observação e a descoberta de todas as APIs na

![Fluxo de tráfego de API com gateways auto-hospedados](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Empacotamento e recursos

O gateway auto-hospedado é uma versão em contêiner e funcionalmente equivalente do gateway gerenciado implantado no Azure como parte de cada serviço de gerenciamento de API. O gateway auto-hospedado está disponível como um [contêiner](https://aka.ms/apim/sputnik/dhub) do Docker baseado em Linux do registro de contêiner da Microsoft. Ele pode ser implantado no Docker, kubernetes ou qualquer outra solução de orquestração de contêiner em execução em um cluster de servidor local, infraestrutura de nuvem ou para fins de avaliação e desenvolvimento em um computador pessoal.

A funcionalidade a seguir encontrada nos gateways gerenciados **não está disponível** nos gateways de hospedagem interna:

- Logs do Azure Monitor
- Gerenciamento de codificação e versão do TLS do upstream (lado de back-end)
- Validação de certificados de cliente e servidor usando [certificados raiz de CA](api-management-howto-ca-certificates.md) carregados no serviço de gerenciamento de API. Para obter mais informações, consulte [validação de certificado no gateway de hospedagem interna](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway).
- Integração com o [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- Retomada da sessão TLS
- Renegociação de certificado de cliente. Isso significa que para [autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) para clientes de API de trabalho devem apresentar seus certificados como parte do HANDSHAKE de TLS inicial. Para garantir isso, habilite a configuração negociar certificado de cliente ao configurar um nome de host personalizado de gateway auto-hospedado.
- Cache interno. Consulte este [documento](api-management-howto-cache-external.md) para saber mais sobre como usar o cache externo em gateways de hospedagem interna.

## <a name="connectivity-to-azure"></a>Conectividade com o Azure

Gateways auto-hospedados exigem conectividade TCP/IP de saída para o Azure na porta 443. Cada gateway auto-hospedado deve ser associado a um único serviço de gerenciamento de API e configurado por meio de seu plano de gerenciamento. O gateway auto-hospedado usa a conectividade com o Azure para:

-   Relatando seu status enviando mensagens de pulsação a cada minuto
-   Verificando regularmente (a cada 10 segundos) e aplicando atualizações de configuração sempre que elas estiverem disponíveis
-   Enviando métricas e logs de solicitação para Azure Monitor, se configurado para fazer isso
-   Enviando eventos para Application Insights, se definido como fazer isso

Quando a conectividade com o Azure for perdida, o gateway auto-hospedado não poderá receber atualizações de configuração, relatar seu status ou carregar a telemetria.

O gateway auto-hospedado é projetado para "falhar de forma estática" e pode sobreviver à perda temporária de conectividade com o Azure. Ele pode ser implantado com ou sem backup de configuração local. No primeiro caso, os gateways auto-hospedados salvarão regularmente uma cópia de backup da última configuração baixada em um volume persistente anexado a seu contêiner ou Pod.

Quando o backup de configuração é desativado e a conectividade com o Azure é interrompida:

-   A execução de gateways de hospedagem interna continuará a funcionar usando uma cópia na memória da configuração
-   Os gateways de hospedagem interna interrompida não poderão ser iniciados

Quando o backup de configuração é ativado e a conectividade com o Azure é interrompida:

-   A execução de gateways de hospedagem interna continuará a funcionar usando uma cópia na memória da configuração
-   Os gateways auto-hospedados interrompidos poderão começar a usar uma cópia de backup da configuração

Quando a conectividade for restaurada, cada gateway autohospedado afetado pela interrupção será reconectado automaticamente com seu serviço de gerenciamento de API associado e baixará todas as atualizações de configuração que ocorreram enquanto o gateway estava "offline".

## <a name="next-steps"></a>Próximas etapas

-   [Leia um White Paper para obter mais informações sobre este tópico](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Implantar o gateway auto-hospedado no Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Implantar o gateway auto-hospedado no kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)

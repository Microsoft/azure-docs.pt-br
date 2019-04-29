---
title: Eu uso os serviços móveis, como o serviço de aplicativo ajuda?
description: Saiba quais são as vantagens que o Serviço de Aplicativo traz para os projetos de serviços móveis existentes.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8a0bf1c39977d1659d398fce7c4bb3ae0024c88a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859912"
---
# <a name="getting-started"> </a>Eu uso os serviços móveis, como o serviço de aplicativo ajuda?
## <a name="overview"></a>Visão geral
Seu serviço móvel existente é seguro e permanecerá com suporte. No entanto, há vantagens que a plataforma de *Serviço de Aplicativo do Azure* oferece para seu aplicativo móvel que não estão disponíveis hoje com os Serviços Móveis:

* Oferta mais simples, mais fácil e mais econômica para aplicativos que incluem tanto Web quanto clientes móveis
* Novos recursos de host, incluindo Trabalhos da Web, CNames personalizados, melhor monitoramento
* Integração com o Gerenciador de Tráfego
* Conectividade com seus recursos locais e VPNs usando VNet, além de conexões híbridas
* Monitoramento, alerta e solução de problemas de seu aplicativo usando o AppInsights
* Espectro mais avançado dos recursos de computação subjacentes e preço
* Dimensionamento automático interno, balanceamento de carga e monitoramento de desempenho.
* Recursos de preparação integrada, backup, reversão e testes em produção

## <a name="new-hosting-features"></a>Novos recursos de hospedagem
Em *Serviço de Aplicativo do Azure*, o código de back-end do *Aplicativo Móvel* é executado no mesmo contêiner do Aplicativo Web e do Aplicativo de API. Você pode tirar proveito de todos os recursos nesse contêiner, incluindo alguns que não estão atualmente presentes nos Serviços Móveis:

* Adicionar lógica de back-end em execução contínua por meio de trabalhos da Web
* Assegurar que seu código de back-end esteja sempre em execução
* Usar CNames personalizado para fornecer nomes amigáveis e estáveis para seus pontos de extremidade móveis de back-end
* Realizar o geodimensionamento de seu aplicativo com o Gerenciador de Tráfego
* Inclua os pacotes e bibliotecas que desejar.
* (Para .NET) Utilize qualquer recurso do ASP.NET, inclusive MVC
* (Para Node.js) Utilize qualquer biblioteca JavaScript pura do ecossistema Node, incluindo bibliotecas comuns do MVC.

## <a name="access-on-premises-data-using-vnet"></a>Acessar dados locais usando VNet
Com os Serviços Móveis, hoje você já pode usar Conexões Híbridas para acessar os recursos locais. No entanto, há situações em que uma solução VPN é preferível. Com o *Serviço de Aplicativo do Azure*, é possível usar o Azure VNet para seu código de back-end de aplicativo móvel.

## <a name="use-your-favorite-backend-language"></a>Use sua linguagem de back-end favorita
*Serviço de Aplicativo do Azure* oferece um suporte mais amplo e avançado para as plataformas ASP.NET e Node.js, incluindo o acesso aos tempos de execução mais recentes.

## <a name="set-up-automatic-scale"></a>Configurar o dimensionamento automático
Com os serviços móveis, todas as instâncias do seu código de back-end estavam sendo executados em VMs pequenas. *Serviço de Aplicativo do Azure* permite que você selecione o tamanho das VMs de um conjunto muito mais rico de opções. Você também pode escalar vertical ou horizontalmente de forma rápida para lidar com qualquer carga de cliente de entrada, com base em várias métricas de desempenho.

## <a name="be-in-the-know"></a>Estar no "saber"
Reaja aos problemas em tempo real com monitoramento e alertas para notificar automaticamente você e sua equipe. Integre a análise avançada de aplicativos e monitoramento de funcionalidades a partir do AppInsights para obter uma compreensão sobre o desempenho do seu aplicativo móvel. Com o *Serviço de Aplicativo do Azure*, é possível configurar alertas com base em uma variedade de métricas de desempenho, tanto programaticamente quanto por meio do portal do Azure.

## <a name="keep-your-assets-safe"></a>Mantenha seus ativos seguros
Faça backup automático de seu back-end e banco de dados. O seu código e dados são protegidos contra desastres e facilmente restaurados, permitindo a você gerir seus negócios com confiança.

## <a name="ready-stage-go"></a>Preparar, apontar, fogo!
Com o *Serviço de Aplicativo do Azure*, agora é possível criar vários ambientes particulares de teste e preparo para seus aplicativos móveis. Use-os para executar testes antes de implantar. Alterne para a produção sem tempo de inatividade. Aplicativos Web são pré-carregados, garantindo a melhor experiência do cliente.

Você pode começar aproveitando o *Serviço de Aplicativo* de seu Serviço Móvel existente seguindo este [tutorial](app-service-mobile-migrating-from-mobile-services.md).

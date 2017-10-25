---
title: "Eu uso os serviços móveis, como o serviço de aplicativo ajuda?"
description: "Saiba quais são as vantagens que o Serviço de Aplicativo traz para os projetos de serviços móveis existentes."
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 22397b6b448b418d5b54a457c3bafaf5c68ecc7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started"> </a>Eu uso os serviços móveis, como o serviço de aplicativo ajuda?
## <a name="overview"></a>Visão geral
Seu serviço móvel existente é seguro e permanecerá com suporte. No entanto, há uma série de vantagens que a plataforma *Serviço de Aplicativo do Azure* oferece para seu aplicativo móvel que não estão disponíveis hoje com os serviços móveis:

* Oferta mais simples, mais fácil e mais econômica para aplicativos que incluem tanto Web quanto clientes móveis
* Novos recursos de host, incluindo Trabalhos da Web, CNames personalizados, melhor monitoramento
* Integração pronta para uso com o Gerenciador de Tráfego
* Conectividade com seus recursos locais e VPNs usando VNet, além de conexões híbridas
* Monitoramento, alerta e solução de problemas de seu aplicativo usando o NewRelic ou o AppInsights
* Espectro mais avançado dos recursos de computação subjacentes e preço
* Dimensionamento automático interno, balanceamento de carga e monitoramento de desempenho.
* Recursos de preparação integrada, backup, reversão e testes em produção

## <a name="new-hosting-features"></a>Novos recursos de hospedagem
Em *Serviço de Aplicativo do Azure*, o código de back-end *Aplicativo Móvel* é executado no mesmo contêiner como Aplicativo Web e Aplicativo de API. Desse modo você pode tirar proveito de todos os recursos nesse contêiner, incluindo alguns que não estão atualmente presentes nos serviços móveis:

* Adicionar lógica de back-end em execução contínua por meio de trabalhos da Web
* Assegurar que seu código de back-end esteja sempre em execução
* Usar CNames personalizado para fornecer nomes amigáveis e estáveis para seus pontos de extremidade móveis de back-end
* Realizar o geodimensionamento de seu aplicativo com o Gerenciador de Tráfego
* Inclua os pacotes e bibliotecas que desejar.
* (Para .NET) Utilizar qualquer recurso do ASP.NET, inclusive MVC
* (Para Node.js) Utilizar qualquer biblioteca JavaScript pura do ecossistema Node, incluindo bibliotecas comuns do MVC.

## <a name="access-on-premises-data-using-vnet"></a>Acessar dados locais usando VNet
Com os serviços móveis, hoje, você já pode usar conexões híbridas para acessar os recursos locais. No entanto, há situações em que uma solução VPN é preferível. Com o *Serviço de Aplicativo do Azure* você pode usar o Azure VNet para seu código de back-end de aplicativo móvel.

## <a name="use-your-favorite-backend-language"></a>Use sua linguagem de back-end favorita
*Serviço de Aplicativo do Azure* oferece um suporte mais amplo e avançado para as plataformas ASP.NET e Node.js, incluindo o acesso aos tempos de execução mais recentes.

## <a name="set-up-automatic-scale"></a>Configurar o dimensionamento automático
Com os serviços móveis, todas as instâncias do seu código de back-end estavam sendo executados em VMs pequenas. *Serviço de Aplicativo do Azure* permite que você selecione o tamanho das VMs de um conjunto muito mais rico de opções. Você também pode escalar vertical ou horizontalmente de forma rápida para lidar com qualquer carga de cliente de entrada, com base em várias métricas de desempenho.

## <a name="be-in-the-know"></a>Estar no "saber"
Reaja aos problemas em tempo real com monitoramento e alertas para notificar automaticamente você e sua equipe. Integre a análise avançada de aplicativos e monitoramento de funcionalidades a partir do New Relic e AppInsights para obter uma compreensão ainda melhor do desempenho do seu aplicativo móvel. Com o *Serviço de Aplicativo do Azure* , você pode configurar alertas com base em várias métricas de desempenho, tanto programaticamente quanto por meio do Portal do Azure.

## <a name="keep-your-assets-safe"></a>Mantenha seus ativos seguros
Faça backup automático de seu back-end e banco de dados. O seu código e dados são protegidos contra desastres e facilmente restaurados, permitindo a você gerir seus negócios com confiança.

## <a name="ready-stage-go"></a>Preparar, apontar, fogo!
Com o *Serviço de Aplicativo do Azure* , agora você pode criar vários ambientes particulares de teste e preparo para seus aplicativos móveis. Use-os para executar testes antes de implantar. Alterne para a produção sem tempo de inatividade. Aplicativos Web são pré-carregados, garantindo a melhor experiência do cliente.

Você pode começar aproveitando o *Serviço de Aplicativo* de seu Serviço Móvel existente seguindo este [tutorial](app-service-mobile-migrating-from-mobile-services.md).

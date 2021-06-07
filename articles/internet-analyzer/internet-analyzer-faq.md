---
title: Perguntas frequentes sobre o Internet Analyzer | Microsoft Docs
description: As perguntas frequentes sobre o Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "74184264"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Perguntas frequentes do Azure Internet Analyzer (versão prévia)

Estas são as perguntas frequentes para o Azure Internet Analyzer – se você tiver outras perguntas, vá para o [Fórum de comentários](https://aka.ms/internetAnalyzerFeedbackForum) e poste sua pergunta. Quando uma pergunta é frequente, a adicionamos a este artigo para que ela possa ser encontrada de forma rápida e fácil.

## <a name="how-do-i-participate-in-the-preview"></a>Como fazer participar da versão prévia?

A visualização está disponível para selecionar clientes. Se você estiver interessado em ingressar na versão prévia, faça o seguinte:

1. Entre no [portal do Azure](https://ms.portal.azure.com).
2. Navegue até a página **assinaturas** .
3. Clique na assinatura do Azure com a qual você planeja usar o analisador da Internet.
4. Vá para as configurações de **provedores de recursos** para a assinatura.
5. Procure **Microsoft. Network** e clique no botão **registrar** (ou **registrar novamente**).
![solicitação de acesso](./media/ia-faq/request-preview-access.png)

6. [Solicite a aprovação](https://aka.ms/internetAnalyzerContact) fornecendo seu endereço de email e a ID de assinatura do Azure que foi usada para fazer a solicitação de acesso.
7. Depois que sua solicitação tiver sido aprovada, você receberá uma confirmação por email e poderá criar/atualizar/modificar os recursos do Internet Analyzer da assinatura do Azure recém permitida.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>É necessário inserir o cliente para executar um teste?

Sim, o cliente do Internet Analyzer deve ser instalado em seu aplicativo para coletar métricas específicas para seus usuários. [Saiba como instalar o cliente.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Sou cobrado pela participação na versão prévia?
Não, o Azure Internet Analyzer é gratuito para uso na versão prévia. Não há nenhum contrato de nível de serviço durante a versão prévia.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Quais cenários o Internet Analyzer foi projetado para abordar?

O Internet Analyzer foi projetado para fornecer informações de desempenho de rede com base em sua população de usuário. Para ajudar a tomar as melhores decisões de desempenho para seus usuários, o Internet Analyzer compara o desempenho de dois pontos de extremidade de Internet usando sua população de usuário distinta. Embora o Analisador de Internet possa responder a várias perguntas, algumas das mais comuns são:

* Qual é o impacto da migração para a nuvem no desempenho? 
    * *Teste sugerido: Personalizado (sua infraestrutura local atual) versus Azure (qualquer ponto de extremidade pré-configurado)*
* Qual é o valor de colocar meus dados na borda versus em um data center? 
    *  *Teste sugerido: Azure versus Azure Front Door, Azure versus CDN do Azure da Microsoft*
* Qual é o benefício de desempenho do Azure Front Door?
    *  *Teste sugerido: Personalizado/Azure/CDN versus Azure Front Door*
* Qual é o benefício de desempenho da CDN do Azure da Microsoft? 
    *  *Teste sugerido: Personalizado/Azure/AFD versus CDN do Azure da Microsoft*
* Como a CDN do Azure da Microsoft cresce? 
    *  *Teste sugerido: Personalizado (outro ponto de extremidade de CDN) versus CDN do Azure da Microsoft*
* Qual é a melhor nuvem para sua população de usuários finais em cada região? 
    *  *Teste sugerido: Personalizado (outro serviço de nuvem) vs. Azure (qualquer ponto de extremidade pré-configurado)*

## <a name="which-tests-can-i-run-in-preview"></a>Quais testes posso executar na visualização?

Cada teste que você cria no Internet Analyzer é composto por dois pontos de extremidade, o ponto final e o ponto de extremidade B. Qualquer uma das seguintes combinações pode ser executada como testes:  
* Dois pontos de extremidade pré-configurados,
* Um ponto de extremidade personalizado e um preconfigurado, ou
* Dois [pontos de extremidades personalizados](internet-analyzer-custom-endpoint.md) (um ponto de extremidade personalizado deve residir no Azure).

Os seguintes pontos de extremidade pré-configurados estão disponíveis durante a visualização:
* **Regiões do Azure**
    * Sul do Brasil
    * Índia Central
    * Centro dos EUA
    * Leste da Ásia
    * Leste dos EUA
    * Oeste do Japão
    * Norte da Europa
    * Norte da África do Sul
    * Sudeste Asiático
    * Norte dos EAU
    * Oeste do Reino Unido  
    * Europa Ocidental
    * Oeste dos EUA
    * Oeste dos EUA 2
* **Várias combinações de regiões do Azure**
    * Leste dos EUA, Sul do Brasil
    * Leste dos EUA, Leste da Ásia
    * Oeste da Europa, Sul do Brasil
    * Oeste da Europa, Sudeste Asiático
    * Oeste da Europa, Norte dos EAU
    * Oeste dos EUA, Leste dos EUA
    * Oeste dos EUA, Oeste da Europa
    * Oeste dos EUA, Norte dos EAU
    * Oeste da Europa, Norte dos EAU, Sudeste Asiático
    * Oeste dos EUA, Oeste da Europa, Leste da Ásia
    * Oeste dos EUA, Norte da Europa, Sudeste Asiático, Norte dos EAU, Norte da África do Sul 
* **Azure + Azure Front Door**: implantação em qualquer combinação única ou múltipla de regiões do Azure listada acima
* **Azure + CDN do Azure do Microsoft**: implantação em qualquer combinação de regiões única do Azure listada acima
* **Azure + Gerenciador de Tráfego do Azure**: implantação em qualquer combinação de regiões múltiplas do Azure listada acima

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Como o Internet Analyzer é diferente dos outros serviços de monitoramento fornecidos pelo Azure?

O Internet Analyzer ajuda você a entender o desempenho de seus usuários finais e ajuda a tomar decisões para melhorar seu desempenho. Enquanto outras ferramentas de monitoramento do Azure fornecem informações sobre seus serviços do Azure, o Internet Analyzer se concentra na medição do desempenho de ponta a ponta da Internet para seus usuários.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Como os dados de medição são manipulados pelo Internet Analyzer?

O Azure tem [fortes processos de segurança e cumpre uma ampla gama de padrões de conformidade](https://azure.microsoft.com/support/trust-center/). Somente você e a sua equipe designada têm acesso aos seus dados. A equipe da Microsoft pode ter acesso restrito a eles apenas em circunstâncias limitadas específicas e com o seu conhecimento. Ele é criptografado em trânsito e em repouso.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, Confira nossa [visão geral do Internet Analyzer](internet-analyzer-overview.md).

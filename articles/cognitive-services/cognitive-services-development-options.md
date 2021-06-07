---
title: Opções de desenvolvimento de serviços cognitivas do Azure
description: Saiba como usar os serviços cognitivas do Azure com diferentes opções de desenvolvimento e implantação, como bibliotecas de cliente, APIs REST, aplicativos lógicos, automatização de energia, Azure Functions, serviço Azure App, Azure Databricks e muito mais.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 975f7eec31839aefcb1782f573d0210df29a4d00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98873562"
---
# <a name="cognitive-services-development-options"></a>Opções de desenvolvimento dos Serviços Cognitivos

Este documento fornece uma visão geral de alto nível das opções de desenvolvimento e implantação para ajudá-lo a começar a usar os serviços cognitivas do Azure.

Os serviços cognitivas do Azure são serviços de ia baseados em nuvem que permitem aos desenvolvedores criar inteligência em seus aplicativos e produtos sem conhecimento profundo do aprendizado de máquina. Com os serviços cognitivas, você tem acesso a recursos de ia ou modelos criados, treinados e atualizados pela Microsoft prontos para serem usados em seus aplicativos. Em muitos casos, você também tem a opção de personalizar os modelos para suas necessidades de negócios. 

Os serviços cognitivas são organizados em quatro categorias: decisão, idioma, fala e visão. Normalmente, você acessaria esses serviços por meio de APIs REST, bibliotecas de cliente e ferramentas personalizadas (como interfaces de linha de comando) fornecidas pela Microsoft. No entanto, esse é apenas um caminho para o sucesso. Por meio do Azure, você também tem acesso a várias opções de desenvolvimento, como:

* Ferramentas de automação e integração, como Aplicativos Lógicos e Power Automate.
* Opções de implantação, como Azure Functions e Serviço de Aplicativo. 
* Contêineres do Docker de Serviços Cognitivos para acesso seguro.
* Ferramentas como Apache Spark, Azure Databricks, Azure Synapse Analytics e Serviço de Kubernetes do Azure para cenários de Big Data. 

Antes de passarmos, é importante saber que os serviços cognitivas são usados principalmente para duas tarefas distintas. Com base na tarefa que você deseja executar, você tem opções de desenvolvimento e implantação diferentes para escolher. 

* [Opções de desenvolvimento para previsão e análise](#development-options-for-prediction-and-analysis)
* [Ferramentas para personalizar e configurar modelos](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Opções de desenvolvimento para previsão e análise 

As ferramentas que você usará para personalizar e configurar modelos são diferentes daquelas que serão usadas para chamar os serviços cognitivas. Na caixa, os serviços mais cognitivas permitem que você envie dados e receba informações sem qualquer personalização. Por exemplo: 

* Você pode enviar uma imagem para o serviço de Pesquisa Visual Computacional para detectar palavras e frases ou contar o número de pessoas no quadro
* Você pode enviar um arquivo de áudio para o serviço de fala e obter transcrições e converter a fala em texto ao mesmo tempo
* Você pode enviar um PDF para o serviço de reconhecimento de formulário e detectar tabelas, células e texto dentro dessas células e obter uma saída JSON com coordenadas e detalhes

O Azure oferece uma ampla variedade de ferramentas projetadas para diferentes tipos de usuários, muitas das quais podem ser usadas com serviços cognitivas. As ferramentas controladas por designer são as mais fáceis de usar e são rápidas de configurar e automatizar, mas podem ter limitações quando se trata de personalização. Nossas APIs REST e bibliotecas de cliente fornecem aos usuários mais controle e flexibilidade, mas exigem mais esforço, tempo e experiência para criar uma solução. Se você usa APIs REST e bibliotecas de cliente, há uma expectativa de que você esteja confortável trabalhando com linguagens de programação modernas, como C#, Java, Python, JavaScript ou outra linguagem de programação popular. 

Vamos dar uma olhada nas diferentes maneiras que você pode trabalhar com os serviços cognitivas.

### <a name="client-libraries-and-rest-apis"></a>Bibliotecas de cliente e APIs REST

As bibliotecas de cliente de serviços cognitivas e as APIs REST fornecem acesso direto ao seu serviço. Essas ferramentas fornecem acesso programático aos serviços cognitivas, seus modelos de linha de base e, em muitos casos, permitem que você personalize programaticamente seus modelos e soluções. 

* **Usuário (s) de destino**: desenvolvedores e cientistas de dados
* **Benefícios**: fornece a maior flexibilidade para chamar os serviços de qualquer linguagem e ambiente. 
* **IU**: N/a-Code somente
* **Assinatura (s)**: conta do Azure + recursos de serviços cognitivas

Se você quiser saber mais sobre as bibliotecas de cliente disponíveis e as APIs REST, use nossa [visão geral dos serviços cognitivas](index.yml) para escolher e realizar o serviço e começar com um dos nossos guias de início rápido para visão, decisão, idioma e fala.

### <a name="cognitive-services-for-big-data"></a>Serviços Cognitivos para Big data

Com os Serviços Cognitivos para Big Data, você pode inserir continuamente modelos inteligentes e aprimorados diretamente no Apache Spark&trade; e em cálculos de SQL. Essas ferramentas liberam os desenvolvedores de detalhes de rede de nível baixo para que eles possam se concentrar na criação de aplicativos inteligentes e distribuídos. Os serviços cognitivas para Big data dão suporte às seguintes plataformas e conectores: Azure Databricks, Azure Synapse, serviço kubernetes do Azure e conectores de dados.

* **Usuário (s) de destino**: cientistas de dados e engenheiros de dados
* **Benefícios**: os serviços cognitivas do Azure para Big data permitem que os usuários canalem terabytes de dados por meio de serviços cognitivas usando Apache Spark &trade; . É fácil criar aplicativos inteligentes de grande escala com qualquer repositório de armazenamento.
* **IU**: N/a-Code somente
* **Assinatura (s)**: conta do Azure + recursos de serviços cognitivas

Se você quiser saber mais sobre Big data para serviços cognitivas, um bom lugar para começar é com a [visão geral](./big-data/cognitive-services-for-big-data.md). Se você estiver pronto para começar a criar, experimente nossos exemplos de [Python](./big-data/samples-python.md) ou [escalares](./big-data/samples-scala.md) .

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions e trabalhos da Web de serviço do Azure

Os [trabalhos da Web do serviço](../app-service/index.yml) [Azure Functions](../azure-functions/index.yml) e do Azure app fornecem serviços de integração do código-primeiro projetados para desenvolvedores e são criados em [serviços Azure apps](../app-service/index.yml). Esses produtos fornecem infraestrutura sem servidor para escrever código. Dentro desse código, você pode fazer chamadas para nossos serviços usando nossas bibliotecas de cliente e APIs REST. 

* **Usuário (s) de destino**: desenvolvedores e cientistas de dados
* **Benefícios**: serviço de computação sem servidor que permite executar código disparado por evento. 
* **Interface do usuário**: Sim
* **Assinatura (s)**: conta do Azure + recurso de serviços cognitivas + assinatura de Azure Functions

### <a name="azure-logic-apps"></a>Aplicativos Lógicos do Azure 

Os [aplicativos lógicos do Azure](../logic-apps/index.yml) compartilham o mesmo designer de fluxo de trabalho e conectores que a automatização de energia, mas fornecem mais avançado e controle, incluindo integrações com o Visual Studio e o DevOps. A automatização de energia facilita a integração com seus recursos de serviços cognitivas por meio de conectores específicos de serviço que fornecem um proxy ou wrapper em relação às APIs. Esses são os mesmos conectores disponíveis na automatização de energia. 

* **Usuário (s) de destino**: desenvolvedores, integradores, profissionais de ti, DevOps
* **Benefícios**: modelo de desenvolvimento primeiro (declarativo) de designer que fornece opções avançadas e integração em uma solução de código baixo
* **Interface do usuário**: Sim
* **Assinatura (s)**: conta do Azure + recurso de serviços cognitivas + implantação de aplicativos lógicos

### <a name="power-automate"></a>Power Automate 

A automatização de energia é um serviço na [plataforma de energia](/power-platform/) que ajuda a criar fluxos de trabalho automatizados entre aplicativos e serviços sem precisar escrever código. Oferecemos vários conectores para facilitar a interação com o recurso de serviços cognitivas em uma solução de automatização de energia. O Power Automate se baseia nos Aplicativos Lógicos. 

* **Usuário (s) de destino**: usuários empresariais (analistas) e administradores do SharePoint
* **Benefícios**: automatizar tarefas manuais repetitivas simplesmente gravando cliques do mouse, pressionamentos de teclas e as etapas copiar colar de sua área de trabalho!
* **Ferramentas de interface do usuário**: Sim – somente interface do usuário
* **Assinatura (s)**: conta do Azure + recurso de serviços cognitivas + assinatura de energia automatizada + assinatura do Office 365

### <a name="ai-builder"></a>AI Builder 

O [ia Builder](/ai-builder/overview) é um recurso da plataforma de energia da Microsoft que você pode usar para melhorar o desempenho de negócios automatizando processos e prevendo resultados. O ia Builder traz o poder do ia para suas soluções por meio de uma experiência de apontar e clicar. Muitos serviços cognitivas, como reconhecedor de formulário, Análise de Texto e Pesquisa Visual Computacional foram diretamente integrados aqui e você não precisa criar seus próprios serviços cognitivas. 

* **Usuário (s) de destino**: usuários empresariais (analistas) e administradores do SharePoint
* **Benefícios**: uma solução completa que traz a potência do ia por meio de uma experiência de apontar e clicar. Nenhuma habilidade de codificação ou ciência de dados é necessária.
* **Ferramentas de interface do usuário**: Sim – somente interface do usuário
* **Assinatura (s)**: Construtor de ia

### <a name="continuous-integration-and-deployment"></a>Integração contínua e implantação

Você pode usar as ações do Azure DevOps e do GitHub para gerenciar suas implantações. Na [seção abaixo](#continuous-integration-and-delivery-with-devops-and-github-actions) abordada, temos dois exemplos de integrações de CI/CD para treinar e implantar modelos personalizados de fala e o serviço de reconhecimento vocal (Luis). 

* **Usuário (s) de destino**: desenvolvedores, cientistas de dados e engenheiros de dados
* **Benefícios**: permite que você ajuste continuamente, atualize e implante aplicativos e modelos programaticamente. Há um benefício significativo ao usar regularmente seus dados para melhorar e atualizar modelos de fala, visão, linguagem e decisão. 
* **Ferramentas de interface do usuário**: N/a-Code somente 
* **Assinatura (s)**: conta do Azure + recurso de serviços cognitivas + conta do github

## <a name="tools-to-customize-and-configure-models"></a>Ferramentas para personalizar e configurar modelos

Conforme você progride em sua jornada criando um aplicativo ou um fluxo de trabalho com os serviços cognitivas, talvez você ache que precisa personalizar o modelo para obter o desempenho desejado. Muitos de nossos serviços permitem que você se baseie nos modelos predefinidos para atender às suas necessidades de negócios específicas. Para todos os nossos serviços personalizáveis, fornecemos uma experiência orientada por interface do usuário para percorrer o processo, bem como APIs para treinamento controlado por código. Por exemplo:

* Você deseja treinar um modelo de Fala Personalizada para reconhecer corretamente os termos médicos com uma taxa de erros do Word (WER) abaixo de 3%
* Você deseja criar um classificador de imagem com Visão Personalizada que possa dizer a diferença entre as árvores coníferas e decídua
* Você deseja criar uma voz neural personalizada com seus dados de voz pessoal para uma experiência de cliente automatizada aprimorada

As ferramentas que você usará para treinar e configurar modelos são diferentes daquelas que serão usadas para chamar os serviços cognitivas. Em muitos casos, os serviços cognitivas que dão suporte à personalização fornecem portais e ferramentas de interface do usuário projetadas para ajudá-lo a treinar, avaliar e implantar modelos. Vamos dar uma olhada rapidamente em algumas opções:<br><br>

| Pilar | Serviço | UI de personalização | Guia de Início Rápido |
|--------|---------|------------------|------------|
| Visão | Visão Personalizada | https://www.customvision.ai/ | [Início rápido](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Visão | Reconhecimento de Formulários | [Ferramenta de rotulagem de exemplo](https://fott-preview.azurewebsites.net/) | [Início rápido](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Decisão | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Início rápido](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Decisão | Assistente de Métricas | https://metricsadvisor.azurewebsites.net/  | [Início rápido](./metrics-advisor/quickstarts/web-portal.md) |
| Decisão | Personalizador | A interface do usuário está disponível no portal do Azure em seu recurso personalizador. | [Início rápido](./personalizer/quickstart-personalizer-sdk.md) |
| Idioma | Reconhecimento Vocal (LUIS) | https://www.luis.ai/ | |
| Idioma | QnA Maker | https://www.qnamaker.ai/ | [Início rápido](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Idioma | Tradutor/Tradutor personalizado | https://portal.customtranslator.azure.ai/ | [Início rápido](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Fala | Comandos personalizados | https://speech.microsoft.com/ | [Início rápido](./speech-service/custom-commands.md) |
| Fala | Fala Personalizada | https://speech.microsoft.com/ | [Início rápido](./speech-service/custom-speech-overview.md) |
| Fala | Voz personalizada | https://speech.microsoft.com/ | [Início rápido](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Integração e entrega contínuas com ações DevOps e GitHub

Reconhecimento vocal e o serviço de fala oferecem soluções contínuas de integração e implantação contínua que são alimentadas pelas ações do Azure DevOps e do GitHub. Essas ferramentas são usadas para treinamento automatizado, teste e gerenciamento de versão de modelos personalizados. 

* [CI/CD para Fala Personalizada](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD para LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Contêineres locais 

Muitos dos serviços cognitivas podem ser implantados em contêineres para acesso e uso local. O uso desses contêineres oferece a flexibilidade de trazer serviços cognitivas mais perto de seus dados para fins de conformidade, segurança ou outras razões operacionais. Para obter uma lista completa de contêineres de serviços cognitivas, consulte [contêineres locais para serviços cognitivas](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Próximas etapas
<!--
* Learn more about low code development options for Cognitive Services -->
* [Criar um recurso de serviços cognitivas e começar a criar](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)

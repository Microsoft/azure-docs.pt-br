---
title: Criação visual
description: Saiba como usar a criação visual no Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: ''
manager: anandsub
ms.date: 09/08/2020
ms.openlocfilehash: c3204d33cac9f0944ec2ea2cf43be65c7afa46df
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500131"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Criação visual no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A experiência (UX) da interface do usuário do Azure Data Factory permite criar e implantar visualmente recursos para seu data factory sem ter que gravar nenhum código. Você pode arrastar atividades para uma tela de pipeline, realizar execuções de teste, depurar iterativamente e implantar e monitorar as execuções do pipeline.

Atualmente, a UX do Azure Data Factory só tem suporte no Microsoft Edge e Google Chrome.

## <a name="authoring-canvas"></a>Tela de criação

Para abrir a **tela de criação**, clique no ícone de lápis. 

![Tela de criação](media/author-visually/authoring-canvas.png)

Aqui, você cria os pipelines, as atividades, os conjuntos de dados, os serviços vinculados, os fluxos de dados, os gatilhos e os runtimes de integração que compõem seu data factory. Para começar a criar um pipeline usando a tela de criação, consulte [Copiar dados usando a atividade de cópia](tutorial-copy-data-portal.md). 

A experiência de criação visual padrão está trabalhando diretamente com o serviço de Data Factory. A integração do Azure Repos Git ou GitHub também tem suporte para permitir o controle do código-fonte e a colaboração para trabalhar nos pipelines do seu data factory. Para saber mais sobre as diferenças entre essas experiências de criação, consulte [Controle do código-fonte no Azure Data Factory](source-control.md).

### <a name="properties-pane"></a>painel Propriedades

Para recursos de nível superior, como pipelines, conjuntos de dados e fluxos de dados, as propriedades de alto nível são editáveis no painel Propriedades, no lado direito da tela. O painel Propriedades contém nome, descrição, anotações e outras propriedades de alto nível. Os sub-recursos, como atividades de pipeline e transformações de fluxo de dados, são editados usando o painel na parte inferior da tela. 

![painel Propriedades](media/author-visually/properties-pane.png)

O painel Propriedades só é aberto, por padrão, na criação de recursos. Para editá-lo, clique no ícone do painel Propriedades localizado no canto superior direito da tela.

### <a name="related-resources"></a>Recursos relacionados

No painel Propriedades, você pode ver quais recursos dependem do recurso selecionado selecionando a guia **relacionado** . Qualquer recurso que referencie o recurso atual será listado aqui.

![Recursos relacionados](media/author-visually/related-resources.png)

Por exemplo, na imagem acima, um pipeline e dois fluxos de dados usam o DataSet selecionado no momento.

## <a name="management-hub"></a>Hub de gerenciamento

O Hub de gerenciamento, acessado pela guia *gerenciar* no Azure data Factory UX, é um portal que hospeda ações de gerenciamento global para seu data Factory. Aqui, você pode gerenciar suas conexões com armazenamentos de dados e computações externas, configuração de controle do código-fonte e configurações de gatilho. Para obter mais informações, saiba mais sobre os recursos do [Hub de gerenciamento](author-management-hub.md).

![Gerenciar serviços vinculados](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>Expressões e funções

Em vez de valores estáticos, as expressões e funções podem ser usadas para especificar muitas propriedades no Azure Data Factory.

Para especificar uma expressão para um valor de propriedade, selecione **Adicionar conteúdo dinâmico** ou clique em **ALT + P** estando dentro do campo.

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content-1.png)

Isso abre o **Construtor de Expressões do Data Factory**, onde você pode criar expressões de variáveis de sistema com suporte, saída de atividade, funções e variáveis ou parâmetros especificados pelo usuário. 

![Construtor de expressões](media/author-visually/dynamic-content-2.png)

Para obter informações sobre a linguagem das expressões, consulte [Expressões e funções no Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Fornecer comentários

Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).

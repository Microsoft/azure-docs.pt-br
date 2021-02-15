---
title: Conceitos de sessões de depuração (visualização)
titleSuffix: Azure Cognitive Search
description: As Sessões de depuração, acessadas por meio do portal do Azure, fornecem um ambiente do tipo IDE em que você pode identificar e corrigir erros, validar alterações e efetuar push de alterações em conjuntos de habilidades no pipeline de enriquecimento de IA. As Sessões de depuração estão em versão prévia.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057613"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Sessões de depuração no Azure Pesquisa Cognitiva

As Sessões de depuração são um editor visual que funciona com um conjunto de habilidades existente no portal do Azure. Em uma Sessão de depuração, você pode identificar e resolver erros, validar alterações e efetuar push de alterações em um conjunto de habilidades de produção no pipeline de enriquecimento de IA.

> [!Important]
> As sessões de depuração são uma versão prévia do recurso fornecida sem um contrato de nível de serviço e não são recomendadas para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="using-debug-sessions"></a>Usar as Sessões de depuração

Quando você inicia uma sessão, o serviço cria uma cópia do conjunto de habilidades, do indexador e do índice em que um único documento é usado para testar o conjunto de habilidades. As alterações feitas dentro da sessão são salvas na sessão. As alterações feitas na sessão de depuração não afetarão o conjunto de habilidades, a menos que as alterações sejam confirmadas nele. A confirmação das alterações substituirá o conjunto de habilidades de produção.

Durante uma sessão de depuração, você pode editar um conjunto de habilidades, inspecionar e validar cada nó na árvore de enriquecimento no contexto de um documento específico. Depois que os problemas de pipeline de enriquecimento forem resolvidos, as alterações poderão ser salvas na sessão e confirmadas para o conjunto de habilidades em produção. 

Se o pipeline de enriquecimento não tiver erros, uma sessão de depuração poderá ser usada para enriquecer um documento de forma incremental, testar e validar cada alteração antes de confirmar as alterações.

## <a name="creating-a-debug-session"></a>Criar uma sessão de depuração

Para iniciar uma sessão de depuração, você deve ter um pipeline de enriquecimento de IA, incluindo uma fonte de dados, um conjunto de habilidades, um indexador e um índice. Para configurar uma sessão de depuração, você precisa nomear a sessão e fornecer uma conta de armazenamento de uso geral que será usada para armazenar em cache as execuções de habilidades durante a execução do indexador. Você também precisará selecionar o indexador que será executado. O indexador tem referências armazenadas à fonte de dados, ao conjunto de habilidades e ao índice. A sessão de depuração usará como padrão o primeiro documento na fonte de dados, ou você pode especificar um documento na fonte de dados para acompanhar.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Criar uma sessão de depuração":::

## <a name="debug-session-features"></a>Recursos da sessão de depuração

A sessão de depuração começa com a execução do conjunto de habilidades no documento selecionado. A sessão de depuração registrará metadados adicionais associados a cada operação no conjunto de habilidades. Os metadados criados pelas execuções de habilidade do pipeline informam o conjunto de recursos a seguir, que é usado para ajudar a identificar e corrigir problemas com o conjunto de habilidades.

## <a name="ai-enrichments"></a>Enriquecimentos de IA

À medida que as habilidades executam uma árvore de enriquecimento, a representação do documento aumenta. Usar uma árvore para visualizar as saídas de habilidades ou enriquecimentos fornece uma visão abrangente de todos os aprimoramentos realizados. Você pode pesquisar todo o documento e inspecionar cada nó da árvore de enriquecimento. Essa perspectiva facilita a formatação dos objetos. Esse formato também fornece indicações visuais do tipo, caminho e conteúdo de cada nó na árvore.

## <a name="skill-graph"></a>Gráfico de Habilidades

A exibição de **Gráfico de Habilidades** fornece uma representação visual e hierárquica do conjunto de habilidades. O gráfico é uma representação de cima para baixo da ordem de execução das habilidades. As habilidades que dependem da saída de outras habilidades serão mostradas mais abaixo no gráfico. As habilidades no mesmo nível na hierarquia podem ser executadas em paralelo. 

A seleção de uma habilidade no gráfico destacará as habilidades conectadas a ela, os nós que criam as entradas dela e os nós que aceitam as saídas dessa habilidade. Cada nó de habilidade exibe o tipo, os erros ou avisos e as contagens de execução. O **Gráfico de Habilidades** é onde você selecionará qual habilidade depurar ou aprimorar. Quando você seleciona uma habilidade, os detalhes são exibidos no painel de detalhes da habilidade à direita do gráfico.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Criar uma sessão de depuração":::

## <a name="skill-details"></a>Detalhes da habilidade

O painel de detalhes da habilidade exibe um conjunto de áreas para trabalhar com uma habilidade específica, quando essa habilidade é destacada no **Gráfico de Habilidades**. Você pode revisar e editar os detalhes das configurações da habilidade. A definição de JSON da habilidade é fornecida. Os detalhes da execução da habilidade e os erros e avisos também são exibidos. A guia **Configurações de Habilidade** e o **Editor JSON de Habilidade** permitem edições diretas da habilidade. O [`</>`](#expression-evaluator) abre uma janela para exibir e editar as expressões das entradas e saídas de habilidades.

Os controles de entrada aninhados na janela de configurações de habilidades podem ser usados para criar formas complexas para projeções, mapeamentos de campo de saída para um campo de tipo complexo ou uma entrada para uma habilidade. Quando usadas com o **Avaliador de expressão**, as entradas aninhadas fornecem um construtor de expressões para testar e validar com facilidade.

## <a name="skill-execution-history"></a>Histórico de execução da habilidade

Uma habilidade pode ser executada várias vezes em um conjunto de habilidades para um único documento. Por exemplo, a habilidade de OCR será executada uma vez para cada imagem extraída de cada documento. No painel de detalhes da habilidade, a guia **Execuções** exibe o histórico de execução da habilidade, fornecendo uma análise mais profunda de cada invocação da habilidade. 

O histórico de execução permite controlar um enriquecimento específico de volta para a habilidade que o gerou. Clicando em uma entrada de habilidade, é possível navegar até a habilidade que gerou essa entrada. Isso permite a identificação da causa raiz de um problema que pode ser manifestado em uma habilidade de downstream. 

Quando um problema potencial é identificado, o histórico de execução exibe links para as habilidades que geraram as entradas específicas, fornecendo um rastreamento de pilha como recurso. Clicando na habilidade associada a uma entrada, é possível navegar até a habilidade para corrigir bugs ou continuar a rastrear o problema específico.

Ao criar uma habilidade personalizada ou depurar um erro com uma habilidade personalizada, há a opção de gerar uma solicitação para uma invocação de habilidades no histórico de execução.

## <a name="enriched-data-structure"></a>Estrutura de Dados Enriquecidos

O painel de **Estrutura de Dados Enriquecidos** mostra os aprimoramentos do documento por meio do conjunto de habilidades, detalhando o contexto de cada enriquecimento e a habilidade de origem. O **Avaliador de expressão** também pode ser usado para exibir o conteúdo de cada enriquecimento.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Criar uma sessão de depuração":::

## <a name="expression-evaluator"></a>Avaliador de expressão

O **Avaliador de expressão** oferece uma noção rápida do valor de qualquer caminho. Ele permite editar o caminho e testar os resultados antes de atualizar qualquer uma das entradas ou o contexto de uma habilidade ou projeção.

## <a name="errorswarnings"></a>Erros/Avisos

Essa janela exibe todos os erros e avisos que o conjunto de habilidades produz conforme é executado no documento na Sessão de depuração.

## <a name="limitations"></a>Limitações

Debug sesisons trabalha com todas as fontes de dados geralmente disponíveis AMD mais fontes de dados de visualização. Atualmente, não há suporte para a API do MongoDB (visualização) e API do Cassandra (visualização) de Cosmos DB.

## <a name="next-steps"></a>Próximas etapas

Agora que você entendeu os elementos das Sessões de depuração, use o tutorial para ter uma experiência prática.

> [!div class="nextstepaction"]
> [Explorar o tutorial do recurso de Sessões de depuração](./cognitive-search-tutorial-debug-sessions.md)
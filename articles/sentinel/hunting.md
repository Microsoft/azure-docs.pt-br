---
title: Recursos de busca no Azure Sentinel | Microsoft Docs
description: Use as consultas de busca interna do Sentinela do Azure para orientá-lo a fazer as perguntas certas para encontrar problemas em seus dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: b88185a09e69b1c7bd6c6c58b8e22190b4a2e394
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576753"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Procurar ameaças com o Azure Sentinel

Se você é um investigador que deseja ser proativo sobre a procura de ameaças à segurança, o Azure Sentinel cria poderosas ferramentas de pesquisa e consulta para procurar ameaças à segurança nas fontes de dados de sua organização. Mas seus sistemas e dispositivos de segurança geram montanhas de dados que podem ser difíceis de analisar e filtrar em eventos significativos. Para ajudar os analistas de segurança a procurarem de forma pró-ativa as novas anomalias que não foram detectadas pelos seus aplicativos de segurança, as consultas de busca interna do Azure Sentinel o orientam a fazer as perguntas certas para encontrar problemas nos dados que você já tem em sua rede. 

Por exemplo, uma consulta interna fornece dados sobre os processos mais incomuns em execução na sua infraestrutura – você não iria querer um alerta sobre cada vez que eles são executados, eles poderiam ser totalmente inofensivos, mas talvez você queira dar uma olhada na consulta para ver se há algo incomum. 



Com a busca do Azure Sentinel, você pode aproveitar os seguintes recursos:

- Consultas internas: para começar, uma página inicial fornece exemplos de consulta pré-carregados projetados para ajudá-lo a começar e a familiarizar-se com as tabelas e a linguagem de consulta. Essas consultas de busca interna são desenvolvidas pelos pesquisadores de segurança da Microsoft de forma contínua, adicionando novas consultas e ajustando as consultas existentes para fornecer a você um ponto de entrada para procurar novas detecções e descobrir onde começar a buscar pelos iniciantes dos novos ataques. 

- Linguagem de consulta avançada com o IntelliSense: criada com base em uma linguagem de consulta que oferece a flexibilidade que você precisa para dar uma busca ao próximo nível.

- Crie seus próprios indicadores: durante o processo de busca, você pode encontrar correspondências ou descobertas, painéis ou atividades que parecem incomuns ou suspeitas. Para marcar esses itens para que você possa voltar a eles no futuro, use a funcionalidade de indicador. Os indicadores permitem salvar itens para mais tarde, para serem usados para criar um incidente para investigação. Para obter mais informações sobre indicadores, consulte [usar indicadores na busca](hunting.md).
- Use blocos de anotações para automatizar a investigação: notebooks são como guias passo a passo que você pode criar para percorrer as etapas de uma investigação e uma busca.  Os notebooks encapsulam todas as etapas de busca em um manual reutilizável que pode ser compartilhado com outras pessoas em sua organização. 
- Consultar os dados armazenados: os dados estão acessíveis em tabelas para consulta. Por exemplo, você pode consultar a criação de processos, eventos DNS e muitos outros tipos de eventos.

- Links para a Comunidade: Aproveite o poder da comunidade maior para encontrar consultas e fontes de dados adicionais.
 
## <a name="get-started-hunting"></a>Introdução à busca

1. No portal do Azure Sentinel, clique em **busca**.
  ![O Azure Sentinel começa a buscar](media/tutorial-hunting/hunting-start.png)

2. Quando você abre a página de **busca** , todas as consultas de busca são exibidas em uma única tabela. A tabela lista todas as consultas escritas pela equipe de analistas de segurança da Microsoft, bem como qualquer consulta adicional que você criou ou modificou. Cada consulta fornece uma descrição do que é buscado e em que tipo de dados ele é executado. Esses modelos são agrupados por suas várias táticas: os ícones à direita categorizam o tipo de ameaça, como o acesso inicial, a persistência e a vazamento. Você pode filtrar esses modelos de consulta buscados usando qualquer um dos campos. Você pode salvar qualquer consulta em seus favoritos. Ao salvar uma consulta em seus favoritos, a consulta é executada automaticamente sempre que a página de **busca** é acessada. Você pode criar sua própria consulta de busca ou clonar e personalizar um modelo de consulta de busca existente. 
 
2. Clique em **Executar consulta** na página detalhes da consulta busca para executar qualquer consulta sem sair da página de busca.  O número de correspondências é exibido na tabela. Examine a lista de consultas de busca e suas correspondências. Confira a qual estágio na cadeia Kill a correspondência está associada.

3. Execute uma revisão rápida da consulta subjacente no painel detalhes da consulta ou clique em **Exibir resultado da consulta** para abrir a consulta no log Analytics. Na parte inferior, examine as correspondências para a consulta.

4.    Clique na linha e selecione **Adicionar indicador** para adicionar as linhas a serem investigadas – você pode fazer isso para qualquer coisa que pareça suspeita. 

5. Em seguida, volte para a página de **busca** principal e clique na guia **indicadores** para ver todas as atividades suspeitas. 

6. Selecione um indicador e clique em **investigar** para abrir a experiência de investigação. Você pode filtrar os indicadores. Por exemplo, se você estiver investigando uma campanha, poderá criar uma marca para a campanha e, em seguida, filtrar todos os indicadores com base na campanha.

1. Depois de descobrir qual consulta de busca fornece informações de alto valor sobre possíveis ataques, você também pode criar regras de detecção personalizadas com base em sua consulta e Surface-las como alertas para seus respondentes de incidente de segurança.

 

## <a name="query-language"></a>Idioma de consulta 

A busca no Azure Sentinel se baseia na linguagem de consulta Kusto. Para obter mais informações sobre a linguagem de consulta e os operadores com suporte, consulte [referência de linguagem de consulta](../azure-monitor/logs/get-started-queries.md).

## <a name="public-hunting-query-github-repository"></a>Repositório GitHub de consulta de busca pública

Confira o [repositório de consultas de busca](https://github.com/Azure/Orion). Contribuir e usar consultas de exemplo compartilhadas por nossos clientes.

 

## <a name="sample-query"></a>Exemplo de consulta

Uma consulta comum começa com um nome de tabela, seguido por uma série de operadores separados por \|.

No exemplo acima, comece com o nome da tabela SecurityEvent e adicione os elementos de pipe conforme necessário.

1. Defina um filtro de tempo para examinar somente os registros dos sete dias anteriores.

2. Adicione um filtro na consulta para mostrar apenas a ID do evento 4688.

3. Adicione um filtro na consulta na linha de comando para conter apenas instâncias de cscript.exe.

4. Projeto apenas as colunas que você está interessado em explorar e limitar os resultados a 1000 e clique em **Executar consulta**.
5. Clique no triângulo verde e execute a consulta. Você pode testar a consulta e executá-la para procurar comportamento anormal.

## <a name="useful-operators"></a>Operadores úteis

A linguagem de consulta é poderosa e tem muitos operadores disponíveis, alguns operadores úteis são listados aqui:

**Where** -filtra uma tabela para o subconjunto de linhas que satisfazem um predicado.

**resumir** – produzir uma tabela que agrega o conteúdo da tabela de entrada.

**unir** – mesclar as linhas de duas tabelas para formar uma nova tabela por meio da correspondência de valores das colunas especificadas de cada tabela.

**Count** – retorna o número de registros no conjunto de registros de entrada.

**Top** -retorna os primeiros N registros classificados pelas colunas especificadas.

**Limit** -retornar até o número especificado de linhas.

**projeto** -selecione as colunas a serem incluídas, renomeadas ou descartadas e insira novas colunas computadas.

**Extend** – criar colunas calculadas e acrescentá-las ao conjunto de resultados.

**makeset** -retorna uma matriz dinâmica (JSON) do conjunto de valores distintos que a expr usa no grupo

**Localizar** -localizar linhas que correspondem a um predicado em um conjunto de tabelas.

## <a name="save-a-query"></a>Salvar uma consulta

Você pode criar ou modificar uma consulta e salvá-la como sua própria consulta ou compartilhá-la com usuários que estão no mesmo locatário.

   ![Salvar consulta](./media/tutorial-hunting/save-query.png)

Criar uma nova consulta de busca:

1. Clique em **nova consulta** e selecione **salvar**.
2. Preencha todos os campos em branco e selecione **salvar**.

   ![Nova Consulta](./media/tutorial-hunting/new-query.png)

Clonar e modificar uma consulta de busca existente:

1. Selecione a consulta de busca na tabela que você deseja modificar.
2. Selecione as reticências (...) na linha da consulta que você deseja modificar e selecione **clonar consulta**.

   ![clonar consulta](./media/tutorial-hunting/clone-query.png)
 

3. Modifique a consulta e selecione **criar**.

   ![consulta personalizada](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar uma investigação de busca com o Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Use blocos de anotações para executar campanhas de busca automatizadas](notebooks.md)
- [Use indicadores para salvar informações interessantes durante a busca](bookmarks.md)
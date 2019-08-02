---
title: Recursos de procura na visualização do Azure Sentinel | Microsoft Docs
description: Este artigo descreve como usar os recursos de procura Sentinel do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 23e7db25e5ebed2a23b4d38bcfe9597b77c6b04b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620737"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Busca de ameaças com na visualização do Azure Sentinel

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se você for um investigador que queiram ser proativo sobre como consultar as ameaças à segurança, o Azure Sentinel poderosa caçar ferramentas de pesquisa e consulta de ameaças à segurança apresentar em fontes de dados da sua organização. Mas Montanhas de dados que podem ser difíceis de analisar e filtrar eventos significativos de gerar seus sistemas e dispositivos de segurança. Para ajudar a segurança analistas procure proativamente novas anomalias que não foram detectadas por seus aplicativos de segurança, o Azure Sentinel' consultas internas procura orientá-lo para fazer as perguntas certas a localizar problemas nos dados que você já tem em sua rede. 

Por exemplo, uma consulta interna fornece dados sobre os processos mais incomuns em execução em sua infraestrutura – você não quer um alerta sobre cada vez que eles são executados, podem ser inteiramente inocentes, mas talvez você queira dar uma olhada na ocasião para ver se a consulta a th ere's algo incomum. 



Com a procura de sentinela do Azure, você pode tirar proveito dos recursos a seguir:

- Consultas internas: Para começar, uma página inicial fornece exemplos de consulta pré-carregados projetados para ajudá-lo iniciado e se familiarizar com as tabelas e a linguagem de consulta. Essas consultas internas de procura são desenvolvidas pela Microsoft os pesquisadores de segurança em uma base contínua, adicionar novas consultas, e consultas de ajuste fino existente para fornecer um ponto de entrada para procurar por novas detecções e descobrir onde iniciar a procura de inícios de novos ataques. 

- Linguagem de consulta avançada com o IntelliSense: Criado sobre uma linguagem de consulta que lhe dá a flexibilidade que necessária para levar a procura de para o próximo nível.

- Crie seus próprios indicadores: Durante o processo de procura, você pode se deparar com correspondências ou descobertas, painéis ou atividades que procuram incomuns ou suspeito. Para marcar os itens para que você pode voltar a eles no futuro, use a funcionalidade de indicador. Indicadores permitem que você salvar itens para mais tarde, para ser usado para criar um caso de investigação. Para obter mais informações sobre os indicadores, consulte usar [indicadores na procura].

- Use blocos de anotações para automatizar a investigação: Blocos de anotações são como Guias estratégicos de passo a passo que você pode compilar para percorrer as etapas de uma investigação e de busca.  Blocos de anotações encapsulam todas as etapas de procura em um guia estratégico reutilizável que podem ser compartilhados com outras pessoas em sua organização. 
- Consulte os dados armazenados: Os dados estão acessíveis nas tabelas para consulta. Por exemplo, você pode consultar a criação do processo, eventos DNS e muitos outros tipos de evento.

- Fornece links para a comunidade: Aproveite o poder da comunidade do maior para localizar fontes de dados e consultas adicionais.
 
## <a name="get-started-hunting"></a>Introdução ao caçar

1. No portal do Azure Sentinel, clique em **procura**.
  ![Sentinela Azure inicia a procura](media/tutorial-hunting/hunting-start.png)

2. Quando você abre o **procura** página, todas as consultas de procura são exibidas em uma única tabela. A tabela lista todas as consultas de escrita pela equipe da Microsoft de analistas de segurança, bem como qualquer consulta adicional criado ou modificado. Cada consulta fornece uma descrição de ele busca para, e o tipo de dados é executada. Esses modelos são agrupados por suas várias táticas - o tipo de ameaça, como o acesso inicial, persistência e extração de categorizam os ícones no lado direito. Você pode filtrar esses modelos de consulta de procura usando qualquer um dos campos. Você pode salvar qualquer consulta aos seus favoritos. Salvando uma consulta aos seus favoritos, a consulta executa automaticamente cada vez que o **procura** página é acessada. Você pode criar sua própria consulta procura ou clone e personalizar um modelo de consulta procura existente. 
 
2. Clique em **executar consulta** na procura de página de detalhes da consulta para executar qualquer consulta sem sair da página de procura.  O número de correspondências é exibido dentro da tabela. Examine a lista de consultas de procura e suas correspondências. Fazer check-out de qual estágio na cadeia de extermínio a correspondência está associada.

3. Executar uma rápida análise da consulta subjacente no painel de detalhes da consulta ou clique em **exibir o resultado da consulta** para abrir a consulta no Log Analytics. Na parte inferior, analise as correspondências para a consulta.

4.  Clique na linha e selecione **Adicionar indicador** para adicionar linhas a ser investigado - você pode fazer isso para qualquer coisa que parece suspeito. 

5. Em seguida, volte para o principal **procura** da página e clique no **indicadores** guia para ver todas as atividades suspeitas. 

6. Selecione um indicador e, em seguida, clique em **investigar** para abrir a experiência de investigação. Você pode filtrar os indicadores. Por exemplo, se você estiver investigando uma campanha, crie uma marca da campanha e, em seguida, filtrar todos os indicadores com base na campanha.

1. Após a descoberta de consulta que procura fornece informações de alto valor sobre possíveis ataques, você também pode criar regras com base em sua consulta e navegue nessas informações como alertas para seu respondentes de incidentes de segurança de detecção personalizada.

 

## <a name="query-language"></a>Idioma de consulta 

Procura no Azure Sentinel baseia-se a linguagem de consulta do Log Analytics do Azure. Para obter mais informações sobre a linguagem de consulta e os operadores com suporte, consulte [referência de linguagem de consulta](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Repositório do GitHub de consulta procura pública

Confira a [repositório de consulta procura](https://github.com/Azure/Orion). Contribuir e usar consultas de exemplo compartilhadas por nossos clientes.

 

## <a name="sample-query"></a>Exemplo de consulta

Uma consulta comum começa com um nome de tabela, seguido por uma série de operadores separados por \|.

No exemplo acima, comece com a tabela, nomeie SecurityEvent e adicionamos elementos conectados, conforme necessário.

1. Defina um filtro de tempo para analisar apenas registros de sete dias anteriores.

2. Adicione um filtro na consulta para mostrar apenas a ID de evento 4688.

3. Adicione um filtro na consulta na linha de comando para conter apenas instâncias do cscript.exe.

4. Projeto apenas as colunas que você tiver interesse em explorar e limita os resultados para 1000 e clique em **executar consulta**.
5. Clique no triângulo verde e execute a consulta. Você pode testar a consulta e executá-lo para procurar por comportamentos anormais.

## <a name="useful-operators"></a>Operadores úteis

A linguagem de consulta é potente e tem muitos operadores disponíveis, alguns operadores úteis são listados aqui:

**onde** -uma tabela para o subconjunto de linhas que atendem a um predicado de filtro.

**Resumir** -gerar uma tabela que agrega o conteúdo da tabela de entrada.

**junção** -mesclar as linhas das duas tabelas para formar uma nova tabela combinando valores de colunas especificadas de cada tabela.

**Contagem de** -retornar o número de registros no conjunto de registros de entrada.

**parte superior** -registros de retornar as primeiras N classificados pelas colunas especificadas.

**limite de** -retornar até o número especificado de linhas.

**projeto** - selecione as colunas para incluir, renomeadas ou removidas e insira novas colunas calculadas.

**estender** - criar colunas calculadas e acrescentá-los ao conjunto de resultados.

**makeset** -retornar uma matriz dinâmica de (JSON) do conjunto de valores distintos que leva Expr no grupo

**localizar** -localizar linhas que correspondem a um predicado em um conjunto de tabelas.

## <a name="save-a-query"></a>Salvar uma consulta

Você pode criar ou modificar uma consulta e salvá-lo como sua própria consulta ou compartilhá-lo com os usuários que estão no mesmo locatário.

   ![Salvar consulta](./media/tutorial-hunting/save-query.png)

Crie uma nova consulta de procura:

1. Clique em **nova consulta** e selecione **salvar**.
2. Preencha todos os campos em branco e selecione **salvar**.

   ![Nova Consulta](./media/tutorial-hunting/new-query.png)

Clonar e modificar uma consulta existente de procura:

1. Selecione a consulta procura na tabela que você deseja modificar.
2. Selecione as reticências (...) na linha da consulta que você deseja modificar e, em seguida, selecione **clonar consulta**.

   ![clonar consulta](./media/tutorial-hunting/clone-query.png)
 

3. Modificar a consulta e selecione **criar**.

   ![consulta personalizada](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar uma investigação de procura com Sentinel do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Usar notebooks para executar campanhas de procura automatizado](notebooks.md)
- [Usar indicadores para salvar informações interessantes durante a procura](bookmarks.md)

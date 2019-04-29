---
title: Configurar o Glossário de negócios no catálogo de dados do Azure
description: O artigo de instruções destaca o glossário de negócios no Catálogo de Dados do Azure para definir e usar um vocabulário de negócios comum para marcar ativos de dados registrados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 649a842c8c8890713bda938c8e11740c5c8be7aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001864"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Como configurar o glossário de negócios para marcação controlada

## <a name="introduction"></a>Introdução

O Catálogo de Dados do Azure permite a descoberta de fonte de dados para que você possa facilmente descobrir e entender as fontes de dados de que você precisa para executar a análise e tomar decisões. Esses recursos têm maior impacto quando você localiza e entende a mais ampla variedade de fontes de dados disponíveis.

Um recurso do Catálogo de Dados que promove maior compreensão dos dados dos ativos é a marcação. Usando marcação, você pode associar palavras-chave a um ativo ou a uma coluna, o que, por sua vez, torna mais fácil descobrir o ativo pesquisa ou navegação. A marcação também ajuda você a entender mais facilmente o contexto e a intenção do ativo.

No entanto, a marcação às vezes pode causar seus próprios problemas. Alguns exemplos de problemas que a marcação pode introduzir são:

* O uso de abreviações em alguns ativos e texto expandido em outros. Essa inconsistência prejudica a descoberta de ativos, embora a intenção fosse marcar os ativos com a mesma marca.
* Possíveis variações significados, dependendo do contexto. Por exemplo, uma marca chamada *Receita* em um conjunto de dados do cliente poderia significar receita por cliente, mas a mesma marca em um conjunto de dados de vendas trimestral poderia significar a receita trimestral da empresa.  

Para ajudar a resolver esses e outros desafios semelhantes, o Catálogo de Dados inclui um glossário de negócios.

Ao usar o glossário de negócios do Catálogo de Dados, uma organização pode documentar os principais termos de negócios e suas definições para criar um vocabulário comum de negócios. Esse controle proporciona consistência no uso de dados em toda a organização. Após a definição de um termo no glossário de negócios, ele pode ser atribuído a um ativo de dados no catálogo. Essa abordagem, *marcação controlada*, é a mesma abordagem que a de marcação.

## <a name="glossary-availability-and-privileges"></a>Disponibilidade de glossário e privilégios

O glossário de negócios está disponível somente na Standard Edition do Catálogo de Dados do Azure. A edição gratuita do catálogo de dados não inclui um glossário e ele não fornece recursos para marcação controlada.

Você pode acessar o glossário de negócios por meio da opção **Glossário** no menu de navegação do portal do Catálogo de Dados.  

![Acessando o glossário de negócios](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Os administradores do Catálogo de Dados e os membros da função de administradores de glossário podem criar, editar e excluir os termos do glossário no glossário de negócios. Todos os usuários do Catálogo de Dados podem exibir as definições de termos e marcar ativos com os termos do glossário.

![Adicionar um novo termo de glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Criar termos do glossário

Os administradores do Catálogo de Dados e os administradores do glossário podem criar termos do glossário clicando no botão **Novo Termo**. Cada termo do glossário contém os seguintes campos:

* Uma definição de negócios para o termo
* Uma descrição que captura o uso pretendido ou as regras de negócios para o ativo ou a coluna
* Uma lista de participantes sabem mais sobre o termo
* O termo pai, que define a hierarquia na qual o termo é organizado

## <a name="glossary-term-hierarchies"></a>Hierarquias de termos do glossário

Ao usar o glossário de negócios do Catálogo de Dados, uma organização pode descrever seu vocabulário de negócios como uma hierarquia de termos e pode criar uma classificação de termos que melhor represente sua taxonomia de negócios.

Um termo deve ser exclusivo em um determinado nível de hierarquia. Não são permitidos nomes duplicados. Não há nenhum limite para o número de níveis em uma hierarquia, mas uma hierarquia geralmente é mais facilmente compreendida quando há três níveis ou menos.

O uso de hierarquias no glossário de negócios é opcional. Deixar o campo de termo pai em branco para termos de glossário cria uma lista simples (não hierárquica) dos termos no glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Marcar ativos com os termos de glossário

Depois que termos do glossário tiverem sido definidos no catálogo, a experiência de marcar ativos é otimizada para pesquisar no glossário conforme o usuário digita uma marca. O portal do Catálogo de Dados exibe uma lista de termos de glossário correspondentes entre os quais escolher. Se o usuário selecionar um termo de glossário na lista, o termo será adicionado ao ativo como uma marca (também chamada de marca de glossário). O usuário também pode optar por criar uma nova marca digitando um termo que não esteja no glossário (também chamada de marca do usuário).

![Ativo de dados marcado com uma marca de usuário e duas de glossário](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Marcas do usuário são o único tipo de marca com suporte na Edição Gratuita do Catálogo de Dados.

### <a name="hover-behavior-on-tags"></a>Comportamento ao passar o mouse sobre marcas

No portal do Catálogo de Dados, os dois tipos de marcas são visualmente distintos e apresentam diferentes comportamentos de focalização. Quando você focaliza uma marca de usuário, pode ver o texto da marca e o usuário ou os usuários que adicionaram a marca. Quando você focaliza uma marca de glossário, também vê a definição do termo do glossário e um link para abrir o glossário de negócios para exibir a definição completa do termo.

### <a name="search-filters-for-tags"></a>Filtros de pesquisa para marcas

Marcas do usuário e marcas de glossário são pesquisáveis e você pode aplicá-las como filtros em uma pesquisa.

## <a name="summary"></a>Resumo

Usando o glossário de negócios no Catálogo de Dados do Azure e a marcação controlada que ele habilita, você pode identificar, gerenciar e descobrir ativos de dados de maneira consistente. O glossário de negócios pode promover o aprendizado do vocabulário de negócios pelos membros da organização. O glossário também dá suporte à captura de metadados significativos, o que simplifica a compreensão e a descoberta de ativos.

## <a name="next-steps"></a>Próximas etapas

* [Documentação da API REST para operações de glossário de negócios](/rest/api/datacatalog/data-catalog-glossary)

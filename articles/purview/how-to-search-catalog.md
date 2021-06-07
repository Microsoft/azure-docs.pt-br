---
title: Como pesquisar o catálogo de dados
description: Este artigo fornece uma visão geral de como pesquisar um catálogo de dados.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588404"
---
# <a name="search-the-azure-purview-data-catalog"></a>Pesquisar no Catálogo de Dados do Azure Purview

A descoberta de dados é a primeira etapa para uma análise de dados ou uma carga de trabalho de governança de dados para consumidores de dados. A descoberta de dados pode ser demorada porque você pode não saber onde encontrar os dados desejados. Mesmo depois de encontrar os dados, você pode ter dúvida sobre se você pode ou não confiar nos dados e assumir uma dependência dele.

O objetivo da pesquisa no Azure alcance é acelerar o processo de descoberta de dados para localizar rapidamente os dados que importa. Este artigo descreve como pesquisar o catálogo de dados do Azure alcance para localizar rapidamente os dados que você está procurando.

## <a name="search-the-catalog-for-assets"></a>Pesquisar ativos no catálogo

No Azure alcance, a barra de pesquisa está localizada na parte superior da UX do alcance Studio.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Captura de tela mostrando o local da barra de pesquisa do Azure alcance" border="true":::

Ao clicar na barra de pesquisa, você poderá ver seu histórico de pesquisa recente e os ativos acessados recentemente. Selecione "Exibir tudo" para ver todos os ativos exibidos recentemente.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Captura de tela mostrando a barra de pesquisa antes de qualquer palavra-chave ser inserida" border="true":::

Insira palavras-chave que ajudem a identificar seu ativo, como seu nome, tipo de dados, classificações e termos do glossário. À medida que você insere palavras-chave relacionadas ao ativo desejado, o Azure alcance exibe sugestões sobre o que pesquisar e possíveis correspondências de ativos. Para concluir a pesquisa, clique em "exibir resultados da pesquisa" ou pressione "Enter".

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Captura de tela mostrando a barra de pesquisa à medida que um usuário entra em palavras-chave" border="true":::

A página resultados da pesquisa mostra uma lista de ativos que correspondem às palavras-chave fornecidas em ordem de relevância. Há vários fatores que podem afetar a pontuação de relevância de um ativo. Você pode filtrar a lista mais a lado selecionando armazenamentos de dados, classificações, contatos, rótulos e termos do glossário específicos que se aplicam ao ativo que você está procurando.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Captura de tela mostrando os resultados de uma pesquisa" border="true":::

 Clique no ativo desejado para exibir a página detalhes do ativo onde você pode exibir as propriedades, incluindo o esquema, a linhagem e os proprietários do ativo.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Captura de tela mostrando a página de detalhes do ativo" border="true":::

## <a name="search-query-syntax"></a>Sintaxe de consulta de pesquisa

Todas as consultas de pesquisa consistem em palavras-chave e operadores. Uma palavra-chave é algo que faria parte das propriedades de um ativo. Palavras-chave potenciais podem ser uma classificação, termo do glossário, descrição do ativo ou um nome de ativo. Uma palavra-chave pode ser apenas uma parte da propriedade à qual você está procurando fazer a correspondência. Use palavras-chave e os operadores listados abaixo para garantir que o Azure alcance retorne os ativos que você está procurando. 

Abaixo estão os operadores que podem ser usados para compor uma consulta de pesquisa. Os operadores podem ser combinados quantas vezes forem necessárias em uma única consulta.

| Operador | Definição | Exemplo |
| -------- | ---------- | ------- |
| OU | Especifica que um ativo deve ter pelo menos uma das duas palavras-chave. Deve estar em todas as maiúsculas. Um espaço em branco também é um operador OR.  | A consulta `hive OR database` retorna ativos que contêm ' Hive ' ou ' database ' ou ambos. |
| AND | Especifica que um ativo deve ter ambas as palavras-chave. Deve estar em todas as maiúsculas | A consulta `hive AND database` retorna ativos que contêm ' Hive ' e ' database '. |
| NOT | Especifica que um ativo não pode conter a palavra-chave à direita da cláusula NOT | A consulta `hive NOT database` retorna ativos que contêm ' Hive ', mas não ' database '. |
| () | Agrupa um conjunto de palavras-chave e operadores juntos. Ao combinar vários operadores, os parênteses especificam a ordem das operações. | A consulta `hive AND (database OR warehouse)` retorna ativos que contêm ' Hive ' e ' database ' ou ' warehouse ', ou ambos. |
| "" | Especifica o conteúdo exato em uma frase à qual a consulta deve corresponder. | A consulta `"hive database"` retorna ativos que contêm a frase "banco de dados do hive" em suas propriedades |
| * | Um curinga que corresponde a um ou vários caracteres. Não pode ser o primeiro caractere em uma palavra-chave. | A consulta `hiv\` * retorna ativos que têm propriedades que começam com ' HIV ', como ' Hive ' ou ' Hive-Table '. |
| ? | Um curinga que corresponde a um único caractere. Não pode ser o primeiro caractere em uma palavra-chave | A consulta `hiv?` retorna ativos que têm propriedades que começam com ' HIV ' e têm quatro letras, como ' Hive ' ou ' Hiva '. |

> [!Note]
> Sempre especifique operadores boolianos (**e**, **ou**, **não**) em maiúsculas. Caso contrário, não importa o caso nem espaços extras.

## <a name="next-steps"></a>Próximas etapas

- [Como criar, importar e exportar os termos do glossário](how-to-create-import-export-glossary.md)
- [Como gerenciar modelos de termo para o Glossário de negócios](how-to-manage-term-templates.md)

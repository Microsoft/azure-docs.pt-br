---
title: Como anotar fontes de dados no Catálogo de Dados do Azure
description: Artigo de instruções que destaca como anotar ativos de dados no Catálogo de Dados do Azure, incluindo nomes amigáveis, marcas, descrições e especialistas.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 3a66c8c5963972828723dd74ffe560a0e2240165
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001929"
---
# <a name="how-to-annotate-data-sources"></a>Como anotar fontes de dados
## <a name="introduction"></a>Introdução
**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o Catálogo de Dados ajuda as pessoas a descobrir, entender e usar fontes de dados, ajudando as empresas a obter mais valor de seus dados existentes. Quando uma fonte de dados é registrada no Catálogo de Dados, seus metadados são copiados e indexados pelo serviço, mas a história não para por aí. O Catálogo de Dados permite que os usuários forneçam seus próprios metadados descritivos – como descrições e marcas – para complementar os metadados extraídos da fonte de dados e fazer com que a fonte de dados fique mais compreensível para mais pessoas.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todo mundo tem uma opinião. E isso é bom.
O Catálogo de Dados reconhece que diferentes usuários têm diferentes perspectivas em fontes de dados empresariais e cada uma dessas perspectivas pode ser valiosa. Considere este cenário:

* O administrador do sistema conhece o contrato de nível de serviço para os servidores ou serviços que hospedam a fonte de dados.
* O administrador de banco de dados conhece a agenda de backup de cada banco de dados e as janelas de processamento de ETL permitidas.
* O proprietário do sistema conhece o processo para que usuários solicitem acesso à fonte de dados.
* O administrador de dados sabe como os ativos e os atributos na fonte de dados são mapeados para o modelo de dados da empresa.
* O analista sabe como os dados são usados no contexto dos processos de negócios aos quais ele oferece suporte.

Cada uma dessas perspectivas é valiosa, e o Catálogo de Dados usa uma abordagem de crowdsourcing para metadados que permitem que cada um seja capturado e usado para fornecer uma visão completa das fontes de dados registradas. Usando o portal do Catálogo de Dados, cada usuário pode adicionar e editar suas próprias anotações, sendo capaz de exibir anotações fornecidas por outros usuários.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
O Catálogo de Dados dá suporte aos seguintes tipos de anotações:

| Anotação | Observações |
| --- | --- |
| Nome amigável |Os nomes amigáveis podem ser fornecidos no nível do ativo de dado, para facilitar a compreensão dos ativos de dados. Os nomes amigáveis serão mais úteis quando o nome do objeto subjacente for confuso, abreviado ou não significativa para os usuários. |
| DESCRIÇÃO |As descrições podem ser fornecidas nos níveis do atributo e do ativo de dados/coluna. As descrições são anotações de texto curto de forma livre que descrevem a perspectiva do usuário sobre o ativo de dados ou seu uso. |
| Marcas (marcas de usuário) |As marcas podem ser fornecidas nos níveis do atributo e do ativo de dados/coluna As marcas de usuário são rótulos definidos pelo usuário que podem ser usados para categorizar os ativos de dados ou os atributos. |
| Marcas (marcas de glossário) |As marcas podem ser fornecidas nos níveis do atributo e do ativo de dados/coluna Marcas de glossário são termos de glossário definidos centralmente que podem ser usados para categorizar os ativos de dados ou atributos usando uma taxonomia comum de negócios. Para saber mais, consulte [Como configurar o Glossário de Negócios para a Marcação Governada](data-catalog-how-to-business-glossary.md) |
| Especialistas |Os especialistas podem ser fornecidos no nível do ativo de dados. Os especialistas identificam usuários ou grupos com perspectivas de especialista sobre os dados e podem servir como pontos de contato para usuários que descobrirem as fontes de dados registradas e que tenham dúvidas não respondidas pelas anotações existentes. |
| Solicitar acesso |As informações sobre a solicitação de acesso podem ser fornecidas no nível do ativo de dados. Essas informações destinam-se aos usuários que descobrirem uma fonte de dados que ainda não tenham permissões para acessar. Os usuários podem inserir o endereço de email do usuário ou grupo que concede acesso, a URL do processo ou a ferramenta que os usuários precisam acessar, ou podem inserir o próprio processo como texto. |
| Documentação |Fornecemos documentação no nível do ativo de dados. A documentação de ativos é informação em rich text que pode incluir links e imagens e fornecer todas as informações que não são transmitidas por meio de marcações e descrições. |

## <a name="annotating-multiple-assets"></a>Anotando vários ativos
Ao selecionarem vários ativos de dados no portal do Catálogo de Dados, os usuários poderão anotar todos os ativos em uma única operação. As anotações serão aplicadas a todos os recursos selecionados, facilitando a seleção e o fornecimento de uma descrição consistente e conjuntos de marcas e de especialistas para ativos de dados relacionados.

> [!NOTE]
> Marcas e especialistas também podem ser fornecidos durante o registro de ativos de dados por meio da ferramenta de registro de fontes de dados do Catálogo de Dados.
>
>

Quando várias tabelas e exibições forem selecionadas, somente as colunas que todos os ativos de dados selecionados tiverem em comum serão exibidas no portal do Catálogo de Dados. Isso permite que os usuários forneçam marcas e descrições de todas as colunas com o mesmo nome para todos os ativos selecionados.

## <a name="annotations-and-discovery"></a>Anotações e descoberta
Assim como os metadados extraídos da fonte de dados durante o registro são adicionados ao índice de pesquisa do Catálogo de Dados, os metadados fornecidos pelo usuário também são indexados. Isso significa que não só as anotações fazem com que seja mais fácil para os usuários compreenderem os dados descobertos, como as anotações também facilitam a descoberta dos ativos de dados anotados por meio da pesquisa com os termos que fazem sentido para eles.

## <a name="summary"></a>Resumo
O registro de uma fonte de dados no Catálogo de Dados torna os dados descobríveis ao copiar metadados estruturais e descritivos da fonte de dados para o serviço Catálogo. Depois que uma fonte de dados tiver sido registrada, os usuários poderão fornecer anotações para facilitar a descoberta e a compreensão de dentro do portal do Catálogo de Dados.

## <a name="see-also"></a>Consulte também
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md) para obter detalhes passo a passo sobre como anotar fontes de dados.

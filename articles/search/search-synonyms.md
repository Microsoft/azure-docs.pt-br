---
pageTitle: Synonyms in Azure Search (preview) | Microsoft Docs
description: "Documentação preliminar para o recurso de sinônimos (versão prévia), exposto na API REST do Azure Search."
services: search
documentationCenter: 
authors: mhko
manager: pablocas
editor: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/07/2016
ms.author: nateko
ms.openlocfilehash: 739a0ad77c68ea74ec25bc80c7539ac8b3f18201
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="synonyms-in-azure-search-preview"></a>Sinônimos no Azure Search (versão prévia)

Sinônimos nos termos equivalentes associados do mecanismo de pesquisa que expandem implicitamente o escopo de uma consulta, sem que o usuário tenha de fornecer realmente o termo. Por exemplo, considerando o termo "cão" e as associações de sinônimo de "canino" e "filhote de cão", qualquer documento contendo "cão", "canino" ou "filhote de cão" cairá dentro do escopo da consulta.

No Azure Search, a expansão do sinônimo é feita no momento da consulta. Você pode adicionar mapas de sinônimo a um serviço sem interromper as operações existentes. Você pode adicionar uma propriedade **synonymMaps** a uma definição de campo sem necessidade de recriar o índice. Consulte [Atualizar Índice](https://docs.microsoft.com/rest/api/searchservice/update-index) para obter mais informações.

## <a name="feature-availability"></a>Disponibilidade de recursos

O recurso de sinônimos está atualmente em versão prévia e só tem suporte na última api-version da versão prévia (api-version=2016-09-01-Preview). Não há suporte do portal do Azure no momento. Como a versão da API é especificada na solicitação, é possível combinar APIs de versão prévia e disponibilidade geral (GA) no mesmo aplicativo. No entanto, as APIs de versão prévia não estão no SLA e os recursos podem mudar, portanto, não recomendamos usá-las em aplicativos de produção.

## <a name="how-to-use-synonyms-in-azure-search"></a>Como usar sinônimos no Azure Search

No Azure Search, suporte a sinônimos baseia-se em mapas de sinônimo que você define e carrega em seu serviço. Esses mapas constituem um recurso independente (como índices ou fontes de dados) e podem ser usados por qualquer campo pesquisável em um índice do serviço de pesquisa.

Mapas de sinônimo e índices são mantidos de maneira independente. Depois de definir um mapa de sinônimo e carregá-lo em seu serviço, habilite o recurso de sinônimo em um campo, adicionando uma nova propriedade chamada **synonymMaps** à definição de campo. Criar, atualizar e excluir um mapa de sinônimo é sempre uma operação de documento inteiro, o que significa que você não pode criar, atualizar nem excluir partes do mapa sinônimo incrementalmente. Até mesmo uma única entrada a atualização requer um recarregamento.

A incorporação de sinônimos ao seu aplicativo de pesquisa é um processo de duas etapas:

1.  Adicione um mapa de sinônimos ao serviço de pesquisa por meio das APIs a seguir.  

2.  Configure um campo de pesquisa para usar o mapa de sinônimos na definição do índice.

### <a name="synonymmaps-resource-apis"></a>APIs do recurso SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adicione ou atualize um mapa de sinônimos em seu serviço, usando POST ou PUT.

Mapas de sinônimos são carregados no serviço via POST ou PUT. Cada regra deve ser delimitada por um caractere de nova linha ('\n'). É possível definir até 5.000 regras por mapa de sinônimos em um serviço gratuito e 10.000 regras em todas as outras SKUs. Cada regra pode ter até 20 expansões.

Nesta versão prévia, os mapas de sinônimos devem estar no formato Apache Solr explicado abaixo. Se você tiver um dicionário de sinônimos em um formato diferente e quiser usá-lo diretamente, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Crie um novo mapa de sinônimos usando HTTP POST, como no exemplo a seguir:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Como alternativa, use PUT e especifique o nome do mapa de sinônimos no URI. Se o mapa de sinônimos não existir, ele será criado.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato de sinônimo Apache Solr

O formato Solr dá suporte a mapeamentos de sinônimo equivalentes e explícitos. Regras de mapeamento são compatíveis com a especificação de filtro de sinônimo de software livre do Apache Solr, descritos neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Abaixo está um exemplo de regra de sinônimos equivalentes.
```
              USA, United States, United States of America
```

Com a regra acima, uma consulta de pesquisa "EUA" será expandida para "EUA" OR "Estados Unidos" OR "Estados Unidos da América".

Mapeamento explícito é indicado por uma seta "=>". Quando especificado, uma sequência de termos de uma consulta de pesquisa que corresponda à esquerda de "=>" será substituída com as alternativas do lado direito. Dada a regra abaixo, consultas de pesquisa "Washington", "Wash." ou "WA" serão regravadas como "WA". Mapeamento explícito só é aplicável na direção especificada e não regrava a consulta "WA" para "Washington" nesse caso.
```
              Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Liste os mapas de sinônimos em seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obtenha um mapa de sinônimos em seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Exclua um mapa de sinônimos em seu serviço.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configure um campo de pesquisa para usar o mapa de sinônimos na definição do índice.

Uma nova propriedade de campo **synonymMaps** pode ser usada a fim de especificar um mapa de sinônimos para usar em um campo pesquisável. Mapas de sinônimos são recursos de nível de serviço e podem ser consultados por qualquer campo de um índice no serviço.

    POST https://[servicename].search.windows.net/indexes?api-version=2016-09-01-Preview
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** pode ser especificado para campos de pesquisa do tipo “Edm.String” ou “Collection(Edm.String)”.

> [!NOTE]
> Nesta versão prévia, só pode haver um mapa de sinônimos por campo. Se você quiser usar vários mapas de sinônimo, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impacto de sinônimos em outros recursos de pesquisa

O recurso de sinônimos regrava a consulta original com sinônimos com o operador OR. Por esse motivo, os perfis de destaque e pontuação acessados tratam o termo original e os sinônimos como equivalentes.

O recurso de sinônimo aplica-se a consultas de pesquisa e não se aplicam a filtros ou facetas. Da mesma forma, sugestões baseiam-se apenas no termo original; correspondências de sinônimo não aparecem na resposta.

Expansões de sinônimo não se aplicam a termos de pesquisa de curinga; prefixo, lógica difusa e termos de regex não são expandidos.

## <a name="tips-for-building-a-synonym-map"></a>Dicas para criação de um mapa de sinônimos

- Um mapa de sinônimos conciso e bem projetado é mais eficiente do que uma lista de possíveis correspondências. Dicionários excessivamente grandes ou complexos demorarão mais para serem analisados e afetarão a latência se a consulta expandir muitos sinônimos. Em vez de adivinhar o termo a ser usado, você pode obter os termos reais por meio de um [relatório de análise de tráfego de pesquisa](search-traffic-analytics.md).

- Como um exercício preliminar e de validação, habilite e use esse relatório para determinar exatamente quais termos se beneficiarão de uma correspondência de sinônimo e continue a usá-lo como validação de que o mapa de sinônimos está produzindo resultado melhor. No relatório predefinido, os blocos "Consultas de pesquisa mais comuns" e "Consultas de pesquisa de resultado zero" fornecerão a você as informações necessárias.

- Você pode criar vários mapas de sinônimos para o aplicativo de pesquisa (por exemplo, por idioma, se seu aplicativo dá suporte a uma base de clientes com vários idiomas). Atualmente, um campo só pode usar um deles. Atualize a propriedade synonymMaps de um campo a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

- Se houver um índice em um ambiente de desenvolvimento (não produção), experimente um dicionário pequeno para ver como a adição de sinônimos altera a experiência de pesquisa, incluindo o impacto nos perfis de pontuação, o realce de acesso e as sugestões.

- [Habilite a análise de tráfego de pesquisa](search-traffic-analytics.md) e use o relatório do Power BI predefinido para saber quais termos são os mais usados e quais retornam zero documentos. Armado com essa visão, revise o dicionário para incluir sinônimos de consultas improdutivas que devem ser resolvidas para documentos no índice.

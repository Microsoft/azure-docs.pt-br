---
title: Método de Interpretação – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o método Interpretar para retornar interpretações formatadas de cadeia de caracteres de consulta do usuário com base em dados de Grafo Acadêmico e Gramática Acadêmica nos Serviços Cognitivos da Microsoft.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: b679f1da0ada3e61fca79cdb985a43dc445877ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61338445"
---
# <a name="interpret-method"></a>Método Interpretar

A API REST **interpretar** captura uma cadeia de caracteres de consulta do usuário final (ou seja, uma consulta inserida por um usuário do aplicativo) e retorna interpretações formatadas da intenção do usuário com base nos dados de Grafo Acadêmico e Gramática Acadêmica.

Para fornecer uma experiência interativa, é possível chamar esse método repetidamente após cada caractere inserido pelo usuário. Nesse caso, é necessário definir o parâmetro **completo** como 1 para habilitar as sugestões de preenchimento automático. Se o aplicativo não precisar de preenchimento automático, será necessário definir o parâmetro **completo** para 0.

**Ponto de extremidade de REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parâmetros de solicitação

NOME     | Value | Obrigatório?  | DESCRIÇÃO
---------|---------|---------|---------
**query**    | Cadeia de caracteres de texto | Sim | Consulta inserida pelo usuário.  Se o parâmetro completo estiver definido como 1, a consulta será interpretada como um prefixo para gerar sugestões de preenchimento automático de consulta.        
**modelo**    | Cadeia de caracteres de texto | Não   | Nome do modelo que você quer consultar.  Atualmente, o valor padrão é o *mais recente*.        
**completo** | 0 ou 1 | Não <br>default:0  | 1 significa que as sugestões de preenchimento automático são geradas com base nos dados de grafo e gramática.         
**count**    | Número | Não <br>default:10 | Número máximo de interpretações para retornar.         
**offset**   | Número | Não <br>default:0  | Índice da primeira interpretação para retornar. Por exemplo, *count=2&offset=0* retorna as interpretações 0 e 1. *count=2&offset=2* retorna interpretações 2 e 3.       
**tempo limite**  | Número | Não <br>default:1000 | Tempo limite em milissegundos. Somente interpretações localizadas antes que o tempo limite tenha decorrido serão retornadas.

<br>
  
## <a name="response-json"></a>Resposta (JSON)

NOME     | DESCRIÇÃO
---------|---------
**query** |O parâmetro *query* da solicitação.
**interpretations** |Uma matriz de 0 ou mais maneiras diferentes de corresponder a entrada do usuário à gramática.
**interpretations[x].logprob**  |A probabilidade de log natural relativa da interpretação. Valores maiores são mais prováveis.
**interpretations[x].parse**  |Uma cadeia de caracteres XML que mostra como cada parte da consulta foi interpretada.
**interpretations[x].rules**  |Uma matriz de 1 ou mais regras definidas na gramática que foram invocadas durante a interpretação. Para a API de Conhecimento Acadêmico, sempre haverá 1 regra.
**interpretations[x].rules[y].name**  |Nome da regra.
**interpretations[x].rules[y].output**  |Saída da regra.
**interpretations[x].rules[y].output.type** |O tipo de dados da saída da regra.  Para a API de Conhecimento Acadêmico, isso sempre será "query".
**interpretations[x].rules[y].output.value**  |A saída da regra. Para a API de Conhecimento Acadêmico, trata-se de uma cadeia de caracteres de expressão de consulta que pode ser passada para os métodos calchistogram e avaliar.
**anulado** | Verdadeiro se a solicitação atingiu o tempo limite.

<br>

#### <a name="example"></a>Exemplo:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>A resposta abaixo contém as duas principais interpretações mais prováveis (por causa do parâmetro *count=2*) que completam a entrada parcial do usuário *artigos de jaime*: *artigos de jaime teevan* e *artigos de jaime green*.  O serviço gerou consultas concluídas em vez de considerar apenas correspondências exatas para o autor *jaime* porque a solicitação especificou *complete=1*. Observe que o valor canônico *j l green* foi correspondido por meio do sinônimo *jamie green*, conforme indicado na análise.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Para recuperar os resultados da entidade para uma interpretação, use *output.value* da API **interpretar** e passe isso para a API **avaliar** API via o parâmetro *expr*. Neste exemplo, a consulta para a primeira interpretação é: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 

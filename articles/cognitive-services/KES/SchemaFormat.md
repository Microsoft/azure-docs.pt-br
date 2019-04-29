---
title: Formato de esquema - API do Serviço de Exploração de Conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o formato de esquema na API de KES (Serviço de Exploração de Conhecimento).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814405"
---
# <a name="schema-format"></a>Formato de esquema

O esquema é especificado em um arquivo JSON que descreve a estrutura de atributo dos objetos no arquivo de dados usado para criar o índice.  Para cada atributo, o esquema especifica o nome, tipo de dados, operações opcionais e lista opcional de sinônimos.  Um objeto pode ter 0 ou mais valores de cada atributo.  Abaixo está um exemplo simplificado de um domínio de publicações acadêmicas:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Nomes de atributo são identificadores de maiúsculas e minúsculas que começam com uma letra e consistem somente em letras (A-Z), números (0-9) e sublinhado (\_).  O atributo reservado "logprob" é usado para especificar as probabilidades de log natural relativa entre os objetos.

## <a name="attribute-type"></a>Tipo de Atributo

Abaixo está uma lista de tipos de dados de atributo com suporte:

| Type | DESCRIÇÃO | Operações | Exemplo |
|------|-------------|------------|---------|
| `String` | Cadeia de caracteres (1 a 1024 caracteres) | equals, starts_with | “Olá Mundo” |
| `Int32` | Inteiro assinado de 32 bits | equals, starts_with, is_between | 2016 |
| `Int64` | Inteiro assinado de 64 bits | equals, starts_with, is_between | 9876543210 |
| `Double` | Valor de ponto flutuante de precisão dupla | equals, starts_with, is_between | 1.602e-19 |
| `Date` | Data (1400-01-01 a 9999-12-31) | equals, is_between | ‘2016-03-14’ |
| `Guid` | Identificador Global Exclusivo | equals | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | Dados não indexados internamente compactados | *Nenhum* | "Capacitar cada pessoa e cada organização do planeta para atingir mais" |
| `Composite` | Composição de vários sub-atributos| *N/D* | { "Name":"harry shum", "Affiliation":"microsoft" } |

Atributos de cadeia de caracteres são usados para representar valores de cadeia de caracteres que podem aparecer como parte da consulta do usuário.  Eles oferecem suporte a correspondência exata da operação *equals*, bem como da operação *starts_with* para cenários de conclusão de consulta, como a correspondência de "micros" com "microsoft".  Sem distinção entre maiúsculas e minúsculas e correspondência difusa para lidar com erros de ortografia terão suporte em uma versão futura.

Atributos Int64/Int32/Double são usados para representar valores numéricos.  A operação *is_between* habilita o suporte de desigualdade (lt, le, gt, ge) em tempo de execução.  A operação *starts_with* oferece suporte a cenários de preenchimento de consulta, como a correspondência de "20" com "2016" ou "3". com "3.14".

Atributos de data são usados para codificar com eficiência os valores de data.  A operação *is_between* habilita o suporte de desigualdade (lt, le, gt, ge) em tempo de execução.
  
Atributos GUID são usados para representar com eficiência os valores GUID com suporte de padrão para a operação *equals*.

Atributos de blob são usados para codificar com eficiência os blobs de dados potencialmente grandes para pesquisa de tempo de execução do objeto correspondente, sem suporte para qualquer operação de indexação com base no conteúdo dos valores de blob.

### <a name="composite-attributes"></a>Atributos compostos

Atributos compostos são usados para representar um agrupamento de valores de atributo.  O nome de cada sub-atributo começa com o nome do atributo composto seguido por ".".  Valores de atributos compostos são especificados como um objeto JSON que contém os valores de atributo aninhado.  Atributos compostos podem ter vários valores de objeto.  No entanto, atributos compostos não podem ter sub-atributos que são atributos compostos.

No exemplo das publicações acadêmicas acima, isso permite que o serviço consulte artigos de "harry shum" enquanto ele está em "microsoft".  Sem atributos compostos, o serviço só pode consultar artigos em que um dos autores é "harry shum" e um dos autores é "Microsoft".  Para obter mais informações, consulte [Consultas compostas](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Operações de atributo

Por padrão, cada atributo está indexado para dar suporte a todas as operações disponíveis para o tipo de dados do atributo.  Se uma determinada operação não for necessária, o conjunto de operações indexadas pode ser especificado explicitamente para reduzir o tamanho do índice.  No snippet a seguir do esquema de exemplo acima, o atributo Author.Id é indexado para dar suporte a apenas a operação *equals*, mas não as operações adicionais *starts_with* e *is_between*  para atributos de Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Quando um atributo é referenciado dentro de uma gramática, a operação *starts_with* precisa ser especificada no esquema para o serviço gerar as conclusões de consultas parciais.  

## <a name="attribute-synonyms"></a>Sinônimos de atributo

Geralmente é desejável para se referir a um valor de atributo de cadeia de caracteres específica por um sinônimo.  Por exemplo, os usuários podem se referir a "Microsoft" como "MSFT" ou "MS".  Nesses casos, a definição do atributo pode especificar o nome de um arquivo de esquema localizado no mesmo diretório que o arquivo de esquema.  Cada linha no arquivo de sinônimo representa uma entrada de sinônimo no seguinte formato JSON: `["<canonical>", "<synonym>"]`.  No esquema de exemplo, "AuthorName.syn" é um arquivo JSON que contém valores de sinônimo para o atributo Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Um valor canônico pode ter vários sinônimos.  Vários valores canônicos podem compartilhar um sinônimo comum.  Nesses casos, o serviço resolverá a ambiguidade por meio de vários interpretações.  Veja abaixo um exemplo de um arquivo de sinônimos AuthorName.syn correspondente para o esquema anterior:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```

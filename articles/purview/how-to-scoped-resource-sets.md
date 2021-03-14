---
title: Como criar uma configuração de conjunto de recursos com escopo
description: Saiba como criar uma regra de configuração de conjunto de recursos com escopo para substituir como os ativos são agrupados em conjuntos de recursos
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 10e925a84dbe187ccdf5e444cb8b3dd4b7bb4676
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607995"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Criar regras de configuração do conjunto de recursos no escopo

Normalmente, os sistemas de processamento de dados em escala armazenam uma única tabela em um disco como vários arquivos. Esse conceito é representado no Azure alcance usando conjuntos de recursos. Um conjunto de recursos é um único objeto no catálogo de dados que representa um grande número de ativos no armazenamento. Para saber mais, confira [noções básicas sobre conjuntos de recursos](concept-resource-sets.md).

Ao verificar uma conta de armazenamento, o Azure alcance usa um conjunto de padrões definidos para determinar se um grupo de ativos é um conjunto de recursos. Em alguns casos, o agrupamento do conjunto de recursos do Azure alcance pode não refletir seu espaço de dados com precisão. As regras do conjunto de recursos com escopo permitem que você personalize ou substitua como o Azure alcance detecta quais ativos são agrupados como conjuntos de recursos e como eles são exibidos no catálogo.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Como criar uma configuração de conjunto de recursos com escopo

Siga as etapas abaixo para criar uma nova configuração de conjunto de recursos com escopo:

1. Vá para o centro de gerenciamento. Selecione **conjuntos de recursos com escopo** no menu. Selecione **+ novo** para criar um novo conjunto de regras de configuração.

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Criar nova regra de conjunto de recursos no escopo" border="true":::

1. Insira o escopo da configuração do conjunto de recursos com escopo definido. Selecione o tipo de conta de armazenamento e o nome da conta de armazenamento na qual você deseja criar um conjunto de regras. Cada conjunto de regras é aplicado em relação a um escopo de caminho de pasta especificado no campo **caminho da pasta** .

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Criar configurações do conjunto de recursos no escopo" border="true":::

1. Para inserir uma regra para um escopo de configuração, selecione **+ nova regra**.

1. Insira os campos a seguir para criar uma regra:

   1. **Nome da regra:** O nome da regra de configuração. Esse campo não tem nenhum efeito sobre os ativos aos quais a regra se aplica.

   1. **Nome qualificado:** Um caminho qualificado que usa uma combinação de texto, realocadores dinâmicos e realocadores estáticos para corresponder ativos à regra de configuração. Esse caminho é relativo ao escopo da regra de configuração. Consulte a seção [sintaxe](#syntax) abaixo para obter instruções detalhadas sobre como especificar nomes qualificados.

   1. **Nome para exibição:** O nome de exibição do ativo. Esse campo é opcional. Use texto sem formatação e substitutos estáticos para personalizar como um ativo é exibido no catálogo. Para obter instruções mais detalhadas, consulte a seção [sintaxe](#syntax) abaixo.

   1. Não **Agrupar como conjunto de recursos:** Se habilitada, o recurso correspondente não será agrupado em um conjunto de recursos.

      :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Criar nova regra de configuração." border="true":::

1. Salve a regra clicando em **Adicionar**.

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Sintaxe do conjunto de recursos com escopo

Ao criar regras de conjunto de recursos no escopo, use a sintaxe a seguir para especificar a quais regras de ativos se aplicam.

### <a name="dynamic-replacers-single-brackets"></a>Realocadores dinâmicos (colchetes únicos)

Os colchetes únicos são usados como **realocadores dinâmicos** em uma regra de conjunto de recursos com escopo definido. Especifique um reposicionamento dinâmico no nome qualificado usando o formato `{<replacerName:<replacerType>}` . Se houver correspondência, os realocadores dinâmicos serão usados como uma condição de agrupamento que indicará que os ativos devem ser representados como um conjunto de recursos. Se os ativos forem agrupados em um conjunto de recursos, o caminho qualificado do conjunto de recursos conterá `{replacerName}` onde o replacer foi especificado.

Por exemplo, se dois ativos `folder1/file-1.csv` e `folder2/file-2.csv` corresponderem à regra `{folder:string}/file-{NUM:int}.csv` , o conjunto de recursos será uma única entidade `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Caso especial: realocadores dinâmicos quando não são agrupados no conjunto de recursos

Se *não agrupar como conjunto de recursos* estiver habilitado para uma regra de conjunto de recursos com escopo, o nome do realocador será um campo opcional. `{:<replacerType>}` é uma sintaxe válida. Por exemplo, `file-{:int}.csv` corresponderia com êxito para `file-1.csv` e `file-2.csv` criaria dois ativos diferentes em vez de um conjunto de recursos.

### <a name="static-replacers-double-brackets"></a>Realocadores estáticos (colchetes duplos)

Os colchetes duplos são usados como **realocadores estáticos** no nome qualificado de uma regra de conjunto de recursos com escopo definido. Especifique um recolocador estático no nome qualificado usando o formato `{{<replacerName>:<replacerType>}}` . Se houver correspondência, cada conjunto de valores de realocador estáticos exclusivo criará agrupamentos de conjuntos de recursos diferentes.

Por exemplo, se dois ativos `folder1/file-1.csv` e `folder2/file-2.csv` corresponderem à regra `{{folder:string}}/file-{NUM:int}.csv` , dois conjuntos de recursos serão criados `folder1/file-{NUM}.csv` e `folder2/file-{NUM}.csv` .

Os realocadores estáticos podem ser usados para especificar o nome de exibição de uma correspondência de ativo com uma regra de conjunto de recursos com escopo definido. Usar `{{<replacerName>}}` no nome de exibição de uma regra usará o valor correspondente no nome do ativo.

### <a name="available-replacement-types"></a>Tipos de substituição disponíveis

Abaixo estão os tipos disponíveis que podem ser usados em realocadores estáticos e dinâmicos:

| Tipo | Estrutura |
| ---- | --------- |
| string | Uma série de 1 ou mais caracteres Unicode, incluindo delimitadores como espaços. |
| INT | Uma série de 1 ou mais caracteres ASCII de 0-9, pode ser 0 prefixado (por exemplo, 0001). |
| guid | Uma série de 32 ou 8-4-4-4-12 representação de cadeia de caracteres de um UUID como defineddefa no [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| date | Uma série de 6 ou 8 0-9 caracteres ASCII com separadores opcionalmente: AAAAMMDD, aaaa-mm-dd, YYMMDD, aa-mm-dd, especificado na [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| time | Uma série de 4 ou 6 0-9 caracteres ASCII com separadores opcionalmente: HHmm, HH: mm, HHmmss, HH: mm: SS especificado em [RFC 3339](https://tools.ietf.org/html/rfc3339). |
|  timestamp | Uma série de 12 ou 14 0-9 caracteres ASCII com separadores opcionalmente: AAAA-MM-ddTHH: mm, AAAAMMDDHHMM, aaaa-MM-ddTHH: mm: SS, AAAAMMDDHHMMSS especificado na [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| booleano | Pode conter ' true ' ou ' false ', não diferencia maiúsculas de minúsculas. |
| número | Uma série de 0 ou mais caracteres ASCII de 0-9, pode ser 0 prefixado (por exemplo, 0001) seguido por um ponto '. ' e uma série de 1 ou mais caracteres ASCII de 0-9, pode ser 0 (por exemplo, 100) |
| hex | Uma série de 1 ou mais caracteres ASCII do conjunto de 0-1 e a-F, o valor pode ser 0 prefixado |
| localidade | Uma cadeia de caracteres que corresponde à sintaxe especificada no [RFC 5646](https://tools.ietf.org/html/rfc5646). |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>Ordem das regras de conjunto de recursos no escopo sendo aplicadas

Abaixo está a ordem das operações para aplicar regras de conjunto de recursos com escopo:

1. Escopos mais específicos terão prioridade se um ativo corresponder a duas regras. Por exemplo, as regras em um escopo `container/folder` serão aplicadas antes das regras no escopo `container` .

1. Ordem das regras em um escopo específico. Isso pode ser editado na UX.

1. Se um ativo não corresponder a nenhuma regra especificada, a heurística do conjunto de recursos padrão será aplicada.

## <a name="examples"></a>Exemplos

### <a name="example-1"></a>Exemplo 1

Extração de dados do SAP em cargas completas e deltas

#### <a name="inputs"></a>Entradas

Arquivos:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="scoped-resource-set-rule"></a>Regra do conjunto de recursos com escopo

**Escopo:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome para exibição:** ' Cliente externo '

**Nome qualificado:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Conjunto de recursos:** verdadeiro

#### <a name="output"></a>Saída

Um ativo de conjunto de recursos

**Nome para exibição:** Cliente externo

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Exemplo 2

Dados de IoT no formato Avro

#### <a name="inputs"></a>Entradas

Arquivos:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rules"></a>Regras do conjunto de recursos com escopo

**Escopo:**`https://myazureblob.blob.core.windows.net/bar/`

Regra 1

**Nome para exibição:** ' Machine-89 '

**Nome qualificado:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Conjunto de recursos:** verdadeiro

Regra 2

**Nome para exibição:** ' Machine-90 '

**Nome qualificado:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*Conjunto de recursos: verdadeiro*

#### <a name="outputs"></a>Saídas

2 conjuntos de recursos

Conjunto de recursos 1

**Nome de exibição:** máquina-89

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Conjunto de recursos 2

**Nome de exibição:** máquina-90

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Exemplo 3

Dados de IoT no formato Avro

#### <a name="inputs"></a>Entradas

Arquivos:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Regra do conjunto de recursos com escopo

**Escopo:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome para exibição:** ' Computador-{{machineid}} '

**Nome qualificado:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Conjunto de recursos:** verdadeiro

#### <a name="outputs"></a>Saídas

Conjunto de recursos 1

**Nome de exibição:** máquina-89

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Conjunto de recursos 2

**Nome de exibição:** máquina-90

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Exemplo 4

Não agrupar em conjuntos de recursos

#### <a name="inputs"></a>Entradas

Arquivos:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Regra do conjunto de recursos com escopo

**Escopo:**`https://myazureblob.blob.core.windows.net/bar/`

**Nome para exibição:**`Machine-{{machineid}}`

**Nome qualificado:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Conjunto de recursos:** falso

#### <a name="outputs"></a>Saídas

4 ativos individuais

Ativo 1

**Nome de exibição:** máquina-89

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Ativo 2

**Nome de exibição:** máquina-89

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Ativo 3

**Nome de exibição:** máquina-89

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Ativo 4

**Nome de exibição:** máquina-90

**Nome qualificado:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Próximas etapas

Comece [registrando e verificando uma conta de armazenamento Azure data Lake Gen2](register-scan-adls-gen2.md).
---
title: Entender o repositório de valor-chave de configuração Azure App
description: Entenda o armazenamento de chave-valor na configuração Azure App, que armazena os dados de configuração como valores de chave. Os valores de chave são uma representação das configurações do aplicativo.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: ad9e96433a7ee72476ae2251c684d17ec7a6d1ce
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930238"
---
# <a name="keys-and-values"></a>Chaves e valores

Azure App configuração armazena dados de configuração como valores de chave. Os valores-chave são uma representação simples e flexível das configurações do aplicativo usadas pelos desenvolvedores.

## <a name="keys"></a>simétricas

As chaves servem como identificadores para valores de chave e são usadas para armazenar e recuperar valores correspondentes. É uma prática comum organizar chaves em um namespace hierárquico usando um caractere delimitador, como `/` ou `:`. Use uma convenção mais adequada para seu aplicativo. A Configuração de Aplicativo trata as chaves como um todo. Ela não analisa as chaves para descobrir como seus nomes são estruturados nem impõem regras a elas.

Aqui está um exemplo de nomes de chave estruturados em uma hierarquia baseada em serviços de componentes:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

O uso de dados de configuração em estruturas de aplicativo pode ditar esquemas de nomenclatura específicos para valores de chave. Por exemplo, a estrutura Spring Cloud do Java define `Environment` recursos que fornecem configurações para um aplicativo Spring.  Eles são parametrizados por variáveis que incluem o *nome do aplicativo* e o *perfil*. As chaves dos dados de configuração relacionados ao Spring Cloud normalmente começarão com esses dois elementos, separadamente por um delimitador.

As chaves armazenadas na Configuração de Aplicativo são cadeias de caracteres baseadas em Unicode que diferenciam maiúsculas de minúsculas. As chaves *app1* e *App1* são distintas em um repositório de Configuração de Aplicativos. Tenha isso em mente ao usar definições de configuração em um aplicativo, pois algumas estruturas manipulam as chaves de configuração sem diferenciar maiúsculas de minúsculas. Não recomendamos o uso de maiúsculas e minúsculas para diferenciar as chaves.

Você pode usar qualquer caractere Unicode em nomes de chave, exceto para `%` . Um nome de chave não pode ser `.` ou `..` um. Há um limite de tamanho combinado de 10 KB em um valor-chave. Esse limite inclui todos os caracteres na chave, seu valor e todos os atributos opcionais associados. Dentro desse limite, você pode ter vários níveis hierárquicos para chaves.

### <a name="design-key-namespaces"></a>Criar namespaces de chave

Há duas abordagens gerais para nomear as chaves usadas para dados de configuração: simples ou hierárquica. Esses métodos são muito semelhantes do ponto de vista do uso de um aplicativo, mas a nomenclatura hierárquica oferece algumas vantagens:

* É mais fácil de ler. Os delimitadores em um nome de chave hierárquica funcionam como espaços em uma sentença. Também fornecem quebras naturais entre palavras.
* É mais fácil de gerenciar. Uma hierarquia de nomes de chave representa grupos lógicos de dados de configuração.
* É mais fácil de usar. É mais simples escrever uma consulta cujo padrão é correspondente às chaves em uma estrutura hierárquica e recupera apenas uma parte dos dados de configuração. Além disso, muitas estruturas de programação mais recentes têm suporte nativo para dados de configuração hierárquica, de tal modo que o aplicativo pode fazer uso de conjuntos de configuração específicos.

Você pode organizar as chaves na Configuração de Aplicativo hierarquicamente de muitas maneiras. Considere essas chaves como sendo [URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Cada chave hierárquica é um *caminho* de recurso formado por um ou mais componentes que são unidos por delimitadores. Escolha qual caractere usar como um delimitador de acordo com as necessidades do aplicativo, da linguagem de programação ou da estrutura. Use vários delimitadores para diferentes chaves na Configuração de Aplicativo.

### <a name="label-keys"></a>Chaves de rótulo

Os pares chave-valor na Configuração de Aplicativo podem, opcionalmente, ter um atributo de rótulo. Os rótulos são usados para diferenciar os pares chave-valor com a mesma chave. Uma chave *app1* com rótulos *A* e *B* forma duas chaves separadas em um repositório de Configuração de Aplicativos. Por padrão, um valor de chave não tem nenhum rótulo. Para referenciar explicitamente um valor de chave sem um rótulo, use `\0` (URL codificado como `%00` ).

O rótulo fornece uma maneira conveniente de criar variantes de uma chave. Um uso comum dos rótulos é especificar vários ambientes para a mesma chave:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Chave de versão-valores

Use rótulos como uma maneira de criar várias versões de um valor-chave. Por exemplo, você pode inserir um número de versão do aplicativo ou uma ID de confirmação do Git em rótulos para identificar pares chave-valor associados a um build de software específico.

> [!NOTE]
> Se você estiver procurando versões de alteração, a configuração de aplicativo manterá todas as alterações de um valor de chave ocorridas no passado determinado período de tempo automaticamente. Consulte [instantâneo pontual](./concept-point-time-snapshot.md) para obter mais detalhes.

### <a name="query-key-values"></a>Valores de chave de consulta

Cada par chave-valor é identificado exclusivamente pela sua chave, além de um rótulo que pode ser `\0`. Você consulta um repositório de configuração de aplicativo para obter valores de chave, especificando um padrão. O repositório de configuração de aplicativo retorna todos os valores de chave que correspondem ao padrão, incluindo seus valores e atributos correspondentes. Use os seguintes padrões de chave em chamadas à API REST para a Configuração de Aplicativo:

| Chave | Descrição |
|---|---|
| `key` é omitido ou `key=*` | Corresponde a todas as chaves |
| `key=abc` | Corresponde exatamente ao nome da chave **abc** |
| `key=abc*` | Corresponde aos nomes de chave que começam com **abc** |
| `key=abc,xyz` | Corresponde aos nomes de chave **ABC** ou **XYZ**. Limitado a cinco CSVs |

Inclua também os seguintes padrões de rótulo:

| Rótulo | Descrição |
|---|---|
| `label` é omitido ou `label=*` | Corresponde a qualquer rótulo, que inclui `\0` |
| `label=%00` | Corresponde ao rótulo `\0` |
| `label=1.0.0` | Corresponde exatamente ao rótulo **1.0.0** |
| `label=1.0.*` | Corresponde aos rótulos que começam com **1.0.** |
| `label=%00,1.0.0` | Corresponde aos rótulos `\0` ou **1.0.0**, limitado a cinco CSVs |

> [!NOTE]
> `*`, `,` e `\` são caracteres reservados em consultas. Se um caractere reservado for usado em nomes de chave ou rótulos, você deverá escapar dele usando `\{Reserved Character}` em consultas.

## <a name="values"></a>Valores

Os valores atribuídos às chaves também são cadeias de caracteres Unicode. Use todos os caracteres Unicode para valores.

### <a name="use-content-type"></a>Usar tipo de conteúdo
Cada chave-valor na configuração do aplicativo tem um atributo Content-Type. Opcionalmente, você pode usar esse atributo para armazenar informações sobre o tipo de valor em um valor-chave que ajuda seu aplicativo a processá-lo corretamente. Você pode usar qualquer formato para o tipo de conteúdo. A configuração de aplicativo usa [tipos de mídia]( https://www.iana.org/assignments/media-types/media-types.xhtml) (também conhecidos como tipos de MIME) para tipos de dados internos, como sinalizadores de recurso, referências de Key Vault e valores de chave JSON.

## <a name="next-steps"></a>Próximas etapas

* [Instantâneo pontual](./concept-point-time-snapshot.md)
* [Gerenciamento de recursos](./concept-feature-management.md)
* [Manipulação de eventos](./concept-app-configuration-event.md)

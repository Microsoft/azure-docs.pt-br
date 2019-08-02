---
title: Repositório de pares chave-valor da Configuração de Aplicativo do Azure | Microsoft Docs
description: Uma visão geral de como os dados de configuração são armazenados na Configuração de Aplicativo Azure AD
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: c7a7e7994ef5e16640f59efdc672f6793bc4f18d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706465"
---
# <a name="keys-and-values"></a>Chaves e valores

A configuração do Aplicativo Azure AD e armazena dados de configuração como pares chave-valor. Os pares chave-valor são uma maneira simples, porém flexível, de representar vários tipos de configurações de aplicativo com os quais os desenvolvedores estão familiarizados.

## <a name="keys"></a>simétricas

As chaves servem como o nome de pares chave-valor e são usadas para armazenar e recuperar os valores correspondentes. É uma prática comum organizar chaves em um namespace hierárquico usando um caractere delimitador, como `/` ou `:`. Use uma convenção que é mais adequada para seu aplicativo. A Configuração de Aplicativo trata as chaves como um todo. Ela não analisa as chaves para descobrir como seus nomes são estruturados nem impõem regras a elas.

O uso de dados de configuração em estruturas do aplicativo pode exigir esquemas de nomenclatura específicos para valores de chave. Por exemplo, a estrutura Spring Cloud do Java define recursos `Environment` que fornecem configurações a um aplicativo do Spring para que sejam parametrizadas por variáveis, incluindo *nome do aplicativo* e *perfil*. As chaves dos dados de configuração relacionados ao Spring Cloud normalmente começarão com esses dois elementos, separadamente por um delimitador.

As chaves armazenadas na Configuração de Aplicativo são cadeias de caracteres baseadas em Unicode que diferenciam maiúsculas de minúsculas. As chaves *app1* e *App1* são distintas em um repositório de configurações de aplicativo. Tenha isso em mente ao usar definições de configuração em um aplicativo, pois algumas estruturas manipulam as chaves de configuração sem diferenciar maiúsculas de minúsculas. Por exemplo, o sistema de configuração do ASP.NET Core trata as chaves como cadeias de caracteres que não diferenciam maiúsculas de minúsculas. Para evitar comportamentos imprevisíveis ao consultar a Configuração de Aplicativo dentro de um aplicativo ASP.NET Core, não use chaves que diferem somente pelo uso de maiúsculas.

Você pode usar qualquer caractere unicode nos nomes de chave inseridos na Configuração de Aplicativo, exceto `*`, `,` e `\`. Esses caracteres são reservados. Caso precise incluir um caractere reservado, você precisará fazer escape dele usando `\{Reserved Character}`. Há um limite de tamanho combinado de 10 mil caracteres em um par chave-valor. Esse limite inclui todos os caracteres na chave, seu valor e todos os atributos opcionais associados. Dentro desse limite, você pode ter vários níveis hierárquicos para chaves.

### <a name="design-key-namespaces"></a>Criar namespaces de chave

Há duas abordagens gerais para nomear as chaves usadas para dados de configuração: simples ou hierárquica. Esses métodos são muito semelhantes do ponto de vista do uso de um aplicativo, mas a nomenclatura hierárquica oferece algumas vantagens:

* É mais fácil de ler. Em vez de uma sequência longa de caracteres, os delimitadores em um nome de chave hierárquica funcionam como espaços em uma sentença. Também fornecem quebras naturais entre palavras.
* É mais fácil de gerenciar. Uma hierarquia de nomes de chave representa grupos lógicos de dados de configuração.
* É mais fácil de usar. É mais simples escrever uma consulta cujo padrão é correspondente às chaves em uma estrutura hierárquica e recupera apenas uma parte dos dados de configuração. Além disso, muitas estruturas de programação mais recentes têm suporte nativo para dados de configuração hierárquica, de tal modo que o aplicativo pode fazer uso de conjuntos de configuração específicos.

Você pode organizar as chaves na Configuração de Aplicativo hierarquicamente de muitas maneiras. Considere essas chaves como sendo [URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Cada chave hierárquica é um *caminho* de recurso formado por um ou mais componentes que são unidos por delimitadores. Escolha qual caractere usar como um delimitador de acordo com as necessidades do aplicativo, da linguagem de programação ou da estrutura. Use vários delimitadores para diferentes chaves na Configuração de Aplicativo.

Estes são vários exemplos de como você pode estruturar os nomes de chave em uma hierarquia:

* Com base em serviços de componentes

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Com base em regiões de implantação

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Chaves de rótulo

Valores de chave na Configuração de Aplicativo podem, opcionalmente, ter um atributo de rótulo. Os rótulos são usados para diferenciar os valores de chave com a mesma chave. Uma chave *app1* com rótulos *A* e *B* forma duas chaves separadas em um repositório de configurações de aplicativos. Por padrão, o rótulo de um valor chave está vazio (ou `null`).

O rótulo fornece uma maneira conveniente de criar variantes de uma chave. Um uso comum dos rótulos é especificar vários ambientes para a mesma chave:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Valores de chave de versão

A Configuração de Aplicativo não faz o controle de versão de valores-chave automaticamente conforme eles são modificados. Use rótulos como uma maneira de criar várias versões de um valor de chave. Por exemplo, você pode inserir um número de versão do aplicativo ou uma ID de confirmação do Git em rótulos para identificar valores de chave associados a um build de software específico.

Você pode usar qualquer caractere unicode em rótulos, exceto para `*`, `,`, e `\`. Esses caracteres são reservados. Para incluir um caractere reservado, você precisará fazer escape dele usando `\{Reserved Character}`.

### <a name="query-key-values"></a>Consultar valores de chave

Cada valor de chave é identificado exclusivamente pela sua chave, além de um rótulo que pode ser `null`. Consulte em um repositório de configurações de aplicativo pares valores de chave especificando um padrão. O repositório de configurações de aplicativo retorna todos os pares chave-valor que correspondem ao padrão e seus valores e seus atributos correspondentes. Use os seguintes padrões de chave em chamadas à API REST para a Configuração de Aplicativo:

| Chave | |
|---|---|
| `key` é omitido ou `key=*` | Corresponde a todas as chaves |
| `key=abc` | Corresponde exatamente ao nome da chave **abc** |
| `key=abc*` | Corresponde aos nomes de chave que começam com **abc** |
| `key=*abc` | Correspondem aos nomes de chave que terminam com **abc** |
| `key=*abc*` | Corresponde aos nomes de chave que contêm **abc** |
| `key=abc,xyz` | Corresponde aos nomes de chave **abc** ou **xyz**, limitado a cinco CSVs |

Inclua também os seguintes padrões de rótulo:

| Rótulo | |
|---|---|
| `label` é omitido ou `label=*` | Corresponde a qualquer rótulo, que inclui `null` |
| `label=%00` | Corresponde ao rótulo `null` |
| `label=1.0.0` | Corresponde exatamente ao rótulo **1.0.0** |
| `label=1.0.*` | Corresponde aos rótulos que começam com **1.0.** |
| `label=*.0.0` | Corresponde aos rótulos que terminam com **.0.0** |
| `label=*.0.*` | Corresponde aos rótulos que contêm **.0.** |
| `label=%00,1.0.0` | Corresponde aos rótulos `null` ou **1.0.0**, limitado a cinco CSVs |

## <a name="values"></a>Valores

Os valores atribuídos às chaves também são cadeias de caracteres Unicode. Use todos os caracteres Unicode para valores. Há um tipo de conteúdo definido pelo usuário opcional associado a cada valor. Use esse atributo para armazenar informações, por exemplo, um esquema de codificação sobre um valor que ajuda o aplicativo para processá-lo corretamente.

Os dados de configuração armazenados em um repositório de configurações de aplicativo, que inclui todas as chaves e todos os valores, são criptografados em repouso e em trânsito. A Configuração de Aplicativo não é uma solução substituta do Azure Key Vault. Não armazene segredos do aplicativo nele.

## <a name="next-steps"></a>Próximas etapas

* [Instantâneo pontual](./concept-point-time-snapshot.md)  
* [Gerenciamento de recursos](./concept-feature-management.md)  

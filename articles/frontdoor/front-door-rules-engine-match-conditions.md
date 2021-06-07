---
title: Condições de correspondência dos Mecanismos de Regras do Azure Front Door
description: Este artigo fornece uma lista das várias condições de correspondência disponíveis no mecanismo de regras do Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: duau
ms.openlocfilehash: 23c3afc9d222c7964c3f957b810a545a737a3262
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068193"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Condições de correspondência do Mecanismo de Regras do Azure Front Door

No [Mecanismo de Regras do AFD](front-door-rules-engine.md), uma regra consiste em zero ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que você pode usar no mecanismo de regras do AFD.

A primeira parte de uma regra é uma condição de correspondência ou um conjunto de condições de correspondência. Uma regra pode consistir em até 10 condições de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais as ações definidas são executadas. Se você usar várias condições de correspondência, as condições de correspondência serão agrupadas em conjunto usando a lógica AND. Para todas as condições de correspondência compatíveis com vários valores (anotados como "separados por espaços"), o operador "OR" é presumido.

Por exemplo, você pode usar uma condição de correspondência para:

- Filtrar solicitações com base em um endereço IP, país ou região específico.
- Filtrar solicitações por informações de cabeçalho.
- Filtrar solicitações de dispositivos móveis ou dispositivos de desktop.

As condições de correspondência a seguir estão disponíveis para uso no mecanismo de regras do Azure Front Door.  

## <a name="device-type"></a>Tipo de dispositivo 

Identifica solicitações feitas de um dispositivo móvel ou dispositivo de desktop.  

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | Dispositivo móvel, desktop

## <a name="post-argument"></a>Argumento post

Identifica solicitações com base em argumentos definidos para o método de solicitação POST usado na solicitação. 

#### <a name="required-fields"></a>Campos obrigatórios

Nome do argumento | Operador | Valor do argumento | Transformação de maiúsculas e minúsculas
--------------|----------|----------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

## <a name="query-string"></a>Cadeia de consulta

Identifica as solicitações que contêm um parâmetro de cadeia de caracteres de consulta específico. Esse parâmetro é definido como um valor que corresponde a um padrão específico. Parâmetros de cadeia de caracteres de consulta (por exemplo, **parameter=value**) na URL de solicitação determinam se essa condição é atendida. Essa condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta por seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Cadeia de consulta | Transformação de maiúsculas e minúsculas
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

## <a name="remote-address"></a>Endereço remoto

Identifica solicitações com base na localização ou no endereço IP do solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|-----------------
Correspondência Geográfica | Código do país
Correspondência de IP | Endereço IP (separado por espaços)
Sem correspondência geográfica | Código do país
Não Correspondência de IP | Endereço IP (separado por espaços)

#### <a name="key-information"></a>Principais informações

- Use a notação CIDR.
- Para especificar vários endereços IP e blocos de endereço IP, use um só espaço entre os valores:
  - **Exemplo de IPv4**: *1.2.3.4 10.20.30.40* corresponde a qualquer solicitação que chega dos endereços 1.2.3.4 ou 10.20.30.40.
  - **Exemplo de IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* corresponde a qualquer solicitação que chega dos endereços 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe para um bloco de endereços IP é o endereço IP básico seguido por uma barra invertida e o tamanho do prefixo. Por exemplo:
  - **Exemplo de IPv4**: *5.5.5.64/26* corresponde a qualquer solicitação que chega dos endereços 5.5.5.64 a 5.5.5.127.
  - **Exemplo de IPv6**: *1:2:3:/48* corresponde a solicitações que chegam dos endereços 1:2:3:0:0:0:0:0 por meio de 1:2:3: ffff:ffff:ffff:ffff:ffff.

## <a name="request-body"></a>Corpo da solicitação

Identifica solicitações com base em um texto específico que aparece no corpo da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Corpo da solicitação | Transformação de maiúsculas e minúsculas
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

## <a name="request-header"></a>Cabeçalho da solicitação

Identifica as solicitações que usam um cabeçalho específico na solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de maiúsculas e minúsculas
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

## <a name="request-method"></a>Método de solicitação

Identifica as solicitações que usam o método de solicitação especificado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Principais informações

- Somente o método de solicitação GET pode gerar conteúdo armazenado em cache no Azure Front Door. Todos os outros métodos de solicitação são solicitados por proxy através da rede. 

## <a name="request-protocol"></a>Protocolo de solicitação

Identifica as solicitações que usam o protocolo especificado usado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | HTTP, HTTPS

## <a name="request-url"></a>URL de Solicitação

Identifica as solicitações que correspondem à URL especificada.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | URL de Solicitação | Transformação de maiúsculas e minúsculas
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

#### <a name="key-information"></a>Principais informações

- Ao usar essa condição de regra, inclua informações de protocolo. Por exemplo: `https://www.<yourdomain\>.com`.

## <a name="request-file-extension"></a>Extensão de arquivo de solicitação

Identifica solicitações que incluem a extensão de arquivo especificada no nome do arquivo na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Extensão | Transformação de maiúsculas e minúsculas
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

#### <a name="key-information"></a>Principais informações

- Para extensão, não inclua um ponto à esquerda; por exemplo, use *html*, em vez de *.html*.

## <a name="request-file-name"></a>Nome do arquivo de solicitação

Identifica solicitações que incluem o nome de arquivo especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Nome do arquivo | Transformação de maiúsculas e minúsculas
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

#### <a name="key-information"></a>Principais informações

- Para especificar vários nomes de arquivo, separe cada nome de arquivo pressionando ENTER. 

## <a name="request-path"></a>Caminho da solicitação

Identifica solicitações que incluem o caminho especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valor | Transformação de maiúsculas e minúsculas
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas, cortar, remover espaço em branco, codificação de URL, decodificação de URL

#### <a name="key-information"></a>Principais informações

- Ao usar essa condição de regra, observe que a correspondência começa após o primeiro `/` do caminho. Por exemplo: `https://www.<yourdomain>.com/folder/page` iniciaria a correspondência em `folder/page`.

## <a name="standard-operator-list"></a>Lista de operadores padrão

Para regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- É igual a 
- Contém 
- Começa com 
- Termina com 
- Menor que
- Menor ou igual a
- Maior que
- Maior ou igual a
- Não qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não é menor que
- Não é menor nem igual a
- Não é maior que
- Não é maior nem igual a

Para operadores numéricos como *Menor que* e *Maior ou igual a*, a comparação usada é baseada no comprimento. O valor na condição de correspondência deve ser um inteiro igual ao comprimento que você deseja comparar. 


## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar seu primeiro [Mecanismo de Regras](front-door-tutorial-rules-engine.md). 
- Saiba mais sobre [ações do Mecanismo de regras](front-door-rules-engine-actions.md)
- Saiba mais sobre o [Mecanismo de regras do Azure Front Door](front-door-rules-engine.md)

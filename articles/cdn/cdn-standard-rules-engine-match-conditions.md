---
title: Condições de correspondência no mecanismo de regras padrão para a CDN do Azure | Microsoft Docs
description: Documentação de referência para condições de correspondência no mecanismo de regras padrão para a rede de distribuição de conteúdo do Azure (CDN do Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81259912"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condições de correspondência no mecanismo de regras padrão para a CDN do Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a rede de distribuição de conteúdo do Azure (CDN do Azure), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que você pode usar no mecanismo de regras padrão para a CDN do Azure.

A primeira parte de uma regra é uma condição de correspondência ou um conjunto de condições de correspondência. No mecanismo de regras padrão para a CDN do Azure, cada regra pode ter até quatro condições de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais as ações definidas são executadas. Se você usar várias condições de correspondência, as condições de correspondência serão agrupadas em conjunto usando a lógica AND.

Por exemplo, você pode usar uma condição de correspondência para:

- Filtrar solicitações com base em um endereço IP, país ou região específico.
- Filtrar solicitações por informações de cabeçalho.
- Filtrar solicitações de dispositivos móveis ou dispositivos de desktop.

## <a name="match-conditions"></a>Condições de correspondência

As seguintes condições de correspondência estão disponíveis para uso no mecanismo de regras padrão para a CDN do Azure. 

### <a name="device-type"></a>Tipo de dispositivo 

Identifica solicitações feitas de um dispositivo móvel ou dispositivo de desktop.  

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | Dispositivo móvel, desktop

### <a name="http-version"></a>Versão HTTP

Identifica solicitações com base na versão HTTP da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | 2,0, 1,1, 1,0, 0,9, todos

### <a name="request-cookies"></a>Solicitar cookies

Identifica solicitações com base nas informações de cookie na solicitação de entrada.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cookie | Operador | Valor do cookie | Transformação de maiúsculas e minúsculas
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Principais informações

- Você não pode usar valores curinga (incluindo asteriscos ( \* )) ao especificar um nome de cookie. você deve usar um nome de cookie exato.
- Você pode especificar apenas um único nome de cookie por instância dessa condição de correspondência.
- Comparações de nome de cookie diferenciam maiusculas de minúsculas.
- Para especificar vários valores de cookie, use um único espaço entre cada valor de cookie. 
- Os valores de cookie podem tirar proveito dos valores curinga.
- Se um valor curinga não tiver sido especificado, apenas uma correspondência exata atenderá a essa condição de correspondência. Por exemplo, "value" corresponderá a "value", mas não a "value1". 

### <a name="post-argument"></a>Argumento post

Identifica solicitações com base em argumentos definidos para o método de solicitação POST usado na solicitação. 

#### <a name="required-fields"></a>Campos obrigatórios

Nome do argumento | Operador | Valor do argumento | Transformação de maiúsculas e minúsculas
--------------|----------|----------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="query-string"></a>Cadeia de consulta

Identifica as solicitações que contêm um parâmetro de cadeia de caracteres de consulta específico. Esse parâmetro é definido como um valor que corresponde a um padrão específico. Parâmetros de cadeia de caracteres de consulta (por exemplo, **parameter=value**) na URL de solicitação determinam se essa condição é atendida. Essa condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta por seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Cadeia de consulta | Transformação de maiúsculas e minúsculas
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="remote-address"></a>Endereço remoto

Identifica solicitações com base na localização ou no endereço IP do solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|-----------------
Qualquer | N/D
Correspondência Geográfica | Código do país
Correspondência de IP | Endereço IP (separado por espaços)
Não qualquer | N/D
Sem correspondência geográfica | Código do país
Não Correspondência de IP | Endereço IP (separado por espaços)

#### <a name="key-information"></a>Principais informações

- Use a notação CIDR.
- Para especificar vários endereços IP e blocos de endereço IP, use um só espaço entre os valores:
  - **Exemplo de IPv4**: *1.2.3.4 10.20.30.40* corresponde a qualquer solicitação que chega dos endereços 1.2.3.4 ou 10.20.30.40.
  - **Exemplo de IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* corresponde a qualquer solicitação que chega dos endereços 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe para um bloco de endereços IP é o endereço IP básico seguido por uma barra invertida e o tamanho do prefixo. Por exemplo:
  - **Exemplo de IPv4**: *5.5.5.64/26* corresponde a qualquer solicitação que chega dos endereços 5.5.5.64 a 5.5.5.127.
  - **Exemplo de IPv6**: *1:2:3:/48* corresponde a qualquer solicitação que chega dos endereços 1:2:3:0:0:0:0:0 por meio de 1:2:3:ffff:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Corpo da solicitação

Identifica solicitações com base em um texto específico que aparece no corpo da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Corpo da solicitação | Transformação de maiúsculas e minúsculas
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="request-header"></a>Cabeçalho da solicitação

Identifica as solicitações que usam um cabeçalho específico na solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de maiúsculas e minúsculas
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="request-method"></a>Método de solicitação

Identifica as solicitações que usam o método de solicitação especificado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Principais informações

- Somente o método GET Request pode gerar conteúdo armazenado em cache na CDN do Azure. Todos os outros métodos de solicitação são solicitados por proxy através da rede. 

### <a name="request-protocol"></a>Protocolo de solicitação

Identifica as solicitações que usam o protocolo especificado usado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | HTTP, HTTPS

### <a name="request-url"></a>URL de Solicitação

Identifica as solicitações que correspondem à URL especificada.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | URL de Solicitação | Transformação de maiúsculas e minúsculas
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Principais informações

- Ao usar essa condição de regra, inclua informações de protocolo. Por exemplo: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Extensão de arquivo de URL

Identifica solicitações que incluem a extensão de arquivo especificada no nome do arquivo na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Extensão | Transformação de maiúsculas e minúsculas
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Principais informações

- Para extensão, não inclua um ponto à esquerda; por exemplo, use *html*, em vez de *.html*.

### <a name="url-file-name"></a>Nome do arquivo da URL

Identifica solicitações que incluem o nome de arquivo especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Nome do arquivo | Transformação de maiúsculas e minúsculas
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Principais informações

- Para especificar vários nomes de arquivo, separe cada nome de arquivo com um único espaço. 

### <a name="url-path"></a>Caminho da URL

Identifica solicitações que incluem o caminho especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valor | Transformação de maiúsculas e minúsculas
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de Caracteres, Int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Principais informações

- Um valor de nome de arquivo pode aproveitar os valores curinga. Por exemplo, cada padrão de nome de arquivo pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para condições de correspondência do mecanismo de regras

### <a name="standard-operator-list"></a>Lista de operadores padrão

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

Para operadores numéricos como *Menor que* e *Maior ou igual a*, a comparação usada é baseada no comprimento. Nesse caso, o valor na condição de correspondência deve ser um inteiro igual ao comprimento que você deseja comparar. 

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras Standard](cdn-standard-rules-engine-reference.md)
- [Ações no mecanismo de regras Standard](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS usando o mecanismo de regras Standard](cdn-standard-rules-engine.md)

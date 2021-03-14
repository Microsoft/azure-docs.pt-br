---
title: Configurar condições de correspondência do conjunto de regras padrão/Premium da porta do Azure
description: Este artigo fornece uma lista das várias condições de correspondência disponíveis com o conjunto de regras Standard/Premium do Azure front door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098433"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Condições de correspondência do conjunto de regras Standard/Premium (visualização) do Azure front door

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

Este tutorial mostra como criar um conjunto de regras com seu primeiro conjunto de regras no portal do Azure. No [conjunto de regras](concept-rule-set.md)Standard/Premium do Azure front door, uma regra consiste em zero ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que você pode usar no conjunto de regras Standard/Premium do Azure front door.

A primeira parte de uma regra é uma condição de correspondência ou um conjunto de condições de correspondência. Uma regra pode consistir em até 10 condições de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais as ações definidas são executadas. Se você usar várias condições de correspondência, as condições de correspondência serão agrupadas em conjunto usando a lógica AND. Para todas as condições de correspondência compatíveis com vários valores (anotados como "separados por espaços"), o operador "OR" é presumido.

Por exemplo, você pode usar uma condição de correspondência para:

* Filtrar solicitações com base em um endereço IP, país ou região específico.
* Filtrar solicitações por informações de cabeçalho.
* Filtrar solicitações de dispositivos móveis ou dispositivos de desktop.
* Filtrar solicitações de nome do arquivo de solicitação e extensão de arquivo.
* Filtrar solicitações da URL de solicitação, protocolo, caminho, Cadeia de caracteres de consulta, postar argumentos, etc.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As seguintes condições de correspondência estão disponíveis para uso no conjunto de regras padrão/Premium da porta do Azure:

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
String | [Lista de operadores](#operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

## <a name="query-string"></a>Cadeia de consulta

Identifica as solicitações que contêm um parâmetro de cadeia de caracteres de consulta específico. Esse parâmetro é definido como um valor que corresponde a um padrão específico. Parâmetros de cadeia de caracteres de consulta (por exemplo, **parameter=value**) na URL de solicitação determinam se essa condição é atendida. Essa condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta por seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Cadeia de consulta | Transformação de maiúsculas e minúsculas
---------|--------------|---------------
[Lista de operadores](#operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

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

* Use a notação CIDR.
* Para vários endereços IP e blocos de endereço IP, a lógica ' OR ' é operada.
    * **Exemplo de IPv4**: se você adicionar dois endereços IP *1.2.3.4* e *10.20.30.40*, a condição será correspondida se qualquer solicitação que chegar de um endereço 1.2.3.4 ou 10.20.30.40.
    * **Exemplo de IPv6**: se você adicionar dois endereços IP *1:2:3:4:5:6:7:8* e *10:20:30:40:50:60:70:80*, a condição será correspondida se alguma solicitação chegar de um endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
* A sintaxe para um bloco de endereços IP é o endereço IP básico seguido por uma barra invertida e o tamanho do prefixo. Por exemplo:
    * **Exemplo de IPv4**: *5.5.5.64/26* corresponde a qualquer solicitação que chega dos endereços 5.5.5.64 a 5.5.5.127.
    * **Exemplo de IPv6**: *1:2:3:/48* corresponde a solicitações que chegam dos endereços 1:2:3:0:0:0:0:0 por meio de 1:2:3: ffff:ffff:ffff:ffff:ffff.

## <a name="request-body"></a>Corpo da solicitação

Identifica solicitações com base em um texto específico que aparece no corpo da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Corpo da solicitação | Transformação de maiúsculas e minúsculas
---------|--------------|---------------
[Lista de operadores](#operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

## <a name="request-header"></a>Cabeçalho da solicitação

Identifica as solicitações que usam um cabeçalho específico na solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de maiúsculas e minúsculas
------------|----------|--------------|---------------
String | [Lista de operadores](#operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

## <a name="request-method"></a>Método de solicitação

Identifica as solicitações que usam o método de solicitação especificado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Igual, não igual | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Principais informações

Somente o método de solicitação GET pode gerar conteúdo armazenado em cache no Azure Front Door. Todos os outros métodos de solicitação são solicitados por proxy através da rede.

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
[Lista de operadores](#operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

#### <a name="key-information"></a>Principais informações

Ao usar essa condição de regra, inclua informações de protocolo. Por exemplo: *https://www.\<yourdomain\>.com*.

## <a name="request-file-extension"></a>Extensão de arquivo de solicitação

Identifica solicitações que incluem a extensão de arquivo especificada no nome do arquivo na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Extensão | Transformação de maiúsculas e minúsculas
---------|-----------|---------------
[Lista de operadores](#operator-list)  | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

#### <a name="key-information"></a>Principais informações

Para extensão, não inclua um ponto à esquerda; por exemplo, use *html*, em vez de *.html*.

## <a name="request-file-name"></a>Nome do arquivo de solicitação

Identifica solicitações que incluem o nome de arquivo especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Nome do arquivo | Transformação de maiúsculas e minúsculas
---------|-----------|---------------
[Lista de operadores](#operator-list)| Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

## <a name="request-path"></a>Caminho da solicitação

Identifica solicitações que incluem o caminho especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valor | Transformação de maiúsculas e minúsculas
---------|-------|---------------
[Lista de operadores](#operator-list) | Cadeia de Caracteres, Int | Letras minúsculas, maiúsculas

## <a name="operator-list"></a><a name = "operator-list"></a>Lista de operadores

Para regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

* Qualquer
* É igual a
* Contém
* Começa com
* Termina com
* Menor que
* Menor ou igual a
* Maior que
* Maior ou igual a
* Não qualquer
* Não contém
* Não começa com
* Não termina com
* Não é menor que
* Não é menor nem igual a
* Não é maior que
* Não é maior nem igual a
* Expressão regular

Para operadores numéricos como *Menor que* e *Maior ou igual a*, a comparação usada é baseada no comprimento. O valor na condição de correspondência deve ser um inteiro igual ao comprimento que você deseja comparar.

## <a name="regular-expression"></a>Expressão regular

O Regex não oferece suporte às seguintes operações:

* Referências anteriores e captura de subexpressãos
* Asserções de largura zero arbitrárias
* Referências de sub-rotinas e padrões recursivos
* Padrões condicionais
* Verbos de controle de retrocesso
* A diretiva \c Single-byte
* A diretiva de correspondência de nova linha \r
* A diretiva de início de redefinição de correspondência \K
* Textos explicativos e código inserido
* Quantificadores de Possessive e agrupamento atômico

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [conjunto de regras](concept-rule-set.md).
* Saiba como [configurar seu primeiro conjunto de regras](how-to-configure-rule-set.md).
* Saiba mais sobre as [ações do conjunto de regras](concept-rule-set-actions.md).

---
title: Variáveis HTTP para o mecanismo de regras de CDN do Azure |Microsoft Docs
description: As variáveis HTTP permitem recuperar metadados de solicitação e resposta HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 8d4fc5fbdc3185c46f00d94537b197ec03f66755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709913"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variáveis HTTP para o mecanismo de regras de CDN do Azure
As variáveis HTTP fornecem os meios através dos quais você pode recuperar metadados de solicitação e resposta HTTP. Esses metadados, em seguida, podem ser usados para alterar dinamicamente uma solicitação ou resposta. O uso de variáveis HTTP é restrito aos recursos de mecanismo de regras a seguir:

- [Regravação da Chave de Cache](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Modificar Cabeçalho de Solicitação do Cliente](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Modificar Cabeçalho de Resposta do Cliente](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Redirecionamento de URL](cdn-rules-engine-reference-features.md#url-redirect)
- [Regravação de URL](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definições
A tabela a seguir descreve as variáveis HTTP compatíveis. Um valor em branco é retornado quando os metadados geográficos (por exemplo, código postal) não estão disponíveis para uma determinada solicitação.


| NOME | Variável | DESCRIÇÃO | Valor de exemplo |
| ---- | -------- | ----------- | ------------ |
| ASN (Solicitante) | %{geo_asnum} | Indica o número do sistema autônomo do solicitante. <br /><br />**Preterido:** %{virt_dst_asnum}. <br />Essa variável foi substituída por %{geo_asnum}. Embora uma regra que use essa variável preterida continue a funcionar, você deve atualizá-la para usar a nova variável. | AS15133 |
| Cidade (Solicitante) | %{geo_city} | Indica a cidade do solicitante. | Los Angeles |
| Continente (Solicitante) | %{geo_continent} | Indica o continente do solicitante por meio de sua abreviação. <br />Os valores válidos são: <br />AF: África<br />AS: Ásia<br />EU: Europa<br />NA: América do Norte<br />OC: Oceania<br />SA: América do Sul<br /><br />**Preterido:** %{virt_dst_continent}. <br />Essa variável foi preterida em favor de % {geo_continent}. <br />Embora uma regra que use essa variável preterida continue a funcionar, você deve atualizá-la para usar a nova variável.| N/D |
| Valor do cookie | %{cookie_Cookie} | Retorna o valor correspondente à chave do cookie identificada pelo termo Cookie. | Exemplo de uso: <br />%{cookie__utma}<br /><br />Valor de exemplo:<br />111662281.2.10.1222100123 |
| País (Solicitante) | %{geo_country} | Indica o país do solicitante de origem por meio de seu código de país. <br />**Preterido:** %{virt_dst_country}. <br /><br />Essa variável foi substituída por %{geo_country}. Embora uma regra que use essa variável preterida continue a funcionar, você deve atualizá-la para usar a nova variável. | EUA |
| Área de Mercado Designada (Solicitante) | %{geo_dma_code} |Indica o mercado de mídia do solicitante pelo seu código de região. <br /><br />Este campo só é aplicável às solicitações que se originam dos Estados Unidos.| 745 |
| Método de Solicitação HTTP | %{request_method} | Indica o método de solicitação HTTP. | GET |
| Código de status HTTP | %{status} | Indica o código de status HTTP da resposta. | 200 |
| Endereço IP (Solicitante) | %{virt_dst_addr} | Indica o endereço IP do solicitante. | 192.168.1.1 |
| Latitude (Solicitante) | %{geo_latitude} | Indica a latitude do solicitante. | 34.0995 |
| Longitude (Solicitante) | %{geo_longitude} | Indica a longitude do solicitante. | -118.4143 |
| Área Metropolitana Estatística (Solicitante) | %{geo_metro_code} | Indica a área metropolitana do solicitante. <br /><br />Este campo só é aplicável às solicitações que se originam dos Estados Unidos.<br />| 745 |
| Porta (Solicitante) | %{virt_dst_port} | Indica a porta efêmera do solicitante. | 55885 |
| Código Postal (Solicitante) | %{geo_postal_code} | Indica o código postal do solicitante. | 90210 |
| Cadeia de Consulta Encontrada | %{is_args} | O valor desta variável varia conforme a solicitação contém ou não uma cadeia de consulta.<br /><br />- Cadeia de Consulta Encontrada: ?<br />-Nenhuma cadeia de caracteres de consulta: NULO | ? |
| Parâmetro Cadeia de Consulta Encontrado | %{is_amp} | O valor desta variável será alterado, com variações, se a solicitação contiver pelo menos um parâmetro de cadeia de consulta.<br /><br />- Parâmetro Encontrado: &<br />-Sem parâmetros: NULO | & |
| Valor do Parâmetro Cadeia de Consulta | %{arg_&lt;parameter&gt;} | Retorna o valor correspondente ao parâmetro de cadeia de caracteres de consulta identificado pelo termo &lt;parameter&gt;. | Exemplo de uso: <br />%{arg_language}<br /><br />Exemplo de Parâmetro de Cadeia de Consulta: <br />?language=en<br /><br />Valor de exemplo: en |
| Valor da Cadeia de Consulta | %{query_string} | Indica o valor de cadeia de consulta definido na URL da solicitação. |key1=val1&key2=val2&key3=val3 |
| Domínio Referenciador | %{referring_domain} | Indica o domínio definido no cabeçalho de solicitação de referenciador. | <www.google.com> |
| Região (Solicitante) | %{geo_region} | Indica a região do solicitante (por exemplo, estado ou província) por meio de sua abreviação alfanumérica. | CA |
| Valor do Cabeçalho da Solicitação | %{http_RequestHeader} | Retorna o valor correspondente ao cabeçalho de solicitação identificado pelo termo RequestHeader. <br /><br />Se o nome do cabeçalho da solicitação contém um traço (por exemplo, User-Agent), substitua-o por um sublinhado (por exemplo, User_Agent).| Exemplo de Uso: % {http_Connection}<br /><br />Valor de exemplo: Keep-Alive | 
| Host de Solicitação | %{host} | Indica o host definido na URL da solicitação. | <www.mydomain.com> |
| Protocolo de Solicitação | %{request_protocol} | Indica o protocolo de solicitação. | HTTP/1.1 |
| Esquema de Solicitação | %{scheme} | Indica o esquema de solicitação. |http |
| URI de Solicitação (Relativo) | %{request_uri} | Indica o caminho relativo, incluindo a cadeia de consulta definida no URI de solicitação. | /marketing/foo.js?loggedin=true |
| URI de Solicitação (Relativo sem a cadeia de consulta) | %{uri} | Indica o caminho relativo para o conteúdo solicitado. <br /><br/>Informações de chave:<br />- Este caminho relativo exclui a cadeia de consulta.<br />- Este caminho relativo reflete regravações de URL. Uma URL será regravada sob as seguintes condições:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Recurso de reconfiguração de URL: Esse recurso regrava o caminho relativo definido no URI da solicitação.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL de CNAME de borda: Este tipo de solicitação é reescrito para a URL da CDN correspondente. |/800001/corigin/rewrittendir/foo.js |
| URI da solicitação | %{request} | Descreve a solicitação. <br />Sintaxe: &lt;Método HTTP&gt; &lt;caminho relativo&gt; &lt;protocolo HTTP&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Valor do Cabeçalho de Resposta | %{resp_&lt;ResponseHeader&gt;} | Retorna o valor correspondente ao cabeçalho da resposta identificado pelo termo &lt;ResponseHeader&gt;. <br /><br />Se o nome do cabeçalho da resposta contém um traço (por exemplo, User-Agent), substitua-o por um sublinhado (por exemplo, User_Agent). | Exemplo de uso: %{resp_Content_Length}<br /><br />Valor de exemplo: 100 |

## <a name="usage"></a>Uso
A tabela a seguir descreve a sintaxe adequada para especificar uma variável HTTP.


| Sintaxe | Exemplo | DESCRIÇÃO |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use esta sintaxe para obter o valor inteiro correspondente à &lt;HTTPVariable&gt; especificada. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use esta sintaxe para o valor inteiro correspondente ao &lt;HTTPVariableDelimiter&gt; especificado. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use uma expressão regular para &lt;HTTPVariableDelimiterExpression&gt; para substituir, excluir ou manipular o valor de uma variável HTTP. |

Nomes de variável HTTP suportam apenas caracteres alfabéticos e sublinhados. Converta caracteres sem suporte em sublinhados.

## <a name="delimiter-reference"></a>Referência de delimitador
Um delimitador pode ser especificado depois de uma variável HTTP para obter os seguintes efeitos:

- Transformar o valor associado à variável.

     Exemplo: Converte o valor inteiro em minúsculas.

- Excluir o valor associado à variável.

- Manipular o valor associado à variável.

     Exemplo: Use expressões regulares para alterar o valor associado à variável HTTP.

Os delimitadores estão descritos na tabela a seguir.

| Delimitador | DESCRIÇÃO |
| --------- | ----------- |
| := | Indica que um valor padrão será atribuído à variável quando ele for: <br />- Ausente <br />- Definido como NULL. |
| :+ | Indica que um valor padrão será atribuído à variável quando um valor tiver sido atribuído a ela. |
| : | Indica que uma subcadeia de caracteres do valor atribuído à variável será expandida. |
| # | Indica que o padrão especificado após este delimitador deve ser excluído quando ele é encontrado no início do valor associado à variável. |
| % | Indica que o padrão especificado após este delimitador deve ser excluído quando ele é encontrado no final do valor associado à variável. <br />Essa definição se aplica somente quando o símbolo % é usado como um delimitador. |
| / | Delimita uma variável HTTP ou um padrão. |
| // | Localiza e substitui todas as instâncias do padrão especificado. |
| /= | Localiza, copia e substitui todas as ocorrências do padrão especificado. |
| , | Converte o valor associado à variável HTTP em minúsculas. |
| ^ | Converte o valor associado à variável HTTP em maiúsculas. |
| ,, | Converte todas as instâncias do caractere especificado no valor associado à variável HTTP em minúsculas. |
| ^^ | Converte todas as instâncias do caractere especificado no valor associado à variável HTTP em maiúsculas. |

## <a name="exceptions"></a>Exceções
A tabela a seguir descreve as circunstâncias em que o texto especificado não é tratado como uma variável HTTP.

| Condição | DESCRIÇÃO | Exemplo |
| --------- | ----------- | --------|
| Símbolo de escape % | O símbolo de porcentagem pode ser ignorado com o uso de uma barra invertida. <br />O valor de exemplo à direita será tratado como um valor literal e não como uma variável HTTP.| \%{host} |
| Variáveis desconhecidas | Uma cadeia de caracteres vazia é sempre retornada para variáveis desconhecidas. | %{unknownvariable} |
| Caracteres ou sintaxe inválidos | Variáveis que contêm caracteres ou sintaxe inválidos são tratadas como valores literais. <br /><br />Exemplo 1 #: O valor especificado contém um caractere inválido (por exemplo,-). <br /><br />Exemplo 2 de #: O valor especificado contém um conjunto duplo de chaves. <br /><br />Exemplo 3 #: O valor especificado não tem uma chave de fechamento.<br /> | Exemplo 1: %{resp_user-agent} <br /><br />Exemplo #2: %{{host}} <br /><br />Exemplo #3: %{host |
| Nome da variável ausente | Um valor NULL sempre é retornado quando uma variável não for especificada. | %{} |
| Caracteres à direita | Caracteres à direita de uma variável são tratados como valores literais. <br />O valor de exemplo à direita contém uma chave à direita que será tratada como um valor literal. | %{host}} |

## <a name="setting-default-header-values"></a>Definir valores de cabeçalho padrão
Um valor padrão pode ser atribuído a um cabeçalho quando ele atender a qualquer uma das seguintes condições:
- Ausente/não definido
- Definido como NULL.

A tabela a seguir descreve como definir um valor padrão.

| Condição | Sintaxe | Exemplo | DESCRIÇÃO |
| --------- | ------ | --------| ----------- |
| Defina um cabeçalho para um valor padrão quando ele atender a qualquer uma das seguintes condições: <br /><br />- Faltando cabeçalho <br /><br />- O valor do cabeçalho é definido como NULL.| %{Variable:=Value} | %{http_referer:=unspecified} | O cabeçalho Referenciador só será definido como *não especificado* quando ele estiver ausente ou definido como NULL. Nenhuma ação ocorrerá se ele tiver sido definido. |
| Defina um cabeçalho para um valor padrão quando ele estiver ausente. | %{Variable=Value} | %{http_referer=unspecified} | O cabeçalho Referenciador só será definido como *não especificado* quando ele estiver ausente. Nenhuma ação ocorrerá se ele tiver sido definido. |
| Defina o cabeçalho para um valor padrão quando ele não atender a qualquer uma das seguintes condições: <br /><br />- Ausente<br /><br /> - Definido como NULL. | %{Variable:+Value} | %{http_referer:+unspecified} | O cabeçalho Referenciador só será definido como *não especificado* quando um valor tiver sido atribuído a ele. Nenhuma ação ocorrerá se ele estiver ausente ou definido como NULL. |

## <a name="manipulating-variables"></a>Manipulação de variáveis
Variáveis podem ser manipuladas das seguintes maneiras:

- Expandindo subcadeias de caracteres
- Removendo padrões

### <a name="substring-expansion"></a>Expansão de subcadeia de caracteres
Por padrão, uma variável será expandida para seu valor total. Use a seguinte sintaxe para expandir somente uma subcadeia de caracteres do valor da variável.

`%<Variable>:<Offset>:<Length>}`

Informações de chave:

- O valor atribuído ao termo Deslocamento determina o caractere inicial da subcadeia de caracteres:

     - Positivo: O caractere inicial da subcadeia de caracteres é calculado a partir do primeiro caractere na cadeia de caracteres.
     - Zero: O caractere inicial da subcadeia de caracteres é o primeiro caractere na cadeia de caracteres.
     - Negativo: O caractere inicial da subcadeia de caracteres é calculado a partir do último caractere na cadeia de caracteres.

- O comprimento da subcadeia de caracteres é determinado pelo termo *Comprimento*:

     - Omitido: Omitir o termo de comprimento permite que a subcadeia de caracteres incluir todos os caracteres entre o caractere inicial e final da cadeia de caracteres.
     - Positivo: Determina o comprimento da subcadeia de caracteres do caractere inicial para a direita.
     - Negativo: Determina o comprimento da subcadeia de caracteres do caractere inicial para a esquerda.

#### <a name="example"></a>Exemplo:

O exemplo a seguir se baseia na URL de solicitação de exemplo a seguir:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A cadeia de caracteres a seguir demonstra vários métodos para manipular variáveis:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Com base na URL de solicitação de exemplo, a manipulação de variável acima produzirá o seguinte valor:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Remoção de padrão
O texto que coincide com um padrão específico pode ser removido do início ou final do valor da variável.

| Sintaxe | Ação |
| ------ | ------ |
| %{Variable#Pattern} | Remove o texto quando o padrão especificado é encontrado no início do valor da variável. |
| %{Variable%Pattern} | Remove o texto quando o padrão especificado é encontrado no final do valor da variável. |

#### <a name="example"></a>Exemplo:

Neste cenário de exemplo, a variável *request_uri* é definida como:

`/800001/myorigin/marketing/product.html?language=en-US`

A tabela a seguir demonstra como funciona essa sintaxe.

| Sintaxe de exemplo | Resultados | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Como a variável inicia com o padrão, ela foi substituída. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Como a variável não termina com o padrão, não houve nenhuma alteração.|

### <a name="find-and-replace"></a>Localizar e substituir
A sintaxe de localizar e substituir é descrita na tabela a seguir.

| Sintaxe | Ação |
| ------ | ------ |
| %{Variable/Find/Replace} | Localiza e substitui a primeira ocorrência do padrão especificado. |
| %{Variable//Find/Replace} | Localiza e substitui todas as ocorrências do padrão especificado. |
| %{Variable^} |Converte o valor inteiro em maiúsculas. |
| %{Variable^Find} | Converte a primeira ocorrência do padrão especificado em maiúsculas. |
| %{Variable,} | Converte o valor inteiro em minúsculas. |
| %{Variable,Find} | Converte a primeira ocorrência do padrão especificado em minúsculas. |

### <a name="find-and-rewrite"></a>Localizar e reescrever
Para uma variação de localizar e substituir, use o texto que corresponde ao padrão especificado quando reescrevê-los. A sintaxe de localizar e reescrever é descrita na tabela a seguir.

| Sintaxe | Ação |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Localiza, copia e substitui todas as ocorrências do padrão especificado. |
| %{Variable/^Find/Rewrite} | Localiza, copia e reescreve o padrão especificado quando ele ocorre no início da variável. |
| %{Variable/$Find/Rewrite} | Localizar, copiar e reescrever o padrão especificado quando ele ocorre no final da variável. |
| %{Variable/Find} | Localiza e exclui todas as ocorrências do padrão especificado. |

Informações de chave:

- Expanda o texto que corresponde ao padrão especificado, especificando um sinal de cifrão seguido por um inteiro (por exemplo, $1).

- Podem ser especificados vários padrões. A ordem na qual o padrão é especificado determina o número inteiro que será atribuído a ele. No exemplo a seguir, o primeiro padrão corresponde à “www.”, o segundo padrão corresponde ao domínio de segundo nível e o terceiro padrão corresponde ao domínio de nível superior:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- O valor reescrito pode consistir em qualquer combinação de texto e esses espaços reservados.

    No exemplo anterior, o nome do host foi reescrito para `cdn.$2.$3:80` (por exemplo, cdn.mydomain.com:80).

- O espaço reservado do padrão (por exemplo, $1) pode ser modificado para maiúsculas ou minúsculas através dos sinalizadores a seguir:
     - U: O valor expandido de letra maiuscula.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Minúsculo o valor expandido.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Um operador deve ser especificado antes do padrão. O operador especificado determina o comportamento padrão de captura:

     - `=`: Indica que todas as ocorrências de um padrão especificado devem ser capturadas e reescritas.
     - `^`: Indica que somente o texto que começa com o padrão especificado será capturado.
     - `$`: Indica que somente o texto que termina com o padrão especificado será captura.
 
- Se você omitir o valor */Reescrever* valor, o texto que corresponde ao padrão é excluído.



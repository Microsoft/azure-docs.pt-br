---
title: Regras e grupos de regras CRS de firewall do aplicativo Web Gateway de Aplicativo do Azure | Microsoft Docs
description: "Esta página fornece informações sobre regras e grupos de regras CRS do firewall do aplicativo Web."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 9265be4ac4258115c9302189d84b20e4894d42bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Lista de regras e grupos de regras CRS de firewall do aplicativo Web oferecidos

O WAF (Firewall do aplicativo Web) do Gateway de Aplicativo protege os aplicativos Web de vulnerabilidades e explorações. Isso é feito por meio de regras que são definidas com base nos conjuntos de regra do núcleo OWASP 2.2.9 ou 3.0. Essas regras podem ser desabilitadas com base em cada regra. Este artigo contém as regras e os conjuntos de regras oferecidos atualmente.

As tabelas a seguir são os grupos de regras e regras que estão disponíveis ao usar o Gateway de Aplicativo com o firewall do aplicativo Web.  Cada tabela representa as regras encontradas em um grupo de regras para uma versão específica do CRS.

##<a name="owasp30"></a>OWASP_3.0

### <a name="crs910"></a>  <p x-ms-format-detection="none">REQUEST-910-IP-REPUTATION</p>

|RuleId|Descrição|
|---|---|
|910011|Regra 910011|
|910012|Regra 910012|
|910000|Solicitação de cliente mal-intencionado conhecido (com base em violações de tráfego anteriores).|
|910100|O IP do cliente é de um país de risco alto.|
|910120|Regra 910120|
|910130|Regra 910130|
|910150|Correspondência na lista negra de HTTP como IP de mecanismo de pesquisa|
|910160|Correspondência na lista negra de HTTP como IP de spammer|
|910170|Correspondência na lista negra de HTTP como IP suspeito|
|910180|Correspondência na lista negra de HTTP como IP de harvester|
|910013|Regra 910013|
|910014|Regra 910014|
|910015|Regra 910015|
|910016|Regra 910016|
|910017|Regra 910017|
|910018|Regra 910018|

### <a name="crs911"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|911011|Regra 911011|
|911012|Regra 911012|
|911100|O método não é permitido pela política|
|911013|Regra 911013|
|911014|Regra 911014|
|911015|Regra 911015|
|911016|Regra 911016|
|911017|Regra 911017|
|911018|Regra 911018|

### <a name="crs912"></a> <p x-ms-format-detection="none">REQUEST-912-DOS-PROTECTION</p>

|RuleId|Descrição|
|---|---|
|912100|Regra 912100|
|912012|Regra 912012|
|912120|Ataque DoS (negação de serviço) identificado de % @{tx.real_ip} (% @{tx.dos_block_counter} ocorrências desde o último alerta)|
|912130|Regra 912130|
|912140|Regra 912140|
|912150|Regra 912150|
|912160|Regra 912160|
|912170|Ataque DoS (negação de serviço) de %@{tx.real_ip} - nº de intermitências de solicitação = %@{ip.dos_burst_counter}|
|912013|Regra 912013|
|912014|Regra 912014|
|912019|Regra 912019|
|912171|Ataque DoS (negação de serviço) de %@{tx.real_ip} - nº de intermitências de solicitação = %@{ip.dos_burst_counter}|
|912015|Regra 912015|
|912016|Regra 912016|
|912017|Regra 912017|
|912018|Regra 912018|

### <a name="crs913"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-913-DETECÇÃO DE SCANNER</p>

|RuleId|Descrição|
|---|---|
|913011|Regra 913011|
|913012|Regra 913012|
|913100|Encontrado Agente-Usuário associado ao verificador de segurança|
|913110|Encontrado cabeçalho de solicitação associado ao verificador de segurança|
|913120|Encontrado nome de arquivo/argumento de solicitação associado ao verificador de segurança|
|913013|Regra 913013|
|913014|Regra 913014|
|913101|Encontrado Agente-Usuário associado a cliente HTTP de scripting/genérico|
|913102|Encontrado Agente-Usuário associado a bot/rastreador Web|
|913015|Regra 913015|
|913016|Regra 913016|
|913017|Regra 913017|
|913018|Regra 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920011|Regra 920011|
|920012|Regra 920012|
|920100|Linha de solicitação de HTTP inválida|
|920130|Falha ao analisar o corpo da solicitação.|
|920140|Falha na validação estrita do corpo de solicitação com várias partes = %@{REQBODY_PROCESSOR_ERROR PE} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} %@{MULTIPART_DATA_BEFORE DB} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF %@{MULTIPART_LF_LINE} SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|920160|O cabeçalho Content-Length HTTP não é numérico.|
|920170|Solicitação GET ou HEAD com conteúdo no corpo.|
|920180|Solicitação POST com cabeçalho Content-Length ausente.|
|920190|Intervalo = último valor de byte inválido.|
|920210|Dados de cabeçalho de conexão em grande número ou conflitantes.|
|920220|Tentativa de ataque de abuso de codificação de URL|
|920240|Tentativa de ataque de abuso de codificação de URL|
|920250|Tentativa de ataque de abuso de codificação UTF8|
|920260|Tentativa de ataque de abuso de meia largura/largura total|
|920270|Caractere inválido na solicitação (caractere nulo)|
|920280|Solicitação com cabeçalho de host ausente|
|920290|Cabeçalho de host vazio|
|920310|A solicitação tem cabeçalho de aceitação vazio|
|920311|A solicitação tem cabeçalho de aceitação vazio|
|920330|Cabeçalho do agente do usuário vazio|
|920340|A solicitação tem conteúdo, mas o cabeçalho Content-Type está ausente|
|920350|O cabeçalho de host é um endereço IP numérico|
|920380|Muitos argumentos na solicitação|
|920360|Nome do argumento muito longo|
|920370|Valor do argumento muito longo|
|920390|Tamanho total de argumentos excedido|
|920400|Tamanho do arquivo carregado muito grande|
|920410|Tamanho total de arquivos carregados muito grande|
|920420|O tipo de conteúdo da solicitação não é permitido pela política|
|920430|A versão do protocolo HTTP não é permitida pela política|
|920440|A extensão de arquivo da URL é restrita pela política|
|920450|Cabeçalho HTTP é restrito pela política (%@{MATCHED_VAR})|
|920013|Regra 920013|
|920014|Regra 920014|
|920200|Intervalo = excesso de campos (6 ou mais)|
|920201|Intervalo = excesso de campos de solicitação de pdf (35 ou mais)|
|920230|Várias codificações de URL detectadas|
|920300|Falta cabeçalho de aceitação da solicitação|
|920271|Caractere inválido na solicitação (caracteres não imprimíveis)|
|920320|Cabeçalho do agente de usuário ausente|
|920015|Regra 920015|
|920016|Regra 920016|
|920272|Caractere inválido na solicitação (além dos caracteres imprimíveis abaixo de ascii 127)|
|920017|Regra 920017|
|920018|Regra 920018|
|920202|Intervalo = excesso de campos de solicitação de pdf (6 ou mais)|
|920273|Caractere inválido na solicitação (além do conjunto muito estrito)|
|920274|Caractere inválido no cabeçalho da solicitação (além do conjunto muito estrito)|
|920460|Regra 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921011|Regra 921011|
|921012|Regra 921012|
|921100|Ataque de solicitação HTTP indesejada.|
|921110|Ataque de solicitação HTTP indesejada|
|921120|Ataque de divisão de resposta HTTP|
|921130|Ataque de divisão de resposta HTTP|
|921140|Ataque de injeção de cabeçalho HTTP por meio de cabeçalhos|
|921150|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF detectado)|
|921160|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF e header-name detectado)|
|921013|Regra 921013|
|921014|Regra 921014|
|921151|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF detectado)|
|921015|Regra 921015|
|921016|Regra 921016|
|921170|Regra 921170|
|921180|Poluição de parâmetro HTTP (% @{TX.1})|
|921017|Regra 921017|
|921018|Regra 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930011|Regra 930011|
|930012|Regra 930012|
|930100|Ataques de percurso de caminho (/../)|
|930110|Ataques de percurso de caminho (/../)|
|930120|Tentativa de acesso ao arquivo do sistema operacional|
|930130|Tentativa de acesso a arquivo restrito|
|930013|Regra 930013|
|930014|Regra 930014|
|930015|Regra 930015|
|930016|Regra 930016|
|930017|Regra 930017|
|930018|Regra 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931011|Regra 931011|
|931012|Regra 931012|
|931100|Possível ataque de RFI (inclusão de arquivo remoto) = parâmetro de URL usando endereço IP|
|931110|Possível ataque de RFI (inclusão de arquivo remoto) = nome de parâmetro vulnerável RFI comum usado com carga de URL|
|931120|Possível ataque de RFI (inclusão de arquivo remoto) = carga de URL usada com caractere de interrogação (?)|
|931013|Regra 931013|
|931014|Regra 931014|
|931130|Possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio|
|931015|Regra 931015|
|931016|Regra 931016|
|931017|Regra 931017|
|931018|Regra 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932011|Regra 932011|
|932012|Regra 932012|
|932120|Execução de comando remoto = encontrado comando do Windows PowerShell|
|932130|Execução de comando remoto = encontrada expressão de Shell do Unix|
|932140|Execução de comando remoto = encontrado comando Windows FOR/IF|
|932160|Execução de comando remoto = encontrado código de Shell do Unix|
|932170|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932013|Regra 932013|
|932014|Regra 932014|
|932015|Regra 932015|
|932016|Regra 932016|
|932017|Regra 932017|
|932018|Regra 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933011|Regra 933011|
|933012|Regra 933012|
|933100|Ataque de injeção de PHP = marcação de abertura/fechamento encontrada|
|933110|Ataque de injeção de PHP = carregamento de arquivo de Script PHP encontrado|
|933120|Ataque de injeção de PHP = política de configuração encontrada|
|933130|Ataque de injeção de PHP = variáveis encontradas|
|933150|Ataque de injeção de PHP = nome da função PHP de alto risco encontrado|
|933160|Ataque de injeção de PHP = chamada de função PHP de alto risco encontrada|
|933180|Ataque de injeção de PHP = chamada de função de variável encontrada|
|933013|Regra 933013|
|933014|Regra 933014|
|933151|Ataque de injeção de PHP = nome da função PHP de médio risco encontrado|
|933015|Regra 933015|
|933016|Regra 933016|
|933131|Ataque de injeção de PHP = variáveis encontradas|
|933161|Ataque de injeção de PHP = chamada de função PHP de valor baixo encontrada|
|933111|Ataque de injeção de PHP = carregamento de arquivo de script PHP encontrado|
|933017|Regra 933017|
|933018|Regra 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941011|Regra 941011|
|941012|Regra 941012|
|941100|Ataque de XSS detectado via libinjection|
|941110|Filtro XSS - Categoria 1 = vetor de marca de script|
|941130|Filtro XSS - Categoria 3 = vetor de atributo|
|941140|Filtro XSS - Categoria 4 = Vector de URI de Javascript|
|941150|Filtro XSS - Categoria 5 = atributos HTML não permitidos|
|941180|Palavras-chave da lista negra do validador de nós|
|941190|Filtros XSS do IE - ataque detectado.|
|941200|Filtros XSS do IE - ataque detectado.|
|941210|Filtros XSS do IE - ataque detectado.|
|941220|Filtros XSS do IE - ataque detectado.|
|941230|Filtros XSS do IE - ataque detectado.|
|941240|Filtros XSS do IE - ataque detectado.|
|941260|Filtros XSS do IE - ataque detectado.|
|941270|Filtros XSS do IE - ataque detectado.|
|941280|Filtros XSS do IE - ataque detectado.|
|941290|Filtros XSS do IE - ataque detectado.|
|941300|Filtros XSS do IE - ataque detectado.|
|941310|Filtro XSS de codificação mal feita US-ASCII - ataque detectado.|
|941350|Codificação UTF-7 IE XSS - ataque detectado.|
|941013|Regra 941013|
|941014|Regra 941014|
|941320|Possível ataque XSS detectado - manipulador de marcação HTML|
|941015|Regra 941015|
|941016|Regra 941016|
|941017|Regra 941017|
|941018|Regra 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942011|Regra 942011|
|942012|Regra 942012|
|942100|Ataque de injeção de SQL detectado via libinjection|
|942140|Ataque de injeção de SQL = nomes comuns do BD detectados|
|942160|Detecta testes sqli cegos usando sleep() ou benchmark().|
|942170|Detecta tentativas de injeção de sleep e benchmark SQL, incluindo consultas condicionais|
|942230|Detecta tentativas de injeção de SQL condicionais|
|942270|Procurando injeção de sql básica. Cadeia de caracteres de ataque comum para mysql oracle e outros.|
|942290|Localiza tentativas de injeção de SQL MongoDB básica|
|942320|Detecta injeções de função/procedimento armazenado em MySQL e PostgreSQL|
|942350|Detecta injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942013|Regra 942013|
|942014|Regra 942014|
|942150|Ataque de injeção de SQL|
|942410|Ataque de injeção de SQL|
|942440|Sequência de comentário SQL detectada.|
|942450|Codificação hexadecimal de SQL identificada|
|942015|Regra 942015|
|942016|Regra 942016|
|942251|Detecta injeções de HAVING|
|942460|Alerta de detecção de anomalias de metacaracteres - caracteres repetitivos que não são palavras|
|942017|Regra 942017|
|942018|Regra 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943011|Regra 943011|
|943012|Regra 943012|
|943100|Possível ataque de fixação da sessão = definindo valores de cookie em HTML|
|943110|Possível ataque de fixação da sessão = nome do parâmetro de SessionID com referenciador fora do domínio|
|943120|Possível ataque de fixação da sessão = nome do parâmetro de SessionID sem referenciador|
|943013|Regra 943013|
|943014|Regra 943014|
|943015|Regra 943015|
|943016|Regra 943016|
|943017|Regra 943017|
|943018|Regra 943018|

##<a name="owasp229"></a>OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|Descrição|
|---|---|
|960911|Linha de solicitação de HTTP inválida|
|981227|Erro do Apache = URI inválido na solicitação.|
|960912|Falha ao analisar o corpo da solicitação.|
|960914|Falha na validação estrita do corpo de solicitação com várias partes = %@{REQBODY_PROCESSOR_ERROR PE} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} %@{MULTIPART_DATA_BEFORE DB} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF %@{MULTIPART_LF_LINE} SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|960915|O analisador de várias partes detectou um limite possivelmente incomparável.|
|960016|O cabeçalho Content-Length HTTP não é numérico.|
|960011|Solicitação GET ou HEAD com conteúdo no corpo.|
|960012|Solicitação POST com cabeçalho Content-Length ausente.|
|960902|Uso inválido de codificação de identidade.|
|960022|O cabeçalho esperado não é permitido para HTTP 1.0.|
|960020|O cabeçalho Pragma requer o cabeçalho Cache-Control para solicitações HTTP/1.1.|
|958291|Intervalo = o campo existe e começa com 0.|
|958230|Intervalo = último valor de byte inválido.|
|958295|Dados de cabeçalho de conexão em grande número ou conflitantes.|
|950107|Tentativa de ataque de abuso de codificação de URL|
|950109|Várias codificações de URL detectadas|
|950108|Tentativa de ataque de abuso de codificação de URL|
|950801|Tentativa de ataque de abuso de codificação UTF8|
|950116|Tentativa de ataque de abuso de meia largura/largura total|
|960901|Caractere inválido na solicitação|
|960018|Caractere inválido na solicitação|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Descrição|
|---|---|
|960008|Solicitação com cabeçalho de host ausente|
|960007|Cabeçalho de host vazio|
|960015|Falta cabeçalho de aceitação da solicitação|
|960021|A solicitação tem cabeçalho de aceitação vazio|
|960009|A solicitação tem um cabeçalho de agente usuário|
|960006|Cabeçalho do agente do usuário vazio|
|960904|A solicitação tem conteúdo, mas o cabeçalho Content-Type está ausente|
|960017|O cabeçalho de host é um endereço IP numérico|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|Descrição|
|---|---|
|960209|Nome do argumento muito longo|
|960208|Valor do argumento muito longo|
|960335|Muitos argumentos na solicitação|
|960341|Tamanho total de argumentos excedido|
|960342|Tamanho do arquivo carregado muito grande|
|960343|Tamanho total de arquivos carregados muito grande|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Descrição|
|---|---|
|960032|O método não é permitido pela política|
|960010|O tipo de conteúdo da solicitação não é permitido pela política|
|960034|A versão do protocolo HTTP não é permitida pela política|
|960035|A extensão de arquivo da URL é restrita pela política|
|960038|O cabeçalho HTTP é restrita pela política|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|Descrição|
|---|---|
|990002|A solicitação indica que um verificador de segurança verificou o site|
|990901|A solicitação indica que um verificador de segurança verificou o site|
|990902|A solicitação indica que um verificador de segurança verificou o site|
|990012|Rastreador do site invasor|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|Descrição|
|---|---|
|960024|Alerta de detecção de anomalias de metacaracteres - caracteres repetitivos que não são palavras|
|950008|Injeção de marcações ColdFusion não documentadas|
|950010|Ataques de injeção de LDAP|
|950011|Ataques de injeção de SSI|
|950018|URL Universal XSS PDF detectada.|
|950019|Ataques de injeção de email|
|950012|Ataque de solicitação HTTP indesejada.|
|950910|Ataque de divisão de resposta HTTP|
|950911|Ataque de divisão de resposta HTTP|
|950117|Ataque de inclusão de arquivo remoto|
|950118|Ataque de inclusão de arquivo remoto|
|950119|Ataque de inclusão de arquivo remoto|
|950120|Possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio|
|981133|Regra 981133|
|981134|Regra 981134|
|950009|Ataque de fixação da sessão|
|950003|Fixação da sessão|
|950000|Fixação da sessão|
|950005|Tentativa de acesso ao arquivo remoto|
|950002|Acesso ao comando do sistema|
|950006|Injeção de comandos do sistema|
|959151|Ataque de injeção de PHP|
|958976|Ataque de injeção de PHP|
|958977|Ataque de injeção de PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Descrição|
|---|---|
|981231|Sequência de comentário SQL detectada.|
|981260|Codificação hexadecimal de SQL identificada|
|981320|Ataque de injeção de SQL = nomes comuns do BD detectados|
|981300|Regra 981300|
|981301|Regra 981301|
|981302|Regra 981302|
|981303|Regra 981303|
|981304|Regra 981304|
|981305|Regra 981305|
|981306|Regra 981306|
|981307|Regra 981307|
|981308|Regra 981308|
|981309|Regra 981309|
|981310|Regra 981310|
|981311|Regra 981311|
|981312|Regra 981312|
|981313|Regra 981313|
|981314|Regra 981314|
|981315|Regra 981315|
|981316|Regra 981316|
|981317|Alerta de detecção de anomalias de instrução SELECT do SQL|
|950007|Ataques de injeção de SQL cega|
|950001|Ataque de injeção de SQL|
|950908|Ataques de injeção de SQL.|
|959073|Ataque de injeção de SQL|
|981272|Detecta testes sqli cegos usando sleep() ou benchmark().|
|981250|Detecta tentativas de injeção de sleep e benchmark SQL, incluindo consultas condicionais|
|981241|Detecta tentativas de injeção de SQL condicionais|
|981276|Procurando injeção de sql básica. Cadeia de caracteres de ataque comum para mysql oracle e outros.|
|981270|Localiza tentativas de injeção de SQL MongoDB básica|
|981253|Detecta injeções de função/procedimento armazenado em MySQL e PostgreSQL|
|981251|Detecta injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Descrição|
|---|---|
|973336|Filtro XSS - Categoria 1 = vetor de marca de script|
|973338|Filtro XSS - Categoria 3 = Vector de URI de Javascript|
|981136|Regra 981136|
|981018|Regra 981018|
|958016|Ataque XSS (script entre sites)|
|958414|Ataque XSS (script entre sites)|
|958032|Ataque XSS (script entre sites)|
|958026|Ataque XSS (script entre sites)|
|958027|Ataque XSS (script entre sites)|
|958054|Ataque XSS (script entre sites)|
|958418|Ataque XSS (script entre sites)|
|958034|Ataque XSS (script entre sites)|
|958019|Ataque XSS (script entre sites)|
|958013|Ataque XSS (script entre sites)|
|958408|Ataque XSS (script entre sites)|
|958012|Ataque XSS (script entre sites)|
|958423|Ataque XSS (script entre sites)|
|958002|Ataque XSS (script entre sites)|
|958017|Ataque XSS (script entre sites)|
|958007|Ataque XSS (script entre sites)|
|958047|Ataque XSS (script entre sites)|
|958410|Ataque XSS (script entre sites)|
|958415|Ataque XSS (script entre sites)|
|958022|Ataque XSS (script entre sites)|
|958405|Ataque XSS (script entre sites)|
|958419|Ataque XSS (script entre sites)|
|958028|Ataque XSS (script entre sites)|
|958057|Ataque XSS (script entre sites)|
|958031|Ataque XSS (script entre sites)|
|958006|Ataque XSS (script entre sites)|
|958033|Ataque XSS (script entre sites)|
|958038|Ataque XSS (script entre sites)|
|958409|Ataque XSS (script entre sites)|
|958001|Ataque XSS (script entre sites)|
|958005|Ataque XSS (script entre sites)|
|958404|Ataque XSS (script entre sites)|
|958023|Ataque XSS (script entre sites)|
|958010|Ataque XSS (script entre sites)|
|958411|Ataque XSS (script entre sites)|
|958422|Ataque XSS (script entre sites)|
|958036|Ataque XSS (script entre sites)|
|958000|Ataque XSS (script entre sites)|
|958018|Ataque XSS (script entre sites)|
|958406|Ataque XSS (script entre sites)|
|958040|Ataque XSS (script entre sites)|
|958052|Ataque XSS (script entre sites)|
|958037|Ataque XSS (script entre sites)|
|958049|Ataque XSS (script entre sites)|
|958030|Ataque XSS (script entre sites)|
|958041|Ataque XSS (script entre sites)|
|958416|Ataque XSS (script entre sites)|
|958024|Ataque XSS (script entre sites)|
|958059|Ataque XSS (script entre sites)|
|958417|Ataque XSS (script entre sites)|
|958020|Ataque XSS (script entre sites)|
|958045|Ataque XSS (script entre sites)|
|958004|Ataque XSS (script entre sites)|
|958421|Ataque XSS (script entre sites)|
|958009|Ataque XSS (script entre sites)|
|958025|Ataque XSS (script entre sites)|
|958413|Ataque XSS (script entre sites)|
|958051|Ataque XSS (script entre sites)|
|958420|Ataque XSS (script entre sites)|
|958407|Ataque XSS (script entre sites)|
|958056|Ataque XSS (script entre sites)|
|958011|Ataque XSS (script entre sites)|
|958412|Ataque XSS (script entre sites)|
|958008|Ataque XSS (script entre sites)|
|958046|Ataque XSS (script entre sites)|
|958039|Ataque XSS (script entre sites)|
|958003|Ataque XSS (script entre sites)|
|973300|Possível ataque XSS detectado - manipulador de marcação HTML|
|973301|Ataque XSS detectado|
|973302|Ataque XSS detectado|
|973303|Ataque XSS detectado|
|973304|Ataque XSS detectado|
|973305|Ataque XSS detectado|
|973306|Ataque XSS detectado|
|973307|Ataque XSS detectado|
|973308|Ataque XSS detectado|
|973309|Ataque XSS detectado|
|973311|Ataque XSS detectado|
|973313|Ataque XSS detectado|
|973314|Ataque XSS detectado|
|973331|Filtros XSS do IE - ataque detectado.|
|973315|Filtros XSS do IE - ataque detectado.|
|973330|Filtros XSS do IE - ataque detectado.|
|973327|Filtros XSS do IE - ataque detectado.|
|973326|Filtros XSS do IE - ataque detectado.|
|973346|Filtros XSS do IE - ataque detectado.|
|973345|Filtros XSS do IE - ataque detectado.|
|973324|Filtros XSS do IE - ataque detectado.|
|973323|Filtros XSS do IE - ataque detectado.|
|973348|Filtros XSS do IE - ataque detectado.|
|973321|Filtros XSS do IE - ataque detectado.|
|973320|Filtros XSS do IE - ataque detectado.|
|973318|Filtros XSS do IE - ataque detectado.|
|973317|Filtros XSS do IE - ataque detectado.|
|973329|Filtros XSS do IE - ataque detectado.|
|973328|Filtros XSS do IE - ataque detectado.|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|Descrição|
|---|---|
|950103|Ataques de percurso de caminho|

### <a name="crs45"></a> crs_45_trojans

|RuleId|Descrição|
|---|---|
|950110|Acesso ao backdoor|
|950921|Acesso ao backdoor|
|950922|Acesso ao backdoor|

## <a name="next-steps"></a>Próximas etapas

Saiba como desabilitar as regras de WAF visitando: [Personalizar regras de WAF](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png
---
title: Regras e grupos de regras do CRS
titleSuffix: Azure Web Application Firewall
description: Esta página fornece informações sobre regras e grupos de regras CRS do firewall do aplicativo Web.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e2c88091072921f1ca674868e401c34d354418de
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746502"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Regras e grupos de regras CRS do firewall do aplicativo Web

O WAF (Firewall do aplicativo Web) do Gateway de Aplicativo protege os aplicativos Web de vulnerabilidades e explorações. Isso é feito por meio de regras que são definidas com base nos conjuntos de regras do OWASP Core 3,1, 3,0 ou 2.2.9. Essas regras podem ser desabilitadas com base em regra por regra. Este artigo contém as regras atuais e os conjuntos de regras oferecidos.

## <a name="core-rule-sets"></a>Conjuntos de regras principais

O WAF do gateway de aplicativo vem pré-configurado com o CRS 3,0 por padrão. Mas você pode optar por usar CRS 3,1 ou CRS 2.2.9 em vez disso. O CRS 3,1 oferece novos conjuntos de regras que se defendem contra infecções de Java, um conjunto inicial de verificações de upload de arquivo, falsos positivos fixos e muito mais. O CRS 3,0 oferece redução de falsos positivos em comparação com o CRS 2.2.9. Você também pode [Personalizar regras para atender às suas necessidades](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Gerencia regras](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

O WAF protege contra as seguintes vulnerabilidades da Web:

- Ataques de injeção de SQL
- Ataques de script entre sites
- Outros ataques comuns, como injeção de comando, indesejada de solicitação HTTP, divisão de resposta HTTP e inclusão de arquivo remoto
- Violações de protocolo HTTP
- Anomalias do protocolo HTTP, como o agente de usuário do host ausente e os cabeçalhos de aceitação
- Bots, rastreadores e scanners
- Configurações incorretas de aplicativo comuns (por exemplo, Apache e IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3,1

O CRS 3,1 inclui 13 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

> [!NOTE]
> O CRS 3,1 só está disponível na SKU WAF_v2.

|Grupo de regras|Descrição|
|---|---|
|**[Geral](#general-31)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Proteger contra scanners de porta e de ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Proteger contra problemas de protocolo e codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Proteger contra injeção de cabeçalho, indesejada de solicitação e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Proteger contra ataques de arquivo e caminho|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Proteger contra ataques de RFI (inclusão de arquivo remoto)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Proteger novamente os ataques de execução remota de código|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Proteger contra ataques de injeção de PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Proteger contra ataques de script entre sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Proteger contra ataques de injeção de SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Proteger contra ataques de fixação da de sessão|
|**[SOLICITAÇÃO-944-APLICATIVO-ATAQUE-SESSÃO-JAVA](#crs944-31)**|Proteger contra ataques JAVA|

### <a name="owasp-crs-30"></a>OWASP CRS 3,0

O CRS 3,0 inclui 12 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

|Grupo de regras|Descrição|
|---|---|
|**[Geral](#general-30)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Proteger contra scanners de porta e de ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Proteger contra problemas de protocolo e codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Proteger contra injeção de cabeçalho, indesejada de solicitação e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Proteger contra ataques de arquivo e caminho|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Proteger contra ataques de RFI (inclusão de arquivo remoto)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Proteger novamente os ataques de execução remota de código|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Proteger contra ataques de injeção de PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Proteger contra ataques de script entre sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Proteger contra ataques de injeção de SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Proteger contra ataques de fixação da de sessão|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

O CRS 2.2.9 inclui 10 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

|Grupo de regras|Descrição|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Proteger contra violações de protocolo (como caracteres inválidos ou GET com um corpo de solicitação)|
|**[crs_21_protocol_anomalies](#crs21)**|Proteger contra informações de cabeçalho incorretas|
|**[crs_23_request_limits](#crs23)**|Proteger contra argumentos ou arquivos que excedem as limitações|
|**[crs_30_http_policy](#crs30)**|Proteger contra métodos, cabeçalhos e tipos de arquivos restritos|
|**[crs_35_bad_robots](#crs35)**|Proteger contra rastreadores e scanners da Web|
|**[crs_40_generic_attacks](#crs40)**|Proteger contra ataques genéricos (como fixação da de sessão, inclusão de arquivo remoto e injeção de PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Proteger contra ataques de injeção de SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Proteger contra ataques de script entre sites|
|**[crs_42_tight_security](#crs42)**|Proteger contra ataques de percurso de caminho|
|**[crs_45_trojans](#crs45)**|Proteger contra cavalos de Tróia de Backdoor|

As regras e os grupos de regras a seguir estão disponíveis ao usar o Firewall do aplicativo Web no gateway de aplicativo.

# <a name="owasp-31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Conjuntos de regras

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Limite possivelmente incomparável de várias partes.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-913-DETECÇÃO DE SCANNER</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado Agente-Usuário associado ao verificador de segurança|
|913101|Encontrado Agente-Usuário associado a cliente HTTP de scripting/genérico|
|913102|Encontrado Agente-Usuário associado a bot/rastreador Web|
|913110|Encontrado cabeçalho de solicitação associado ao verificador de segurança|
|913120|Encontrado nome de arquivo/argumento de solicitação associado ao verificador de segurança|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de solicitação de HTTP inválida|
|920120|Tentativa de ignorar dados de várias partes/formulário|
|920121|Tentativa de ignorar dados de várias partes/formulário|
|920130|Falha ao analisar o corpo da solicitação.|
|920140|Falha na validação estrita do corpo da solicitação de várias partes|
|920160|O cabeçalho Content-Length HTTP não é numérico.|
|920170|Solicitação GET ou HEAD com conteúdo no corpo.|
|920171|Solicitação GET ou HEAD com codificação de transferência.|
|920180|Solicitação POST com cabeçalho Content-Length ausente.|
|920190|Intervalo = último valor de byte inválido.|
|920200|Intervalo = excesso de campos (6 ou mais)|
|920201|Intervalo = excesso de campos de solicitação de pdf (35 ou mais)|
|920202|Intervalo = excesso de campos de solicitação de pdf (6 ou mais)|
|920210|Dados de cabeçalho de conexão em grande número ou conflitantes.|
|920220|Tentativa de ataque de abuso de codificação de URL|
|920230|Várias codificações de URL detectadas|
|920240|Tentativa de ataque de abuso de codificação de URL|
|920250|Tentativa de ataque de abuso de codificação UTF8|
|920260|Tentativa de ataque de abuso de meia largura/largura total|
|920270|Caractere inválido na solicitação (caractere nulo)|
|920271|Caractere inválido na solicitação (caracteres não imprimíveis)|
|920272|Caractere inválido na solicitação (além dos caracteres imprimíveis abaixo de ascii 127)|
|920273|Caractere inválido na solicitação (além do conjunto muito estrito)|
|920274|Caractere inválido no cabeçalho da solicitação (além do conjunto muito estrito)|
|920280|Solicitação com cabeçalho de host ausente|
|920290|Cabeçalho de host vazio|
|920300|Falta cabeçalho de aceitação da solicitação|
|920310|A solicitação tem cabeçalho de aceitação vazio|
|920311|A solicitação tem cabeçalho de aceitação vazio|
|920320|Cabeçalho do agente de usuário ausente|
|920330|Cabeçalho do agente do usuário vazio|
|920340|A solicitação tem conteúdo, mas o cabeçalho Content-Type está ausente|
|920341|Solicitação contendo conteúdo requer cabeçalho Content-Type|
|920350|O cabeçalho de host é um endereço IP numérico|
|920360|Nome do argumento muito longo|
|920370|Valor do argumento muito longo|
|920380|Muitos argumentos na solicitação|
|920390|Tamanho total de argumentos excedido|
|920400|Tamanho do arquivo carregado muito grande|
|920410|Tamanho total de arquivos carregados muito grande|
|920420|O tipo de conteúdo da solicitação não é permitido pela política|
|920430|A versão do protocolo HTTP não é permitida pela política|
|920440|A extensão de arquivo da URL é restrita pela política|
|920450|Cabeçalho HTTP é restrito pela política (%@{MATCHED_VAR})|
|920460|Caracteres de escape anormal|
|920470|Cabeçalho Content-Type ilegal|
|920480|Restringir o parâmetro charset dentro do cabeçalho Content-Type|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921110|Ataque de solicitação HTTP indesejada|
|921120|Ataque de divisão de resposta HTTP|
|921130|Ataque de divisão de resposta HTTP|
|921140|Ataque de injeção de cabeçalho HTTP por meio de cabeçalhos|
|921150|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF detectado)|
|921151|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF detectado)|
|921160|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF e header-name detectado)|
|921170|Poluição de parâmetro HTTP|
|921180|Poluição de parâmetro HTTP (% {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataques de percurso de caminho (/../)|
|930110|Ataques de percurso de caminho (/../)|
|930120|Tentativa de acesso ao arquivo do sistema operacional|
|930130|Tentativa de acesso a arquivo restrito|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível ataque de RFI (inclusão de arquivo remoto) = parâmetro de URL usando endereço IP|
|931110|Possível ataque de RFI (inclusão de arquivo remoto) = nome de parâmetro vulnerável RFI comum usado com carga de URL|
|931120|Possível ataque de RFI (inclusão de arquivo remoto) = carga de URL usada com caractere de interrogação (?)|
|931130|Possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932100|Execução de comando remoto: injeção de comando Unix|
|932105|Execução de comando remoto: injeção de comando Unix|
|932106|Execução de comando remoto: injeção de comando Unix|
|932110|Execução de comando remoto: injeção de comando do Windows|
|932115|Execução de comando remoto: injeção de comando do Windows|
|932120|Execução de comando remoto = encontrado comando do Windows PowerShell|
|932130|Execução de comando remoto = encontrada expressão de Shell do Unix|
|932140|Execução de comando remoto = encontrado comando Windows FOR/IF|
|932150|Execução de comando remoto: execução direta de comando Unix|
|932160|Execução de comando remoto = encontrado código de Shell do Unix|
|932170|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932180|Tentativa de carregamento de arquivo restrito|
|932190|Execução de comando remoto: tentativa de técnica de bypass de curinga|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção de PHP = marcação de abertura/fechamento encontrada|
|933110|Ataque de injeção de PHP = carregamento de arquivo de script PHP encontrado|
|933111|Ataque de injeção de PHP: carregamento de arquivo de script PHP encontrado|
|933120|Ataque de injeção de PHP = política de configuração encontrada|
|933130|Ataque de injeção de PHP = variáveis encontradas|
|933131|Ataque de injeção de PHP: variáveis encontradas|
|933140|Ataque de injeção de PHP: fluxo de e/s encontrado|
|933150|Ataque de injeção de PHP = nome da função PHP de alto risco encontrado|
|933151|Ataque de injeção de PHP: nome de função PHP Medium-Risk encontrado|
|933160|Ataque de injeção de PHP = chamada de função PHP de alto risco encontrada|
|933161|Ataque de injeção de PHP: chamada de função PHP Low-Value encontrada|
|933170|Ataque de injeção de PHP: injeção de objeto serializado|
|933180|Ataque de injeção de PHP = chamada de função de variável encontrada|
|933190|Ataque de injeção de PHP: marca de fechamento de PHP encontrada|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque de XSS detectado via libinjection|
|941101|Ataque de XSS detectado via libinjection|
|941110|Filtro XSS - Categoria 1 = vetor de marca de script|
|941130|Filtro XSS - Categoria 3 = vetor de atributo|
|941140|Filtro XSS - Categoria 4 = Vector de URI de Javascript|
|941150|Filtro XSS - Categoria 5 = atributos HTML não permitidos|
|941160|NoScript XSS InjectionChecker: injeção de HTML|
|941170|NoScript XSS InjectionChecker: injeção de atributo|
|941180|Palavras-chave de Node-Validator blocklist|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando JavaScript ofuscado|
|941220|XSS usando script VB ofuscado|
|941230|XSS usando a marca ' embed '|
|941240|XSS usando o atributo ' import ' ou ' Implementation '|
|941250|Filtros XSS do IE – ataque detectado|
|941260|XSS usando a marca ' meta '|
|941270|XSS usando href ' link '|
|941280|XSS usando a marca ' base '|
|941290|XSS usando a marca ' applet '|
|941300|XSS usando a marca ' Object '|
|941310|Filtro XSS de codificação mal feita US-ASCII - ataque detectado.|
|941320|Possível ataque XSS detectado - manipulador de marcação HTML|
|941330|Filtros XSS do IE - ataque detectado.|
|941340|Filtros XSS do IE - ataque detectado.|
|941350|Codificação UTF-7 IE XSS - ataque detectado.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção de SQL detectado via libinjection|
|942110|Ataque de injeção de SQL: teste de injeção comum detectado|
|942120|Ataque de injeção de SQL: operador SQL detectado|
|942130|Ataque de injeção de SQL: SQL tautology detectado.|
|942140|Ataque de injeção de SQL = nomes comuns do BD detectados|
|942150|Ataque de injeção de SQL|
|942160|Detecta testes sqli cegos usando sleep() ou benchmark().|
|942170|Detecta tentativas de injeção de sleep e benchmark SQL, incluindo consultas condicionais|
|942180|Detecta tentativas de bypass de autenticação SQL básica 1/3|
|942190|Detecta tentativas de coleta de informações e de execução do código MSSQL|
|942200|Detecta injeções de comment-/space-obfuscated e o encerramento de backticks do MySQL|
|942210|Detecta tentativas de injeção de SQL encadeadas 1/2|
|942220|Procurando ataques de estouro de inteiros, eles são obtidos de skipfish, exceto 3.0.00738585072|
|942230|Detecta tentativas de injeção de SQL condicionais|
|942240|Detecta a opção de conjunto de caracteres MySQL e tentativas do DoS MSSQL|
|942250|Detecta a correspondência em relação a, MESCLAr e executar injeçãos IMEDIATAs|
|942251|Detecta injeções de HAVING|
|942260|Detecta tentativas básicas de bypass de autenticação SQL 2/3|
|942270|Procurando injeção de sql básica. Cadeia de caracteres de ataque comum para MySQL Oracle e outros|
|942280|Detecta postgres injeção de pg_sleep, WAITFOR atrasa ataques e tentativas de desligamento de banco de dados|
|942290|Localiza tentativas de injeção de SQL MongoDB básica|
|942300|Detecta comentários, condições e injeções ch(a)r do MySQL|
|942310|Detecta tentativas de injeção de SQL encadeadas 2/2|
|942320|Detecta injeções de função/procedimento armazenado em MySQL e PostgreSQL|
|942330|Detecta investigações de injeção de SQL clássicas 1/2|
|942340|Detecta tentativas básicas de bypass de autenticação SQL 3/3|
|942350|Detecta injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Detecta injeções de SQL básicas concatenadas e tentativas de SQLLFI|
|942361|Detecta a injeção de SQL básica com base na alteração de palavra-chave ou União|
|942370|Detecta investigações de injeção de SQL clássicas 2/2|
|942380|Ataque de injeção de SQL|
|942390|Ataque de injeção de SQL|
|942400|Ataque de injeção de SQL|
|942410|Ataque de injeção de SQL|
|942420|Detecção de anomalias de caracteres SQL restrita (cookies): número de caracteres especiais excedido (8)|
|942421|Detecção de anomalias de caracteres SQL restrita (cookies): número de caracteres especiais excedido (3)|
|942430|Detecção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12)|
|942431|Detecção de anomalias de caracteres SQL restrita (args): número de caracteres especiais excedido (6)|
|942432|Detecção de anomalias de caracteres SQL restrita (args): número de caracteres especiais excedido (2)|
|942440|Sequência de comentário SQL detectada.|
|942450|Codificação hexadecimal de SQL identificada|
|942460|Alerta de detecção de anomalias de metacaracteres - caracteres repetitivos que não são palavras|
|942470|Ataque de injeção de SQL|
|942480|Ataque de injeção de SQL|
|942490|Detecta sondagens de injeção SQL clássicas 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação da sessão = definindo valores de cookie em HTML|
|943110|Possível ataque de fixação da sessão = nome do parâmetro de SessionID com referenciador fora do domínio|
|943120|Possível ataque de fixação da sessão = nome do parâmetro de SessionID sem referenciador|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-944-APLICATIVO-ATAQUE-SESSÃO-JAVA</p>

|RuleId|Descrição|
|---|---|
|944120|Execução de carga possível e execução de comando remoto|
|944130|Classes Java suspeitas|
|944200|Exploração da desserialização do Java Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Conjuntos de regras

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Limite possivelmente incomparável de várias partes.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-913-DETECÇÃO DE SCANNER</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado Agente-Usuário associado ao verificador de segurança|
|913110|Encontrado cabeçalho de solicitação associado ao verificador de segurança|
|913120|Encontrado nome de arquivo/argumento de solicitação associado ao verificador de segurança|
|913101|Encontrado Agente-Usuário associado a cliente HTTP de scripting/genérico|
|913102|Encontrado Agente-Usuário associado a bot/rastreador Web|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de solicitação de HTTP inválida|
|920130|Falha ao analisar o corpo da solicitação.|
|920140|Falha na validação estrita do corpo da solicitação de várias partes|
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
|920200|Intervalo = excesso de campos (6 ou mais)|
|920201|Intervalo = excesso de campos de solicitação de pdf (35 ou mais)|
|920230|Várias codificações de URL detectadas|
|920300|Falta cabeçalho de aceitação da solicitação|
|920271|Caractere inválido na solicitação (caracteres não imprimíveis)|
|920320|Cabeçalho do agente de usuário ausente|
|920272|Caractere inválido na solicitação (além dos caracteres imprimíveis abaixo de ascii 127)|
|920202|Intervalo = excesso de campos de solicitação de pdf (6 ou mais)|
|920273|Caractere inválido na solicitação (além do conjunto muito estrito)|
|920274|Caractere inválido no cabeçalho da solicitação (além do conjunto muito estrito)|
|920460|Caracteres de escape anormal|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921100|Ataque de solicitação HTTP indesejada.|
|921110|Ataque de solicitação HTTP indesejada|
|921120|Ataque de divisão de resposta HTTP|
|921130|Ataque de divisão de resposta HTTP|
|921140|Ataque de injeção de cabeçalho HTTP por meio de cabeçalhos|
|921150|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF detectado)|
|921160|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF e header-name detectado)|
|921151|Ataque de injeção de cabeçalho HTTP por meio de conteúdo (CR/LF detectado)|
|921170|Poluição de parâmetro HTTP|
|921180|Poluição de parâmetro HTTP (% @{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataques de percurso de caminho (/../)|
|930110|Ataques de percurso de caminho (/../)|
|930120|Tentativa de acesso ao arquivo do sistema operacional|
|930130|Tentativa de acesso a arquivo restrito|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível ataque de RFI (inclusão de arquivo remoto) = parâmetro de URL usando endereço IP|
|931110|Possível ataque de RFI (inclusão de arquivo remoto) = nome de parâmetro vulnerável RFI comum usado com carga de URL|
|931120|Possível ataque de RFI (inclusão de arquivo remoto) = carga de URL usada com caractere de interrogação (?)|
|931130|Possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932120|Execução de comando remoto = encontrado comando do Windows PowerShell|
|932130|Execução de comando remoto = encontrada expressão de Shell do Unix|
|932140|Execução de comando remoto = encontrado comando Windows FOR/IF|
|932160|Execução de comando remoto = encontrado código de Shell do Unix|
|932170|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de comando remoto = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção de PHP = marcação de abertura/fechamento encontrada|
|933110|Ataque de injeção de PHP = carregamento de arquivo de script PHP encontrado|
|933120|Ataque de injeção de PHP = política de configuração encontrada|
|933130|Ataque de injeção de PHP = variáveis encontradas|
|933150|Ataque de injeção de PHP = nome da função PHP de alto risco encontrado|
|933160|Ataque de injeção de PHP = chamada de função PHP de alto risco encontrada|
|933180|Ataque de injeção de PHP = chamada de função de variável encontrada|
|933151|Ataque de injeção de PHP = nome da função PHP de médio risco encontrado|
|933131|Ataque de injeção de PHP = variáveis encontradas|
|933161|Ataque de injeção de PHP = chamada de função PHP de valor baixo encontrada|
|933111|Ataque de injeção de PHP = carregamento de arquivo de script PHP encontrado|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque de XSS detectado via libinjection|
|941110|Filtro XSS - Categoria 1 = vetor de marca de script|
|941130|Filtro XSS - Categoria 3 = vetor de atributo|
|941140|Filtro XSS - Categoria 4 = Vector de URI de Javascript|
|941150|Filtro XSS - Categoria 5 = atributos HTML não permitidos|
|941180|Palavras-chave de Node-Validator blocklist|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando JavaScript ofuscado|
|941220|XSS usando script VB ofuscado|
|941230|XSS usando a marca ' embed '|
|941240|XSS usando o atributo ' import ' ou ' Implementation '|
|941260|XSS usando a marca ' meta '|
|941270|XSS usando href ' link '|
|941280|XSS usando a marca ' base '|
|941290|XSS usando a marca ' applet '|
|941300|XSS usando a marca ' Object '|
|941310|Filtro XSS de codificação mal feita US-ASCII - ataque detectado.|
|941330|Filtros XSS do IE - ataque detectado.|
|941340|Filtros XSS do IE - ataque detectado.|
|941350|Codificação UTF-7 IE XSS - ataque detectado.|
|941320|Possível ataque XSS detectado - manipulador de marcação HTML|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção de SQL detectado via libinjection|
|942110|Ataque de injeção de SQL: teste de injeção comum detectado|
|942130|Ataque de injeção de SQL: SQL tautology detectado.|
|942140|Ataque de injeção de SQL = nomes comuns do BD detectados|
|942160|Detecta testes sqli cegos usando sleep() ou benchmark().|
|942170|Detecta tentativas de injeção de sleep e benchmark SQL, incluindo consultas condicionais|
|942190|Detecta tentativas de coleta de informações e de execução do código MSSQL|
|942200|Detecta injeções de comment-/space-obfuscated e o encerramento de backticks do MySQL|
|942230|Detecta tentativas de injeção de SQL condicionais|
|942260|Detecta tentativas básicas de bypass de autenticação SQL 2/3|
|942270|Procurando injeção de sql básica. Cadeia de caracteres de ataque comum para mysql oracle e outros.|
|942290|Localiza tentativas de injeção de SQL MongoDB básica|
|942300|Detecta comentários, condições e injeções ch(a)r do MySQL|
|942310|Detecta tentativas de injeção de SQL encadeadas 2/2|
|942320|Detecta injeções de função/procedimento armazenado em MySQL e PostgreSQL|
|942330|Detecta investigações de injeção de SQL clássicas 1/2|
|942340|Detecta tentativas básicas de bypass de autenticação SQL 3/3|
|942350|Detecta injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Detecta injeções de SQL básicas concatenadas e tentativas de SQLLFI|
|942370|Detecta investigações de injeção de SQL clássicas 2/2|
|942150|Ataque de injeção de SQL|
|942410|Ataque de injeção de SQL|
|942430|Detecção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12)|
|942440|Sequência de comentário SQL detectada.|
|942450|Codificação hexadecimal de SQL identificada|
|942251|Detecta injeções de HAVING|
|942460|Alerta de detecção de anomalias de metacaracteres - caracteres repetitivos que não são palavras|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação da sessão = definindo valores de cookie em HTML|
|943110|Possível ataque de fixação da sessão = nome do parâmetro de SessionID com referenciador fora do domínio|
|943120|Possível ataque de fixação da sessão = nome do parâmetro de SessionID sem referenciador|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Conjuntos de regras

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Descrição|
|---|---|
|960911|Linha de solicitação de HTTP inválida|
|981227|Erro do Apache = URI inválido na solicitação.|
|960912|Falha ao analisar o corpo da solicitação.|
|960914|Falha na validação estrita do corpo da solicitação de várias partes|
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

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

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

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|Descrição|
|---|---|
|960209|Nome do argumento muito longo|
|960208|Valor do argumento muito longo|
|960335|Muitos argumentos na solicitação|
|960341|Tamanho total de argumentos excedido|
|960342|Tamanho do arquivo carregado muito grande|
|960343|Tamanho total de arquivos carregados muito grande|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|Descrição|
|---|---|
|960032|O método não é permitido pela política|
|960010|O tipo de conteúdo da solicitação não é permitido pela política|
|960034|A versão do protocolo HTTP não é permitida pela política|
|960035|A extensão de arquivo da URL é restrita pela política|
|960038|O cabeçalho HTTP é restrita pela política|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|Descrição|
|---|---|
|990002|A solicitação indica que um verificador de segurança verificou o site|
|990901|A solicitação indica que um verificador de segurança verificou o site|
|990902|A solicitação indica que um verificador de segurança verificou o site|
|990012|Rastreador do site invasor|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

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

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

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

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

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

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|Descrição|
|---|---|
|950103|Ataques de percurso de caminho|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Descrição|
|---|---|
|950110|Acesso ao backdoor|
|950921|Acesso ao backdoor|
|950922|Acesso ao backdoor|

---

## <a name="next-steps"></a>Próximas etapas

- [Personalizar regras de firewall do aplicativo Web usando o portal do Azure](application-gateway-customize-waf-rules-portal.md)

---
title: "Limites e configuração – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Valores de limites e de configuração do serviço para os Aplicativos Lógicos do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 4babb3033e75edc5c85ce89dac569b9f2beae9f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-limits-and-configuration"></a>Limites e configuração de Aplicativos Lógicos

Esse tópico descreve os limites atuais e detalhes de configuração para Aplicativos Lógicos do Azure.

## <a name="limits"></a>limites

### <a name="http-request-limits"></a>Limites de solicitação HTTP

Esses limites se aplicam a uma única solicitação HTTP ou uma chamada de conector.

#### <a name="timeout"></a>Tempo limite

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Tempo limite da solicitação | 120 segundos | Um [padrão assíncrono](../logic-apps/logic-apps-create-api-app.md) ou [loop until](logic-apps-loops-and-scopes.md) pode compensar, conforme necessário |
|||| 

#### <a name="message-size"></a>Tamanho da mensagem

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Tamanho da mensagem | 100 MB | Alguns conectores e APIs podem não oferecer suporte a 100 MB. | 
| Limite de avaliação da expressão | 131.072 caracteres | `@concat()`, `@base64()`, `string` não podem ser maiores do que esse limite. | 
|||| 

#### <a name="retry-policy"></a>Política de repetição

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Tentativas de repetição | 90 | O padrão é 4. Você pode configurar com o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Atraso máximo de nova tentativa | 1 dia | Você pode configurar com o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Atraso mínimo de nova tentativa | 5 segundos | Você pode configurar com o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Retenção e duração da execução

Estes limites se aplicam à execução de um único aplicativo lógico.

| Nome | Limite | 
| ---- | ----- | 
| Duração da execução | 90 dias | 
| Retenção de armazenamento | 90 dias a partir da hora de início de execução | 
| Intervalo de recorrência mín. | 1 segundo </br>Para aplicativos lógicos com Plano do Serviço de Aplicativo: 15 segundos | 
| Intervalo de recorrência máx. | 500 dias | 
||| 

Para exceder os limites de retenção de duração ou armazenamento no fluxo de processamento normal, [entre em contato conosco](mailto://logicappsemail@microsoft.com) para que podemos ajudar com suas necessidades.

### <a name="looping-and-debatching-limits"></a>Limites de loop e debatching

Estes limites se aplicam à execução de um único aplicativo lógico.

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Itens ForEach | 100.000 | Você pode usar a [ação de consulta](../connectors/connectors-native-query.md) para filtrar matrizes maiores, conforme o necessário. | 
| Iterações Until | 5.000 | | 
| Itens SplitOn | 100.000 | | 
| Paralelismo de ForEach | 50 | O padrão é 20. <p>Para definir um nível específico de paralelismo em um loop ForEach, defina a propriedade `runtimeConfiguration` na ação `foreach`. <p>Para executar em sequência um loop ForEach, defina a propriedade `operationOptions` como "Sequencial" na ação `foreach`. | 
|||| 

### <a name="throughput-limits"></a>Limites de taxa de transferência

Estes limites se aplicam uma única instância de aplicativo lógico.

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Execuções de ações a cada cinco minutos | 100.000 | Pode distribuir carga de trabalho entre vários aplicativos conforme necessário. | 
| Chamadas de saída simultâneas a ações | ~2.500 | Diminua o número de solicitações simultâneas ou reduza a duração conforme necessário. | 
| Ponto de extremidade de tempo de execução: chamadas de entrada simultâneas | ~1,000 | Diminua o número de solicitações simultâneas ou reduza a duração conforme necessário. | 
| Ponto de extremidade de tempo de execução: lê chamadas por 5 minutos | 60.000 | Pode distribuir carga de trabalho entre vários aplicativos conforme necessário. | 
| Ponto de extremidade de tempo de execução: invoca chamadas por 5 minutos | 45,000 | Pode distribuir carga de trabalho entre vários aplicativos conforme necessário. | 
|||| 

Para exceder esses limites no processamento normal ou executar um teste de carga que possa exceder esses limites, [entre em contato conosco](mailto://logicappsemail@microsoft.com) para que possamos ajudá-lo com suas necessidades.

### <a name="logic-app-definition-limits"></a>Limites de definição de aplicativos lógicos

Estes limites se aplicam uma única definição de aplicativo lógico.

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Ações por fluxo de trabalho | 500 | Para estender esse limite, adicione fluxos de trabalho aninhados conforme necessário. |
| Profundidade de aninhamento de ação permitida | 8 | Para estender esse limite, adicione fluxos de trabalho aninhados conforme necessário. | 
| Fluxos de trabalho por região e assinatura | 1000 | | 
| Gatilhos por fluxo de trabalho | 10 | | 
| Limite de casos de escopo do comutador | 25 | | 
| Número de variáveis por fluxo de trabalho | 250 | | 
| Máximo de caracteres por expressão | 8.192 | | 
| Tamanho máximo de `trackedProperties` em caracteres | 16.000 | 
| `action`/`trigger`  | 80 | | 
| `description`  | 256 | | 
| `parameters` limit | 50 | | 
| `outputs` limit | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Limites do conector personalizado

Esses limites se aplicam a conectores personalizados que você pode criar de APIs da Web.

| Nome | Limite | 
| ---- | ----- | 
| Número de conectores personalizados que você pode criar | 1.000 por assinatura do Azure | 
| Número de solicitações por minuto para cada conexão criada por um conector personalizado | 500 solicitações para cada conexão criada pelo conector |
||| 

### <a name="integration-account-limits"></a>Limites da conta de integração

Estes limites se aplicam aos artefatos que podem ser adicionados a uma conta de integração.

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Esquema | 8 MB | Você pode usar [URI do blob](../logic-apps/logic-apps-enterprise-integration-schemas.md) para carregar arquivos com mais de 2 MB. | 
| Mapa (arquivo XSLT) | 2 MB | | 
| Ponto de extremidade de tempo de execução: lê chamadas por 5 minutos | 60.000 | Pode distribuir carga de trabalho entre várias contas conforme necessário. | 
| Ponto de extremidade de tempo de execução: invoca chamadas por 5 minutos | 45,000 | Pode distribuir carga de trabalho entre várias contas conforme necessário. | 
| Ponto de extremidade do tempo de execução: acompanhando chamadas por 5 minutos | 45,000 | Pode distribuir carga de trabalho entre várias contas conforme necessário. | 
| Ponto de extremidade de tempo de execução: bloqueando chamadas simultâneas | ~1,000 | Diminua o número de solicitações simultâneas ou reduza a duração conforme necessário. | 
|||| 

Estes limites se aplicam ao número de artefatos que podem ser adicionados a uma conta de integração.

#### <a name="free-pricing-tier"></a>Tipo de preço Gratuito

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Contratos | 10 | | 
| Outros tipos de artefato | 25 |Os tipos de artefatos incluem parceiros, esquemas, certificados e mapas. Cada tipo pode conter até o número máximo de artefatos. | 
|||| 

#### <a name="standard-pricing-tier"></a>Tipo de preço Standard

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| Qualquer tipo de artefato | 500 | Os tipos de artefatos incluem contratos, parceiros, esquemas, certificados e mapas. Cada tipo pode conter até o número máximo de artefatos. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Tamanho da mensagem dos protocolos B2B (AS2, X12, EDIFACT)

Esses limites se aplicam a protocolos B2B.

| Nome | Limite | Observações | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Aplicável ao decodificar e codificar | 
| X12 | 50 MB | Aplicável ao decodificar e codificar | 
| EDIFACT | 50 MB | Aplicável ao decodificar e codificar | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuração: endereços IP

### <a name="logic-apps-service"></a>Serviço de Aplicativos Lógicos

As chamadas que um aplicativo lógico faz diretamente, ou seja, por meio de [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) ou outras solicitações HTTP, provenientes de endereços IP nessa lista.

|Região de Aplicativos Lógicos|IP de Saída|
|-----------------|-----------|
|Leste da Austrália|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sudeste da Austrália|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sul do Brasil|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Canadá Central|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Leste do Canadá|52.232.128.155, 52.229.120.45, 52.229.126.25|
|Índia Central|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Centro dos EUA|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Ásia Oriental|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Leste dos EUA|13.92.98.111, 40.121.91.41, 40.114.82.191|
|Leste dos EUA 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Leste do Japão|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Oeste do Japão|40.74.140.4, 104.214.137.243, 138.91.26.45|
|Centro-Norte dos EUA|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Norte da Europa|40.113.12.95, 52.178.165.215, 52.178.166.21|
|Centro-Sul dos Estados Unidos|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Sudeste Asiático|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Sul da Índia|52.172.50.24, 52.172.55.231, 52.172.52.0|
|Centro-Oeste dos EUA|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Europa Ocidental|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Índia Ocidental|104.211.164.80, 104.211.162.205, 104.211.164.136|
|Oeste dos EUA|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Oeste dos EUA 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Sul do Reino Unido|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Oeste do Reino Unido|51.141.54.185, 51.141.45.238, 51.141.47.136|
| | |

### <a name="connectors"></a>Conectores

As chamadas que [conectores](../connectors/apis-list.md) fazem são provenientes de endereços IP nessa lista.

|Região de Aplicativos Lógicos|IP de Saída|
|-----------------|-----------|
|Leste da Austrália|40.126.251.213|
|Sudeste da Austrália|40.127.80.34|
|Sul do Brasil|191.232.38.129|
|Canadá Central|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Leste do Canadá|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|Índia Central|104.211.98.164|
|Centro dos EUA|40.122.49.51|
|Ásia Oriental|23.99.116.181|
|Leste dos EUA|191.237.41.52|
|Leste dos EUA 2|104.208.233.100|
|Leste do Japão|40.115.186.96|
|Oeste do Japão|40.74.130.77|
|Centro-Norte dos EUA|65.52.218.230|
|Norte da Europa|104.45.93.9|
|Centro-Sul dos Estados Unidos|104.214.70.191|
|Sudeste Asiático|13.76.231.68|
|Sul da Índia|104.211.227.225|
|Europa Ocidental|40.115.50.13|
|Índia Ocidental|104.211.161.203|
|Oeste dos EUA|104.40.51.248|
|Sul do Reino Unido|51.140.80.51|
|Oeste do Reino Unido|51.141.47.105|
| | | 

## <a name="next-steps"></a>Próximas etapas  

* [Criar seu primeiro aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)  
* [Exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Vídeo: automatizar processos de negócios com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Vídeo: integrar seus sistemas com os Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/P462)

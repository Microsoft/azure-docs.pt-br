---
title: Analisar o desempenho de nó de borda na CDN do Azure | Microsoft Docs
description: Analisar o desempenho do nó de borda no CDN do Microsoft Azure A análise de desempenho de borda fornece informações detalhadas de uso de tráfego e largura de banda para o CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7dfa252c29121adca2ecc77c08b2fca81d56e575
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61069891"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analisar o desempenho do nó de borda no CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Visão geral
A análise de desempenho de borda fornece informações detalhadas de uso de tráfego e largura de banda para o CDN. Essas informações, em seguida, podem ser usadas para gerar estatísticas de tendências, que permitem que você obtenha mais informações sobre como os seus ativos estão sendo armazenados em cache e entregues aos clientes. Por sua vez, isso permite que você forme uma estratégia sobre como otimizar a entrega de conteúdo e determinar quais problemas devem ser resolvidos para aproveitar melhor o CDN. Como resultado, não só será possível melhorar o desempenho de entrega de dados, mas você também poderá reduzir os custos com CDN.

> [!NOTE]
> Todos os relatórios usam notação UTC/GMT, ao especificar um valor de data/hora.
> 
> 

## <a name="reports-and-log-collection"></a>Coleta de logs e relatórios
Os dados de atividade CDN devem ser coletados pelo módulo de análise de desempenho de borda antes que ele possa gerar relatórios sobre os mesmos. Esse processo de coleta ocorre uma vez por dia e abrange a atividade que ocorreu durante o dia anterior. Isso significa que as estatísticas do relatório representam uma amostra das estatísticas do dia quando ele foi processado e não necessariamente contém o conjunto completo de dados para o dia atual. A principal função desses relatórios é avaliar o desempenho. Eles não devem ser usados para fins de cobrança ou estatísticas numéricas exatas.

> [!NOTE]
> Os dados brutos do qual são gerados relatórios de análise de desempenho de borda ficam disponíveis por pelo menos 90 dias.
> 
> 

## <a name="dashboard"></a>painel
O painel de análise de desempenho de borda controla o tráfego CDN atual e o histórico por meio de um gráfico e de estatísticas. Use este painel para detectar tendências de longo prazo e recentes sobre o desempenho do tráfego CDN para sua conta.

Este painel consiste em:

* Um gráfico interativo que permite a visualização das principais tendências e métricas.
* Uma linha do tempo que fornece uma noção dos padrões de longo prazo das principais tendências e métricas.
* As principais métricas e informações estatísticas sobre como nossa rede CDN melhora o tráfego do site, conforme medido pelo desempenho, uso e a eficiência geral.

### <a name="accessing-the-edge-performance-dashboard"></a>Acessar o painel de desempenho de borda
1. Na folha do perfil do CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o ponteiro do mouse sobre a guia **Análise** e, em seguida, sobre o submenu **Análise de desempenho de borda**.  Clique em **Painel**.
   
    O painel de análise de nó de borda é exibido.

### <a name="chart"></a>Gráfico
O painel contém um gráfico que rastreia uma métrica ao longo do período selecionado na linha do tempo que aparece diretamente abaixo dele.  Uma linha do tempo com um grafo que inclui até os últimos dois anos de atividade CDN é exibida diretamente abaixo do gráfico.

#### <a name="using-the-chart"></a>Usando o gráfico
* Por padrão, a taxa de eficiência de cache para os últimos 30 dias será representada.
* Este gráfico é gerado diariamente a partir de dados agrupados.
* Passar a seta do mouse sobre um dia no grafo de linha indicará uma data e o valor da métrica nessa data.
* Clique em Realçar finais de semana para ativar ou desativar uma sobreposição de luz barras verticais cinzas que representam os finais de semana para o gráfico. Esse tipo de sobreposição é útil para identificar padrões de tráfego nos finais de semana.
* Clique em Exibir o ano anterior para ativar ou desativar uma sobreposição de atividade do ano anterior no mesmo período de tempo para o gráfico. Esse tipo de comparação fornece informações sobre padrões de uso CDN a longo prazo. O canto superior direito do grafo contém uma legenda que indica o código de cor para cada grafo de linha.

#### <a name="updating-the-chart"></a>Atualizando o gráfico
* Intervalo de tempo: Execute uma das ações a seguir:
  * Selecione a região desejada na linha do tempo. O gráfico será atualizado com dados que correspondem ao período de tempo selecionado.
  * Clique duas vezes no gráfico para exibir todos os dados históricos disponíveis até um máximo de dois anos.
* Métrica: Clique no ícone de gráfico que aparece ao lado da métrica desejada. O gráfico e a linha do tempo serão atualizados com dados para a métrica correspondente.

### <a name="key-metrics-and-statistics"></a>Principais métricas e estatísticas
#### <a name="efficiency-metrics"></a>Métricas de eficiência
A finalidade dessas métricas é verificar se é possível melhorar a eficiência do cache. Os principais benefícios derivados de eficiência de cache são:

* Redução de carga no servidor de origem que pode levar a:
  * Um melhor desempenho do servidor principal
  * Redução dos custos operacionais.
* Uma melhor aceleração de entrega de dados, pois mais solicitações serão servidas diretamente a partir do CDN.

| Campo | DESCRIÇÃO |
| --- | --- |
| Eficiência de cache |Indica a porcentagem dos dados transferidos que foram servidos a partir do cache. Essa métrica mede quando uma versão em cache do conteúdo solicitado foi fornecida diretamente do CDN (servidores de borda) para os solicitantes (por exemplo, o navegador da web) |
| Taxa de acertos |Indica a porcentagem de solicitações que foram atendidas no cache. Essa métrica mede quando uma versão armazenada em cache do conteúdo solicitado foi fornecida diretamente do CDN (servidores de borda) para os solicitantes (por exemplo, o navegador da web) |
| % de bytes remotos - Nenhuma configuração de cache |Indica a porcentagem de tráfego que foi fornecida dos servidores de origem para o CDN (servidores de borda) que não será armazenada em cache como resultado do recurso de Ignorar cache (mecanismo de regras de HTTP). |
| % de bytes remotos - Cache expirado |Indica a porcentagem de tráfego que foi atendida dos servidores de origem para o CDN (servidores de borda) como resultado de revalidação de conteúdo obsoleto. |

#### <a name="usage-metrics"></a>Métricas de uso
O objetivo dessas métricas é fornecer informações sobre as seguintes medidas de redução de custos:

* Minimizar os custos operacionais por meio do CDN.
* Reduzir as despesas com CDN por meio de eficiência de cache e compactação.

> [!NOTE]
> OS números de volume de tráfego representam o tráfego que foi usado em cálculos de razões e porcentagens e pode mostrar apenas uma parte do tráfego total para clientes de grande volume.
> 
> 

| Campo | DESCRIÇÃO |
| --- | --- |
| Média de bytes enviados |Indica o número médio de bytes transferidos para cada solicitação atendida do CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web). |
| Nenhuma taxa de byte de configuração de cache |Indica a porcentagem de tráfego atendido a partir do CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web) que não será armazenado em cache devido ao recurso de Ignorar cache. |
| Taxa de bytes compactados |Indica a porcentagem de tráfego enviado do CDN (servidores de borda) para os solicitantes (por exemplo, o navegador da web) em um formato compactado. |
| Bytes de saída |Indica a quantidade de dados, em bytes, que foram entregues do CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web). |
| Bytes de entrada |Indica a quantidade de dados, em bytes, enviadas de solicitantes (por exemplo, o navegador da web) para o CDN (servidores de borda). |
| Bytes remotos |Indica a quantidade de dados, em bytes, enviadas do CDN e dos servidores de origem do cliente para o CDN (servidores de borda). |

#### <a name="performance-metrics"></a>Métricas de desempenho
O objetivo dessas métricas é acompanhar o desempenho geral do CDN para o tráfego.

| Campo | DESCRIÇÃO |
| --- | --- |
| Taxa de transferência |Indica a taxa média, em que o conteúdo foi transferido do CDN para um solicitante. |
| Duration |Indica o tempo médio, em milissegundos, gasto para fornecer um ativo para um solicitante (por exemplo, o navegador da web). |
| Taxa de solicitação compactada |Indica a porcentagem de acertos enviada do CDN (servidores de borda) para os solicitantes (por exemplo, o navegador da web) em um formato compactado. |
| Taxa de erros 4xx |Indica a porcentagem de acertos que gerou um código de status 4xx. |
| Taxa de erros 5xx |Indica a porcentagem de acertos que gerou um código de status 5xx. |
| Acertos |Indica o número de solicitações de conteúdo do CDN. |

#### <a name="secure-traffic-metrics"></a>Métricas de tráfego de segurança
O objetivo dessas métricas é acompanhar o desempenho do CDN para o tráfego de HTTPS.

| Campo | DESCRIÇÃO |
| --- | --- |
| Eficiência de cache seguro |Indica a porcentagem de dados transferidos para solicitações de HTTPS que foram atendidas a partir do cache. Essa métrica mede quando uma versão em cache do conteúdo solicitado foi fornecida diretamente do CDN (servidores de borda) para os solicitantes (por exemplo, o navegador da web) por HTTPS. |
| Taxa de transferência segura |Indica a taxa média, em que o conteúdo foi transferido do CDN (servidores de borda) para os solicitantes (por exemplo, servidores web) por HTTPS. |
| Duração média segura |Indica o tempo médio, em milissegundos, gasto para fornecer um ativo para um solicitante (por exemplo, o navegador da web) por HTTPS. |
| Acertos seguros |Indica o número de solicitações de HTTPS para o conteúdo do CDN. |
| Bytes de saída seguros |Indica a quantidade de tráfego de HTTPS, em bytes, que foram entregues do CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web). |

## <a name="reports"></a>Relatórios
Cada relatório neste módulo contém um gráfico e as estatísticas de uso de largura de banda e o tráfego para diferentes tipos de métricas (por exemplo, códigos de status HTTP, códigos de status do cache, solicitação de URL, etc.). Essas informações podem ser usadas para obter mais detalhes sobre como o conteúdo está sendo fornecido aos seus clientes e para ajustar o comportamento do CDN para melhorar o desempenho de entrega de dados.

### <a name="accessing-the-edge-performance-reports"></a>Acessando os relatórios de desempenho de borda
1. Na folha do perfil do CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o ponteiro do mouse sobre a guia **Análise** e, em seguida, sobre o submenu **Análise de desempenho de borda**.  Clique em **Objeto grande de HTTP**.
   
    A tela de relatórios de análise de nó de borda é exibido.

| Relatório | DESCRIÇÃO |
| --- | --- |
| Resumo diário |Permite que você veja tendências diárias de tráfego em um período de tempo especificado. Cada barra no grafo representa uma data específica. O tamanho da barra indica o número total de ocorrências que ocorreram nessa data. |
| Resumo por hora |Permite que você veja tendências de tráfego por hora em um período de tempo especificado. Cada barra no grafo representa uma única hora em uma determinada data. O tamanho da barra indica o número total de ocorrências que ocorreu durante essa hora. |
| Protocolos |Exibe a análise de tráfego entre os protocolos HTTP e HTTPS. Um gráfico de rosca indica a porcentagem de acertos que ocorreu para cada tipo de protocolo. |
| Métodos HTTP |Permite que você obtenha uma ideia de quais métodos HTTP estão sendo usados para solicitar seus dados. Normalmente, os métodos de solicitação HTTP mais comuns são GET, HEAD e POST. Um gráfico de rosca indica a porcentagem de acertos que ocorreu para cada tipo de método de solicitação de HTTP. |
| URLs |Contém um grafo que exibe as 10 principais URLs solicitadas. É exibida uma barra para cada URL. A altura da barra indica quantas ocorrências aquela URL específica gerou ao longo do tempo coberto pelo relatório. As estatísticas das 100 URLs mais solicitadas são exibidas diretamente abaixo desse grafo. |
| CNAMEs |Contém um grafo que exibe os 10 principais CNAMEs usados para solicitar ativos ao longo do tempo de um relatório. As estatísticas dos 100 CNAMEs mais solicitados são exibidos diretamente abaixo desse grafo. |
| Origens |Contém um grafo que exibe os 10 principais ou servidores de origem do cliente ou CDN dos quais os ativos foram solicitados por um período de tempo especificado. As estatísticas dos 100 servidores de origem do cliente ou CDN mais solicitados são exibidos diretamente abaixo desse grafo. Os servidores de origem do cliente são identificados pelo nome definido na opção de Nome de diretório. |
| POPs geográficos |Mostra a quantidade do tráfego que está sendo roteado por meio de um determinado ponto-de-presença (POP). A abreviação de três letras representa um POP em nossa rede CDN. |
| Clientes |Contém um grafo que exibe os 10 principais clientes que solicitaram ativos durante um período de tempo especificado. Para os fins deste relatório, todas as solicitações originadas do mesmo endereço IP são consideradas como sendo do mesmo cliente. As estatísticas dos 100 clientes mais solicitados são exibidas diretamente abaixo desse grafo. Este relatório é útil para determinar os padrões de atividade de download para os principais clientes. |
| Status do Cache |Fornece uma análise detalhada do comportamento de cache, que pode revelar abordagens para aprimorar a experiência geral do usuário final. Uma vez que o desempenho mais rápido provêm de acertos do cache, você pode otimizar velocidades de entrega de dados minimizando erros de cache e acertos de cache expirados. |
| Detalhes NONE |Contém um grafo que exibe as 10 principais URLs para ativos para os quais a atualização de conteúdo de cache não foi verificada durante um período de tempo especificado. As estatísticas das 100 principais URLs para estes tipos de ativos são exibidas diretamente abaixo desse grafo. |
| Detalhes CONFIG_NOCACHE |Contém um grafo que exibe as 10 principais URLs para ativos que não foram armazenados em cache devido à configuração de CDN do cliente. Esses tipos de ativos foram atendidos diretamente a partir do servidor de origem. As estatísticas das 100 principais URLs para estes tipos de ativos são exibidas diretamente abaixo desse grafo. |
| Detalhes UNCACHEABLE |Contém um grafo que exibe as 10 principais URLs para ativos que não puderam ser armazenados em cache devido a dados de cabeçalho de solicitação. As estatísticas das 100 principais URLs para estes tipos de ativos são exibidas diretamente abaixo desse grafo. |
| Detalhes TCP_HIT |Contém um grafo que exibe as 10 principais URLs para ativos que são atendidas imediatamente a partir do cache. As estatísticas das 100 principais URLs para estes tipos de ativos são exibidas diretamente abaixo desse grafo. |
| Detalhes TCP_MISS |Contém um grafo que exibe as 10 principais URLs para ativos com um status de cache de TCP_MISS. As estatísticas das 100 principais URLs para estes tipos de ativos são exibidas diretamente abaixo desse grafo. |
| Detalhes TCP_EXPIRED_HIT |Contém um grafo que exibe as 10 principais URLs para ativos obsoletos que foram atendidos imediatamente a partir do POP. As estatísticas das 100 principais URLs para estes tipos de ativos são exibidas diretamente abaixo desse grafo. |
| Detalhes TCP_EXPIRED_MISS |Contém um grafo que exibe as 10 principais URLs para ativos obsoletos para o qual uma nova versão precisava ser recuperada do servidor de origem. As estatísticas das 100 principais URLs para estes tipos de ativos são exibidas diretamente abaixo desse grafo. |
| Detalhes TCP_CLIENT_REFRESH_MISS |Contém um gráfico de barras que mostra as 10 principais URLs para ativos que foram recuperados de um servidor de origem devido a uma solicitação sem cache do cliente. As estatísticas das 100 principais URLs para estes tipos de solicitações são exibidas diretamente abaixo desse gráfico. |
| Tipos de solicitação do cliente |Indica o tipo de solicitações feitas pelos clientes HTTP (por exemplo, navegadores). Este relatório inclui um gráfico de rosca que fornece uma ideia sobre como as solicitações estão sendo tratadas. As informações de largura de banda e o tráfego para cada tipo de solicitação são exibidas abaixo do gráfico. |
| Agente do usuário |Contém um grafo de barras exibindo os 10 principais agentes do usuário para solicitar seu conteúdo por meio de nosso CDN. Normalmente, um agente de usuário é um navegador da web, media player ou um navegador do celular. As estatísticas para os 100 principais agentes do usuário são exibidas diretamente abaixo desse gráfico. |
| Referenciadores |Contém um grafo de barras exibindo os 10 principais referenciadores ao conteúdo acessado por meio do nosso CDN. Normalmente, um referenciador é a URL da página da web ou do recurso que está vinculado ao seu conteúdo. Informações detalhadas são fornecidas abaixo do grafo para os 100 principais referenciadores. |
| Tipos de compressão |Contém um gráfico de rosca que divide os ativos solicitados entre os que foram e os que não foram compactados nos nossos servidores de borda. A porcentagem de ativos compactados é dividida pelo tipo de compactação usado. Informações detalhadas são fornecidas abaixo do grafo para cada tipo e status de compressão. |
| Tipos de arquivo |Contém um grafo de barras que exibe os 10 tipos de arquivo que foram solicitados por meio do nosso CDN para sua conta. Para os fins deste relatório, um tipo de arquivo é definido pela extensão de nome de arquivo do ativo e o tipo de mídia da Internet (por exemplo, .html \[text/html\], .htm \[text/html\], .aspx \[text/html\], etc.). Informações detalhadas são fornecidas abaixo do grafo para os 100 tipos de arquivo. |
| Arquivos exclusivos |Contém um grafo que plota o número total de ativos exclusivos que foram solicitados em um determinado dia durante um período de tempo especificado. |
| Resumo de autenticação de token |Contém um gráfico de pizza que fornece uma visão geral sobre se ativos solicitados foram protegidos pela autenticação baseada em Token. Ativos protegidos são exibidos no gráfico de acordo com os resultados da sua tentativa de autenticação. |
| Detalhes da autenticação de token negada |Contém um grafo de barras que permite que você veja os 10 principais pedidos que foram negados devido à autenticação baseada em token. |
| Códigos de resposta HTTP |Fornece uma análise dos códigos de status HTTP (por exemplo, 200 OK, 403 Forbidden, 404 Not Found, etc.) que foram entregues aos clientes HTTP por nossos servidores de borda. Um gráfico de pizza permite avaliar rapidamente como seus ativos foram atendidos. Dados estatísticos detalhados são fornecidos para cada código de resposta abaixo do grafo. |
| Erros 404 |Contém um grafo de barras que permite que você visualize as 10 principais solicitações que resultaram em um código de resposta 404 Not Found. |
| Erros 403 |Contém um grafo de barras que permite que você visualize as 10 principais solicitações que resultaram em um código de resposta 403 Forbidden. Um código de resposta 403 Forbidden ocorre quando uma solicitação for negada por um servidor de origem do cliente ou um servidor de borda em nosso POP. |
| Erros 4xx |Contém um grafo de barras que permite que você visualize as 10 principais solicitações que resultaram em um código de resposta no intervalo de 400. Os códigos de resposta 403 Not Found e 404 Forbidden não estão incluídos neste relatório. Normalmente, um código de resposta 4xx ocorre quando uma solicitação for negada devido a um erro do cliente. |
| Erros 504 |Contém um grafo de barras que permite que você visualize as 10 principais solicitações que resultaram em um código de resposta 504 Gateway Timeout. Um código de resposta 504 Gateway Timeout ocorre quando um tempo limite ocorre quando um proxy HTTP está tentando se comunicar com outro servidor. No caso do nosso CDN, um código de resposta 504 Gateway Timeout normalmente ocorre quando um servidor de borda não consegue estabelecer comunicação com um servidor de origem do cliente. |
| Erros 502 |Contém um grafo de barras que permite que você visualize as 10 principais solicitações que resultaram em um código de resposta 502 Bad Gateway. Um código de resposta 502 Bad Gateway ocorre quando ocorre uma falha de protocolo HTTP entre um servidor e um proxy HTTP. No caso do nosso CDN, um código de resposta 502 Bad Gateway normalmente ocorre quando um servidor de origem do cliente retorna uma resposta inválida para um servidor de borda. Uma resposta é inválida, se ela não pode ser analisada ou se está incompleta. |
| Erros 5xx |Contém um grafo de barras que permite que você visualize as 10 principais solicitações que resultaram em um código de resposta no intervalo de 500.  Os códigos de resposta 502 Bad Gateway e 504 Gateway Timeout não estão incluídos no relatório. |

## <a name="see-also"></a>Consulte também
* [Visão geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real na CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)


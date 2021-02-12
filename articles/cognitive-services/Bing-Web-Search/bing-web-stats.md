---
title: Adicionar análise à API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: As Estatísticas do Bing fornecem análise para a API de Pesquisa de Imagem do Bing. A análise inclui o volume de chamadas, as principais cadeias de caracteres de consulta, a distribuição geográfica e muito mais.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 02fea02cca5950ef8467377a866e9a765af9e2e1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349547"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Adicionar análise ao APIs de Pesquisa do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

As estatísticas do Bing fornecem análises para o APIs de Pesquisa do Bing. Essas análises incluem o volume de chamada, as principais cadeias de caracteres de consulta, a distribuição geográfica e muito mais. Você pode habilitar as estatísticas do Bing no [portal do Azure](https://ms.portal.azure.com) navegando até o recurso do Azure e clicando em **habilitar estatísticas do Bing**.

> [!IMPORTANT]
> * As estatísticas do Bing não estão disponíveis com recursos no `F0` tipo de preço gratuito.
> * Não é possível usar dados disponíveis por meio do painel das Estatísticas do Bing para criar aplicativos para distribuição a terceiros.
> * A habilitação das estatísticas do Bing aumenta um pouco sua taxa de assinatura. Consulte os [preços](https://aka.ms/bingstatisticspricing) para obter detalhes.


A imagem a seguir mostra a análise disponível para cada ponto de extremidade de API Pesquisa do Bing.

![Distribuição por matriz de suporte de ponto de extremidade](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Acessar sua análise

O Bing atualiza dados de análise a cada 24 horas e mantém um histórico de até 13 meses que você pode acessar por meio do [painel de análise](https://bingapistatistics.com). Verifique se você está conectado usando o mesmo conta Microsoft (MSA) usado para se inscrever nas estatísticas do Bing.

> [!NOTE]  
> * Pode demorar até 24 horas para que as métricas apareçam no painel. O painel mostra a data e hora em que os dados foram atualizados pela última vez.  
> * As métricas estarão disponíveis a partir do momento que você habilitar o suplemento de Estatísticas do Bing.

## <a name="filter-the-data"></a>Filtrar os dados

Por padrão, os gráficos exibem todas as métricas e os dados aos quais você tem acesso. É possível filtrar os dados mostrados nos gráficos e grafos, selecionando os recursos, mercados, pontos de extremidade e período de relatório nos quais está interessado. Você pode alterar os seguintes filtros:

- **ID do recurso**: a ID exclusiva do recurso que identifica a assinatura do Azure. Se você assinar mais de uma camada de API de Pesquisa do Bing, a lista conterá várias IDs. Por padrão, todos os recursos são selecionados.  
  
- **Mercados**: os mercados de onde vêm os resultados. Por exemplo, en-us (inglês, Estados Unidos). Por padrão, todos os mercados são selecionados. O `en-WW` mercado é o mercado que o Bing usa se a chamada não especificar um mercado e o Bing não puder determinar o mercado do usuário.  
  
- **Pontos de extremidade**: os pontos de extremidade de API de Pesquisa do Bing. A lista contém todos os pontos de extremidade para os quais você tem uma assinatura paga. Por padrão, todos os pontos de extremidade são selecionados.  

- **Período**: o período do relatório. É possível especificar:
  - **Todos**: inclui até 13 meses de dados  
  - **Últimas 24 horas**: inclui análise das últimas 24 horas  
  - **Última semana**: inclui análises dos 7 dias anteriores  
  - **Mês passado**: inclui análises dos últimos 30 dias  
  - **Um intervalo de datas personalizado**: inclui a análise do intervalo de datas especificado, se disponível  

## <a name="charts-and-graphs"></a>Gráficos e grafos

O painel mostra gráficos e grafos das métricas disponíveis para o ponto de extremidade selecionado. Nem todas as métricas estão disponíveis para todos os pontos de extremidade. Os gráficos e grafos de cada ponto de extremidade são estáticos (não é possível selecionar os gráficos e grafos a serem exibidos). O painel mostra apenas gráficos e grafos para os quais há dados.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

A seguir estão as possíveis métricas e restrições de ponto de extremidade.

- **Volume da chamada**: mostra o número de chamadas feitas durante o período do relatório. Se o período do relatório for de um dia, o gráfico mostrará o número de chamadas feitas por hora. Caso contrário, o gráfico mostra o número de chamadas feitas por dia do período do relatório.  
  
  > [!NOTE]
  > O volume de chamadas pode diferir dos relatórios de cobrança, que geralmente incluem apenas chamadas com êxito.

- **Principais consultas**: mostra as principais consultas e o número de ocorrências de cada consulta durante o período do relatório. É possível configurar o número de consultas mostradas. Por exemplo, é possível mostrar as 25, 50 ou 75 principais consultas. As principais consultas não estão disponíveis para os pontos de extremidade a seguir:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Alguns termos de consulta podem ser suprimidos para remover informações confidenciais, como emails, números de telefone, SSN etc.

- **Distribuição geográfica**: os mercados nos quais os resultados da pesquisa são originados. Por exemplo, `en-us` (Inglês, Estados Unidos). O Bing usa o parâmetro de consulta `mkt` para determinar o mercado, se especificado. Caso contrário, o Bing usa sinais como o endereço IP do chamador para determinar o mercado.

- **Distribuição de código de resposta**: os códigos de status HTTP de todas as chamadas durante o período do relatório.

- **Distribuição de origem de chamada**: os tipos de navegadores usados pelos usuários. Por exemplo, Microsoft Edge, Chrome, Safari e FireFox. As chamadas feitas de fora de um navegador (como bots, postmaster ou uso de ondulação de um aplicativo de console) são agrupadas em bibliotecas. A origem é determinada usando o valor do cabeçalho do Agente do Usuário da solicitação. Se a solicitação não incluir o cabeçalho do Agente do Usuário, o Bing tentará derivar a origem de outros sinais.  

- **Distribuição de pesquisa segura**: a distribuição de valores de pesquisa segura. Por exemplo, desativado, moderado ou estrito. O parâmetro de consulta `safeSearch` contém o valor, se especificado. Caso contrário, o Bing padroniza o valor para moderado.  

- **Respostas de distribuição solicitada**: a API de pesquisa na Web responde que você solicitou no `responseFilter` parâmetro de consulta.  

- **Distribuição de respostas retornadas**: as respostas que a API de Pesquisa da Web retornou na resposta.

- **Distribuição do servidor de resposta**: o servidor de aplicativos que atendeu as solicitações de API. Os valores possíveis são Bing.com (para tráfego veiculado em dispositivos de desktop e laptop) e Bing.com-mobile (para tráfego veiculado em dispositivos móveis). O servidor é determinado usando o valor do cabeçalho do Agente do Usuário da solicitação. Se a solicitação não incluir o cabeçalho do Agente do Usuário, o Bing tentará derivar o servidor de outros sinais.

## <a name="next-steps"></a>Próximas etapas

* [Quais são as APIs de Pesquisa do Bing?](bing-api-comparison.md)
* [Requisitos de uso e exibição da API de Pesquisa do Bing](use-display-requirements.md)
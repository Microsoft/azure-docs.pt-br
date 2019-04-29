---
title: Analisar estatísticas de uso com os Relatórios HTTP Avançados da CDN do Azure | Microsoft Docs
description: Saiba como criar relatórios HTTP avançados na CDN do Microsoft Azure. Esses relatórios fornecem informações detalhadas sobre a atividade da CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c8cb4713e38ca0da610c687325f3810f57da2b26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216030"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analisar estatísticas de uso com os relatórios HTTP avançados da CDN do Azure
## <a name="overview"></a>Visão geral
Este documento explica os relatórios HTTP avançados na CDN do Microsoft Azure. Esses relatórios fornecem informações detalhadas sobre a atividade da CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Acessando relatórios HTTP avançados
1. Na folha do perfil do CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **Análise**, em seguida, passe o mouse sobre o submenu **Relatórios HTTP Avançados**.  Clique em **Plataforma Grande HTTP**.
   
    ![Portal de gerenciamento da CDN - menu Relatórios Avançados](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    As opções de relatório são exibidas.

## <a name="geography-reports-map-based"></a>Relatórios de Geografia (baseados em mapa)
Há cinco relatórios que tiram proveito de um mapa para indicar as regiões de onde o conteúdo está sendo solicitado. Esses relatórios são Mapa mundial, Mapa dos Estados Unidos, Mapa do Canadá, Mapa da Europa e Mapa do Pacífico Asiático.

Cada relatório baseado em mapa classifica as entidades geográficas (ou seja, países, estados e províncias) de acordo com a porcentagem de ocorrências originadas da região. Além disso, um mapa é fornecido para ajudá-lo a visualizar os locais de onde o conteúdo está sendo solicitado. É possível fazer isso codificando por cores cada região de acordo com a quantidade da demanda percebida nelas. As regiões com cores mais claras indicam menor demanda por seu conteúdo, enquanto regiões mais escuras indicam altos níveis de demanda por seu conteúdo.

As informações detalhadas de tráfego e de largura de banda para cada região são fornecidas diretamente abaixo do mapa. Isso permite que você exiba o número total e a porcentagem de ocorrências, a quantidade total de dados transferidos (em gigabytes) e a porcentagem dos dados transferidos para cada região. Exiba uma descrição para cada uma das métricas. Finalmente, quando você focaliza uma região (por exemplo, país, estado ou província), o nome e a porcentagem de ocorrências na região serão exibidos como uma dica de ferramenta.

Uma breve descrição abaixo é apresentada abaixo para cada tipo de relatório geográfico baseado em mapa.

| Nome do relatório | DESCRIÇÃO |
| --- | --- |
| Mapa mundial |Esse relatório permite que você exiba a demanda mundial por seu conteúdo CDN. Cada país é codificado por cor no mapa mundial para indicar a porcentagem de ocorrências originadas da região. |
| Mapa dos Estados Unidos |Esse relatório permite que você exiba a demanda por seu conteúdo CDN nos Estados Unidos. Cada estado é codificado por cor no mapa para indicar a porcentagem de ocorrências originadas da região. |
| Mapa do Canadá |Esse relatório permite que você exiba a demanda por seu conteúdo CDN no Canadá. Cada município é codificado por cor no mapa para indicar a porcentagem de ocorrências originadas da região. |
| Mapa da Europa |Esse relatório permite que você exiba a demanda por seu conteúdo CDN na Europa. Cada país é codificado por cores nesse mapa para indicar a porcentagem de ocorrências originadas da região. |
| Mapa do Pacífico Asiático |Esse relatório permite que você exiba a demanda por seu conteúdo CDN na Ásia. Cada país é codificado por cores nesse mapa para indicar a porcentagem de ocorrências originadas da região. |

## <a name="geography-reports-bar-charts"></a>Relatórios de geografia (gráficos de barras)
Há dois relatórios adicionais que fornecem informações estatísticas de acordo com a geografia, que são Principais cidades e Principais países. Esses relatórios classificam cidades e países, respectivamente, de acordo com o número de ocorrências originadas dessas regiões. Ao gerar esse tipo de relatório, um gráfico de barras indicará as dez principais cidades ou países que solicitaram o conteúdo em uma plataforma específica. Esse gráfico de barras permite avaliar rapidamente as regiões que geram o maior número de solicitações de seu conteúdo.

O lado esquerdo do grafo (eixo y) indica o número de ocorrências na região especificada. Diretamente abaixo do grafo (eixo x), você encontrará um rótulo para cada uma das dez regiões principais.

### <a name="using-the-bar-charts"></a>Usando gráficos de barras
* Se você passar o mouse sobre uma barra, o nome e o número total de ocorrências na região serão exibidos como uma dica de ferramenta.
* A dica de ferramenta para o relatório Principais cidades identifica uma cidade pelo nome, pelo estado/província e pela abreviação do país.
* Se a cidade ou região (ou seja, estado/província) de onde se originou uma solicitação não puder ser determinada, ela será indicada como desconhecida. Se o país for desconhecido, dois pontos de interrogação (isto é, ??) serão exibidos.
* Um relatório pode incluir as métricas para “Europa” ou para a “Região do Pacífico Asiático”. Esses itens não foram criados para fornecer informações estatísticas sobre todos os endereços IP nessas áreas. Na verdade, eles se aplicam somente às solicitações originadas de endereços IP distribuídos pela Europa ou pela Ásia/Pacífico, em vez de uma cidade ou país específico.

Os dados usados para gerar o gráfico de barras podem ser exibidos abaixo dele. Lá, você encontrará o número total e a porcentagem de ocorrências, a quantidade total de dados transferidos (em gigabytes) e a porcentagem dos dados transferidos para as 250 regiões principais. Exiba uma descrição para cada uma das métricas.

Uma breve descrição é apresentada para ambos os tipos de relatórios abaixo.

| Nome do relatório | DESCRIÇÃO |
| --- | --- |
| Principais cidades |Este relatório classifica cidades de acordo com o número de ocorrências originadas dessa região. |
| Principais países |Este relatório classifica países de acordo com o número de ocorrências originadas dessa região. |

## <a name="daily-summary"></a>Resumo diário
O relatório Resumo diário permite exibir o número total de ocorrências e de dados transferidos por uma plataforma específica diariamente. Essas informações podem ser usadas de discernir rapidamente padrões de atividade da CDN. Por exemplo, esse relatório pode ajudá-lo a detectar quais dias tiveram tráfego maior ou menor que o esperado.

Ao gerar esse tipo de relatório, um gráfico de barras fornece uma indicação visual da quantidade de demanda específica da plataforma percebida diariamente ao longo do período coberto pelo relatório. Ele fará isso exibindo uma barra para cada dia no relatório. Por exemplo, a seleção do período de tempo "Semana Passada" irá gerar um gráfico de barras com sete barras. Cada barra indica o número total de ocorrências percebidas naquele dia.

O lado esquerdo do grafo (eixo y) indica o número de ocorrências na data especificada. Diretamente abaixo do grafo (eixo x), você encontrará um rótulo que indica a data (formato: YYYY-MM-DD) para cada dia incluído no relatório.

> [!TIP]
> Se você passar o mouse sobre uma barra, o número total de ocorrências naquela data será exibido como uma dica de ferramenta.
> 
> 

Os dados usados para gerar o gráfico de barras podem ser exibidos abaixo dele. Lá, você encontrará o número total de ocorrências e a quantidade de dados transferidos (em gigabytes) para cada dia coberto pelo relatório.

## <a name="by-hour"></a>Por hora
O relatório Por hora permite exibir o número total de ocorrências e de dados transferidos por uma plataforma específica por hora. Essas informações podem ser usadas de discernir rapidamente padrões de atividade da CDN. Por exemplo, esse relatório pode ajudá-lo a detectar os períodos de tempo durante o dia que tiveram tráfego maior ou menor que o esperado.

Ao gerar esse tipo de relatório, um gráfico de barras fornece uma indicação visual da quantidade de demanda específica da plataforma percebida por hora ao longo do período coberto pelo relatório. Ele fará isso exibindo uma barra para cada hora coberta pelo relatório. Por exemplo, a seleção de um período de 24 horas irá gerar um gráfico de barras com vinte e quatro barras. Cada barra indica o número total de ocorrências percebidas naquela hora.

O lado esquerdo do grafo (eixo y) indica o número de ocorrências na hora especificada. Diretamente abaixo do grafo (eixo x), você encontrará um rótulo que indica a data/hora (formato: YYYY-MM-DD HH: mm) para cada hora incluída no relatório. A hora é relatada usando o formato de 24 horas e é especificada usando o fuso horário UTC/GMT.

> [!TIP]
> Se você passar o mouse sobre uma barra, o número total de ocorrências durante aquela hora será exibido como uma dica de ferramenta.
> 
> 

Os dados usados para gerar o gráfico de barras podem ser exibidos abaixo dele. Lá, você encontrará o número total de ocorrências e a quantidade de dados transferidos (em gigabytes) para cada hora coberta pelo relatório.

## <a name="by-file"></a>Por arquivo
O relatório Por arquivo permite que você exiba a quantidade de demanda e o tráfego ocorridos em uma plataforma específica para os ativos mais solicitados. Ao gerar este tipo de relatório, um gráfico de barras será gerado sobre os dez ativos mais solicitados durante o período de tempo especificado.

> [!NOTE]
> Para os fins deste relatório, as URLs da borda CNAME são convertidas nas URLs equivalentes da CDN. Isso permite uma contagem precisa do número total de ocorrências associadas a um ativo, independentemente da URL da CDN ou da borda CNAME URL usada para solicitá-las.
> 
> 

O lado esquerdo do grafo (eixo y) indica o número de solicitações para cada ativo durante o período de tempo especificado. Diretamente abaixo do grafo (eixo x), você encontrará um rótulo que indica o nome do arquivo de cada um dos dez ativos mais solicitados.

Os dados usados para gerar o gráfico de barras podem ser exibidos abaixo dele. Lá, você encontrará as seguintes informações sobre cada um dos 250 ativos mais solicitados: o número total e a porcentagem de ocorrências, a quantidade total de dados transferidos (em gigabytes) e a porcentagem dos dados transferidos.

## <a name="by-file-detail"></a>Por detalhes do arquivo
O relatório Por detalhes do arquivo permite que você exiba a quantidade de demanda e o tráfego ocorridos em uma plataforma específica para um ativo específico. A opção Por detalhes do arquivo está na parte superior deste relatório. Essa opção fornece uma lista de seus ativos mais solicitados na plataforma selecionada. Para gerar um relatório Por detalhes do arquivo, você precisa selecionar o ativo desejado na opção Detalhes do Arquivo Para. Depois disso, um gráfico de barras indicará a quantidade de demanda diária que ele gerou durante o período de tempo especificado.

O lado esquerdo do grafo (eixo y) indica o número total de solicitações recebidas por um ativo em um dia específico. Diretamente abaixo do grafo (eixo x), você encontrará um rótulo que indica a data (formato: YYYY-MM-DD) para a qual CDN por demanda para o ativo foi relatada.

Os dados usados para gerar o gráfico de barras podem ser exibidos abaixo dele. Lá, você encontrará o número total de ocorrências e a quantidade de dados transferidos (em gigabytes) para cada dia coberto pelo relatório.

## <a name="by-file-type"></a>Por tipo de arquivo
O relatório Por tipo de arquivo permite que você exiba a quantidade de demanda e o tráfego gerados por tipo de arquivo. Ao gerar este tipo de relatório, um gráfico de rosca indica a porcentagem de ocorrências geradas pelos dez principais tipos de arquivo.

> [!TIP]
> Se você passar o mouse sobre uma fatia no gráfico de rosca, o tipo de mídia de Internet daquele tipo de arquivo será exibido como uma dica de ferramenta.
> 
> 

Os dados usados para gerar o gráfico de rosca podem ser exibidos abaixo dele. Lá, você encontrará o tipo de mídia de Internet/extensão de nome de arquivo, o número total de acessos, a porcentagem de ocorrências, a quantidade de dados transferidos (em gigabytes) e a porcentagem de dados transferidos para cada um dos 250 principais tipos de arquivo.

## <a name="by-directory"></a>Por diretório
O relatório Por diretório permite que você exiba a quantidade de demanda e o tráfego ocorridos em uma plataforma específica relativo a conteúdo de um diretório específico. Ao gerar este tipo de relatório, um gráfico de barras indica o número total de ocorrências geradas pelo conteúdo nos dez diretórios principais.

### <a name="using-the-bar-chart"></a>Usando o gráfico de barras
* Passe o mouse sobre uma barra para exibir o caminho relativo para o diretório correspondente.
* O conteúdo armazenado em uma subpasta de um diretório não será contado no cálculo da demanda por diretório. Esse cálculo depende exclusivamente do número de solicitações gerado para o conteúdo armazenado no diretório real.
* Para os fins deste relatório, as URLs da borda CNAME são convertidas nas URLs equivalentes da CDN. Isso permite uma contagem precisa de todas as estatísticas associadas a um ativo, independentemente da URL da CDN ou da borda CNAME URL usada para solicitá-las.

O lado esquerdo do grafo (eixo y) indica o número total de solicitações para o conteúdo armazenado em seus dez diretórios principais. Cada barra no gráfico representa um diretório. Use o esquema de codificação de cores para fazer a correspondência entre uma barra e um diretório listado na seção 250 principais diretórios completos.

Os dados usados para gerar o gráfico de barras podem ser exibidos abaixo dele. Lá, você encontrará as seguintes informações sobre cada um dos principais 250 diretórios: o número total e a porcentagem de ocorrências, a quantidade total de dados transferidos (em gigabytes) e a porcentagem dos dados transferidos.

## <a name="by-browser"></a>Por navegador
O relatório Por navegador permite que você veja quais navegadores foram usados para solicitar o conteúdo. Ao gerar este tipo de relatório, um gráfico de pizza indica a porcentagem de solicitações tratadas pelos dez principais navegadores.

### <a name="using-the-pie-chart"></a>Usando o gráfico de pizza
* Passe o mouse sobre uma fatia no gráfico de pizza para exibir o nome e a versão do navegador.
* Para fins de relatório, cada combinação exclusiva navegador/versão é considerada um navegador diferente.
* A fatia chamada "Outros" indica a porcentagem de solicitações tratadas por todos os outros navegadores e versões.

Os dados usados para gerar o gráfico de pizza podem ser exibidos abaixo dele. Lá, você encontrará o tipo/número de versão do navegador, o número total de ocorrências e a porcentagem de ocorrências para cada um dos 250 principais navegadores.

## <a name="by-referrer"></a>Por referenciador
O relatório Por referenciador permite exibir os principais referenciadores de conteúdo na plataforma selecionada. Um referenciador indica o nome do host do qual uma solicitação foi gerada. Ao gerar este tipo de relatório, um gráfico de barras indica a quantidade de demanda (isto é, ocorrências) gerada pelos dez principais referenciadores.

O lado esquerdo do grafo (eixo y) indica o número total de solicitações recebidas por um ativo para cada referenciador. Cada barra no gráfico representa um referenciador. Use o esquema de codificação de cores para fazer a correspondência entre uma barra e um referenciador listado na seção 250 principais referenciadores.

Os dados usados para gerar o gráfico de barras podem ser exibidos abaixo dele. Lá você encontrará a URL, o número total e a porcentagem de ocorrências geradas por cada um dos 250 principais referenciadores.

## <a name="by-download"></a>Por download
O relatório Por download permite analisar padrões de download para o conteúdo mais solicitado. A parte superior do relatório contém um gráfico de barras que compara tentativas de downloads com downloads concluídos para os dez ativos mais solicitados. Cada barra é codificada por cores separando as tentativas de download (azul) dos downloads concluídos (verde).

> [!NOTE]
> Para os fins deste relatório, as URLs da borda CNAME são convertidas nas URLs equivalentes da CDN. Isso permite uma contagem precisa de todas as estatísticas associadas a um ativo, independentemente da URL da CDN ou da borda CNAME URL usada para solicitá-las.
> 
> 

O lado esquerdo do grafo (eixo y) indica o nome do arquivo para cada um dos dez ativos mais solicitados. Diretamente abaixo do grafo (eixo x), você encontrará rótulos que indicam o número total de downloads tentados/concluídos.

Diretamente abaixo do gráfico de barras, as informações a seguir serão listadas para os 250 ativos mais solicitados: caminho relativo (incluindo nome do arquivo), número de vezes que seu download foi concluído, número de vezes que foi solicitado e a porcentagem de solicitações que resultaram em um download completo.

> [!TIP]
> Nossa CDN não é informada por um cliente HTTP (ou seja, o navegador) quando um ativo é baixado por completo. Como resultado, temos que calcular se um ativo foi baixado por completo de acordo com os códigos de status e solicitações de intervalo de bytes. A primeira coisa que procuramos ao fazer esse cálculo é saber se a solicitação resulta em um código de status 200 OK. Nesse caso, vamos examinar as solicitações de intervalo de bytes para garantir que abrangem todo o ativo. Por fim, podemos comparar a quantidade de dados transferidos ao tamanho do ativo solicitado. Se os dados transferidos forem iguais ou maiores que o tamanho do arquivo e as solicitações de intervalo de bytes forem apropriadas para esse ativo, a ocorrência será contada como um download completo.
> 
> Devido à natureza interpretativa desse relatório, você deve ter em mente os pontos a seguir que podem alterar a consistência e a precisão dele.
> 
> * Padrões de tráfego não podem ser previstos com precisão quando os agentes-usuários se comportam de maneira diferente. Isso pode produzir resultados de download concluído acima de 100%.
> * Ativos que tiram proveito do download progressivo de HTTP podem não ser representados com precisão no relatório. Isso ocorre porque os usuários buscam posições diferentes em um vídeo.
> 
> 

## <a name="by-404-errors"></a>Por erros 404
O relatório de erros 404 permite que você identifique o tipo de conteúdo que gera o maior número de códigos de status 404 Não encontrado. A parte superior do relatório contém um gráfico de barras para os dez principais ativos para os quais foi retornado um código de status 404 Não encontrado. Esse gráfico de barras compara o número total de solicitações com solicitações que resultaram em código de status 404 Não encontrado para esses ativos. Cada barra é codificada por cor. Uma barra amarela é usada para indicar que a solicitação resultou em um código de status 404 Não encontrado. Uma barra vermelha é usada para indicar o número total de solicitações do ativo.

> [!NOTE]
> Para os fins desse relatório, observe o seguinte:
> 
> * Uma ocorrência representa qualquer solicitação para um ativo, independentemente do código de status.
> * As URLs de borda CNAME são convertidas nas URLs equivalentes da CDN. Isso permite uma contagem precisa de todas as estatísticas associadas a um ativo, independentemente da URL da CDN ou da borda CNAME URL usada para solicitá-las.
> 
> 

O lado esquerdo do grafo (eixo y) indica o nome do arquivo para cada um dos dez ativos mais solicitados que resultaram em código de status 404 Não encontrado. Diretamente abaixo do grafo (eixo x), você encontrará rótulos que indicam o número total de solicitações e o número de solicitações que resultaram em um código de status 404 Não encontrado.

Diretamente abaixo do gráfico de barras, as informações a seguir serão listadas para os 250 ativos mais solicitados: caminho relativo (incluindo nome do arquivo), número de solicitações que resultaram em código de status 404 Não encontrado, número de vezes que o ativo foi solicitado e a porcentagem de solicitações que resultaram em código de status 404 Não encontrado.

## <a name="see-also"></a>Consulte também
* [Visão geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real na CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Analisar o desempenho de borda](cdn-edge-performance.md)


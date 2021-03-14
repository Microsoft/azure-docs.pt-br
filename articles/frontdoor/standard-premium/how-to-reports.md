---
title: Relatórios Standard/Premium (visualização) do Azure front door
description: Este artigo explica como os relatórios funcionam na porta frontal do Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098548"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Relatórios Standard/Premium (visualização) do Azure front door

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os relatórios de análise Standard/Premium da porta do Azure fornecem uma visão interna e em torno de como a porta de entrada do Azure se comporta junto com as métricas de firewall do aplicativo Web associadas. Você também pode aproveitar os logs de acesso para fazer mais solução de problemas e depuração. Os relatórios de análise de porta frontal do Azure incluem relatórios de tráfego e relatórios de segurança.

| Relatórios | Detalhes |
|---------|---------|
| Visão geral das principais métricas | Mostra os dados gerais que foram enviados das bordas da porta frontal do Azure para os clientes<br/>-Largura de banda de pico<br/>-Solicitações <br/>-Taxa de acertos do cache<br/> -Latência total<br/>-taxa de erros 5XX |
| Tráfego por domínio | -Fornece uma visão geral de todos os domínios no perfil<br/>-Divisão de dados transferidos da borda de AFD para o cliente<br/>-Total de solicitações<br/>-código de resposta de 3XX/4XX/5XX por domínios |
| Tráfego por local | -Mostra uma exibição de mapa de solicitação e uso pelos principais países<br/>-Exibição de tendência dos principais países |
| Uso | -Exibe a transferência de dados da borda da porta frontal do Azure para os clientes<br/>-Transferência de dados para fora da origem para a borda de AFD<br/>-Largura de banda da borda de AFD para clientes<br/>-Largura de banda da origem para a borda de AFD<br/>-Solicitações<br/>-Latência total<br/>-Tendência de contagem de solicitação por código de status HTTP |
| Cache | -Mostra a taxa de acertos do cache por contagem de solicitações<br/>-Exibição de tendência de solicitações de erro e perda |
| URL superior | -Mostra a contagem de solicitações <br/>-Dados transferidos <br/>-Taxa de acertos do cache <br/>-Distribuição de código de status de resposta para os ativos 50 mais solicitados. |
| Principal referenciador | -Mostra a contagem de solicitações <br/>-Dados transferidos <br/>-Taxa de acertos do cache <br/>-Distribuição de código de status de resposta para os principais referenciadores 50 que geram tráfego. |
| Agente principal do usuário | -Mostra a contagem de solicitações <br/>-Dados transferidos <br/>-Taxa de acertos do cache <br/>-Distribuição de código de status de resposta para os principais agentes de usuário 50 que foram usados para solicitar conteúdo. |

| Relatórios de segurança | Detalhes |
|---------|---------|
| Visão geral das principais métricas | -Mostra as regras de WAF correspondentes<br/>-Regras de OWASP correspondentes<br/>-Regras de BOT correspondentes<br/>-Regras personalizadas correspondentes |
| Métricas por dimensões | -Divisão da tendência de regras WAF correspondidas por ação<br/>-Gráfico de rosca de eventos por tipo de conjunto de regras e evento por grupo de regras<br/>-Lista suspensa de principais eventos por ID de regra, país, endereço IP, URL e agente do usuário  |

> [!NOTE]
> Os relatórios de segurança só estão disponíveis com o SKU Premium da porta frontal do Azure.

A maioria dos relatórios baseia-se nos logs de acesso e é oferecida gratuitamente aos clientes na porta de recepção do Azure. O cliente não precisa habilitar os logs de acesso ou fazer nenhuma configuração para exibir esses relatórios. Os relatórios podem ser acessados por meio do portal e da API. O download de CSV também tem suporte. 

Os relatórios dão suporte a qualquer intervalo de datas selecionado nos últimos 90 dias. Com pontos de dados de cada 5 minutos, a cada hora ou todos os dias com base no intervalo de datas selecionado. Normalmente, você pode exibir dados com atraso de uma hora e, ocasionalmente, com atraso de até algumas horas. 

## <a name="access-reports-using-the-azure-portal"></a>Acessar relatórios usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) e selecione o perfil padrão/Premium da porta do Azure.

1. No painel de navegação, selecione **relatórios ou segurança** em *análise*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Captura de tela da página de aterrissagem de relatórios":::

1. Há sete guias para dimensões diferentes, selecione a dimensão de interesse.

   * Tráfego por domínio
   * Uso 
   * Tráfego por local
   * Cache
   * URL superior
   * Principal referenciador
   * Agente principal do usuário

1. Depois de escolher a dimensão, você pode selecionar filtros diferentes.
  
    1. **Mostrar dados para** -selecione o intervalo de datas para o qual você deseja exibir o tráfego por domínio. Os intervalos disponíveis são:
        
        * Últimas 24 horas
        * Últimos 7 dias
        * Últimos 30 dias
        * Últimos 90 dias
        * Este mês
        * Mês passado
        * Data personalizada

         Por padrão, os dados são mostrados nos últimos sete dias. Para guias com gráficos de linhas, a granularidade dos dados é exibida com os intervalos de datas selecionados como o comportamento padrão. 
    
        * 5 minutos-um ponto de dados a cada 5 minutos para intervalos de datas menor ou igual a 24 horas.
        * Por hora – um dado a cada hora para intervalos de datas entre 24 horas e 30 dias
        * Por dia – um dado por dia para intervalos de datas maiores que 30 dias.

        Você sempre pode usar a agregação para alterar a granularidade de agregação padrão. Observação: 5 minutos não funciona para o intervalo de dados por mais de 14 dias. 

    1. **Local** -selecione um ou vários locais de cliente por país. Os países são agrupados em seis regiões: América do Norte, Ásia, Europa, África, Oceania e América do Sul. Consulte [mapeamento de região/país](https://en.wikipedia.org/wiki/Subregion). Por padrão, todos os países são selecionados.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Captura de tela de relatórios para a dimensão de local.":::
   
    1. **Protocolo** -selecione http ou HTTPS para exibir os dados de tráfego.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Captura de tela de relatórios para a dimensão de protocolo.":::
    
    1. **Domínios** – selecione pontos de extremidade únicos ou múltiplos ou domínios personalizados. Por padrão, todos os pontos de extremidade e domínios personalizados são selecionados. 
    
        * Se você excluir um ponto de extremidade ou um domínio personalizado em um perfil e recriar o mesmo ponto de extremidade ou domínio em outro perfil. O ponto de extremidade será considerado um segundo ponto de extremidade.  
        * Se você estiver exibindo relatórios por domínio personalizado, ao excluir um domínio personalizado e associá-lo a um ponto de extremidade diferente. Eles serão tratados como um domínio personalizado. Se exibir por ponto de extremidade-eles serão tratados como itens separados. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Captura de tela de relatórios para a dimensão de domínio.":::

1. Se você quiser exportar os dados para um arquivo CSV, selecione o link *baixar CSV* na guia selecionado.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Captura de tela de baixar arquivo CSV para relatórios.":::

### <a name="key-metrics-for-all-reports"></a>Métricas-chave para todos os relatórios

| Métrica | Descrição |
|---------|---------|
| Dados Transferidos | Mostra os dados transferidos da borda de AFD são POPs para o cliente para o período de tempo, locais do cliente, domínios e protocolos selecionados. |
| Largura de banda de pico | O uso de pico de largura de banda em bits por segundo da borda da porta frontal do Azure é exibido para o cliente para o período de tempo, locais de cliente, domínios e protocolos selecionados. | 
| Total de Solicitações | O número de solicitações que o AFD Edge POPs respondeu ao cliente para o período de tempo, os locais do cliente, os domínios e os protocolos selecionados. |
| Taxa de Acertos do Cache | A porcentagem de todas as solicitações armazenáveis em cache para as quais o AFD atuou o conteúdo de seus caches de borda para o período de tempo, os locais do cliente, os domínios e os protocolos selecionados. |
| Taxa de erros 5XX | A porcentagem de solicitações para as quais o código de status HTTP para o cliente foi um 5XX para o período de tempo, os locais do cliente, os domínios e os protocolos selecionados. |
| Latência total | Latência média de todas as solicitações para o período de tempo, locais de cliente, domínios e protocolos selecionados. A latência de cada solicitação é medida como o tempo total de quando a solicitação do cliente é recebida pela porta frontal do Azure até o último byte de resposta enviado da porta frontal do Azure para o cliente. |

## <a name="traffic-by-domain"></a>Tráfego por domínio

O tráfego por domínio fornece uma exibição de grade de todos os domínios nesse perfil de porta frontal do Azure. Neste relatório, você pode exibir: 
* Requests
* Dados transferidos da porta frontal do Azure para o cliente
* Solicitações com código de status (3XX, 4Xx e 5XX) de cada domínio

Os domínios incluem o ponto de extremidade e os domínios personalizados, conforme explicado na sessão de relatório de acesso.  

Você pode ir para outras guias para investigar ainda mais ou exibir o log de acesso para obter mais informações se encontrar as métricas abaixo da sua expectativa. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Captura de tela da página de aterrissagem para relatórios":::


## <a name="usage"></a>Uso

Este relatório mostra as tendências de tráfego e código de status de resposta por diferentes dimensões, incluindo:

* Dados transferidos do Edge para o cliente e da origem para a borda no gráfico de linhas. 

* Dados transferidos do Edge para o cliente por protocolo no gráfico de linhas. 

* Número de solicitações de borda a clientes no gráfico de linhas.  

* Número de solicitações de borda a clientes por protocolo, HTTP e HTTPS, no gráfico de linhas. 

* Largura de banda da borda para o cliente no gráfico de linhas. 

* Latência total, que mede o tempo total da solicitação do cliente recebida pela porta frontal até o último byte de resposta enviado da porta frontal para o cliente.

* Número de solicitações de borda a clientes por código de status HTTP, no gráfico de linhas. Cada solicitação gera um código de status HTTP. O código de status HTTP aparece em HTTPStatusCode no log bruto. O código de status descreve como a borda da CDN tratou a solicitação. Por exemplo, um código de status 2xx indica que a solicitação foi servida com êxito para um cliente. Enquanto um código de status 4xx indica que ocorreu um erro. Para saber mais sobre os códigos de status HTTP, confira a Lista de códigos de status HTTP. 

* Número de solicitações da borda para clientes por código de status HTTP. Percentual de solicitações por código de status HTTP entre todas as solicitações na grade. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Captura de tela de relatórios por uso" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Tráfego por local

Este relatório exibe os principais locais de 50 pelo país dos visitantes que acessam seu ativo mais. O relatório também fornece uma análise das métricas por país e fornece uma visão geral dos países em que a maior parte do tráfego é gerada. Por fim, você pode ver qual país está tendo uma taxa maior de acertos de cache ou códigos de erro 4XX/5XX.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Captura de tela de relatórios por locais" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Os seguintes itens estão incluídos nos relatórios:

* Uma exibição de mapa mundial dos 50 principais países por dados transferidos ou solicitações de sua escolha.
* Exibição de tendência de dois gráficos de linha dos cinco principais países por dados transferidos e solicitações de sua escolha. 
* Uma grade dos principais países com os dados correspondentes transferidos de AFD para clientes, dados transferidos de% de todos os países, solicitações,% de solicitação entre todos os países, taxa de acertos de cache, código de resposta 4XX e código de resposta 5XX.

## <a name="caching"></a>Cache

Os relatórios de cache fornecem uma exibição de gráfico de acertos/erros de cache e taxa de acertos de cache com base em solicitações. Essas métricas-chave explicam como a CDN está armazenando conteúdo em cache, pois o desempenho mais rápido resulta de acertos de cache. Você pode otimizar as velocidades de entrega de dados minimizando os erros de cache. Este relatório inclui:

* Tendência de perda de cache e contagem de erros no gráfico de linhas.

* Taxa de acertos do cache no gráfico de linhas.

Acertos/erros de cache descrevem o número de solicitação acertos de cache e erros de cache para solicitações de cliente.

* Ocorrências: as solicitações de cliente que são servidas diretamente dos servidores de borda da CDN do Azure. Refere-se a essas solicitações cujos valores de CacheStatus em logs brutos são atingido, PARTIAL_HIT ou acesso remoto. 

* Erro: as solicitações do cliente que são servidas pelos servidores de borda da CDN do Azure buscando conteúdo da origem. Refere-se a essas solicitações cujos valores para o campo CacheStatus em logs brutos são IGNORADOs. 

A **taxa de acertos do cache** descreve a porcentagem de solicitações armazenadas em cache que são servidas diretamente do Edge. A fórmula da taxa de acertos do cache é: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Esse relatório leva os cenários de cache em consideração e as solicitações que atenderam aos seguintes requisitos são levadas em conta de cálculo. 

* O conteúdo solicitado foi armazenado em cache no POP mais próximo ao solicitante ou ao escudo de origem. 

* Conteúdo em cache parcial para Agrupamento de objetos.

Ele exclui todos os seguintes casos: 

* Solicitações que foram negadas devido ao conjunto de regras.

* Solicitações que contêm regras de correspondência definidas que foram definidas para o cache desabilitado. 

* Solicitações que são bloqueadas pelo WAF. 

* Cabeçalhos de resposta de origem indicam que eles não devem ser armazenados em cache. Por exemplo, os cabeçalhos Cache-Control: private, Cache-Control: no-cache ou Pragma: no-cache impedirão que um arquivo seja armazenado em cache. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Captura de tela de relatórios para cache.":::

## <a name="top-urls"></a>Principais URLs

As principais URLs permitem que você exiba a quantidade de tráfego incorrido em um determinado ponto de extremidade ou domínio personalizado. Você verá dados para os ativos 50 mais solicitados durante qualquer período nos últimos 90 dias. URLs populares serão exibidas com os valores a seguir. O usuário pode classificar URLs por contagem de solicitação,% de solicitação, dados transferidos e dados transferidos%. Todas as métricas são agregadas por hora e podem variar de acordo com o período selecionado. URL refere-se ao valor de RequestUri no log de acesso. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Captura de tela de relatórios para a URL superior.":::

* URL, refere-se ao caminho completo do ativo solicitado no formato de `http(s)://contoso.com/index.html/images/example.jpg` . 
* Contagens de solicitação. 
* Solicitação% do total de solicitações atendidas pela porta frontal do Azure. 
* Dados transferidos. 
* % De dados transferidos. 
* % De taxa de acertos do cache
* Solicitações com código de resposta como 4XX
* Solicitações com código de resposta como 5XX

> [!NOTE]
> As principais URLs podem mudar ao longo do tempo e obter uma lista precisa das principais URLs de 50, a porta de front-end do Azure conta todas as solicitações de URL por hora e manter o total acumulado durante o curso de um dia. As URLs na parte inferior das 500 URLs podem aumentar ou soltar a lista ao longo do dia, portanto, o número total dessas URLs é aproximações.  
>
> As principais URLs 50 podem aumentar e cair na lista, mas raramente desaparecem da lista, de modo que os números das principais URLs são geralmente confiáveis. Quando uma URL sai da lista e aumenta novamente em um dia, o número de solicitações durante o período em que elas estão ausentes na lista é estimado com base no número de solicitação da URL que aparece nesse período.  
>
> A mesma lógica se aplica ao agente principal do usuário. 

## <a name="top-referrers"></a>Principais referenciadores

Os principais referenciadores permitem que os clientes exibam o principal referenciador 50 que originou a maioria das solicitações para o conteúdo em um determinado ponto de extremidade ou domínio personalizado. Você pode exibir dados para qualquer período nos últimos 90 dias. Um referenciador indica a URL da qual uma solicitação foi gerada. O referenciador pode vir de um mecanismo de pesquisa ou outros sites. Se um usuário digitar uma URL (por exemplo, http (s)://contoso.com/index.html) diretamente na linha de endereço de um navegador, o referenciador para o solicitado será "vazio". O relatório de principais referenciadores inclui os valores a seguir. Você pode classificar por contagem de solicitações,% de solicitação, dados transferidos e dados transferidos%. Todas as métricas são agregadas por hora e podem variar de acordo com o período selecionado. 

* Referenciador, o valor de referenciador em logs brutos 
* Contagens de solicitações 
* Solicitação% do total de solicitações atendidas pela CDN do Azure no período de tempo selecionado. 
* Dados Transferidos 
* % De dados transferidos 
* % De taxa de acertos do cache
* Solicitações com código de resposta como 4XX
* Solicitações com código de resposta como 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Captura de tela de relatórios para o referenciador principal.":::

## <a name="top-user-agent"></a>Agente principal do usuário

Este relatório permite que você tenha exibição gráfica e estatística dos principais agentes de usuário 50 que foram usados para solicitar conteúdo. Por exemplo,
* Mozilla/5.0 (Windows NT 10,0; WOW64 
* AppleWebKit/537.36 (KHTML, como Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

Uma grade exibe as contagens de solicitação,% de solicitação, dados transferidos e dados transferidos, taxa de acertos do cache%, solicitações com o código de resposta como 4XX e solicitações com código de resposta como 5XX. O agente do usuário refere-se ao valor de UserAgent nos logs do Access.

## <a name="security-report"></a>Relatório de segurança

Este relatório permite que você tenha exibição gráfica e estatística de padrões WAF por diferentes dimensões.

| Dimensões | Descrição |
|---------|---------|
| Métricas de visão geral – regras de WAF correspondentes | Solicitações que correspondem a regras de WAF personalizadas, regras de WAF gerenciadas e Gerenciador de bot. |
| Métricas de visão geral – solicitações bloqueadas | A porcentagem de solicitações que são bloqueadas por regras WAF entre todas as solicitações que corresponderam às regras do WAF. |
| Métricas de visão geral-regras gerenciadas correspondentes | Tendência de quatro linhas de gráficos para solicitações que são bloquear, registrar, permitir e redirecionar. |
| Métricas de visão geral-regra personalizada com correspondência | Solicitações que correspondem às regras de WAF personalizadas. |
| Métricas de visão geral – regra de bot correspondente | Solicitações que correspondem ao Gerenciador de bot. |
| WAF a tendência de solicitação por ação | Tendência de quatro linhas de gráficos para solicitações que são bloquear, registrar, permitir e redirecionar. |
| Eventos por tipo de regra | O gráfico de rosca do WAF solicita a distribuição por tipo de regra, por exemplo, bot, regras personalizadas e regras gerenciadas. |
| Eventos por grupo de regras | Gráfico de rosca do WAF solicita a distribuição por grupo de regras. |
| Solicitações por ações | Uma tabela de solicitações por ações, em ordem decrescente. |
| Solicitações por IDs de regra principais | Uma tabela de solicitações pelas principais IDs de regra de 50, em ordem decrescente. |
| Solicitações por países principais |  Uma tabela de solicitações dos primeiros 50 países, em ordem decrescente. |
| Solicitações por IPs de cliente principais |  Uma tabela de solicitações dos principais IPs de 50, em ordem decrescente. |
| Solicitações por URL de solicitação superior |  Uma tabela de solicitações pelas principais URLs de 50, em ordem decrescente. |
| Solicitar por nomes de host principais | Uma tabela de solicitações pelo nome de host 50 principal, em ordem decrescente. |
| Solicitações por agentes de usuário principais | Uma tabela de solicitações dos principais agentes de usuário do 50, em ordem decrescente. |

## <a name="cvs-format"></a>Formato do CVS

Você pode baixar arquivos CSV para guias diferentes em relatórios. Esta seção descreve os valores em cada arquivo CSV.

### <a name="general-information-about-the-cvs-report"></a>Informações gerais sobre o relatório do CVS

Cada relatório CSV inclui algumas informações gerais e as informações estão disponíveis em todos os arquivos CSV. com variáveis com base no relatório baixado. 


| Valor | Descrição |
|---------|---------|
| Relatório | O nome do relatório. | 
| Domínios | A lista de pontos de extremidade ou domínios personalizados para o relatório. |
| StartDateUTC | O início do intervalo de datas para o qual você gerou o relatório, em tempo universal coordenado (UTC) |
| EndDateUTC | O final do intervalo de datas para o qual você gerou o relatório, em tempo universal coordenado (UTC) |
| GeneratedTimeUTC | A data e a hora em que você gerou o relatório, em tempo universal coordenado (UTC) |
| Local | A lista dos países em que as solicitações do cliente foram originadas. O valor é todos por padrão. Não aplicável ao relatório de segurança. |
| Protocolo | O protocolo da solicitação, HTTP ou HTTPs. Não aplicável à URL e ao tráfego principais por agente do usuário em relatórios e relatório de segurança. |
| Agregação | A granularidade da agregação de dados em cada linha, a cada 5 minutos, a cada hora e todos os dias. Não aplicável ao tráfego por domínio, URL principal e tráfego por agente do usuário em relatórios e relatório de segurança. |

### <a name="data-in-traffic-by-domain"></a>Dados no tráfego por domínio

* Domain 
* Total de solicitações 
* Taxa de Acertos do Cache 
* Solicitações 3XX 
* Solicitações 4XX 
* Solicitações 5XX 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Dados no tráfego por local

* Local
* TotalRequests
* Quest
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Dados em uso

Há três relatórios neste arquivo CSV. Um para o protocolo HTTP, um para o protocolo HTTPS e outro para o código de status HTTP. 

Os relatórios para HTTP e HTTPs compartilham o mesmo conjunto de dados. 

* Hora 
* Protocolo 
* Datatransferid (bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Relatório de código de status HTTP. 

* Hora 
* Datatransferid (bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Dados em cache 

* Hora
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Dados na URL superior 

* URL 
* TotalRequests 
* Quest 
* Datatransferid (bytes) 
* Metadadostransferidos 

### <a name="data-in-user-agent"></a>Dados no agente do usuário 

* UserAgent 
* TotalRequests 
* Quest 
* Datatransferid (bytes) 
* Metadadostransferidos 

### <a name="security-report"></a>Relatório de segurança 

Há sete tabelas todos com os mesmos campos abaixo.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

As sete tabelas são para hora, ID da regra, país, endereço IP, URL, nome do host, agente do usuário. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [métricas de monitoramento do Standard/Premium de porta do Azure](how-to-monitor-metrics.md).

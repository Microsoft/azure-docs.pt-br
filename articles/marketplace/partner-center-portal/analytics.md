---
title: Análise para o Marketplace comercial no Partner Center
description: Saiba como acessar relatórios analíticos para monitorar as vendas, avaliar o desempenho e otimizar suas ofertas do marketplace.
author: mattwojo
manager: evansma
ms.author: shthota
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: e467def33eb2980e58611d50c314a1206877e3b0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619679"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Análise para o Marketplace comercial no Partner Center

Saiba como acessar os relatórios analíticos no Microsoft Partner Center para monitorar as vendas, avaliar o desempenho e otimizar suas ofertas no marketplace. Como parceiro, você pode monitorar suas listagens de oferta usando a visualização de dados e gráficos de insight com suporte pelo Centro de parceiros e encontrar maneiras de maximizar suas vendas. As ferramentas de análises aprimoradas permitem que você agir sobre os resultados de desempenho e manter relações melhor com seus clientes e revendedores. 

Para acessar as ferramentas de análise do Partner Center, abra o **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** painel em Marketplace comercial.

|**Painel**|**Conteúdo exibido**|
|:---|:---|
|[Resumo](#summary-dashboard)|Gráficos, tendências e valores de dados de agregação que resumem a atividade de suas ofertas do marketplace|
|[Pedidos](#orders-dashboard)|Informações sobre seus pedidos em um formato que pode ser baixado e gráfico|
|[Clientes](#customer-dashboard)|Informações sobre seus clientes, incluindo as tendências de crescimento, apresentadas em um formato de gráfico e disponível para download|
|[Downloads](#downloads-dashboard)|Uma lista de suas solicitações de download ao longo dos últimos 30 dias|

## <a name="summary-dashboard"></a>Painel de resumo

O **resumo** painel apresenta uma visão geral com base em cada tipo de oferta. **Insights** Mostrar informações críticas em um relance e fornecer uma visão geral da atividade venda de suas ofertas. Você pode visualizar esses relatórios usando o **resumo** painel:

- [Total de pedidos](#totals)
- [Total de clientes](#totals)
- [Localização geográfica dos seus clientes](#customers-by-geography)
- [As tendências emergentes com base em suas informações de cliente e pedido](#growth-trend-chart)
- [Placar de líderes de clientes com pedidos mais altos](#customer-leaderboard)
- [Número de pedidos, organizadas por nome da oferta](#offers-by-orders)

### <a name="date-range"></a>Intervalo de datas

Você pode encontrar uma seleção de intervalo de data no canto superior direito de cada página. A saída a **resumo** grafos de página podem ser personalizados, selecionando um intervalo de datas com base no passado 3, 6 ou 12 meses, ou selecionando um intervalo de datas personalizado com um máximo de duração de 12 meses. O intervalo de datas padrão é de seis meses.

![Painel de análise do Partner Center](./media/analyze-dashboard.png)

### <a name="totals"></a>Totais

O **totais** seção exibe uma contagem de todos os pedidos criados ou adquiridos, durante o intervalo de datas selecionado de clientes. 

- O valor da porcentagem lado **Total de pedidos** e **Total de clientes** representa a quantidade de crescimento em comparação comparada o mês anterior. 
- Um triângulo verde apontando para cima indica a uma tendência de crescimento positivo. Para baixo apontando um triângulo vermelho indica uma tendência de crescimento negativo em relação ao mês anterior. 
- Ordem e as tendências de crescimento do cliente são representadas por gráficos de barras e exibição o valor para cada mês passando o mouse sobre as colunas do gráfico.

![Totais de analisar Partner Center](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Clientes por geografia

O **os clientes por geografia** mapa de calor exibe uma contagem de clientes em um mapa mundial. 

- Você pode mover o mapa para exibir o local exato. 
- Você pode aplicar zoom em um local específico. 
- O mapa de calor tem uma grade suplementar para exibir os detalhes da contagem de clientes (ou [ordenar contagem](#orders-by-geography)) no local específico. 
- Você pode pesquisar e selecionar um país na grade para aplicar zoom para o local no mapa. Reverter para o modo de exibição original, pressionando as **Home** botão no mapa.
- Um **novo** o cliente adquiriu uma das suas ofertas pela primeira vez durante o mês dentro do intervalo de datas selecionado.

![Geografia do cliente do Partner Center analisar](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Gráfico de tendência de crescimento

Você pode exibir tendências com base no crescimento de sua **pedidos criados** ou **clientes adquiridos**, exibidos de mês a mês acordo com o intervalo de datas selecionado. Você poderá analisar melhor essas tendências, selecionando os links abaixo do gráfico, o qual navegar para os respectivos **ordem** ou **cliente** páginas.

![Tendências de crescimento do Partner Center analisar](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Placar de líderes de cliente

Os clientes de 50 principais com o maior número de pedidos são exibidos em uma *painel de líderes*, classificados pela contagem de ordem mais alta e o percentual de ordem. 

- Selecione um cliente para exibir seus detalhes do perfil, organizados por oferta de pedidos ou pedidos organizados por tipo e preços do canal de licença do Azure. 
- O **oferecidos pelo pedidos** gráfico de rosca apresenta as principais ofertas de quatro (pela contagem dos pedidos) e o restante oferece agrupados em como 'Rest a todos'.

> [!NOTE]
> Informações pessoais do cliente serão apresentadas apenas se o cliente forneceu consentimento. Você pode exibir essas informações se você efetuou logon com uma **proprietário** nível de permissões de função. Os usuários com o **Colaborador** função não será capaz de exibir essas informações. [Saiba mais sobre as funções de usuário e permissões](./manage-account.md#define-user-roles-and-permissions).

![Tendências de crescimento do Partner Center analisar](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Ofertas de pedidos

O **oferecidos pelo pedidos** gráfico organiza suas ordens ativas de acordo com o nome da oferta. 

- É possível arrastar sobre fatias do gráfico de rosca esquerdo para o gráfico de rosca à direita para exibir mais detalhes da oferta que você arrastou. Esses dois gráficos permitem que você compare uma oferta específica com o desempenho de todas as suas ofertas ('Rest tudo'). 

![Ofertas do Partner Center analisar por pedidos](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Painel de controle de pedidos

O **pedidos** painel de controle da **analisar** menu exibe os pedidos atuais para todas as suas ofertas de SaaS. Você pode exibir as representações gráficas de:

- [Total de pedidos](#order-totals)
- [Pedidos por geografia](#orders-by-geography)
- [Tendências de ordens ativas e canceladas](#trends-for-active-and-canceled-orders)
- [Pedidos organizados por tipo de licença do marketplace](#orders-by-marketplace-license-type)
- [Pedidos organizados por clientes novos e existentes](#orders-by-customer-type)
- [Tabela de detalhes do pedido](#order-details-table)

> [!NOTE]
> Há diferenças entre como análise de relatórios de exibição no Portal de parceiro nuvem (CPP) e o novo programa de Marketplace comercial no Partner Center. Uma maneira específica é que o **percepções do vendedor** CPP tem um **ordens e uso** guia, que exibe dados de ofertas baseadas em uso e não se baseiam no uso de ofertas. No Partner Center, o **pedidos** página tem uma guia separada para SaaS oferece.

### <a name="order-totals"></a>Total de pedidos

O **totais dos pedidos** seção exibe uma contagem de todos os pedidos criados, incluindo os dois **Active** e **cancelado** pedidos, durante o selecionado [intervalo de datas ](#date-range). 

- O valor da porcentagem lado **Total de pedidos** representa a quantidade de crescimento em comparação comparada o mês anterior. 
- Um triângulo verde apontando para cima indica a uma tendência de crescimento positivo. Para baixo apontando um triângulo vermelho indica uma tendência de crescimento negativo em relação ao mês anterior. 
- Tendências de crescimento são representadas por gráficos de barras e exibição o valor para cada mês passando o mouse sobre as colunas do gráfico.

### <a name="orders-by-geography"></a>Pedidos por geografia

O **pedidos por geografia** mapa de calor exibe uma contagem das suas ordens de compra em um mapa mundial e funciona da mesma maneira como o  **[clientes pelo mapa de calor de Geografia](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Tendências de ordens ativas e canceladas

O **ativo ordena por ofertas** gráfico de rosca organiza todas as suas ordens Active Directory de acordo com seus nomes de oferta.

- As principais 4 ofertas são exibidas no gráfico e o restante das ofertas são agrupados como 'Rest a todos'.
- Você pode selecionar as ofertas específicas na legenda para exibir somente as ofertas no gráfico. 
- Passar o mouse sobre uma fatia no gráfico exibirá o número de pedidos e a porcentagem dessa oferta em comparação comparada o número total de pedidos em todas as ofertas.
- **Ordena por tendência de ofertas** Exibe tendências de crescimento mês a mês. A coluna do mês representa o número de pedidos pelo nome da oferta. O gráfico de linhas exibe a tendência da porcentagem de crescimento plotada em um eixo z.
- Você pode usar o controle deslizante na parte superior do gráfico para Role para a direita e esquerda ao longo do eixo x e o foco nos pontos de dados específico.
- Você pode exibir o gráfico de tendência, selecionando um item específico na legenda.
- Você também pode optar por exibir as tendências e os dados para **ordens canceladas**. O gráfico funcionarão da mesma forma que as ordens ativas.

![Ordens ativas do Partner Center analisar](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Pedidos por tipo de licença do marketplace
<!-- Section needs review and clarification!  -->
O **pedidos por tipo de licença do marketplace** gráfico exibe uma contagem dos pedidos de mês a mês com base no tipo de licença e do método de cobrança da oferta do marketplace. Tipos de licença incluem:

- **Cobrado por meio do Azure**: A Microsoft cobra os clientes em seu nome quando você opta por [vender a sua oferta por meio do Microsoft](./create-new-saas-offer.md#sell-through-microsoft) com esse tipo de licença. Os tipos de pagamento incluem pré-pagas por meio de cartão de crédito ou de faturamento do Enterprise.
- **Traga sua própria licença**: Microsoft não cobra de clientes para o uso desse tipo de oferta do marketplace. Listado como **[obtê-lo agora (gratuito)](./create-new-saas-offer.md#get-it-now-free)** no marketplace.
- **Grátis**: Microsoft não cobra de clientes para o uso desse tipo de oferta do marketplace. Listado como **[avaliação gratuita](./create-new-saas-offer.md##free-trial-listing)** no marketplace.
- **Microsoft, como revendedor**: Representa as ofertas vendidas pelos revendedores da Microsoft como parte dos  **[programa de provedor de solução de nuvem (CSP)](./create-new-saas-offer.md#csp-program-opt-in)** .

![Pedidos do Partner Center analisar por tipo de licença](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Pedidos por tipo de cliente

O **pedidos por tipo de cliente** gráfico de barras exibe o número de pedidos dividido entre **novos clientes** e **clientes existentes**. 

- Um **novo cliente** adquiriu uma ou mais das suas ofertas pela primeira vez dentro do mesmo mês do calendário (eixo y). Uma **cliente existente** anteriormente adquiriu uma oferta de você antes do mês do calendário relatado (no eixo y). 
- Um gráfico de pizza adicional representa todos os pedidos criados pelo cliente novo ou existente para o intervalo de datas selecionado.
- Em ambos os gráficos, você pode optar por exibir apenas novos ou apenas clientes existentes, selecionando a legenda do respectiva.

![Pedidos de analisar do Partner Center por tipo de cliente](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Tabela de detalhes do pedido

O **tabela de detalhes do pedido** exibe uma lista numerada de 1000 pedidos principais classificados por data de aquisição.

- Cada coluna na grade é classificável.
- Os dados podem ser extraídos para um arquivo TSV se a contagem de registros for inferior a 1000.
- Se o número de registros acima de 1000, exportados dados serão assíncrona colocados em uma página de downloads para os próximos 30 dias.
- Filtros podem ser aplicados para o **tabela de detalhes do pedido** para exibir apenas os dados que você está interessado. Dados podem ser filtrados por país, tipo de licença do Azure, tipo de licença do Marketplace, tipo de oferta, status do pedido, trilhas gratuitas, nome de ID, Customer ID, e a empresa de assinatura do Marketplace.

![Detalhes do pedido de analisar Partner Center](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Painel do cliente

O **Customer** painel de controle da **analisar** menu exibe dados para os clientes que adquiriram suas ofertas. Você pode exibir as representações gráficas de:

- [Totais de cliente](#customer-totals)
- [Clientes por geografia](#customers-by-geography)
- [Tendências de cliente](#customer-trends)
- [Clientes por pedidos](#customers-by-orders)
- [Tabela de detalhes do cliente](#customer-details-table)

### <a name="customer-totals"></a>Totais de cliente

O **totais de cliente** seção exibe uma contagem de todos os clientes, incluindo novos, existentes e formados, durante o selecionado [intervalo de datas](#date-range).

- Porcentagem de clientes de crescimento em comparação comparada o mês anterior é indicada pelo número e um indicador para cima no indicador verde ou para baixo em vermelho.
- Tendências de crescimento são representadas por gráficos de barras e exibição o valor para cada mês passando o mouse sobre as colunas do gráfico.

#### <a name="customer-types"></a>Tipos de clientes

Há três tipos de cliente: novos, existentes e formados. 

- Um novo cliente adquiriu uma ou mais das suas ofertas pela primeira vez no mês selecionado.
- Um cliente existente tiver adquirido uma ou mais das suas ofertas antes do mês selecionado.
- Um cliente churned cancelou todas as ofertas compradas anteriormente.

### <a name="customer-trends"></a>Tendências de cliente

O **tendências de cliente** gráfico exibe uma contagem de todos os clientes, incluindo novos, existentes e formados, com uma tendência de crescimento mês a mês.

- O gráfico de linhas representa as porcentagens de crescimento geral do cliente. 
- A coluna month representa a contagem de clientes empilhadas por clientes novos, existentes e formados.
- A contagem de clientes formados é exibida na direção do eixo Y negativa.
- Você pode selecionar itens a serem exibidos mais de legenda específica exibições detalhadas. Por exemplo, selecione os novos clientes da legenda para exibir somente os novos clientes.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar à direita e esquerda no eixo x e o foco nos pontos de dados específico, para exibir mais detalhes.
- Passar o mouse sobre uma coluna do gráfico para exibir detalhes apenas nesse mês.

![Tendências de cliente do Partner Center analisar](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Clientes por pedidos

O **clientes por pedidos** gráfico exibe a **superior cliente percentil** ao longo do eixo x, conforme determinado por seus números de pedidos. O eixo y exibe a contagem dos pedidos do cliente. O eixo z (gráfico de linha) exibe o percentual cumulativo do número total de pedidos. Você pode exibir detalhes focalizando pontos ao longo do gráfico de linhas.

Por exemplo, você pode aprender a partir desses dados de pedido de cliente a 30% superior dos seus clientes estão contribuindo para 83% dos pedidos, iguais a 2,130 pedidos.

![Pedidos de clientes do Partner Center analisar](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

O **tabela de detalhes do cliente** exibe uma lista numerada dos clientes da parte superior 1000 classificados pela data em que eles adquiriram pela primeira vez que uma das suas ofertas.

- Informações pessoais do cliente só estará disponíveis se o cliente forneceu consentimento. Você só pode exibir essas informações se você efetuou logon com uma **proprietário** nível de função de permissões. [Saiba mais sobre as funções de usuário e permissões](./manage-account.md#define-user-roles-and-permissions).
- Cada coluna na grade é classificável.
- Os dados podem ser extraídos para um arquivo TSV se a contagem de registros for inferior a 1000.
- Se o número de registros acima de 1000, exportados dados serão assíncrona colocados em uma página de downloads para os próximos 30 dias.
- Filtros podem ser aplicados à tabela para exibir somente os dados que você está interessado. Os dados podem ser filtradas pelo nome da empresa, ID do cliente, ID de assinatura do Marketplace, tipo de licença do Azure, data adquirido, data perdido, cliente de Email, Zip/cidade/estado/país do cliente, idioma do cliente, etc.

![Detalhes do cliente do Partner Center analisar](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Downloads do painel

O **baixa** painel de controle da **analisar** menu exibe as solicitações de todos os downloads que contêm mais de 1.000 linhas de dados do cliente ou pedido.

Você receberá uma notificação pop-up e um email contendo um link para o **Downloads** dashboard sempre que você solicitar um download com mais de 1.000 linhas de dados. Estes downloads de dados estarão disponíveis por um período de 30 dias e, em seguida, removida.

![Downloads para analisar o Partner Center](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Dicionário de termos de dados

| Nome do atributo | Relatórios | Definição|
|---|---|---|
| Tipo de licença do Azure | Cliente, pedido | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como o canal |
| Tipo de licença do Azure: Provedor de soluções de nuvem | Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio do Provedor de Soluções de Nuvem, que atua como revendedor.|
| Tipo de licença do Azure: Enterprise | Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio de um Contrato Enterprise, assinado diretamente com a Microsoft.|
| Tipo de licença do Azure: Enterprise por meio do revendedor  | Cliente, pedido | O cliente final procures do Azure e sua oferta do Marketplace por meio de um revendedor que facilita a seu Enterprise Agreement com a Microsoft.|  |
| Tipo de licença do Azure: Pago conforme o uso| Cliente, pedido | O cliente final procures do Azure e sua oferta do Marketplace por meio de um contrato "Pré-pago", conectado diretamente com a Microsoft.||
| Nome da instância de nuvem| Classificar| A Microsoft Cloud em que ocorreu uma implantação de VM.||
| Nome da instância de nuvem: Azure Global| Classificar| A nuvem da Microsoft global pública.|| |
| Nome da instância de nuvem: Azure Government | Classificar| Nuvens da Microsoft específica do governo para um dos governos a seguir: Estados Unidos da América, Alemanha ou China.| |
| Cidade do cliente| Cliente| O nome da cidade fornecido pelo cliente. Cidade pode ser diferente da cidade na assinatura do Azure do cliente.||
| Idioma de comunicação do cliente  | Cliente| O idioma preferido pelo cliente para comunicação.||
| Nome da empresa do cliente | Cliente, pedido | O nome da empresa fornecido pelo cliente. Nome pode ser diferente da cidade na assinatura do Azure do cliente.|  |
| País/Região do cliente | Cliente, pedido | O nome de país fornecido pelo cliente. País pode ser diferente de país na assinatura do Azure do cliente.|  |
| Email do cliente| Cliente| O endereço de email fornecido pelo cliente final. Email pode ser diferente do endereço de email na assinatura do Azure do cliente.||
| Nome do cliente| Cliente| O nome fornecido pelo cliente. Nome pode ser diferente do nome fornecido na assinatura do Azure do cliente.| |
| ID do cliente | Cliente, pedido | O identificador exclusivo atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Marketplace do Azure.|  |
| Código postal do cliente  | Cliente| O CEP fornecido pelo cliente. Código poderia ser diferente do que o CEP fornecido na assinatura do Azure do cliente.| |
| Estado do cliente| Cliente| O estado (endereço) fornecido pelo cliente. Estado pode ser diferente do último nome fornecido na assinatura do Azure do cliente.| |
| Data da aquisição| Cliente| A primeira data, o cliente comprou qualquer oferta que foram publicada por você.| |
| Data da perda| Cliente| A data da última o cliente cancelou o último de todas as ofertas comprada anteriormente.||
| É o novo cliente  | Classificar| O valor identificará um novo cliente que adquirir uma ou mais das suas ofertas pela primeira vez (ou não). Valor será "Sim" se dentro do mesmo mês do calendário para "Data adquirido". Valor será "Não" se o cliente tiver comprado a qualquer uma das suas ofertas antes do mês do calendário relatados. |
| É a versão prévia SKU| Classificar| O valor informará se você ter marcado a SKU como "visualização". Valor será "Sim" se o SKU foi marcado adequadamente, e o Azure somente assinaturas autorizadas por você podem implantar e usar essa imagem. Valor será "Não" se o SKU não tiver sido identificado como "visualização".  |
| Contato promocional significa usar| Cliente| O valor informará se o cliente proativamente participar do contato promocional de Publicadores. Não estamos apresentando a opção aos clientes no momento; portanto, indicamos "Não" de forma generalizada. Após a implantação desse recurso, começaremos a atualizar de acordo.|
| Tipo de licença do Marketplace| Classificar| O método de cobrança da oferta do Marketplace.||
| Tipo de licença do Marketplace: Cobrado por meio do Azure| Classificar| A Microsoft é seu agente para esta oferta do Marketplace e cobra os clientes em seu nome. (Cartão de crédito PAYG ou fatura Enterprise)||
| Tipo de licença do Marketplace: Traga sua própria licença | Classificar| A máquina virtual requer uma chave de licença fornecida pelo cliente para implantar. Microsoft não cobra de clientes para listar suas ofertas dessa maneira por meio do marketplace.||
| Tipo de licença do Marketplace: Grátis| Classificar| A oferta está configurada para ser livres para todos os usuários. Microsoft não cobra de clientes para o uso desta oferta.||
| Tipo de licença do Marketplace: Microsoft, como revendedor  | Classificar| A Microsoft é o revendedor para esta oferta do Marketplace.|  |
| ID de assinatura do Marketplace | Cliente, pedido | O identificador exclusivo associado à assinatura do Azure do cliente usado para comprar sua oferta do Marketplace. ID anteriormente era o GUID de assinatura do Azure.||
| Nome da oferta  | Classificar| O nome da oferta do Marketplace.|| |
| Tipo de Oferta  | Classificar| O tipo de oferta do Microsoft Marketplace.|||
| Tipo de oferta: Aplicativo gerenciado  | Ordem, | Use o aplicativo do Azure: tipo de oferta de aplicativo gerenciado quando as seguintes condições forem necessárias: Você pode implantar uma solução baseada em assinatura para o cliente usando uma VM ou uma solução inteira com base em IaaS. Você ou seu cliente exigem que a solução ser gerenciado por um parceiro. |
| Tipo de oferta: Aplicativo do Azure| Ordem, | Use o tipo de oferta de modelo de solução de aplicativo do Azure quando sua solução exige a automação de implantação e configuração adicional além de uma VM simples.||
| Tipo de oferta: Serviço de consultoria| Classificar| Serviços de Consultoria no Azure Marketplace ajudam a conectar os clientes aos serviços para dar suporte a aumentar seu uso do Azure.| |
| Tipo de oferta: Contêiner | Classificar| Use o tipo de oferta de Contêiner quando a solução é uma imagem de contêiner do Docker provisionada como um serviço de contêiner do Azure baseado em Kubernetes.||
| Tipo de oferta: Dynamics 365 Business Central| Classificar| Use este tipo de oferta quando sua solução é integrada com o Dynamics 365 para Finanças e operações| |
| Tipo de oferta: Dynamics 365 for Customer Engagement | Classificar| Use esse tipo de oferta quando sua solução é integrada ao Dynamics 365 for Customer Engagement.||
| Tipo de oferta: Módulo do IoT Edge | Classificar| Módulos do IoT Edge do Azure são as unidades de computação menor gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros 3º ou seu próprio código específico da solução. |
| Tipo de oferta: Aplicativo do Power BI | Classificar| Use o tipo de oferta de aplicativo do Power BI quando você implanta um aplicativo integrado com o Power BI.|  |
| Tipo de oferta: Aplicativo de SaaS| Classificar| Use o tipo de oferta de aplicativo SaaS para permitir que o cliente compre a solução técnica baseada em SaaS como uma assinatura.||
| Tipo de oferta: Máquina Virtual | Classificar| Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente.||
| Tipo de oferta: Extensão do Visual Studio Marketplace  | Classificar| Disponível anteriormente do tipo de oferta para os desenvolvedores de extensão de DevOps do Azure. Vai avanço do Azure DevOps os desenvolvedores de extensão podem vender sua extensão diretamente aos clientes. Ofertas de extensão podem ser configuradas como paga ou incluindo uma versão de avaliação. |
| Data de cancelamento do pedido| Classificar| A data em que o pedido do Marketplace foi cancelado.||
| ID do pedido| Classificar| O identificador exclusivo do pedido de cliente para o serviço do Marketplace. Ofertas de máquina virtual baseada em uso não estão associadas um pedido.| |
| Data de compra do pedido| Classificar| A data em que o pedido do Marketplace foi criado.|||
| Status do pedido| Classificar| O status de um pedido do Marketplace no momento em que os dados foram atualizados pela última vez.|     |
| Status do pedido: Ativo  | Classificar| O cliente comprou um pedido e não tenha cancelado sua ordem.|         |
| Status do pedido: cancelada | Classificar| O cliente comprada anteriormente um pedido e subsequentemente cancelado sua ordem.||
| Provedor Email| Cliente| O endereço de email do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente é uma empresa por meio do revendedor, esse será o revendedor. Se um provedor de solução de nuvem (CSP) estiver envolvido, esse será o CSP.|
| Nome do provedor| Cliente| O nome do provedor envolvido na relação entre o cliente da Microsoft e de término. Se o cliente é uma empresa por meio do revendedor, esse será o revendedor. Se um provedor de solução de nuvem (CSP) estiver envolvido, esse será o CSP.|
| SKU| Classificar| Nome da SKU conforme definido durante a publicação. Uma oferta pode ter várias SKUs, mas uma SKU só pode ser associada com uma única oferta.||
| Data de término da avaliação gratuita| Classificar| A data em que o período de avaliação gratuita deste pedido terminará ou terminou.||
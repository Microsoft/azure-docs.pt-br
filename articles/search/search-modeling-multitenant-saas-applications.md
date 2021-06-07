---
title: Multilocação e isolamento de conteúdo
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre os padrões de design comuns para aplicativos SaaS multilocatários ao usar o Azure Pesquisa Cognitiva.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: cd21197d6d1559b681ae622b974f6eb7ba95ad3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91397361"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Padrões de design para aplicativos SaaS multilocatários e Pesquisa Cognitiva do Azure

Um aplicativo multilocatário é aquele que fornece os mesmos serviços e funcionalidades para qualquer número de locatários que não conseguem ver nem compartilhar os dados de qualquer outro locatário. Este documento discute estratégias de isolamento de locatários para aplicativos multilocatários criados com o Azure Pesquisa Cognitiva.

## <a name="azure-cognitive-search-concepts"></a>Conceitos de Pesquisa Cognitiva do Azure
Como uma solução de pesquisa como serviço, o [Azure pesquisa cognitiva](search-what-is-azure-search.md) permite aos desenvolvedores adicionar experiências de pesquisa avançada a aplicativos sem gerenciar qualquer infraestrutura ou se tornar um especialista em recuperação de informações. Os dados são carregados para o serviço e, em seguida, são armazenados na nuvem. Usando solicitações simples para a API de Pesquisa Cognitiva do Azure, os dados podem ser modificados e pesquisados. 

### <a name="search-services-indexes-fields-and-documents"></a>Serviços Search, índices, campos e documentos

Antes de discutir os padrões de design, é importante entender alguns conceitos básicos.

Ao usar o Azure Pesquisa Cognitiva, um assina um *serviço de pesquisa*. À medida que os dados são carregados no Azure Pesquisa Cognitiva, eles são armazenados em um *índice* dentro do serviço de pesquisa. Pode haver um número de índices em um único serviço. Para usar os conceitos familiares de bancos de dados, o serviço de pesquisa pode ser comparado a um banco de dados, enquanto os índices dentro de um serviço podem ser comparados a tabelas em um banco de dados.

Cada índice dentro de um serviço de pesquisa tem seu próprio esquema, que é definido por um número de *campos* personalizáveis. Os dados são adicionados a um índice de Pesquisa Cognitiva do Azure na forma de *documentos* individuais. Cada documento deve ser carregado em um índice específico e deve se ajustar o esquema do índice. Ao pesquisar dados usando o Azure Pesquisa Cognitiva, as consultas de pesquisa de texto completo são emitidas em relação a um índice específico.  Para comparar esses conceitos àqueles de um banco de dados, os campos podem ser comparados a colunas em uma tabela e os documentos podem ser comparados a linhas.

### <a name="scalability"></a>Escalabilidade
Qualquer serviço de Pesquisa Cognitiva do Azure no [tipo de preço](https://azure.microsoft.com/pricing/details/search/) Standard pode ser dimensionado em duas dimensões: armazenamento e disponibilidade.

* *Partições* podem ser adicionadas para aumentar o armazenamento de um serviço de pesquisa.
* *Réplicas* podem ser adicionados a um serviço para aumentar a taxa de solicitações que pode lidar com um serviço de pesquisa.

Adicionar e remover partições e réplicas permitirá que a capacidade do serviço de pesquisa cresça de acordo com a quantidade de dados e tráfego que o aplicativo exige. Para que um serviço de pesquisa obtenha um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de leitura, ele requer duas réplicas. Para que um serviço de pesquisa obtenha um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)de leitura/gravação, ele requer três réplicas.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Limites de serviço e índice no Azure Pesquisa Cognitiva
Há alguns [tipos de preço](https://azure.microsoft.com/pricing/details/search/) diferentes no pesquisa cognitiva do Azure, cada uma das camadas tem [limites e cotas](search-limits-quotas-capacity.md)diferentes. Alguns desses limites estão no nível de serviço, alguns estão no nível do índice e alguns estão no nível da partição.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| **Máximo de réplicas por serviço** |3 |12 |12 |12 |12 |
| **Máximo de partições por serviço** |1 |12 |12 |12 |3 |
| **Máximo de unidades de pesquisa (réplicas * partições) por serviço** |3 |36 |36 |36 |36 (máx. de 3 partições) |
| **Armazenamento máximo por serviço** |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| **Armazenamento máximo por partição** |2 GB |25 GB |100 GB |200 GB |200 GB |
| **Índices máximos por serviço** |5 |50 |200 |200 |3000 (máx. de 1000 índices/partição) |

#### <a name="s3-high-density"></a>Alta densidade S3
No tipo de preço S3 do Azure Pesquisa Cognitiva, há uma opção para o modo HD (alta densidade) projetada especificamente para cenários multilocatários. Em muitos casos, é necessário dar suporte a um grande número de locatários menores em um único serviço para obter os benefícios de simplicidade e redução de custos.

S3 HD permite que os muitos índices pequenos sejam empacotados no gerenciamento de um único serviço de pesquisa, negociando a capacidade de escalar horizontalmente índices usando partições para a capacidade de hospedar mais índices em um único serviço.

Um serviço S3 é projetado para hospedar um número fixo de índices (máximo de 200) e permitir que cada índice seja dimensionado horizontalmente conforme novas partições são adicionadas ao serviço. A adição de partições a S3 HD Services aumenta o número máximo de índices que o serviço pode hospedar. O tamanho máximo ideal para um índice S3HD individual é de cerca de 50 a 80 GB, embora não haja limite de tamanho rígido em cada índice imposto pelo sistema.

## <a name="considerations-for-multitenant-applications"></a>Considerações para aplicativos multilocatários
Aplicativos multilocatários devem distribuir efetivamente recursos entre locatários preservando algum nível de privacidade entre os vários locatários. Há algumas considerações ao criar a arquitetura para esse aplicativo:

* *Isolamento de locatários:* os desenvolvedores de aplicativos precisam tomar as medidas apropriadas para garantir que nenhum locatário tenha acesso não autorizado ou indesejado aos dados de outros locatários. Além da perspectiva de privacidade de dados, estratégias de isolamento de locatários requerem um gerenciamento eficiente de recursos compartilhados e a proteção de vizinhos com ruídos.
* *Custo de recursos de nuvem:* como com qualquer outro aplicativo, as soluções de software devem permanecer competitivas em termos de custo como um componente de um aplicativo multilocatário.
* *Facilidade de operações:* ao desenvolver uma arquitetura de multilocatários, o impacto sobre as operações e a complexidade do aplicativo é uma consideração importante. O Azure Pesquisa Cognitiva tem um [SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Superfície global:* aplicativos multilocatários talvez precisem atender efetivamente locatários distribuídos em todo o mundo.
* *Escalabilidade:* os desenvolvedores de aplicativos precisam considerar como eles reconciliam entre manter um nível suficientemente baixo de complexidade do aplicativo e criar o aplicativo para dimensionar com número de locatários e o tamanho dos dados e a carga de trabalho de locatários.

O Azure Pesquisa Cognitiva oferece alguns limites que podem ser usados para isolar dados e carga de trabalho dos locatários.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modelando multilocação com o Azure Pesquisa Cognitiva
No caso de um cenário de multilocatário, o desenvolvedor do aplicativo consome um ou mais serviços de pesquisa e divide seus locatários entre serviços, índices ou ambos. O Azure Pesquisa Cognitiva tem alguns padrões comuns ao modelar um cenário multilocatário:

1. *Índice por locatário:* cada locatário tem seu próprio índice dentro de um serviço de pesquisa que é compartilhado com outros locatários.
2. *Serviço por locatário:* Cada locatário tem seu próprio serviço de Pesquisa Cognitiva do Azure dedicado, oferecendo mais alto nível de dados e separação de carga de trabalho.
3. *Mistura de ambos:* locatários maiores e mais ativos são atribuídos a serviços dedicados enquanto locatários menores são atribuídos a índices individuais dentro de serviços compartilhados.

## <a name="1-index-per-tenant"></a>1. índice por locatário

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png" alt-text="Uma descrição do modelo de índice por locatário" border="false":::

Em um modelo de índice por locatário, vários locatários ocupam um único serviço de Pesquisa Cognitiva do Azure, em que cada locatário tem seu próprio índice.

Os locatários obtêm isolamento de dados porque todas as solicitações de pesquisa e operações de documentos são emitidas em um nível de índice no Azure Pesquisa Cognitiva. Na camada de aplicativo, há o reconhecimento da necessidade de direcionar o tráfego de vários locatários para os índices certos enquanto gerencia recursos no nível de serviço em todos os locatários.

Um atributo de chave do modelo de índice por locatário é a capacidade do desenvolvedor do aplicativo de subscrever a capacidade de um serviço de pesquisa entre locatários do aplicativo. Se os locatários têm uma distribuição desigual de carga de trabalho, a combinação ideal de locatários pode ser distribuída em índices de um serviço de pesquisa para acomodar inúmeros locatários altamente ativos e com uso intensivo de recursos, ao mesmo tempo em que atende uma cauda longa de locatários menos ativos. A desvantagem é a incapacidade do modelo de lidar com situações em que cada locatário é altamente ativo simultaneamente.

O modelo de índice por locatário fornece a base para um modelo de custo variável, em que um serviço de Pesquisa Cognitiva do Azure inteiro é comprado antecipadamente e, posteriormente, preenchido com locatários. Isso permite que a capacidade não utilizada seja designada para contas gratuitas e de avaliação.

Para aplicativos com uma superfície global, o modelo de índice por locatário pode não ser o mais eficiente. Se locatários do aplicativo são distribuídos em todo o mundo, um serviço separado pode ser necessário para cada região que pode duplicar os custos em cada um deles.

O Azure Pesquisa Cognitiva permite a escala dos índices individuais e o número total de índices a serem aumentados. Se um tipo de preço apropriado for escolhido, partições e réplicas poderão ser adicionadas ao serviço de pesquisa inteiro quando um índice individual dentro do serviço se tornar muito extenso em termos de armazenamento ou tráfego.

Se o número total de índices aumenta muito para um único serviço, outro serviço deve ser configurado para acomodar novos locatários. Se os índices tiverem que ser movidos entre os serviços de pesquisa à medida que novos serviços forem adicionados, os dados do índice precisarão ser copiados manualmente de um índice para outro, pois o Azure Pesquisa Cognitiva não permite que um índice seja movido.

## <a name="2-service-per-tenant"></a>2. serviço por locatário

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png" alt-text="Uma descrição do modelo de serviço por locatário" border="false":::

Em uma arquitetura de serviço por locatário, cada locatário tem seu próprio serviço de pesquisa.

Nesse modelo, o aplicativo atinge o nível máximo de isolamento para seus locatários. Cada serviço tem armazenamento dedicado e taxa de transferência para lidar com solicitação de pesquisa, bem como chaves de API separadas.

Para aplicativos em que cada locatário tem uma grande superfície ou a carga de trabalho tem menos variabilidade de locatário para locatário, o modelo de serviço por locatário é uma opção adequada, já que recursos não são compartilhados entre cargas de trabalho de vários locatários.

Um modelo de serviço por locatário também oferece o benefício de um modelo de custo fixo e previsível. Não há nenhum investimento antecipado em um serviço de pesquisa inteiro até que haja um locatário para preenchê-lo. No entanto, o custo por locatário é maior do que um modelo de índice por locatário.

O modelo de serviço por locatário é uma opção eficiente para aplicativos com uma superfície global. Com locatários distribuídos geograficamente, é fácil ter cada serviço do locatário na região apropriada.

Os desafios de dimensionamento desse padrão surgem quando locatários individuais excedem o serviço. No momento, o Azure Pesquisa Cognitiva não dá suporte à atualização do tipo de preço de um serviço de pesquisa, portanto, todos os dados precisariam ser copiados manualmente para um novo serviço.

## <a name="3-mixing-both-models"></a>3. misturando os dois modelos
Outro padrão para modelar a multilocação é misturar estratégias de índice por locatário e de serviço por locatário.

Combinando os dois padrões, locatários maiores do aplicativo podem ocupar serviços dedicados, enquanto a cauda longa de locatários menores, menos ativos pode ocupar índices em um serviço compartilhado. Esse modelo garante que os locatários maiores tenham consistentemente alto desempenho do serviço, ajudando a proteger os locatários menores de vizinhos com ruídos.

No entanto, implementar essa estratégia depende da antecipação para prever quais locatários exigirão um serviço dedicado em vez de um índice em um serviço compartilhado. A complexidade do aplicativo aumenta com a necessidade de gerenciar esses dois modelos multilocação.

## <a name="achieving-even-finer-granularity"></a>Como obter granularidade ainda maior
Os padrões de design acima para modelar cenários de multilocatário no Azure Pesquisa Cognitiva assumem um escopo uniforme em que cada locatário é uma instância inteira de um aplicativo. No entanto, às vezes, os aplicativos podem manipular vários escopos menores.

Se os modelos de serviço por locatário e de índice por locatário não são escopos suficientemente pequenos, é possível modelar um índice para atingir um nível ainda maior de granularidade.

Para que um único índice se comporte de modo diferente para pontos de extremidade de cliente diferentes, é possível adicionar um campo a um índice que designa um valor determinado para cada cliente possível. Cada vez que um cliente chama o Azure Pesquisa Cognitiva para consultar ou modificar um índice, o código do aplicativo cliente especifica o valor apropriado para esse campo usando a funcionalidade de [filtro](./query-odata-filter-orderby-syntax.md) do Azure pesquisa cognitiva no momento da consulta.

Esse método pode ser usado para obter uma funcionalidade de contas de usuário separadas, níveis de permissão separados e até mesmo aplicativos completamente separados.

> [!NOTE]
> Usar a abordagem descrita acima para configurar um único índice para atender a vários locatários afeta a relevância dos resultados da pesquisa. Pontuações de relevância de pesquisa são calculadas em um escopo no nível do índice, não no nível do locatário, para que dados de todos os locatários sejam incorporados às estatísticas subjacentes das pontuações de relevância, tal como frequência de termo.
> 
> 

## <a name="next-steps"></a>Próximas etapas
O Azure Pesquisa Cognitiva é uma opção atraente para muitos aplicativos. Ao avaliar os vários padrões de design para aplicativos multilocatários, considere os [vários tipos de preço](https://azure.microsoft.com/pricing/details/search/) e os respectivos [limites de serviço](search-limits-quotas-capacity.md) para melhor adaptar os pesquisa cognitiva do Azure para ajustar as cargas de trabalho e arquiteturas de todos os tamanhos do aplicativo.

Quaisquer perguntas sobre cenários de multilocatário e Pesquisa Cognitiva do Azure podem ser direcionadas para o azuresearch_contact@microsoft.com .
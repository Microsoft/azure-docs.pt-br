Você pode criar vários serviços dentro de uma assinatura, cada um provisionado em uma camada específica, limitada somente pelo número de serviços permitidos em cada camada. Por exemplo, você pode criar até 12 serviços na camada Básico e outros 12 serviços na camada S1 dentro da mesma assinatura. Para obter mais informações sobre as camadas, confira [Escolher uma camada ou SKU para o Azure Search](../articles/search/search-sku-tier.md).

Os limites de serviço máximos podem ser aumentados mediante solicitação. Se você precisar de mais serviços na mesma assinatura, entre em contato com o Suporte do Azure.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD <sup>1</sup> |
| --- | --- | --- | --- | --- | --- | --- |
| Quantidade máxima de serviços |1 |12 |12 |6 |6 |6 |
| SU de redução horizontal máxima <sup>2</sup> |N/D <sup>3</sup> |3 SU <sup>4</sup> |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> S3 HD não oferece suporte a [indexadores](../articles/search/search-indexer-overview.md) no momento. 

<sup>2</sup> As SU (unidades de pesquisa) são unidades faturáveis, alocadas como uma *réplica* ou como uma *partição*. Você precisa de ambos de recursos para as operações de armazenamento, indexação e consulta. Para saber mais sobre como as unidades de pesquisa são calculadas, além de um gráfico de combinações válidas que permanecem sob os limites máximos, confira [Dimensionar os níveis de recursos para cargas de trabalho de consulta e índice](../articles/search/search-capacity-planning.md). 

<sup>3</sup> Gratuito é baseado nos recursos compartilhados usados por vários assinantes. Neste nível, não há nenhum recurso dedicado para um assinante individual. Por esse motivo, a escala máxima é marcada como não aplicável.

<sup>4</sup> Básica tem uma partição fixa. Nesse nível, SUs adicionais são usadas para alocar mais réplicas para cargas de trabalho de consulta maior.


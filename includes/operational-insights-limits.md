
>[!NOTE]
>O Log Analytics era conhecido como Operational Insights.
>
>

Os limites a seguir se aplicam aos recursos do Log Analytics por assinatura:

| Recurso | Limite padrão | Comentários
| --- | --- | --- |
| Número de espaços de trabalho gratuitos por assinatura | 10 | Esse limite não pode ser aumentado. |
| Número de espaços de trabalho pagos por assinatura | N/D | Você está limitado pelo número de recursos dentro de um grupo de recursos e número de grupos de recursos por assinatura | 


Os limites a seguir se aplicam a cada espaço de trabalho de Log Analytics:

|  | Grátis | Standard | Premium | Autônomo | OMS |
| --- | --- | --- | --- | --- | --- |
| Volume de dados coletado por dia |500 MB<sup>1</sup> |Nenhum |Nenhum | Nenhum | Nenhum
| Período de retenção de dados |7 dias |1 mês |12 meses | 1 mês<sup>2</sup> | 1 mês <sup>2</sup>|

<sup>1</sup> Quando os clientes atingirem os seus limites diários de transferência de dados (500MB), a análise de dados será interrompida e reiniciada no início do dia seguinte. Um dia é baseado em UTC.

<sup>2</sup> O período de retenção de dados para planos de preços Autônomo e do OMS pode ser aumentado para 730 dias.

| Categoria | Limites | Comentários
| --- | --- | --- |
| API do Coletor de Dados | O tamanho máximo para uma postagem única é de 30 MB<br>O tamanho máximo para valores de campo é de 32 KB | Dividir volumes maiores em várias postagens<br>Campos com mais de 32 KB são truncados. |
| API de Pesquisa | 5000 registros retornados para dados não agregados<br>500000 registros para os dados agregados | Os dados agregados são uma pesquisa que inclui o comando `measure`
 

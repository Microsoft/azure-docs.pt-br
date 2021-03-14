---
title: Métricas personalizadas no Azure Monitor (versão prévia)
description: Saiba mais sobre as métricas personalizadas no Azure Monitor e como elas são modeladas.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: c6e946d5aedb06899a44851b79581dbc518f41b0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052306"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Métricas personalizadas no Azure Monitor (versão prévia)

Ao implantar recursos e aplicativos no Azure, é melhor começar a coletar dados telemétricos para obter percepções sobre sua integridade e desempenho. O Azure disponibiliza algumas métricas para você imediatamente. Essas métricas são chamadas [de Standard ou Platform](./metrics-supported.md). No entanto, eles são limitados por natureza. 

Você pode querer coletar alguns indicadores de desempenho personalizados ou métricas específicas de negócios para fornecer insights mais profundos. Essas **métricas personalizadas** podem ser coletadas por meio da telemetria do aplicativo, um agente que é executado nos recursos do Azure ou até mesmo um sistema de monitoramento externo e enviado diretamente ao Monitor do Azure. Depois de publicados no Azure Monitor, você pode procurar, consultar e alertar sobre métricas personalizadas para os recursos e aplicativos do Azure, lado a lado com as métricas padrão emitidas pelo Azure.

Azure Monitor métricas personalizadas são atuais em visualização pública. 

## <a name="methods-to-send-custom-metrics"></a>Métodos para enviar métricas personalizadas

Métricas personalizadas podem ser enviadas ao Monitor do Azure por vários métodos:
- Instrua seu aplicativo usando o SDK do Azure Application Insights e envie a telemetria personalizada ao Monitor do Azure. 
- Instale o agente de Azure Monitor (versão prévia) em sua [VM do Azure do Windows ou Linux](../agents/azure-monitor-agent-overview.md) e use uma [regra de coleta de dados](../agents/data-collection-rule-azure-monitor-agent.md) para enviar contadores de desempenho para Azure monitor métricas.
- Instale a extensão WAD (Windows Azure Diagnostics) em [VM](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md), [máquina virtual do Azure configurada](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), [VM clássica](../essentials/collect-custom-metrics-guestos-vm-classic.md) ou [Serviços em nuvem clássicos](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) e enviar contadores de desempenho para o Monitor do Azure. 
- Instale o [agente InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) em sua VM do Azure Linux e envie as métricas usando o plug-in de saída do Monitor do Azure.
- Envie métricas personalizadas [diretamente para a API REST do Azure monitor](./metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Modelo de preços e retenção

Verifique a [página de preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter detalhes sobre quando a cobrança será habilitada para métricas personalizadas e consultas de métricas. Detalhes específicos de preços de todas as métricas, incluindo métricas personalizadas e consultas de métricas, estão disponíveis nesta página. Em resumo, não há nenhum custo para ingerir métricas padrão (métricas de plataforma) em Azure Monitor repositório de métricas, mas as métricas personalizadas incorrerão em custos quando eles entrarem em disponibilidade geral. As consultas de API de métricas incorrem em custos.

As métricas personalizadas são mantidas durante o [mesmo período de tempo que as métricas de plataforma](../essentials/data-platform-metrics.md#retention-of-metrics). 

> [!NOTE]  
> As métricas enviadas para Azure Monitor por meio do SDK do Application Insights são cobradas como dados de log ingeridos. Eles só incorrerão em cobranças de métricas adicionais se o recurso de Application Insights [habilitar alertas em dimensões de métricas personalizadas](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) tiver sido selecionado. Essa caixa de seleção envia dados para o banco de dado Azure Monitor métricas usando a API de métricas personalizadas para permitir o alerta mais complexo.  Saiba mais sobre os preços e o [modelo de preços de Application insights](../app/pricing.md#pricing-model) [em sua região](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="how-to-send-custom-metrics"></a>Como enviar métricas personalizadas

Quando você envia as métricas personalizadas para o Azure Monitor, cada ponto de dados ou valor, relatado deve incluir as informações a seguir.

### <a name="authentication"></a>Autenticação
Para enviar métricas personalizadas para o Monitor do Azure, a entidade que envia a métrica precisa de um token válido do Azure AD (Azure Active Directory) no cabeçalho **Portador** da solicitação. Há algumas maneiras para adquirir um token de portador válido:
1. [Identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Fornece uma identidade a um recurso do Azure, como uma VM. A MSI (Managed Service Identity, identidade de serviço gerenciado) foi projetada para fornecer permissões de recursos para executar determinadas operações. Um exemplo é permitir que um recurso emita métricas sobre si mesmo. Um recurso, ou seu MSI, pode receber permissões de **Monitoring Metrics Publisher** em outro recurso. Com essa permissão, o MSI também pode emitir métricas para outros recursos.
2. [Entidade de serviço do Azure ad](../../active-directory/develop/app-objects-and-service-principals.md). Nesse cenário, um aplicativo ou serviço do Azure AD pode receber permissões para emitir métricas sobre um recurso do Azure.
Para autenticar a solicitação, o Monitor do Azure valida o token do aplicativo usando as chaves públicas do Azure AD. A função **existente do Monitoring Metrics Publisher** já tem essa permissão. Ele está disponível no portal do Azure. A entidade de serviço, dependendo dos recursos para os quais ela emite métricas personalizadas, pode receber a função **Monitoring Metrics Publisher** no escopo necessário. Exemplos são uma assinatura, grupo de recursos ou recurso específico.

> [!TIP]  
> Quando você solicita que um token do Azure AD emita métricas personalizadas, verifique se o público ou recurso para o qual o token é solicitado é `https://monitoring.azure.com/`. Certifique-se de incluir à direita '/'.

### <a name="subject"></a>Assunto
Essa propriedade indica a ID de recurso do Azure para a qual a métrica personalizada é relatada. Essas informações serão codificadas na URL da chamada à API que está sendo feita. Cada API só pode enviar valores de métrica para um único recurso do Azure.

> [!NOTE]  
> Você não pode emitir métricas personalizadas contra o ID do recurso de um grupo de recursos ou assinatura.


### <a name="region"></a>Região
Essa propriedade captura em que região do Azure o recurso para o qual você está emitindo métricas está implantado. As métricas devem ser emitidas para o ponto de extremidade regional do Azure Monitor que está na mesma região em que o recurso está implantado. Por exemplo, as métricas personalizadas de uma VM implantada no oeste dos EUA devem ser enviadas ao ponto de extremidade regional do Azure Monitor WestUS. As informações de região também estão codificadas na URL da chamada à API.

> [!NOTE]  
> Durante a pré-visualização pública, as métricas personalizadas estão disponíveis apenas em um subconjunto de regiões do Azure. Uma lista de regiões com suporte é fornecida em uma seção posterior deste artigo.
>
>

### <a name="timestamp"></a>Timestamp
Cada ponto de dados enviado ao Azure Monitor deve estar marcado com um carimbo de data/hora. Esse registro de data e hora captura o DateTime no qual o valor da métrica é medido ou coletado. O Monitor do Azure aceita dados de métricas com registros de data e hora em até 20 minutos no passado e 5 minutos no futuro. O carimbo de data/hora deve estar no formato ISO 8601.

### <a name="namespace"></a>Namespace
Namespaces são uma maneira de categorizar ou agrupar métricas semelhantes. Ao usar namespaces, você pode obter isolamento entre grupos de métricas que podem coletar diferentes insights ou indicadores de desempenho. Por exemplo, você pode ter um namespace chamado **contosomemorymetrics** que controla as métricas de uso de memória cujo perfil é seu aplicativo. Outro namespace chamado **contosoapptransaction** pode rastrear todas as métricas sobre transações de usuário em seu aplicativo.

### <a name="name"></a>Name
**Nome** é o nome da métrica que está sendo relatada. Normalmente, o nome é descritivo para ajudar a identificar o que está sendo medido. Um exemplo é uma métrica que mede o número de bytes de memória usados em uma determinada VM. Pode ter um nome de métrica como **Memory Bytes In Use**.

### <a name="dimension-keys"></a>Chaves de dimensão
Uma dimensão é um par de chave ou valor que ajuda a descrever características adicionais sobre a métrica que está sendo coletada. Usando as características adicionais, você pode coletar mais informações sobre a métrica, o que permite insights mais profundos. Por exemplo, a métrica **Memory Bytes In Use** pode ter uma chave de dimensão chamada **Process** que captura quantos bytes de memória cada processo em uma VM consome. Usando essa chave, você pode filtrar a métrica para ver quantos processos específicos de memória usam ou para identificar os cinco principais processos por uso de memória.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Uma métrica personalizada pode ter até 10 dimensões.

### <a name="dimension-values"></a>Valores de dimensão
Ao relatar um ponto de dados de métrica, para cada chave de dimensão na métrica que está sendo relatada, há um valor de dimensão correspondente. Por exemplo, você pode querer relatar a memória usada pelo ContosoApp em sua VM:

* O nome da métrica seria **Bytes de Memória em Uso**.
* A chave de dimensão seria **processo**.
* O valor da dimensão seria **ContosoApp.exe**.

Ao publicar um valor de métrica, você pode especificar apenas um valor de dimensão por chave de dimensão. Se você coletar a mesma utilização de memória para vários processos na VM, poderá relatar vários valores de métrica para esse registro de data e hora. Cada valor de métrica especificaria um valor de dimensão diferente para a chave de dimensão **Process**.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Se uma postagem de métrica definir chaves de dimensão, os valores de dimensão correspondentes serão obrigatórios.

### <a name="metric-values"></a>Valores métricos
O Azure Monitor armazena todas as métricas em intervalos com granularidade de um minuto. Entendemos que, durante um determinado minuto, uma métrica pode precisar ser amostrada várias vezes. Um exemplo é a utilização da CPU. Ou talvez precise ser medido para muitos eventos distintos. Um exemplo é latências de transações de entrada. Para limitar o número de valores brutos que você precisa emitir e pagar no Azure Monitor, é possível pré-agregar os valores localmente e emiti-los:

* **Mín.**: O valor mínimo observado de todas as amostras e medições durante o minuto.
* **Máx**: O valor máximo observado de todas as amostras e medições durante o minuto.
* **Sum**: A soma de todos os valores observados de todas as amostras e medições durante o minuto.
* **Contar**: o número de amostras e medições feitas durante o minuto.

Por exemplo, se houvesse quatro transações de entrada para seu aplicativo durante um determinado minuto, as latências medidas resultantes de cada uma delas podem ser as seguintes:

|Transação 1|Transação 2|Transação 3|Transação 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

Em seguida, a publicação de métrica resultante para o Azure Monitor seria a seguinte:
* Mín.: 4
* Máx.: 16
* Soma: 40
* Contagem: 4

Se seu aplicativo não é capaz de pré-agregar localmente e precisa emitir cada exemplo ou evento separado imediatamente após a coleta, você pode emitir os valores de medida brutos. Por exemplo, toda vez que uma transação de login ocorre no seu aplicativo, você publica uma métrica no Monitor do Azure com apenas uma única medida. Portanto, para uma transação de login que levou 12 ms, a publicação de métrica seria a seguinte:
* Mín.: 12
* Máx.: 12
* Soma: 12
* Contagem: 1

Com esse processo, você pode emitir vários valores para a mesma combinação de métrica e dimensão durante um determinado minuto. O Monitor do Azure, em seguida, pega todos os valores brutos emitidos por um determinado minuto e os agrega juntos.

### <a name="sample-custom-metric-publication"></a>Publicação de métrica personalizada de exemplo
No exemplo a seguir, você cria uma métrica personalizada chamada **Bytes de Memória em Uso** no namespace **Perfil de Memória** da métrica para uma máquina virtual. A métrica tem uma única dimensão chamada **Process**. Para que o carimbo de hora determinado, podemos emitir valores de métrica para dois processos diferentes:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> O Application Insights, a extensão de diagnósticos e o agente InfluxData Telegraf já estão configurados para emitir valores métricos em relação ao ponto final regional correto e transportar todas as propriedades precedentes em cada emissão.
>
>

## <a name="custom-metric-definitions"></a>Definições de métricas personalizadas
Não há necessidade de predefinir uma métrica personalizada no Monitor do Azure antes de ser emitida. Cada ponto de dados de métrica publicado contém informações de espaço de nomes, nome e dimensão. Portanto, na primeira vez em que uma métrica personalizada é emitida para o Azure Monitor, uma definição de métrica é criada automaticamente. Essa definição de métrica é, então, detectável em qualquer recurso em que a métrica é emitida por meio das definições de métrica.

> [!NOTE]  
> Azure Monitor ainda não dá suporte à definição de **unidades** para uma métrica personalizada.

## <a name="using-custom-metrics"></a>Usando métricas personalizadas
Depois que as métricas personalizadas são enviadas ao Monitor do Azure, você pode pesquisá-las por meio do portal do Azure e consultá-las por meio das APIs REST do Monitor do Azure. Você também pode criar alertas para notificá-lo quando certas condições forem atendidas.

> [!NOTE]
> Você precisa ser uma função de leitor ou colaborador para exibir métricas personalizadas. Consulte [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procurar suas métricas personalizadas no portal do Azure
1.    Acesse o [portal do Azure](https://portal.azure.com).
2.    Selecione o painel **Monitor**.
3.    Selecione **Métricas**.
4.    Selecione um recurso em que você emitiu métricas personalizadas.
5.    Selecione o namespace de métricas para sua métrica personalizada.
6.    Selecione a métrica personalizada.

> [!NOTE]
> Consulte [introdução ao Azure Metrics Explorer](./metrics-getting-started.md) para obter mais informações sobre como exibir métricas no portal do Azure.

## <a name="supported-regions"></a>Regiões com suporte
Durante a pré-visualização pública, a capacidade de publicar métricas personalizadas está disponível apenas em um subconjunto de regiões do Azure. Essa restrição significa que as métricas podem ser publicadas apenas para recursos em uma das regiões suportadas. Confira [geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para obter mais informações sobre regiões do Azure. O código de região do Azure usado nos pontos de extremidade abaixo é apenas o nome da região com espaço em branco removido a tabela a seguir lista o conjunto de regiões do Azure com suporte para métricas personalizadas. Ele também lista os terminais correspondentes que as métricas de recursos nessas regiões devem ser publicadas para:

|Região do Azure |Prefixo de ponto de extremidade regional|
|---|---|
| Todas as regiões de nuvem pública | https://<azure_region_code>. monitoring.azure.com |
| **Azure Governamental** | |
| Governo dos EUA do Arizona | https: \/ /usgovarizona.Monitoring.Azure.us |
| **China** | |
| Leste da China 2 | https: \/ /chinaeast2.Monitoring.Azure.cn |

## <a name="latency-and-storage-retention"></a>Latência e retenção de armazenamento

Adicionar uma nova métrica ou uma nova dimensão que está sendo adicionada a uma métrica pode levar até 2 a 3 minutos para aparecer. Uma vez no sistema, os dados devem aparecer em menos de 30 segundos 99% do tempo. 

Se você excluir uma métrica ou remover uma dimensão, a alteração poderá levar uma semana a um mês para ser excluída do sistema.

## <a name="quotas-and-limits"></a>Cotas e limites
O Monitor do Azure impõe os seguintes limites de uso em métricas personalizadas:

|Categoria|Limite|
|---|---|
|Série de tempo de atividade/assinaturas/região|50.000|
|Chaves de dimensão por métrica|10|
|Tamanho da cadeia de caracteres para namespaces de métrica, nomes de métrica, chaves de dimensão e valores de dimensão|256 caracteres|

Uma série temporal ativa é definida como qualquer combinação exclusiva de métrica, chave de dimensão ou valor de dimensão que teve valores de métrica publicados nas últimas 12 horas.

## <a name="next-steps"></a>Próximas etapas
Use métricas personalizadas de diferentes serviços: 
 - [Máquinas virtuais](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Conjunto de escala de máquina virtual](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Máquinas virtuais do Azure (clássico)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Linux Virtual Machine usando o agente Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Serviços de nuvem clássico](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)

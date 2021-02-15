---
title: Noções básicas sobre preços Aplicativo Azure gateway
description: Este artigo descreve o processo de cobrança para o gateway de Aplicativo Azure e o Firewall do aplicativo Web para SKUs V1 a v2
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601604"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Noções básicas sobre preços para o gateway de Aplicativo Azure e o Firewall do aplicativo Web

>[!NOTE]
>Os preços mostrados neste artigo são exemplos e são apenas para fins ilustrativos. Para obter informações sobre preços de acordo com sua região, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/).

Aplicativo Azure gateway é uma solução de balanceamento de carga de camada 7, que permite a entrega de aplicativos Web escalonável, altamente disponível e segura no Azure.

Não há custos antecipados ou custos de encerramento associados ao gateway de aplicativo.
Você será cobrado apenas pelos recursos previamente provisionados e utilizados com base no consumo por hora real. Os custos associados ao gateway de aplicativo são classificados em dois componentes: custos fixos e custos variáveis. Os custos reais dentro de cada componente variam de acordo com o SKU que está sendo utilizado.

Este artigo descreve os custos associados a cada SKU e é recomendável que os usuários utilizem este documento para planejar e gerenciar os custos associados ao gateway de Aplicativo Azure.

## <a name="v1-skus"></a>SKUs v1

Os SKUs do gateway de aplicativo Standard e do WAF v1 são cobrados como uma combinação de:

* Custo Fixo

    Custo com base na hora em que um determinado tipo de gateway de aplicativo/WAF é provisionado e está em execução para solicitações de processamento.
    O componente de custo fixo leva em consideração os seguintes fatores:
    * Camada-gateway de aplicativo padrão ou WAF
    * Tamanho-pequeno, médio & grande
    * Contagem de instância-número de instâncias a serem implantadas

    Para N instâncias, os custos associados serão N * custo de uma instância de uma camada específica (Standard & WAF) & tamanho (pequena, média & grande).

* Custo variável

    Custo com base na quantidade de dados processados pelo gateway de aplicativo/WAF. Os bytes de solicitação e resposta processados pelo gateway de aplicativo seriam considerados para cobrança.

Custo total = custo fixo + custo variável

### <a name="standard-application-gateway"></a>Gateway de aplicativo padrão

Custo fixo & custo variável será calculado de acordo com o tipo de gateway de aplicativo.
A tabela a seguir mostra os preços de exemplo com base em um instantâneo do preço do leste dos EUA e são destinadas apenas para fins ilustrativos.

#### <a name="fixed-cost-east-us-region-pricing"></a>Custo fixo (preço da região leste dos EUA)

|              Tipo de gateway de aplicativo             |  Custos ($/HR)  |
| ------------------------------------------------- | ---------------|
|                     Pequeno                         |    $0.25      |
|                     Médio                        |    $0.07       |
|                     Grande                         |    $0.32       |

As estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

#### <a name="variable-cost-east-us-region-pricing"></a>Custo variável (preço da região leste dos EUA)

|              Dados processados             |  Pequeno ($/GB)  |  Médio ($/GB) |  Grande ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Primeiros 10 TB/mês                       |     US$ 0,008     |      Gratuita      |     Gratuita      |
| Próximos 30 TB (10 a 40 TB)/mês             |     US$ 0,008     |     $0.07     |     Gratuita      |
| Mais de 40 TB/mês                        |     US$ 0,008     |     $0.07     |     $0.35   |

Para obter mais informações sobre preços de acordo com sua região, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

Custo fixo & custos variáveis serão calculados de acordo com o tipo de gateway de aplicativo provisionado.

A tabela a seguir mostra os preços de exemplo com base em um instantâneo do preço do leste dos EUA e são apenas para fins ilustrativos.

#### <a name="fixed-cost-east-us-region-pricing"></a>Custo fixo (preço da região leste dos EUA)

|              Tipo de gateway de aplicativo             |  Custos ($/HR)  |
| ------------------------------------------------- | ---------------|
|                     Pequeno                         |       NA       |
|                     Médio                        |     $0.126     |
|                     Grande                         |     $0.448     |

As estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

#### <a name="variable-cost-east-us-region-pricing"></a>Custo variável (preço da região leste dos EUA)

|              Dados processados             |  Pequeno ($/GB)  |  Médio ($/GB) |  Grande ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Primeiros 10 TB/mês                       |     US$ 0,008     |      Gratuita      |     Gratuita      |
| Próximos 30 TB (10 a 40 TB)/mês             |     US$ 0,008     |     $0.07     |     Gratuita      |
| Mais de 40 TB/mês                        |     US$ 0,008     |     $0.07     |     $0.35   |

Para obter mais informações sobre preços de acordo com sua região, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Transferências de dados de saída-os dados que saem dos data centers do Azure dos gateways de aplicativo serão cobrados com as [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/bandwidth/)padrão.

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Exemplo 1 (a) – gateway de aplicativo padrão com contagem de 1 instância

Vamos supor que você provisionou um gateway de aplicativo padrão de tipo médio com 1 instância e processa 500 GB em um mês. Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

Preço fixo = $0.07 * 730 (horas) = $51.01 as estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

Custos variáveis = Free (a camada média não tem custos para os primeiros 10 TB processados por mês) custos totais = $51.01 + 0 = $51.01 

> [!NOTE]
> Para dar suporte a cenários de alta disponibilidade, é necessário configurar um mínimo de 2 instâncias para SKUs v1. Consulte [SLA para o gateway de aplicativo](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Exemplo 1 (b) – gateway de aplicativo padrão com contagem de instâncias de > 1

Vamos supor que você provisionou um gateway de aplicativo padrão de tipo médio com cinco instâncias e processa 500 GB em um mês. Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

Preço fixo = 5 (contagem de instâncias) * $0.07 * 730 (horas) = $255.05 as estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

Custos variáveis = Free (a camada média não tem custos para os primeiros 10 TB processados por mês) custos totais = $255.05 + 0 = $255.05 

### <a name="example-2--waf-application-gateway"></a>Exemplo 2 – WAF Application Gateway

Vamos supor que você provisionou um gateway de aplicativo padrão de tipo pequeno e um gateway de aplicativo WAF de tipo grande nos primeiros 15 dias do mês. O gateway de aplicativo pequeno processa 15 TB na duração em que está ativo e o gateway de aplicativo grande WAF processa 100 TB na duração em que está ativo. Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira: 

###### <a name="small-instance-standard-application-gateway"></a>Gateway de aplicativo Standard de instância pequena

24 horas * 15 dias = 360 horas

Preço fixo = $0.25 * 360 (horas) = $9

Custos variáveis = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Custos totais = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>Gateway de aplicativo WAF de instância grande
24 horas * 15 dias = 360 horas

Preço fixo = $0.448 * 360 (horas) = $161.28

Custos variáveis = 60 * 1000 * $0.0035/GB = $210 (camada grande não tem custos para os primeiros 40 TB processados por mês)

Custos totais = $161.28 + $210 = $371.28

## <a name="v2-skus"></a>SKUs v2  

Os SKUs do gateway de aplicativo V2 e WAF v2 dão suporte ao dimensionamento automático e garantem alta disponibilidade por padrão.

### <a name="key-terms"></a>Principais termos

##### <a name="capacity-unit"></a>Unidade de capacidade 
Unidade de capacidade é a medida de utilização da capacidade para um gateway de aplicativo em vários parâmetros.

Uma unidade de capacidade única consiste nos seguintes parâmetros:
* 2500 conexões persistentes
* taxa de transferência de 2,22 Mbps
* 1 unidade de computação

Se qualquer um desses parâmetros for excedido, outras n unidades de capacidade serão necessárias, mesmo que os outros dois parâmetros não excedam os limites da unidade de capacidade única.
O parâmetro com a maior utilização entre os três acima será usado internamente para calcular unidades de capacidade, que, por sua vez, é cobrado.

##### <a name="compute-unit"></a>Unidade de computação
Unidade de computação é a medida de capacidade de computação consumida. Fatores que afetam o consumo de unidade de computação são conexões TLS/s, cálculos de regravação de URL e processamento de regra WAF. O número de solicitações que uma unidade de computação pode manipular depende de vários critérios, como o tamanho da chave do certificado TLS, o algoritmo de troca de chaves, as regravações de cabeçalho e, no caso de WAF, o tamanho da solicitação de entrada.

Diretrizes da unidade de computação:
* Standard_v2 - Cada unidade de computação tem capacidade para aproximadamente 50 conexões por segundo com o certificado TLS da chave RSA de 2048 bits.

* WAF_v2 - Cada unidade de computação pode aceitar aproximadamente 10 solicitações simultâneas por segundo para uma combinação de tráfego de 70 a 30%, com 70% de solicitações menores que 2 KB GET/POST e permanecendo mais altas. O desempenho do WAF não é afetado pelo tamanho da resposta no momento.

##### <a name="instance-count"></a>Contagem de Instâncias 
O pré-provisionamento de recursos para SKUs do gateway de aplicativo V2 é definido em termos de contagem de instâncias. Cada instância garante um mínimo de 10 unidades de capacidade em termos de capacidade de processamento. A mesma instância pode dar suporte a mais de 10 unidades de capacidade para diferentes padrões de tráfego, dependendo dos parâmetros de unidade de capacidade.

A escala definida manualmente e os limites definidos para dimensionamento automático (mínimo ou máximo) são definidos em termos de contagem de instâncias. A escala definida manualmente para contagem de instâncias e a contagem mínima de instâncias na configuração de dimensionamento automático reservará 10 unidades/instâncias de capacidade. Esses CUs reservados serão cobrados desde que o gateway de aplicativo esteja ativo, independentemente do consumo de recursos real. Se o consumo real cruzar o limite de 10 unidades de capacidade/instância, unidades de capacidade adicionais serão cobradas sob o componente variável.

Os SKUs v2 são cobrados com base no consumo e constituem duas partes:

* Custos fixos

    Custo com base na hora em que o gateway de aplicativo v2/WAF V2 é provisionado e está disponível para solicitações de processamento. Isso garante alta disponibilidade – mesmo se 0 instâncias forem reservadas especificando 0 na contagem de instâncias mínima como parte do dimensionamento automático. 
    
    O custo fixo também inclui o custo associado ao IP público anexado ao gateway de aplicativo.

    O número de instâncias em execução a qualquer momento não é considerado um fator para os custos fixos para SKUs v2. Os custos fixos de execução de um Standard_V2 (ou WAF_V2) seriam iguais por hora, independentemente do número de instâncias em execução na mesma região do Azure.

* Custos de unidade de capacidade 

    Custo com base no número de unidades de capacidade reservadas ou utilizadas Adicionalmente, conforme necessário para processar as solicitações de entrada.  Os custos baseados em consumo são computados por hora.

Custos totais = custos fixos + custos de unidade de capacidade

> [!NOTE]
> Uma hora parcial é cobrada como uma hora completa.

A tabela a seguir mostra os preços de exemplo com base em um instantâneo do preço do leste dos EUA e são apenas para fins ilustrativos.

#### <a name="fixed-costs-east-us-region-pricing"></a>Custos fixos (preço da região leste dos EUA)

|              SKU V2             |  Custos ($/HR)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0.246     |
|              WAF_V2             |     $0.443     |

As estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

#### <a name="variable-costs-east-us-region-pricing"></a>Custos variáveis (preço da região leste dos EUA)

|        Unidade de capacidade         |  Standard_V2 ($/HR)  |  WAF_V2 ($/HR) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        US$ 0,008        |     $0.144    |

Para obter mais informações sobre preços de acordo com sua região, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Transferências de dados de saída-os dados que saem dos data centers do Azure dos gateways de aplicativo serão cobrados com as [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/bandwidth/)padrão.

### <a name="example-1-a--manual-scaling"></a>Exemplo 1 (a) – dimensionamento manual 
Vamos supor que você provisionou um gateway de aplicativo Standard_V2 com dimensionamento manual definido como 8 instâncias para o mês inteiro. Durante esse tempo, ele recebe uma média de transferência de dados de 88,8 Mbps.

Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

1 CU pode lidar com a taxa de transferência de 2,22 Mbps.

CUs necessário para lidar com 88,8 Mbps = 88,8/2,22 = 40 CUs 

Com pré-provisionado CUs = 8 (contagem de instâncias) * 10 = 80 

Desde 80 (capacidade reservada) > 40 (capacidade necessária), nenhum CUs adicional é necessário. 

Preço fixo = $0.246 * 730 (horas) = $179.58

Custos variáveis = $0.08 * 8 (unidades de instância) * 10 (unidades de capacidade) * 730 (horas) = $467.02

Custos totais = $179.58 + $467.02 = $646.78

![Diagrama de escala manual 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Exemplo 1 (b) – dimensionamento manual com tráfego indo além da capacidade provisionada

Vamos supor que você provisionou um gateway de aplicativo Standard_V2 com dimensionamento manual definido como 3 instâncias para o mês inteiro. Durante esse tempo, ele recebe uma média de transferência de dados de 88,8 Mbps.

Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

1 CU pode lidar com a taxa de transferência de 2,22 Mbps.

CUs necessário para lidar com 88,8 Mbps = 88,8/2,22 = 40 

Com pré-provisionado CUs = 3 (contagem de instâncias) * 10 = 30 

Desde 40 (capacidade necessária) > 30 (capacidade reservada), são necessários outros CUs.
O número de CUs adicionais utilizado dependerá da capacidade livre disponível em cada instância.

Se a capacidade de processamento equivalente a 10 CUs adicional estava disponível para uso nas 3 instâncias reservadas.

Preço fixo = $0.246 * 730 (horas) = $179.58

Custos variáveis = $0.08 * (3 (unidades de instância) * 10 (unidades de capacidade) + 10 (unidades de capacidade adicionais)) * 730 (horas) = $233.06

Custos totais = $179.58 + $233.06 = $413.18

No entanto, se a capacidade de processamento equivalente a apenas 8 CUs adicionais estivesse disponível para uso nas 3 instâncias reservadas.
Nesse cenário, o recurso de gateway de aplicativo está sob escala e pode potencialmente levar a aumento na latência ou solicitações sendo ignoradas.

Preço fixo = $0.246 * 730 (horas) = $179.58

Custos variáveis = $0.08 * (3 (unidades de instância) * 10 (unidades de capacidade) + 7 (unidades de capacidade adicionais)) * 730 (horas) = $216.08

Custos totais = $179.58 + $216.08 = $395.66


![Diagrama de escala Manual 2.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> No caso do dimensionamento manual, todas as solicitações adicionais que excederem a capacidade máxima de processamento das instâncias reservadas poderão causar impacto na disponibilidade do aplicativo. Em situações de alta carga, as instâncias reservadas podem ser capazes de fornecer mais de 10 unidades de capacidade de capacidade de processamento, dependendo da configuração e do tipo de solicitações de entrada. Mas é recomendável provisionar o número de instâncias de acordo com seus requisitos de tráfego.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Exemplo 2 – instância de WAF_V2 com dimensionamento automático

Vamos supor que você provisionou um WAF_V2 com o dimensionamento automático habilitado e defina a contagem mínima de instâncias como 6 para o mês inteiro. A carga da solicitação fez com que a instância WAF fosse expandida e utilize 65 unidades de capacidade (expansão de 5 unidades de capacidade, enquanto as 60 unidades eram reservadas) para o mês inteiro.
Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

As estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

Preço fixo = $0.443 * 730 (horas) = $323.39

Custos variáveis = $0.144 * 65 (unidades de capacidade) * 730 (horas) = $683.28

Custos totais = $323.39 + $683.28 = $1006.67

![Diagrama de dimensionamento automático 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> O tráfego real observado para o seu gateway de aplicativo provavelmente terá um padrão constante de tráfego, e a carga observada em seu gateway de aplicativo flutuaria de acordo com o uso real.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Exemplo 3 (a) – WAF_V2 instância com dimensionamento automático e configuração de escala de 0 min

Vamos supor que você provisionou um WAF_V2 com o dimensionamento automático habilitado e defina a contagem de instâncias mínima como 0 para o mês inteiro. A carga de solicitação no WAF é mínima, mas consistentemente presente por hora para o mês inteiro. A carga está abaixo da capacidade de uma unidade de capacidade única.
Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

As estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

Preço fixo = $0.443 * 730 (horas) = $323.39

Custos variáveis = $0.144 * 1 (unidades de capacidade) * 730 (horas) = $10.512

Custos totais = $323.39 + $10.512 = $333.902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Exemplo 3 (b) – WAF_V2 instância com dimensionamento automático com contagem de instâncias de 0 min

Vamos supor que você provisionou um WAF_V2 com o dimensionamento automático habilitado e defina a contagem mínima de instâncias como 0 para o mês inteiro. No entanto, há 0 tráfego direcionado para a instância de WAF do mês inteiro.
Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

Preço fixo = $0.443 * 730 (horas) = $323.39

Custos variáveis = $0.144 * 0 (unidades de capacidade) * 730 (horas) = $0

Custos totais = $323.39 + $0 = $323.39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Exemplo 3 (C) – WAF_V2 instância com dimensionamento manual definido como 1 instância

Vamos supor que você provisionou um WAF_V2 e o defina para o dimensionamento manual com o valor mínimo aceitável de 1 instância para o mês inteiro. No entanto, há 0 tráfego direcionado para o WAF do mês inteiro.
Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

As estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

Preço fixo = $0.443 * 730 (horas) = $323.39

Custos variáveis = $0.144 * 1 (contagem de instâncias) * 10 (unidades de capacidade) * 730 (horas) = $105.12

Custos totais = $323.39 + $105.12 = $428.51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Exemplo 4 – WAF_V2 com dimensionamento automático, cálculos de unidade de capacidade

Vamos supor que você provisionou um WAF_V2 com o dimensionamento automático habilitado e defina a contagem mínima de instâncias como 0 para o mês inteiro. Durante esse tempo, ele recebe 25 novas conexões de TLS/s com uma média de transferência de dados de 8,88 Mbps.
Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

As estimativas de preço mensal baseiam-se em 730 horas de uso por mês.

Preço fixo = $0.443 * 730 (horas) = $323.39

Custos variáveis = $0.144 * 730 (horas) * {Max (25/50, 8.88/2.22)} = $23.36 (4 unidades de capacidade necessárias para lidar com 8,88 Mbps)

Custos totais = $323.39 + $23.36 = $346.75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Exemplo 5 (a) – Standard_V2 com dimensionamento automático, cálculos baseados em tempo

Vamos supor que você provisionou um standard_V2 com o dimensionamento automático habilitado e defina a contagem mínima de instâncias como 0 e esse gateway de aplicativo está ativo por 2 horas.
Durante a primeira hora, ele recebe o tráfego que pode ser manipulado por 10 unidades de capacidade e, durante a segunda hora, recebe o tráfego que exigia 20 unidades de capacidade para lidar com a carga.
Os custos do gateway de aplicativo usando o preço mencionado acima seriam calculados da seguinte maneira:

Preço fixo = $0.246 * 2 (horas) = $0.492

Custos variáveis = $0.08 * 10 (unidades de capacidade) * 1 (horas) + $0.08 * 20 (unidades de capacidade) * 1 (horas) = $0.24

Custos totais = $0.492 + $0.24 = $0.732


## <a name="monitoring-billed-usage"></a>Monitoramento de uso cobrado

Você pode exibir a quantidade de consumo para parâmetros diferentes (unidade de computação, taxa de transferência & conexões persistentes), bem como as unidades de capacidade que estão sendo utilizadas como parte das métricas do gateway de aplicativo na seção **monitoramento** .

![Diagrama da seção de métricas.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Métricas úteis para estimativa de custo

* Unidades de capacidade atuais

    Contagem de unidades de capacidade consumidas para balancear a carga do tráfego entre os três parâmetros – conexões atuais, taxa de transferência e unidade de computação

* Unidades de capacidade cobráveis fixas

    O número mínimo de unidades de capacidade mantidas provisionadas de acordo com a configuração de contagem mínima de instâncias (uma instância é convertida em 10 unidades de capacidade) na configuração do gateway de aplicativo.

* Estimativa de unidades de capacidade cobradas

    A estimativa de unidades de capacidade cobradas indica o número de unidades de capacidade por meio do qual a cobrança é estimada. Isso é calculado como o maior valor entre as unidades de capacidade atuais (unidades de capacidade necessárias para balancear a carga do tráfego) e as unidades de capacidade cobráveis fixas (unidades de capacidade mínimas mantidas provisionadas).

Mais métricas como taxa de transferência, conexões atuais e unidades de computação também estão disponíveis para entender afunilamentos e estimar o número de unidades de capacidade necessárias. Informações detalhadas estão disponíveis em [métricas do gateway de aplicativo](application-gateway-metrics.md)

#### <a name="example---estimating-capacity-units-being-utilized"></a>Exemplo – Estimando as unidades de capacidade que estão sendo utilizadas

**Métricas observadas:**

* Unidades de computação = 17,38
* Taxa de transferência = 1.37 M bytes/s-10,96 Mbps
* Conexões atuais = 123.08 k
* Unidades de capacidade calculadas = Max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Unidades de capacidade observadas nas métricas = 49,23

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para saber mais sobre como os preços funcionam no gateway Aplicativo Azure:

* [Página de preços do Aplicativo Azure gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Calculadora de preços do Aplicativo Azure gateway](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)

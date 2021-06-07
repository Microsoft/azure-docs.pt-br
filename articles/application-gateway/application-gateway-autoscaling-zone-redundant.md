---
title: Dimensionamento automático e Gateway de Aplicativo com redundância de zona v2
description: Este artigo apresenta o SKU do Aplicativo Azure Standard_v2 e WAF_v2, que inclui os recursos de Dimensionamento automático e com redundância de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: fad6e27c4ee7e8c10237cb3face5cfab9329b2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059714"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Dimensionamento automático e Gateway de Aplicativo com redundância de zona v2 

O gateway de aplicativo está disponível em um SKU Standard_v2. O WAF (firewall do aplicativo Web) está disponível em um SKU WAF_v2. O SKU v2 oferece aprimoramentos de desempenho e adiciona suporte a novos recursos críticos como dimensionamento automático, redundância de zona e suporte para VIPs estáticos. Os recursos existentes no SKU Standard e WAF continuam a ser compatíveis no novo SKU v2, com algumas exceções listadas na seção [comparação](#differences-from-v1-sku).

O novo SKU v2 inclui os seguintes aprimoramentos:

- **Dimensionamento** automático: as implantações de gateway de aplicativo ou WAF no SKU de dimensionamento automático podem ser expandidas ou baseadas com base na alteração de padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Esse SKU oferece a verdadeira elasticidade. No SKU Standard_v2 e WAF_v2, o Gateway de Aplicativo pode operar tanto em capacidade fixa (dimensionamento automático desabilitado) quanto em modo habilitado em dimensionamento automático. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de dimensionamento automático é útil em aplicativos que observam variação no tráfego do aplicativo.
- **Redundância de Zona**: Uma implantação do Gateway de Aplicativo ou do WAF pode abranger várias Zonas de Disponibilidade, eliminando a necessidade de provisionar instâncias separadas do Gateway de Aplicativo em cada zona com um Gerenciador de Tráfego. Você pode escolher uma única zona ou várias zonas nas quais as instâncias do Gateway de Aplicativo são implantadas, o que proporciona maior resiliência de falha de zona. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.

  A redundância de zona está disponível somente quando as Zonas do Azure estão disponíveis. Em outras regiões, todos os outros recursos são compatíveis. Para obter mais informações, confira [Regiões e Zonas de Disponibilidade no Azure](../availability-zones/az-overview.md)
- **VIP estático**: O SKU v2 do Gateway de Aplicativo é compatível exclusivamente com o tipo VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado durante o ciclo de vida da implantação, mesmo após a reinicialização.  Não há um VIP estático na v1, portanto, você deve usar a URL do gateway de aplicativo em vez do endereço IP para roteamento de nome de domínio para os Serviços de Aplicativos por meio do gateway de aplicativo.
- **Regravação de cabeçalho**: O Gateway de Aplicativo permite adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP com o SKU v2. Para obter mais informações, confira [Regravar cabeçalhos HTTP com o Gateway de Aplicativo](rewrite-http-headers.md)
- **Integração do Key Vault**: O Gateway de Aplicativo v2 dá suporte à integração com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. Para obter mais informações, confira [Encerramento de TLS com certificados do Key Vault](key-vault-certs.md).
- **Controlador de Entrada do Serviço de Kubernetes do Azure**: O Controlador de Entrada v2 do Gateway de Aplicativo permite usar o Gateway de Aplicativo do Azure como a entrada para um AKS (Serviço de Kubernetes do Azure), conhecido como Cluster de AKS. Para obter mais informações, confira [O que é o Controlador de Entrada do Gateway de Aplicativo?](ingress-controller-overview.md)
- **Aprimoramentos de desempenho**: O SKU v2 oferece um desempenho de descarregamento de TLS até 5 vezes melhor que o SKU Standard/WAF.
- **Menor tempo de implementação e atualização** O SKU v2 oferece menor tempo de implantação e atualização em comparação ao SKU Standard/WAF. Isso também inclui alterações na configuração do WAF.

![Diagrama da zona de dimensionamento automático.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões com suporte

O SKU Standard_v2 e WAF_v2 está disponível nas seguintes regiões: Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA, Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Centro dos EUA, Norte da Europa, Oeste da Europa, Sudeste da Ásia, França Central, Oeste do Reino Unido, Leste do Japão, Oeste do Japão, Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Canadá Central, Leste do Canadá, Leste da Ásia, Coreia Central, Sul da Coreia, Sul do Reino Unido, Índia Central, Oeste da Índia, Sul da Índia.

## <a name="pricing"></a>Preços

Com o SKU v2, o modelo de preço é orientado pelo consumo e não é mais anexado a contagens ou tamanhos de instância. O preço do SKU v2 possui dois componentes:

- **Preço fixo** - Este é o preço por hora (ou hora parcial) para provisionar um Gateway Standard_v2 ou WAF_v2. Observe que a ausência de instâncias mínimas adicionais ainda garante a alta disponibilidade do serviço, que está sempre inclusa no preço fixo.
- **Preço da unidade de capacidade** - Este é um custo baseado no consumo que é cobrado junto com o custo fixo. O preço da unidade de capacidade também é calculado por hora ou hora parcial. Há três dimensões para a unidade de capacidade - unidade de computação, conexões persistentes e taxa de transferência. A unidade de computação é uma medida da capacidade do processador consumida. Entre os fatores que afetam a unidade de computação, estão as conexões TLS/s, as computações de reescrita de URL e o processamento de regra WAF. A conexão persistente é uma medida de conexões TCP estabelecidas com o gateway de aplicativo em determinado intervalo de cobrança. A taxa de transferência é a média de Megabits/segundo processados pelo sistema em determinado intervalo de cobrança.  A cobrança é realizada no nível da Unidade de capacidade para qualquer coisa acima da contagem de instâncias reservadas.

Cada unidade de capacidade é composta de no máximo: 1 unidade de computação, 2500 conexões persistentes e taxa de transferência de 2,22 Mbps.

Para saber mais, consulte [noções básicas sobre preços](understanding-pricing.md).

## <a name="scaling-application-gateway-and-waf-v2"></a>Dimensionamento do Gateway de Aplicativo e WAF v2

O Gateway de Aplicativo e o WAF podem ser configurados para dimensionar em dois modos:

- **Dimensionamento automático** - Com o dimensionamento automático habilitado, os SKUs do Gateway de Aplicativo e WAF v2 são escalados vertical ou horizontalmente com base nos requisitos de tráfego do aplicativo. Esse modo oferece maior elasticidade ao aplicativo e elimina a necessidade de adivinhar o tamanho do gateway de aplicativo ou a contagem de instâncias. Esse modo também permite economizar custos, não exigindo que o gateway seja executado na capacidade máxima provisionada para a carga máxima de tráfego prevista. Você deve especificar uma contagem mínima e, como opção, uma contagem máxima de instâncias. A capacidade mínima garante que o Gateway de Aplicativo e o WAF v2 não fiquem abaixo da contagem mínima de instâncias especificada, mesmo na ausência de tráfego. Cada instância é aproximadamente equivalente a 10 unidades de capacidade reservadas adicionais. Zero significa nenhuma capacidade reservada, sendo puramente de dimensionamento automático por natureza. Como opção, você também pode especificar uma contagem máxima de instâncias, o que garante que o Gateway de Aplicativo não seja dimensionado além do número especificado de instâncias. Você será cobrado apenas pela quantidade de tráfego veiculada pelo Gateway. As contagens de instâncias podem variar de 0 a 125. O valor padrão da contagem máxima de instâncias é 20, se não for especificado.
- **Manual** - Como alternativa, você pode escolher o modo manual em que o gateway não realize o dimensionamento automático. Nesse modo, se houver mais tráfego do que o Gateway de Aplicativo ou WAF pode suportar, isso poderá resultar em perda de tráfego. Com o modo manual, é obrigatório especificar a contagem de instâncias. A contagem de instâncias pode variar de 1 a 125 instâncias.

## <a name="autoscaling-and-high-availability"></a>Dimensionamento automático e alta disponibilidade

Os Gateways de Aplicativo do Azure são sempre implantados com alta disponibilidade. O serviço é composto de várias instâncias como configuradas (se o dimensionamento automático estiver desabilitado) ou conforme exigido pela carga do aplicativo (se o dimensionamento automático estiver habilitado). Observe que, da perspectiva do usuário, você não tem necessariamente visibilidade das instâncias individuais, mas apenas do serviço de Gateway de Aplicativo como um todo. Se determinada instância tiver um problema e parar de funcionar, o Gateway de Aplicativo do Azure cria uma nova instância de forma transparente.

Observe que, mesmo se você configurar o dimensionamento automático sem instâncias mínimas, o serviço ainda ficará altamente disponível, o que sempre está incluso no preço fixo.

No entanto, a criação de uma nova instância pode demorar um pouco (cerca de seis ou sete minutos). Portanto, se você não quiser lidar com esse tempo de inatividade, pode configurar uma contagem de instâncias mínima de 2, que é ideal com o suporte à Zona de Disponibilidade. Dessa forma, você terá pelo menos duas instâncias no Gateway de Aplicativo do Azure em circunstâncias normais. Portanto, se uma delas tiver um problema, a outra tentará lidar com o tráfego, durante o tempo em que uma nova instância está sendo criada. Observe que uma instância do Gateway de Aplicativo do Azure pode dar suporte à cerca de 10 unidades de capacidade. Portanto, dependendo da quantidade de tráfego que você normalmente possui, convém definir a configuração de dimensionamento automático de instância mínima como um valor maior que 2.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de recursos entre SKU v1 e SKU v2

A tabela a seguir compara os recursos disponíveis em cada SKU.

| Recurso                                           | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância de zona                                   |          | &#x2713; |
| VIP estático                                        |          | &#x2713; |
| Controlador de entrada do AKS (Serviço de Kubernetes do Azure) |          | &#x2713; |
| Integração do Cofre da Chave do Azure                       |          | &#x2713; |
| Regravação de cabeçalhos HTTP(S)                           |          | &#x2713; |
| Roteamento baseado em URL                                 | &#x2713; | &#x2713; |
| Hospedagem de vários sites                             | &#x2713; | &#x2713; |
| Redirecionamento de tráfego                               | &#x2713; | &#x2713; |
| Firewall do aplicativo Web (WAF)                    | &#x2713; | &#x2713; |
| Regras personalizadas de WAF                                  |          | &#x2713; |
| Terminação dos protocolos TLS/SSL            | &#x2713; | &#x2713; |
| Criptografia TLS de ponta a ponta                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Suporte para WebSocket                                 | &#x2713; | &#x2713; |
| Suporte do HTTP/2                                    | &#x2713; | &#x2713; |
| Descarregamento de conexão                               | &#x2713; | &#x2713; |

> [!NOTE]
> Agora o SKU v2 de dimensionamento automático dá suporte a [investigações de integridade padrão](application-gateway-probe-overview.md#default-health-probe) para monitorar automaticamente a integridade de todos os recursos no pool de back-end e realçar os membros de back-end considerados não íntegros. A investigação de integridade padrão é configurada automaticamente para back-ends que não têm uma configuração de investigação personalizada. Para saber mais, confira [investigações de integridade no gateway de aplicativo](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>Diferenças em relação ao SKU v1

Esta seção descreve os recursos e as limitações do SKU v2 que são diferentes do SKU v1.

|Diferença|Detalhes|
|--|--|
|Certificado de autenticação|Sem suporte.<br>Para obter mais informações, confira [Visão geral de TLS de ponta a ponta com o Gateway de Aplicativo](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Combinando Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede|Sem suporte|
|Rota Definida pelo Usuário (UDR) na sub-rede do Gateway de Aplicativo|Com Suporte (cenários específicos). Em versão prévia.<br> Para obter mais informações sobre os cenários com suporte, confira [Visão geral da configuração do Gateway de Aplicativo](configuration-infrastructure.md#supported-user-defined-routes).|
|NSG para o intervalo de porta de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 to 65534 para Standard SKU.<br>Consulte mais informações em [Perguntas Frequentes](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Logs de desempenho no diagnóstico do Azure|Sem suporte.<br>As métricas do Azure devem ser usadas.|
|Cobrança|Cobrança agendada para iniciar em 1º de julho de 2019.|
|Modo FIPS|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Integração do observador de rede|Sem suporte.|
|Integração da Central de Segurança do Azure|Ainda não está disponível.

## <a name="migrate-from-v1-to-v2"></a>Migrar de v1 para v2

Um script do Azure PowerShell está disponível na galeria do PowerShell para ajudar a migrar do Gateway de Aplicativo/WAF v1 para o SKU de Dimensionamento Automático v2. Esse script ajuda a copiar a configuração do gateway v1. A migração de tráfego ainda é de sua responsabilidade. Para obter mais informações, confira [Migrar Gateway de Aplicativo do Azure de v1 para v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicativo com redundância de zona e dimensionamento automático com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).

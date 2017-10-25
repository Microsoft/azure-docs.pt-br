---
title: "Visão geral do Azure Load Balancer Standard | Microsoft Docs"
description: "Visão geral dos recursos do Azure Load Balancer Standard"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: 0ed8d3432a988c468260589cfe12090529c403d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Visão geral do Azure Load Balancer Standard (versão prévia)

Juntos, o SKU do Azure Load Balancer Standard e o SKU de IP público Standard, permitem compilar arquiteturas altamente escalonáveis e confiáveis.  Aplicativos que usam o Load Balancer Standard podem tirar proveito dos novos recursos, além da baixa latência, alta taxa de transferência e escala para milhões de fluxos para todos os aplicativos de TCP e UDP.

>[!NOTE]
> O SKU do Load Balancer Standard está atualmente em versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use o [SKU do Load Balancer Básico](load-balancer-overview.md) geralmente disponível para os serviços de produção.  Recursos associados com essa versão prévia, [Zonas de disponibilidade](https://aka.ms/availabilityzones) e [Portas de HA](https://aka.ms/haports), exigem uma inscrição separada nesse momento. Siga as respectivas instruções para inscrição além da [Versão prévia padrão](#preview-sign-up) do Load Balancer.

## <a name="why-use-load-balancer-standard"></a>Por que usar um Load Balancer Standard

Use o Load Balancer Standard para contar com a gama completa de data centers virtuais, desde implantações de pequena escala até arquiteturas grandes, complexas e com várias zonas, e tire proveito destes recursos:

- É possível atingir uma [escala empresarial](#enterprisescale) com o Load Balancer Standard.  Ele pode ser usado com qualquer instância de Máquina Virtual dentro de uma Rede Virtual, chegando até a 1000 instâncias de VM.

- [Novas informações de diagnóstico](#diagnosticinsights) estão disponíveis para que você possa entender, gerenciar e solucionar problemas deste componente vital de seu data center virtual. Use o Azure Monitor (versão prévia) para mostrar, filtrar e agrupar novas métricas multidimensionais para medições contínuas da integridade do caminho de dados, do front-end à VM, por investigações de integridade do ponto de extremidade, tentativas de conexão TCP, conexões de saída.

- Agora, os [Grupos de Segurança de Rede](#nsg) são exigidos para qualquer instância de VM associada aos SKUs Standard do Load Balancer ou do IP Público e fornecem mais segurança.

- [Portas de alta disponibilidade fornecem alta confiabilidade](#highreliability) e escala para soluções de virtualização de rede e outros cenários de aplicativo. As portas de alta disponibilidade balanceiam todas as portas em um front-end do Load Balancer interno para um pool de instâncias de VM.

- [Conexões de saída](#outboundconnections) agora usam um novo modelo de alocação de porta SNAT que fornece mais resiliência e escala.

- O [Load Balancer Standard com Zonas de disponibilidade](#availabilityzones) pode ser usado para construir arquiteturas com redundância de zona e por zona, ambos com balanceamento de carga entre zonas. Você pode alcançar a redundância de zona sem dependência dos registros de DNS: um único endereço IP tem redundância de zona por padrão e pode acessar qualquer VM em uma VNet de uma região em todas as Zonas de disponibilidade.


Você pode usar o Load Balancer Standard em uma configuração pública ou interna que continua a oferecer suporte aos seguintes cenários fundamentais:

- balanceamento de carga do tráfego de entrada para instâncias de back-end íntegras.
- encaminhamento do tráfego de entrada da porta para uma única instância de back-end.
- conversão do tráfego de saída de um endereço IP privado na VNet para um endereço IP público.

### <a name = "enterprisescale"></a>Escala empresarial

 Use o Load Balancer Standard na criação de seu data center virtual de alto desempenho. Você pode usar instâncias autônomas de VM ou até 1000 instâncias de conjuntos de dimensionamento de máquinas virtuais em um pool de back-end e oferecer suporte a qualquer aplicativo TCP ou UDP. Com o Load Balancer Standard, o aplicativo ainda poderá tirar proveito da baixa latência de encaminhamento, ótimo desempenho de taxa de transferência e escala para milhões de fluxos em um serviço do Azure totalmente gerenciado.
 
O Load Balancer Standard pode encaminhar o tráfego a qualquer instância de VM em uma VNet em uma região. Os tamanhos de pool de back-end podem chegar a 1000 instâncias com qualquer combinação de:

- VMs autônomas sem Conjuntos de Disponibilidade
- VMs autônomas com Conjuntos de Disponibilidade
- Conjuntos de dimensionamento de máquinas virtuais (conjunto de dimensionamento de máquinas virtuais) com até 1000 instâncias
- múltiplos conjuntos de dimensionamento de máquina virtual
- combinação de VMs e conjuntos de dimensionamento de máquinas virtuais.

Não exige-se mais conjuntos de disponibilidade, mas você pode optar por usar os conjuntos de disponibilidade pelos outros benefícios que eles oferecem.

### <a name = "diagnosticinsights"></a>Informações do diagnóstico

O Load Balancer Standard fornece novos recursos de diagnóstico multidimensionais para configurações públicas e internas do Load Balancer. Essas novas métricas são fornecidas pelo Azure Monitor (versão prévia) e utilizam todos os recursos relacionados, incluindo a capacidade de integração com vários consumidores de downstream.

| Métrica | Descrição |
| --- | --- |
| Disponibilidade do VIP | O Load Balancer Standard usa continuamente o caminho de dados de dentro de uma região para o front-end do Load Balancer e, finalmente, para a pilha do SDN que dá suporte a sua VM. Contanto que instâncias íntegras permaneçam, a medição seguirá o mesmo caminho que o tráfego com balanceamento de carga de seus aplicativos, e validará o caminho de dados que seus clientes possam estar usando. A medição é invisível para seu aplicativo e não interfere nele.|
| Disponibilidade do DIP | O Load Balancer Standard usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade de seu aplicativo, conforme sua configuração.  Essa métrica fornece uma exibição agregada ou por ponto de extremidade filtrado de cada ponto de extremidade de instância individual no pool do Load Balancer.  Você pode verificar como o Load Balancer vê a integridade de seu aplicativo conforme indicado pela configuração de sua investigação de integridade.
| Pacotes SYN | O Load Balancer Standard não encerra conexões TCP ou interage com fluxos de pacote TCP ou UDP; os fluxos e seus handshakes ocorrem sempre entre a origem e a instância da VM. Para solucionar melhor todos os seus cenários de protocolo TCP, use essa métrica para entender quantas tentativas de conexão TCP são feitas. Essa métrica informa o número de pacotes TCP SYN que foram recebidos, e pode refletir a tentativa de clientes estabelecerem conexões com o serviço.|
| Conexões SNAT | O Load Balancer Standard informa o número de conexões de saída mascaradas para o front-end do endereço IP público.  As portas SNAT são um recurso esgotável, e essa métrica pode dar uma indicação do grau de dependência que seu aplicativo tem do SNAT para conexões com origem externa.|
| Contadores de bytes | O Load Balancer Standard informa os dados processados por front-end.|
| Contadores de pacotes | O Load Balancer Standard informa os pacotes processados por front-end.|

### <a name = "highreliability"></a>Alta confiabilidade

Configure as regras de balanceamento de carga para dimensionar seu aplicativo e ser altamente confiável.  Você pode configurar regras para portas individuais, ou pode usar as novas Portas de alta disponibilidade para balancear todo o tráfego, independentemente do número de porta TCP ou UDP.  

Você pode usar o novo recurso de Portas de alta disponibilidade para desbloquear vários cenários, incluindo alta disponibilidade e escalabilidade para soluções de virtualização de rede, e outros cenários nos quais é impraticável ou indesejável especificar portas individuais. As Portas de alta disponibilidade fornecem redundância e escala permitindo o máximo de instâncias necessárias, em vez de ficarem restritas a cenários ativos/passivos. As configurações do investigação de integridade protegem seu serviço encaminhando o tráfego apenas às instâncias íntegras.

Os fornecedores de solução de virtualização de rede podem fornecer cenários resilientes com suporte total do fornecedor por meio da remoção de um ponto único de falha para seus clientes, e permitindo várias instâncias ativas para escala. Você pode escalar para duas ou mais instâncias, seu a sua solução permitir essas configurações. O fornecedor de sua solução de virtualização de rede precisa fornecer mais orientações para esses cenários.

### <a name = "availabilityzones"></a>Zonas de disponibilidade

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Aumente a resiliência de seu aplicativo com as Zonas de disponibilidade nas regiões com suporte. No momento, as Zonas de disponibilidade estão em uma versão prévia em regiões específicas, e exigem aceitação adicional.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

Você pode escolher se o Load Balancer deve fornecer um front-end com redundância de zona ou zonal para cada um dos seus aplicativos.  É fácil criar redundância de zona com o Load Balancer Standard.  Um único endereço IP front-end tem automaticamente redundância de zona.  Um front-end com redundância de zona é atendido por todas as Zonas de disponibilidade em uma região, simultaneamente. Isso cria um caminho de dados com redundância de zona para conexões de entrada e saída. A redundância de zona no Azure não exige vários endereços IP e registros DNS. 

Essa redundância de zona está disponível para front-ends público ou interno. Seu endereço IP Público, bem como o IP privado interno do front-end do Load Balancer, pode ter redundância de zona.

Crie um endereço IP Público com redundância de zona com o seguinte (adicione "sku" a qualquer modelo do Resource Manager existente):

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Crie uma configuração de IP de front-end com redundância de zona do Load Balancer com o seguinte (adicione "sku" a qualquer modelo do Resource Manager existente):

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Se o front-end do IP Público tiver redundância de zona, quaisquer conexões de saída provenientes de instâncias da VM terão redundância de zona automaticamente e serão protegidas contra falha de zona.  A alocação da porta SNAT sobrevive à falha da zona.

#### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zonas

O balanceamento de carga entre zonas está disponível dentro de uma região para o pool de back-ends, permitindo a máxima flexibilidade para suas instâncias de Máquina Virtual.  Um front-end pode entregar fluxos para qualquer VM na VNet, independentemente da zona de disponibilidade da instância da VM.

Além disso, você também pode optar por especificar uma zona específica para suas instâncias de front-end e back-end, a fim de alinhar o caminho de dados e os recursos com uma zona específica.

Como VNets e sub-redes nunca sofrem restrição de zona, basta você definir um pool de back-ends com suas instâncias de VM desejadas, e a configuração estará concluída.

#### <a name="zonal-deployments"></a>Implantações em zona

Opcionalmente, também é possível alinhar o front-end a uma zona específica definindo um front-end em zona.  Um front-end em zona é atendido somente pela única zona de disponibilidade designada e, quando combinado com instâncias da VM em zona, você pode alinhar recursos a zonas específicas.

Um endereço IP público criado em uma zona específica existirá sempre somente nessa zona.  Não é possível alterar a zona de um endereço IP público.  Se você quiser ter um endereço IP público que possa ser anexado aos recursos em várias zonas, será necessário criar um IP público de zona redundante.

Crie um endereço IP Público em Zona de disponibilidade 1 com o seguinte (adicione "zonas" e "sku" a qualquer modelo do Resource Manager existente):

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Crie um front-end interno do Load Balancer na Zona de disponibilidade 1 com o seguinte (adicionar "sku" a qualquer modelo do Resource Manager existente e coloque "zonas" no recurso filho da configuração de IP de front-end):

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Além disso, você pode usar o balanceamento de carga em zonas seu o back-end colocando suas instâncias da VM em uma VNet dentro de um pool de back-ends.

O próprio recurso de Load Balancer Standard será sempre regional e com redundância de zona quando houver suporte para Zonas de disponibilidade. Um endereço IP Público ou front-tend interno do Load Balancer que não recebeu uma zona pode ser implantado em qualquer região, independentemente do suporte das Zonas de disponibilidade. Se uma região receber Zonas de disponibilidade posteriormente, um IP Público já implantado ou um front-end interno do Load Balancer terá redundância de zona automaticamente. O caminho de dados com redundância de zona não implica em 0% de perda de pacotes.

### <a name = "nsg"></a>Grupos de segurança de rede

O Load Balancer Standard e o IP Público Standard são totalmente integrados à VNet, e os Grupos de Segurança de Rede (NSG) agora são exigidos. O NSG aceita uma lista de permissões de fluxos e permite configurações nas quais os clientes estão em controle total de quando permitir o tráfego até suas implantações por meio do NSG, em vez de quando outras configurações são concluídas.

Associe um NSG com sub-redes ou com NICs de instâncias de VM no pool de back-end.  Isso se aplica ao Load Balancer Standard e IP Público Standard quando usado como IP Público no Nível da Instância. Você deve incluir explicitamente na lista de permissão qual tráfego você deseja permitir com o NSG para que ele flua.

Para saber mais sobre grupos de segurança de rede e como aplicá-los ao seu cenário, consulte [Grupos de Segurança de Rede](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Conexões de saída

 O Load Balancer Standard fornece conexões de saída para as VMs dentro da VNet quando associado a um Load Balancer usando o SNAT (Tradução de endereços de rede de origem) para mascaramento de porta.

Quando um recurso do Load Balancer público está associado a instâncias de VM, a origem de cada conexão de saída é reescrita a partir do espaço de endereço IP privado da VNet para o endereço IP público do front-end do Load Balancer.  O Load Balancer Standard usa um novo algoritmo SNAT (Tradução de endereços de rede de origem) para mascarar a porta a fim de aumentar a escala e a robustez.  

Além disso, as conexões de saída, quando são usadas com um front-end com redundância de zona, também têm redundância de zona, e as alocações de porta SNAT sobrevivem à falha da zona.

O novo algoritmo do Load Balancer Standard pré-aloca as portas SNAT à interface de rede de cada VM no momento em que elas são adicionadas ao pool, de acordo com as seguintes camadas:

| Tamanho do pool de back-end | Portas SNAT pré-alocadas |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

As portas SNAT não são convertidas diretamente para o número de conexões. Uma porta SNAT pode ser reutilizada para vários destinos exclusivos.  Examine o artigo [conexões de saída](load-balancer-outbound-connections.md) para obter detalhes.

Se o pool de back-end aumentar e mudar de um nível de tamanho para o próximo tamanho maior, metade das suas portas alocadas será recuperada. Qualquer conexão associada a um porta recuperada atingirá o tempo limite e precisará ser restabelecida. Novas tentativas de conexão terão êxito imediatamente. Se o seu pool de back-ends for reduzido de um nível de tamanho para o próximo tamanho menor, as portas SNAT disponíveis crescerão, e as conexões existentes não serão afetadas.

O Load Balancer Standard também tem uma opção de configuração adicional em uma base por regra, a fim de fornecer controle do cliente sobre qual front-end será usado para mascarar a porta SNAT quando houver vários front-ends disponíveis.

Por fim, quando apenas o Load Balancer Standard atender às instâncias de VM, as conexões SNAT de saída não estarão disponíveis. Você pode restaurar explicitamente essa capacidade atribuindo também as instâncias de VM a um Load Balancer público, ou atribuindo também os IPs Públicos como IPs Públicos no nível da instância diretamente em cada instância da VM. Isso pode ser necessário para alguns cenários de aplicativo e sistema operacional. 

### <a name="port-forwarding"></a>Encaminhamento de porta

 Os Load Balancers Básico e Standard fornecem a capacidade de configurar regras de NAT de entrada para mapear uma porta front-end para uma instância individual de back-end.  Há muitos usos para essa capacidade, incluindo a exposição de pontos de extremidade do protocolo RDP, pontos de extremidade SSH ou vários outros cenários de aplicativo.

O Load Balancer Standard continua a oferecer a capacidade de encaminhamento de porta por meio de regras NAT de entrada.  Quando usadas com front-ends com redundância de zona, regras de NAT de entrada ficam com redundância de zona e sobreviverão à falha da zona.

### <a name="multiple-frontends"></a>Vários front-ends

Configure vários front-ends para obter flexibilidade de design em que os aplicativos exigem a exposição de vários endereços IP individuais (como sites TLS ou pontos de extremidade do Grupos de Disponibilidade AlwaysOn do SQL).  Encontre mais detalhes [aqui](load-balancer-multivip-overview.md).

O Load Balancer Standard continua a oferecer vários front-ends em que é desejável expor um ponto de extremidade de aplicativo específico em um endereço IP exclusivo.

 Encontre mais detalhes [aqui](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Sobre SKUs

Os SKUs só estão disponíveis no modelo de implantação do Azure Resource Manager.  Esta versão prévia apresenta dois SKUs (Básico e Standard) para os recursos do Load Balancer e do IP Público.  Os SKUs diferem em alguns comportamentos intrínsecos, características de desempenho, limitações e capacidades. As Máquinas Virtuais podem ser usadas com qualquer SKU. Para os recursos do Load Balancer e do IP Público, os SKUs permanecem atributos opcionais e, quando são omitidos, assumem o padrão de Básico.

>[!IMPORTANT]
>O SKU de um recurso não pode ser trocado.  Você não pode alterar a SKU de um recurso existente.  

### <a name="load-balancer"></a>Balanceador de carga

O [recurso existente do Load Balancer](load-balancer-overview.md) se torna o SKU Básico e permanece disponível para todos e inalterado.

O SKU do Load Balancer Standard é uma nova oferta e está atualmente em versão prévia. A versão da API 2017-08-01 para Microsoft.Network/loadBalancers apresenta SKUs para o recurso.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Quando usado em uma região que também oferece Zonas de disponibilidade, o Load Balancer Standard fica automaticamente resiliente à zona, a menos que tenha sido declarado zonal.

### <a name="public-ip"></a>IP público

O [recurso de IP público existente](../virtual-network/virtual-network-ip-addresses-overview-arm.md) se torna o SKU Básico e permanece disponível para todos com todas as suas habilidades, características de desempenho e limitações.

O SKU do IP Público Standard é uma nova oferta e está atualmente em versão prévia. A versão da API de 2017-08-01 para recursos Microsoft.Network/publicIPAddresses apresenta SKUs.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Ao contrário do IP Público Básicos que oferece vários métodos de alocação, o IP Público Standard em sempre Alocação estática.

Quando usado em uma região que também oferece Zonas disponibilidade, o IP Público Standard fica automaticamente resiliente à zona, a menos que tenha sido declarado zonal.  Um IP público zonal não pode ser alterado de uma zona para outra.

## <a name="migration-between-skus"></a>Migração entre SKUs

Os SKUs não são mutáveis.  Se você quiser mudar do SKU de um recurso para outro, execute estas etapas:

### <a name="migrating-from-basic-to-standard-sku"></a>Migrar do SKU Básico para Standard

1. Crie um novo recurso Standard (Load Balancer e IPs Públicos, conforme o necessário) e recrie as regras e as definições de investigação.
2. Remova os recursos do SKU Básico (IP Público e LB) de todas as instâncias de VM (incluindo todas as instâncias de um conjunto de disponibilidade).
3. Anexe todas as instâncias de VM aos novos recursos de SKU Standard.

### <a name="migrating-from-standard-to-basic-sku"></a>Migrar do SKU Standard para o Básico:

1. Crie um novo recurso Básico (Load Balancer e IPs Públicos, conforme o necessário) e recrie as regras e as definições de investigação. 
2. Remova os recursos do SKU Standard (IP Público e LB) de todas as instâncias de VM (incluindo todas as instâncias de um conjunto de disponibilidade).
3. Anexe todas as instâncias de VM aos novos recursos de SKU Básico.

  >[!NOTE]
  >Portas de alta disponibilidade, diagnóstico do SKU Standard, só estão disponíveis no SKU Standard. Você não pode migrar do Standard para o Básico e manter essa funcionalidade.

SKUs correspondentes devem ser usados para os recursos do Load Balancer e do IP Público.  Não é possível combinar recursos de SKU Básico e Standard, ou anexar uma VM, VMs em um conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais definidos para ambos simultaneamente.

## <a name="region-availability"></a>Disponibilidade de região

O Load Balancer Standard está disponível atualmente nestas regiões:
- Leste dos EUA 2
- Centro dos EUA
- Norte da Europa
- Centro-Oeste dos EUA
- Europa Ocidental
- Sudeste Asiático

## <a name="service-limits--abilities-comparison"></a>Comparação de limites e recursos do serviço

Os [Limites de serviço de rede](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits) do Azure se aplicam de acordo com a região e com a assinatura. 

A tabela a seguir fornece uma comparação de recursos e limites entre os SKUs Básico e Standard para o Load Balancer:

| Balanceador de carga | Basic | Standard |
| --- | --- | --- |
| Tamanho do pool de back-end | até 100 | até 1000 |
| Limite do pool de back-ends | Conjunto de disponibilidade | VNet, região |
| Design do pool de back-ends | VMs em conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais no conjunto de disponibilidade | Qualquer instância de VM na VNet |
| Portas de alta disponibilidade | Sem suporte | Disponível |
| Diagnostics | Limitado, somente público | Disponível |
| Disponibilidade do VIP  | Sem suporte | Disponível |
| Mobilidade rápida de IP | Sem suporte | Disponível |
|Cenários de Zonas de disponibilidade | Somente zonal | Zonal, com redundância de zona, balanceamento de carga entre zonas |
| Algoritmo SNAT de saída | Sob demanda | Pré-alocada |
| Seleção de front-end de SNAT de saída | Não é configurável, vários candidatos | Configuração opcional para reduzir candidatos |
| Grupo de Segurança de Rede | Opcional na sub-rede/NIC | Obrigatório |

A tabela a seguir fornece uma comparação de recursos e limites entre os SKUs Básico e Standard para o IP Público:

| IP público | Basic | Standard |
| --- | --- | --- |
| Cenários de Zonas de disponibilidade | Somente zonal | Redundância de zona (padrão), zonal (opcional) | 
| Mobilidade rápida de IP | Sem suporte | Disponível |
| Disponibilidade do VIP | Sem suporte | Disponível |
| Contadores | Sem suporte | Disponível |
| Grupo de Segurança de Rede | Opcional na NIC | Obrigatório |


## <a name="preview-sign-up"></a>Inscrição na versão prévia

Para participar da versão prévia do SKU do Load Balancer Standard e seu SKU de IP Público Standard complementar, registre sua assinatura para obter acesso usando o PowerShell ou CLI do Azure 2.0.

- Inscrever-se usando o PowerShell

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- Inscrever-se usando a CLI do Azure 2.0

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>O registro do recurso padrão Load Balancer pode levar até uma hora.

>[!NOTE]
>Se desejar usar o Padrão do Load Balancer com [Zonas de disponibilidade](https://aka.ms/availabilityzones) e [Portas de HA](https://aka.ms/haports), uma inscrição separada será necessária para essas visualizações.  Siga as respectivas instruções.

## <a name="pricing"></a>Preços

O SKU do Load Balancer Standard é cobrado com base em regras configuradas e nos dados processados.  Nenhum encargo é gerado durante o período de versão prévia.  Veja as páginas de preço do [Load Balancer](https://aka.ms/lbpreviewpricing) e do [IP Público](https://aka.ms/lbpreviewpippricing) para saber mais.

Os clientes continuam a desfrutar do SKU do Load Balancer Básico sem custo adicional.

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam no momento da versão prévia e estão sujeitos a alterações:

- No momento, as instâncias de back-end do Load Balancer não podem ser localizadas em VNets emparelhadas. Todas as instâncias de back-end devem estar na mesma região.
- Os SKUs não são mutáveis. Você não pode alterar a SKU de um recurso existente.
- Você pode usar o SKU Básico ou Standard com uma VM autônoma, todas as instâncias de VM em um conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais. Uma VM autônoma, todas as instâncias de VM em um conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais não podem ser usadas com ambos ao mesmo tempo. Não é permitido misturar SKUs.
- Usar um Load Balancer Standard interno com uma instância de VM (ou qualquer parte de um conjunto de disponibilidade) desabilita [conexões de saída SNAT padrão](load-balancer-outbound-connections.md).  Você pode restaurar essa capacidade para uma VM autônoma, ou para instâncias de VM em um conjunto de disponibilidade ou em um conjunto de dimensionamento de máquinas virtuais, e estabelecer conexões de saída atribuindo simultaneamente um Load Balancer Standard público ou um IP Público Standard como IP Público no nível da instância para a mesma instância de VM. Após a conclusão, o SNAT de mascaramento de porta para um endereço IP Público é fornecido novamente.
- Talvez seja necessário agrupar instâncias de VM em conjuntos de disponibilidade para alcançar a escala de pool de back-end completa. É possível posicionar até 150 conjuntos de disponibilidade e VMs autônomas em um único pool de back-ends.
- Não há suporte para IPv6.
- No contexto das Zonas de disponibilidade, um front-end não pode ser alternado de zonal para com redundância de zona, ou vice-versa. Após a criação com redundância de zona, sempre será com redundância de zona. Após a criação em zona, sempre será em zona.
- No contexto das Zonas de disponibilidade, um endereço IP Público em zona não pode ser movido de uma zona para outra.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Básico](load-balancer-overview.md)
- Saiba mais sobre as [Zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre algumas das outras principais [funcionalidades de rede](../networking/networking-overview.md) do Azure


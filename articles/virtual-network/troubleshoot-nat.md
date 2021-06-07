---
title: Solucionar problemas de conectividade da NAT da Rede Virtual do Azure
titleSuffix: Azure Virtual Network
description: Solucione problemas com a NAT da Rede Virtual.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 51bc4233393be6d914578581597e8cce9d0373b0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059251"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Solucionar problemas de conectividade da NAT da Rede Virtual do Azure

Este artigo ajuda os administradores a diagnosticar e resolver problemas de conectividade ao usar a NAT da Rede Virtual.

## <a name="problems"></a>Problemas

* [Esgotamento de SNAT](#snat-exhaustion)
* [O ping do ICMP está falhando](#icmp-ping-is-failing)
* [Falhas na conectividade](#connectivity-failures)
* [Coexistência de IPv6](#ipv6-coexistence)
* [A conexão não se origina de IP(s) do gateway NAT](#connection-doesnt-originate-from-nat-gateway-ips)

Para resolver esses problemas, siga as etapas na seção a seguir.

## <a name="resolution"></a>Resolução

### <a name="snat-exhaustion"></a>Esgotamento de SNAT

Um só [recurso do Gateway da NAT](nat-gateway-resource.md) dá suporte de 64.000 a até 1 milhão de fluxos simultâneos.  Cada endereço IP fornece 64.000 portas SNAT para o inventário disponível. Você pode usar até 16 endereços IP por recurso do Gateway da NAT.  O mecanismo SNAT é descrito [aqui](nat-gateway-resource.md#source-network-address-translation) mais detalhadamente.

Frequentemente, a causa raiz do esgotamento de SNAT é um antipadrão para a forma como a conectividade de saída é estabelecida, gerenciada ou como os temporizadores configuráveis têm os valores padrão alterados.  Revise esta seção com cuidado.

#### <a name="steps"></a>Etapas

1. Verifique se você modificou o tempo limite de ociosidade padrão para um valor superior a 4 minutos.
2. Investigar como o aplicativo está criando a conectividade de saída (por exemplo, revisão de código ou captura de pacote). 
3. Determinar se essa atividade é um comportamento esperado ou se o aplicativo está tendo um comportamento inadequado.  Usar [métricas](nat-metrics.md) no Azure Monitor para substanciar suas descobertas. Use a categoria "Com falha" para obter a métrica de Conexões SNAT.
4. Avaliar se os padrões apropriados são seguidos.
5. Avaliar se o esgotamento de porta SNAT deve ser mitigado com endereços IP adicionais atribuídos ao recurso do Gateway da NAT.

#### <a name="design-patterns"></a>Padrões de design

Sempre aproveite a reutilização de conexão e o pool de conexões quando possível.  Esses padrões evitarão problemas de esgotamento de recursos e resultarão em um comportamento previsível. Os primitivos desses padrões podem ser encontrados em muitas bibliotecas e estruturas de desenvolvimento.

_**Solução:**_ Usar padrões e melhores práticas recomendadas apropriados

- Os recursos de Gateway da NAT têm um tempo limite de ociosidade de TCP padrão de 4 minutos.  Se essa configuração for alterada para um valor mais alto, o NAT manterá fluxos mais longos e poderá causar [pressão desnecessária no inventário de portas SNAT](nat-gateway-resource.md#timers).
- As solicitações atômicas (uma solicitação por conexão) são uma opção de design ruim. Esses limites de padrão escala, reduzem o desempenho e diminuem a confiabilidade. Em vez disso, reutilize as conexões HTTP/S para reduzir os números de conexões e as portas SNAT associadas. A escala do aplicativo aumentará e o desempenho vai melhorar devido a um custo reduzido de Handshakes, sobrecarga e operação de criptografia ao usar o TLS.
- O DNS pode introduzir muitos fluxos individuais no volume quando o cliente não está armazenando em cache o resultado dos resolvedores de DNS. Use o cache.
- Os fluxos UDP (por exemplo, pesquisas de DNS) alocam portas SNAT para a duração do tempo limite de ociosidade. Quanto maior o tempo limite de ociosidade, maior a pressão em portas SNAT. Use o tempo limite de ociosidade curto (por exemplo, 4 minutos).
- Use pools de conexão para formatar o volume de conexão.
- Nunca abandone silenciosamente um fluxo de TCP nem dependa de temporizadores TCP para limpar o fluxo. Se você não permitir que o TCP feche explicitamente a conexão, o estado permanecerá alocado em sistemas intermediários e pontos de extremidade e tornará as portas SNAT não disponíveis para outras conexões. Esse padrão pode disparar falhas de aplicativo e esgotamento de SNAT. 
- Não altere os valores de temporizador relacionados ao fechamento de TCP no nível do sistema operacional sem um conhecimento especializado do impacto. Embora a pilha de TCP seja recuperada, o desempenho do aplicativo poderá ser prejudicado quando os pontos de extremidade de uma conexão tiverem uma incompatibilidade de expectativas. O desejo de alterar temporizadores é geralmente um sinal de um problema de design subjacente. Examine as seguintes recomendações.

A exaustão de SNAT também pode ser amplificada com outros antipadrões no aplicativo subjacente. Examine esses padrões adicionais e as melhores práticas para aprimorar a escala e a confiabilidade do seu serviço.

- Explore o impacto de reduzir o [tempo limite de ociosidade de TCP](nat-gateway-resource.md#timers) a valores mais baixos, incluindo o tempo limite de ociosidade padrão de 4 minutos para liberar o inventário de portas SNAT mais cedo.
- Considere os [padrões de sondagem assíncrona](/azure/architecture/patterns/async-request-reply) nas operações de execução longa, a fim de liberar recursos de conexão para outras operações.
- Os fluxos de longa vida (por exemplo, conexões TCP reutilizadas) devem usar keep alives TCP ou keep alives da camada de aplicativo para evitar que os sistemas intermediários atinjam o tempo limite. Aumentar o tempo limite de ociosidade é um último recurso e pode não resolver a causa raiz. Um tempo limite longo pode causar falhas de taxa baixa quando o tempo limite expira e introduz atraso e falhas desnecessárias.
- Os [padrões normais de repetição](/azure/architecture/patterns/retry) devem ser usados para evitar tentativas agressivas/intermitências durante uma falha transitória ou uma recuperação de falha.
A criação de uma conexão TCP para todas as operações HTTP (também conhecidas como "conexões atômicas") é um antipadrão.  As conexões atômicas impedirão que o aplicativo seja bem dimensionado e gaste recursos.  Sempre inclua várias operações em um pipeline na mesma conexão.  Seu aplicativo se beneficiará da velocidade da transação e dos custos de recursos.  Quando o aplicativo usa uma criptografia de camada de transporte (por exemplo, TLS), há um custo significativo associado ao processamento de novas conexões.  Examine [Padrões de design de nuvem do Azure](/azure/architecture/patterns/) para obter padrões adicionais de melhores práticas.

#### <a name="additional-possible-mitigations"></a>Possíveis mitigações adicionais

_**Solução:**_ escale a conectividade de saída da seguinte maneira:

| Cenário | Evidência |Atenuação |
|---|---|---|
| Você está enfrentando a contenção de portas SNAT e o esgotamento de porta SNAT durante períodos de alto uso. | A categoria "Com falha" na [métrica](nat-metrics.md) de Conexões SNAT no Azure Monitor mostra falhas transitórias ou persistentes ao longo do tempo e um alto volume de conexão.  | Determine se você pode adicionar recursos adicionais de endereço IP público ou recursos de prefixo IP público. Essa adição permitirá até 16 endereços IP no total para o Gateway da NAT. Essa adição fornecerá mais inventário para as portas SNAT disponíveis (64.000 por endereço IP) e permitirá que você escale ainda mais o seu cenário.|
| Você já recebeu 16 endereços IP e ainda está enfrentando o esgotamento de porta SNAT. | Falha na tentativa de adicionar outro endereço IP. O número total de endereços IP dos recursos de endereço IP público ou dos recursos de prefixo de IP público excede um total de 16. | Distribua o ambiente de aplicativo em várias sub-redes e forneça um recurso do Gateway da NAT para cada sub-rede.  Reavalie seus padrões de design para fazer uma otimização com base nas [diretrizes](#design-patterns) anteriores. |

>[!NOTE]
>É importante entender por que o esgotamento de SNAT ocorre. Verifique se você está usando os padrões corretos para cenários escalonáveis e confiáveis.  A adição de mais portas SNAT a um cenário sem entender a causa da demanda deve ser o último recurso. Se você não entender por que o cenário está aplicando pressão no inventário de portas SNAT, a adição de mais portas SNAT ao inventário pela adição de mais endereços IP só atrasará a mesma falha de esgotamento à medida que o aplicativo for dimensionado.  Você pode estar mascarando outras ineficiências e antipadrões.

### <a name="icmp-ping-is-failing"></a>O ping do ICMP está falhando

O [NAT da Rede Virtual](nat-overview.md) dá suporte a protocolos UDP e TCP IPv4. Não há suporte para o ICMP e é esperada uma falha.  

_**Solução:**_ Em vez disso, use testes de conexão TCP (por exemplo, "ping do TCP") e testes da camada de aplicativo específicos do UDP para validar a conectividade de ponta a ponta.

A tabela a seguir pode ser usada como um ponto de partida para saber quais ferramentas serão usadas para iniciar os testes.

| Sistema operacional | Teste de conexão TCP genérica | Teste de camada de aplicativo TCP | UDP |
|---|---|---|---|
| Linux | nc (teste de conexão genérica) | cURL (teste da camada de aplicativo TCP) | específico ao aplicativo |
| Windows | [PsPing](/sysinternals/downloads/psping) | [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) do PowerShell | específico ao aplicativo |

### <a name="connectivity-failures"></a>Falhas na conectividade

Problemas de conectividade com a [NAT de Rede Virtual](nat-overview.md) podem ser causados por vários problemas diferentes:

* falhas permanentes devido a erros de configuração.
* [esgotamento de SNAT](#snat-exhaustion) transitório ou persistente do Gateway da NAT;
* falhas transitórias na infraestrutura do Azure; 
* falhas transitórias no caminho entre o Azure e o destino público da Internet; 
* falhas transitórias ou persistentes no destino da Internet pública.

Use ferramentas como as mostradas a seguir para validar a conectividade. [Não há suporte para o ping de ICMP](#icmp-ping-is-failing).

| Sistema operacional | Teste de conexão TCP genérica | Teste de camada de aplicativo TCP | UDP |
|---|---|---|---|
| Linux | nc (teste de conexão genérica) | cURL (teste da camada de aplicativo TCP) | específico ao aplicativo |
| Windows | [PsPing](/sysinternals/downloads/psping) | [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) do PowerShell | específico ao aplicativo |

#### <a name="configuration"></a>Configuração

Verifique a configuração:
1. O recurso de gateway NAT tem pelo menos um recurso IP público ou um recurso de prefixo de IP público? Você deve ter pelo menos um endereço IP associado ao gateway NAT para que ele possa fornecer conectividade de saída.
2. A sub-rede da rede virtual está configurada para usar o gateway NAT?
3. Você está usando uma UDR (rota definida pelo usuário) e substituindo o destino?  Os recursos do gateway NAT se tornam a rota padrão (0/0) em sub-redes configuradas.

#### <a name="snat-exhaustion"></a>Esgotamento de SNAT

Examine a seção sobre o [esgotamento de SNAT](#snat-exhaustion) neste artigo.

#### <a name="azure-infrastructure"></a>Infraestrutura do Azure

O Azure monitora e opera sua infraestrutura com muito cuidado. Podem ocorrer falhas transitórias, não há garantia de que as transmissões ocorrem sem perdas.  Use padrões de design que permitam retransmissões de SYN para aplicativos TCP. Use tempos limite de conexão longos o suficiente para permitir a retransmissão de TCP SYN, a fim de reduzir impactos transitórios causados pela perda de um pacote SYN.

_**Solução:**_

* verifique se há um [esgotamento de SNAT](#snat-exhaustion).
* O parâmetro de configuração em uma pilha TCP que controla o comportamento de retransmissão de SYN é chamado de RTO ([tempo limite de retransmissão](https://tools.ietf.org/html/rfc793)). O valor de RTO é ajustável, mas geralmente 1 segundo ou mais, por padrão, com retirada exponencial.  Se o tempo limite de conexão do seu aplicativo for muito curto (por exemplo, 1 segundo), você poderá observar tempos limite de conexão esporádicos.  Aumente o tempo limite de conexão do aplicativo.
* Se você observar tempos limite inesperados maiores com os comportamentos de aplicativo padrão, abra um caso de suporte para uma solução de problemas adicional.

Não recomendamos a redução artificial do tempo limite da conexão TCP nem o ajuste do parâmetro RTO.

#### <a name="public-internet-transit"></a>Trânsito da Internet pública

As chances de falhas transitórias aumentam com um caminho mais longo para o destino e mais sistemas intermediários. É esperado que as falhas transitórias possam aumentar em frequência na [infraestrutura do Azure](#azure-infrastructure). 

Siga as mesmas diretrizes da seção anterior sobre a [infraestrutura do Azure](#azure-infrastructure).

#### <a name="internet-endpoint"></a>Ponto de extremidade da Internet

As seções anteriores se aplicam, juntamente com o ponto de extremidade da Internet com o qual a comunicação é estabelecida. Outros fatores que podem afetar o sucesso da conectividade são:

* gerenciamento de tráfego no lado de destino, incluindo
- Limitação da taxa de API imposta pelo lado do destino
- Mitigações de DDoS volumétrico ou modelagem do tráfego da camada de transporte
* firewall ou outros componentes no destino 

Normalmente, as capturas de pacotes na origem e no destino (se disponível) são necessárias para determinar o que está ocorrendo.

_**Solução:**_

* verifique se há um [esgotamento de SNAT](#snat-exhaustion). 
* Valide a conectividade com um ponto de extremidade na mesma região ou em outro lugar para comparação.  
* Se estiver criando um teste de taxa de transação ou alto volume, explore se a redução da taxa reduz a ocorrência de falhas.
* Se a taxa de alteração afetar a taxa de falhas, verifique se os limites de taxa de API ou outras restrições no lado do destino podem ter sido atingidas.
* Se a investigação for inconclusiva, abra um caso de suporte para uma solução de problemas adicional.

#### <a name="tcp-resets-received"></a>Redefinições de TCP recebidas

O gateway de NAT gera redefinições de TCP na VM de origem para o tráfego que não é reconhecido como em andamento.

Um motivo possível é que a conexão TCP tenha atingido o tempo limite de ociosidade.  Ajuste o tempo limite de ociosidade de 4 minutos a até 120 minutos.

As redefinições de TCP não são geradas no lado público dos recursos do gateway da NAT. As redefinições de TCP no lado do destino são geradas pela VM de origem, e não pelo recurso do gateway da NAT.

_**Solução:**_

* examine as recomendações de [padrões de design](#design-patterns).  
* Abra um caso de suporte para obter soluções de problemas adicionais, se necessário.

### <a name="ipv6-coexistence"></a>Coexistência de IPv6

A [NAT de Rede Virtual](nat-overview.md) dá suporte a protocolos UDP e TCP IPv4, e não há suporte para a implantação em uma sub-rede [com um prefixo IPv6](nat-overview.md#limitations).

_**Solução:**_ implante o Gateway da NAT em uma sub-rede sem o prefixo IPv6.

Indique seu interesse em funcionalidades adicionais por meio do [UserVoice da NAT de Rede Virtual](https://aka.ms/natuservoice).

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>A conexão não se origina de IP(s) do gateway NAT

Você configura o gateway NAT, os endereços IP a serem usados e qual sub-rede deve usar um recurso de gateway NAT. No entanto, as conexões das instâncias de máquina virtual que existiam antes do gateway NAT ser implantado não usam os endereços IP.  Eles parecem estar usando endereços IP não usados com o recurso de gateway NAT.

_**Solução:**_

O [NAT da Rede Virtual](nat-overview.md) substitui a conectividade de saída da sub-rede em que está configurada. Ao fazer a transição do SNAT padrão ou do balanceador de carga de saída do SNAT para o uso de gateways NAT, novas conexões começarão imediatamente a usar os endereços IP associados ao recurso do gateway NAT.  No entanto, se uma máquina virtual ainda tiver uma conexão estabelecida durante a mudança para o recurso de gateway NAT, a conexão continuará usando o endereço IP SNAT antigo que foi atribuído quando a conexão foi estabelecida.  Verifique se você realmente está estabelecendo uma nova conexão e não reutilizando uma conexão que já existia porque o sistema operacional ou o navegador estava armazenando em cache as conexões em um pool de conexões.  Por exemplo, ao usar _curl_ no PowerShell, certifique-se de especificar o parâmetro _-DisableKeepalive_ para forçar uma nova conexão.  Se estiver usando um navegador, as conexões também poderão ser organizadas em pool.

Não é necessário reinicializar uma máquina virtual configurando uma sub-rede para um recurso de gateway NAT.  No entanto, se uma máquina virtual for reinicializada, o estado da conexão será liberado.  Quando o estado da conexão for liberado, todas as conexões começarão a usar os endereços IP do recurso de gateway NAT.  No entanto, esse é um efeito colateral da máquina virtual ser reinicializada, e não um indicador de que uma reinicialização é necessária.

Se ainda tiver problemas, abra um caso de suporte para solução de problemas.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [NAT de Rede Virtual](nat-overview.md)
* Saiba mais sobre o [recurso do Gateway da NAT](nat-gateway-resource.md)
* Saiba mais sobre [métricas e alertas para recursos do Gateway da NAT](nat-metrics.md).
* [Diga-nos o que criar em seguida para a NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).
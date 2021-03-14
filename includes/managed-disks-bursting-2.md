---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3035b5d2803ff91e84bc6b47a99963185f9195d3
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623293"
---
## <a name="disk-level-bursting"></a>Intermitência em nível de disco

### <a name="on-demand-bursting-preview"></a>Intermitência sob demanda (versão prévia)

Os discos que usam o modelo de intermitência sob demanda da intermitência de disco podem ultrapassar os destinos provisionados originais, conforme a necessidade da carga de trabalho, até o destino de intermitência máximo. Por exemplo, em um disco p30 de 1 TiB, o IOPS provisionado é de 5000 IOPS. Quando a intermitência de disco está habilitada neste disco, suas cargas de trabalho podem emitir o IOs para esse disco até o desempenho máximo de intermitência de 30.000 IOPS e 1.000 MBps.

Se você esperar que suas cargas de trabalho sejam executadas com frequência além do destino de desempenho provisionado, a intermitência de disco não será econômica. Nesse caso, recomendamos que você altere o nível de desempenho do disco para uma [camada superior](../articles/virtual-machines/disks-performance-tiers.md) , para melhor desempenho de linha de base. Examine os detalhes de cobrança e avalie-os em relação ao padrão de tráfego de suas cargas de trabalho.

Antes de habilitar a intermitência sob demanda, entenda o seguinte:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Cobrança

Os discos que usam o modelo de intermitência sob demanda são cobrados de uma taxa fixa de habilitação de intermitência por hora e os custos de transação se aplicam a qualquer transação de intermitência além do destino provisionado. Os custos de transação são cobrados usando o modelo pago conforme o uso, com base no IOs de disco não armazenado em cache, incluindo leituras e gravações que excedem os destinos provisionados. Veja a seguir um exemplo de padrões de tráfego de disco em uma hora de cobrança:

Configuração de disco: SSD Premium – 1 TiB (p30), intermitência de disco habilitada.

- 00:00:00 – IOPS de disco de 00:10:00 abaixo do destino provisionado de 5.000 IOPS 
- 00:10:01 – o aplicativo de 00:10:10 emitiu um trabalho em lotes fazendo com que o IOPS de disco seja estourado em 6.000 IOPS por 10 segundos 
- 00:10:11 – IOPS de disco de 00:59:00 abaixo do destino provisionado de 5.000 IOPS 
- 00:59:01 – o aplicativo 01:00:00 emitiu outro trabalho em lotes fazendo com que o IOPS de disco seja estourado em 7.000 IOPS por 60 segundos 

Nessa hora de cobrança, o custo da intermitência consiste em dois encargos:

O primeiro encargo é a taxa fixa de habilitação de intermitência de $X (determinada pela sua região). Essa taxa fixa sempre é cobrada no disco, desconsidere o status de anexo até que ele seja desabilitado. 

O segundo é o custo da transação de intermitência. A intermitência de disco ocorreu em dois intervalos de tempo. De 00:10:01 a 00:10:10, a transação de intermitência acumulada é (6.000 – 5.000) X 10 = 10.000. De 00:59:01 a 01:00:00, a transação de intermitência acumulada é (7.000 – 5.000) X 60 = 120.000. As transações de intermitência total são 10.000 + 120.000 = 130.000. O custo da transação de intermitência será cobrado em $Y com base em 13 unidades de 10.000 transações (com base no preço regional).

Com isso, o custo total na intermitência de disco dessa hora de cobrança é igual a $X + $Y. O mesmo cálculo se aplicaria para intermitência sobre o destino provisionado de MBps. Convertemos o excedente de MB para transações com tamanho de e/s de 256 KB. Se o tráfego do disco exceder o destino de IOPS e MBps provisionados, você poderá consultar o exemplo abaixo para calcular as transações de intermitência. 

Configuração de disco: SSD Premium – 1 TB (p30), intermitência de disco habilitado.

- 00:00:01 – o aplicativo 00:00:05 emitiu um trabalho em lotes, fazendo com que o IOPS de disco seja estourado em 10.000 IOPS e 300 MBps por cinco segundos.
- 00:00:06 – o aplicativo 00:00:10 emitiu um trabalho de recuperação fazendo com que o IOPS de disco seja estourado em 6.000 IOPS e 600 MBps por cinco segundos.

A transação de intermitência é contabilizada como o número máximo de transações de IOPS ou MBps de intermitência. De 00:00:01 a 00:00:05, a transação de intermitência acumulada é máxima ((10.000 – 5.000), (300-200) * 1024/256)) * 5 = 25.000 transações. De 00:00:06 a 00:00:10, a transação de intermitência acumulada é máxima ((6.000 – 5.000), (600-200) * 1024/256)) * 5 = 8.000 transações. Além disso, você inclui a taxa fixa de habilitação de intermitência para obter o custo total para habilitar a intermitência de disco baseada sob demanda. 

Você pode consultar a [página de preços do Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes sobre preços e usar a calculadora de [preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) para fazer a avaliação de sua carga de trabalho. 

### <a name="credit-based-bursting"></a>Intermitência com base em crédito

A intermitência baseada em crédito está disponível para tamanhos de disco P20 e menor em todas as regiões nas nuvens pública, governamental e China do Azure. Por padrão, a intermitência de disco está habilitada em todas as implantações novas e existentes de tamanhos de disco com suporte. A intermitência no nível da VM usa apenas a intermitência baseada em crédito.

## <a name="virtual-machine-level-bursting"></a>Intermitência no nível da máquina virtual

A intermitência no nível da VM usa apenas o modelo baseado em crédito para intermitência, ela é habilitada por padrão para todas as VMs que dão suporte a ela.

A intermitência no nível da VM está habilitada em todas as regiões na nuvem pública do Azure nesses tamanhos com suporte: 
- [Lsv2-series](../articles/virtual-machines/lsv2-series.md)
- [Séries Dv3 e DSv3](../articles/virtual-machines/dv3-dsv3-series.md)
- [Séries Ev3 e Esv3](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Fluxo de intermitência

O sistema de crédito de intermitência aplica-se da mesma maneira no nível de VM e no nível de disco. Seu recurso, seja uma VM ou um disco, começará com créditos totalmente em estoque em seu próprio Bucket de intermitência. Esses créditos permitem que você dispare por até 30 minutos na taxa máxima de intermitência. Você acumula créditos sempre que os IOPS ou MB/s do recurso estão sendo utilizados abaixo do destino de desempenho do recurso. Se o recurso tiver créditos de intermitência acumulados e sua carga de trabalho precisar de desempenho extra, seu recurso poderá usar esses créditos para ir acima de seus limites de desempenho e aumentar seu desempenho para atender às demandas de carga de trabalho.

![Diagrama de Bucket de intermitência.](media/managed-disks-bursting/bucket-diagram.jpg)

A forma como você passa seus créditos disponíveis cabe a você. Você pode usar os 30 minutos de créditos de intermitência consecutivamente ou esporadicamente ao longo do dia. Quando os recursos são implantados, eles são fornecidos com uma alocação completa de créditos. Quando esses depauperam, demora menos de um dia para refazer o estoque. Os créditos podem ser gastos a seu critério, o Bucket de intermitência não precisa estar cheio para que os recursos sejam intermitentes. O acúmulo de intermitência varia dependendo de cada recurso, pois ele se baseia em IOPS não utilizados e MB/s abaixo de seus destinos de desempenho. Os recursos de desempenho de linha de base mais altos podem acumular seus créditos de intermitência mais rápido que a linha de base que executa recursos. Por exemplo, um deixar de disco P1 acumulará 120 IOPS por segundo, enquanto um disco deixar P20 acumulará 2.300 IOPS por segundo.

## <a name="bursting-states"></a>Estados de intermitência
Há três Estados em que o recurso pode estar com a intermitência ativada:
- **Acumulando** – o tráfego de e/s do recurso está usando menos do que o destino de desempenho. Acumular créditos de intermitência para IOPS e MB/s é feito separadamente um do outro. Seu recurso pode estar acumulando créditos de IOPS e créditos de MB/s ou vice-versa.
- **Intermitência** – o tráfego do recurso está usando mais do que o destino de desempenho. O tráfego de intermitência consumirá de forma independente os créditos de IOPS ou largura de banda.
- **Constante** – o tráfego do recurso está exatamente no destino de desempenho.

## <a name="bursting-examples"></a>Exemplos de intermitência

Os exemplos a seguir mostram como a intermitência funciona com várias combinações de VM e disco. Para facilitar o acompanhamento dos exemplos, vamos nos concentrar em MB/s, mas a mesma lógica é aplicada de forma independente ao IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual não expansível com discos com intermitência
**Combinação de VM e disco:** 
- Standard_D8as_v4 
    - MB/s não armazenados em cache: 192
- Disco do sistema operacional P4
    - MB/s provisionados: 25
    - Máximo de MB/s de intermitência: 170 
- 2 discos de dados P10 
    - MB/s provisionados: 100
    - Máximo de MB/s de intermitência: 170

 Quando a VM é inicializada, ela recupera dados do disco do sistema operacional. Como o disco do sistema operacional faz parte de uma VM que está sendo inicializada, o disco do sistema operacional estará cheio de créditos de intermitência. Esses créditos permitirão que o disco do so estoure sua inicialização em 170 MB/s segundo.

![A VM envia uma solicitação de 192 MB/s de taxa de transferência para o disco do sistema operacional, o disco do sistema operacional responde com dados de 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Após a conclusão da inicialização, um aplicativo é executado na VM e tem uma carga de trabalho não crítica. Essa carga de trabalho requer 15 MB/S que se espalham uniformemente em todos os discos.

![O aplicativo envia a solicitação de 15 MB/s de taxa de transferência para a VM, a VM recebe solicitação e envia a cada um de seus discos uma solicitação de 5 MB/s, cada disco retorna 5 MB/s, a VM retorna 15 MB/s para o aplicativo.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Em seguida, o aplicativo precisa processar um trabalho em lote que requer 192 MB/s. 2 MB/s são usados pelo disco do sistema operacional e o restante são divididos uniformemente entre os discos de dados.

![O aplicativo envia a solicitação de 192 MB/s de taxa de transferência para a VM, a VM recebe a solicitação e envia a massa de sua solicitação aos discos de dados (95 MB/s cada) e 2 MB/s para o disco do sistema operacional, os discos de dados são rompidos para atender à demanda e todos os discos retornam a taxa de transferência solicitada para a VM,](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Máquina virtual expansível com discos não intermitentes
**Combinação de VM e disco:** 
- Standard_L8s_v2 
    - MB/s não armazenados em cache: 160
    - Máximo de MB/s de intermitência: 1.280
- Disco do sistema operacional P50
    - MB/s provisionados: 250 
- 2 discos de dados P10 
    - MB/s provisionados: 250

 Após a inicialização inicial, um aplicativo é executado na VM e tem uma carga de trabalho não crítica. Essa carga de trabalho requer 30 MB/s que se espalham uniformemente em todos os discos.
![O aplicativo envia a solicitação de 30 MB/s de taxa de transferência para a VM, a VM recebe solicitação e envia a cada um de seus discos uma solicitação de 10 MB/s, cada disco retorna 10 MB/s, a VM retorna 30 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Em seguida, o aplicativo precisa processar um trabalho em lote que requer 600 MB/s. O Standard_L8s_v2 intermitência para atender a essa demanda e, em seguida, as solicitações para os discos são distribuídas uniformemente para os discos P50.

![O aplicativo envia a solicitação de 600 MB/s de taxa de transferência para a VM, e a VM usa picos para tomar a solicitação e envia a cada um de seus discos uma solicitação de 200 MB/s, cada disco retorna 200 MB/s, as intermitências de VM para retornarem de 600 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual expansível com discos expansível
**Combinação de VM e disco:** 
- Standard_L8s_v2 
    - MB/s não armazenados em cache: 160
    - Máximo de MB/s de intermitência: 1.280
- Disco do sistema operacional P4
    - MB/s provisionados: 25
    - Máximo de MB/s de intermitência: 170 
- 2 discos de dados P4 
    - MB/s provisionados: 25
    - Máximo de MB/s de intermitência: 170 

Quando a VM for iniciada, ela será intermitente para solicitar seu limite de intermitência de 1.280 MB/s do disco do sistema operacional e o disco do sistema operacional responderá com seu desempenho de intermitência de 170 MB/s.

![Na inicialização, a VM é intermitente para enviar uma solicitação de 1.280 MB/s para o disco do sistema operacional, o disco do sistema operacional é estouros para retornar os 1.280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Após a inicialização, você inicia um aplicativo que tem uma carga de trabalho não crítica. Esse aplicativo requer 15 MB/s que é distribuído uniformemente em todos os discos.

![O aplicativo envia a solicitação de 15 MB/s de taxa de transferência para a VM, a VM recebe solicitação e envia a cada um de seus discos uma solicitação de 5 MB/s, cada disco retorna respostas de 5 MB/s, a VM retorna 15 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Em seguida, o aplicativo precisa processar um trabalho em lote que requer 360 MB/s. O Standard_L8s_v2 é intermitente para atender a essa demanda e, em seguida, solicitações. Apenas 20 MB/s são necessários para o disco do sistema operacional. Os 340 MB/s restantes são tratados pelos discos de dados de intermitência P4.

![O aplicativo envia a solicitação de 360 MB/s de taxa de transferência para a VM, a VM usa picos para tomar a solicitação e envia a cada um de seus discos de dados uma solicitação de 170 MB/s e 20 MB/s do disco do sistema operacional, cada disco retorna os MB/s necessários, as intermitências de VM para retornar 360 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)

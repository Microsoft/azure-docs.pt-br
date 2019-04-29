---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 05820cc5f7b7d61d83f73ea5b62b05f8712e0997
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771186"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas

Com uma instância de máquina virtual reservada otimizada para flexibilidade do tamanho da instância, a reserva comprada pode se aplicar aos tamanhos de VMs (máquinas virtuais) no mesmo grupo de série de tamanho. Por exemplo, se você comprar uma reserva para um tamanho de VM que está listado na tabela da série DSv2, como Standard_DS5_v2, o desconto de reserva poderá ser aplicado aos outros quatro tamanhos listados nessa mesma tabela:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mas esse desconto de reserva não se aplica aos tamanhos de VMs que estão listados em tabelas diferentes, como o que está na tabela de memória alta da série DSv2: Standard_DS11_v2, Standard_DS12_v2, e assim por diante.

Dentro do grupo de série de tamanho, o número de VMs às quais o desconto de reserva se aplica depende do tamanho de VM escolhido ao comprar uma reserva. Isso também depende das VMs que você tem em execução. A coluna de proporção que está listada nas tabelas a seguir compara o volume relativo para cada tamanho de VM nesse grupo. Use o valor da proporção para calcular como o desconto de reserva se aplica às VMs você tem em execução.

## <a name="examples"></a>Exemplos

Os exemplos a seguir usam os tamanhos e proporções na tabela da série DSv2.

 Você compra uma instância de VM reservada com o tamanho Standard_DS4_v2 em que a proporção ou o volume relativo em comparação comparada os outros tamanhos dessa série é 8.

- Cenário 1: Executar oito VMs dimensionadas de acordo com Standard_DS1_v2 com uma proporção de 1. O desconto de reserva se aplica a todas essas oito VMs.
- Cenário 2: Executar duas VMs dimensionadas de acordo com Standard_DS2_v2 com uma proporção de 2 cada. Além disso, executar uma VM dimensionada de acordo com Standard_DS3_v2 com uma proporção de 4. O volume total é de 2 + 2 + 4 = 8. Portanto, o desconto de reserva se aplica a todas essas três VMs.
- Cenário 3: Executar uma Standard_DS5_v2 com uma proporção de 16. O desconto de reserva se aplica à metade do custo de computação da VM.

As seções a seguir mostram quais tamanhos estão no mesmo grupo de série de tamanho quando você compra uma instância de VM reservada otimizada para a flexibilidade de tamanho da instância.

## <a name="b-series"></a>Série B

| Tamanho | Proporção|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Para obter mais informações, consulte [Tamanhos das máquinas virtuais da série B expansível](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Memória alta da série B

| Tamanho | Proporção|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Para obter mais informações, consulte [Tamanhos das máquinas virtuais da série B expansível](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Série D

| Tamanho | Proporção|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Memória alta da série D

| Tamanho | Proporção|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Série Ds

| Tamanho | Proporção|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Memória alta da série Ds

| Tamanho | Proporção|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Série DSv2

| Tamanho | Proporção|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>Memória alta da série DSv2

| Tamanho | Proporção|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>Série DSv3

| Tamanho | Proporção|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Para obter mais informações, consulte [Tamanhos das máquinas virtuais para uso geral](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Série Dv2

| Tamanho | Proporção|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Memória alta da série Dv2

| Tamanho | Proporção|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv3-series"></a>Dv3-series

| Tamanho | Proporção|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Para obter mais informações, consulte [Tamanhos das máquinas virtuais para uso geral](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>ESv3-series

| Tamanho | Proporção|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32 16s_v3|16|
|Standard_E64s_v3|28.8|
|Standard_E64-16s_v3|28.8|
|Standard_E64-32s_v3|28.8|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para memória](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Ev3-series

| Tamanho | Proporção|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para memória](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Série F

| Tamanho | Proporção|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>Série FS

| Tamanho | Proporção|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Para obter mais informações, consulte [Gerações anteriores de tamanhos de máquinas virtuais](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Série Fsv2

| Tamanho | Proporção|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Para obter mais informações, veja [Compute optimized virtual machine sizes](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1) (Tamanhos de máquinas virtuais com computação otimizada).

## <a name="h-series"></a>Série H

| Tamanho | Proporção|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Para obter mais informações, consulte [Tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Memória alta da série H

| Tamanho | Proporção|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Para obter mais informações, consulte [Tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Série Ls

| Tamanho | Proporção|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para armazenamento](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>Série M

| Tamanho | Proporção|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Frações da série M

| Tamanho | Proporção|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Memória alta fracionária da série M

| Tamanho | Proporção|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Tamanho fracionário da série M

| Tamanho | Proporção|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Memória alta da série M

| Tamanho | Proporção|
|---|---|
| Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64 32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Série NC

| Tamanho | Proporção|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Série NCv2

| Tamanho | Proporção|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Série NCv3

| Tamanho | Proporção|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Série ND

| Tamanho | Proporção|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Série NV

| Tamanho | Proporção|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

Para obter mais informações, consulte [Tamanhos de máquinas virtuais otimizados para GPU](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).



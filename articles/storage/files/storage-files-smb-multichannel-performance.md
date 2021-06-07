---
title: Desempenho do SMB Multichannel – Arquivos do Azure
description: Saiba mais sobre o desempenho do SMB Multichannel.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374976"
---
# <a name="smb-multichannel-performance"></a>Desempenho do SMB Multichannel

O SMB Multichannel dos Arquivos do Azure (versão prévia) permite que um cliente SMB 3.x estabeleça várias conexões de rede com os compartilhamentos de arquivos premium em uma conta do FileStorage. O protocolo SMB 3.0 introduziu o recurso SMB Multichannel nos clientes do Windows Server 2012 e do Windows 8. Por isso, qualquer cliente SMB 3.x dos Arquivos do Azure que dá suporte a SMB Multichannel pode aproveitar o recurso para os próprios compartilhamentos de arquivos premium do Azure. Não há nenhum custo adicional para habilitar o SMB Multichannel em uma conta de armazenamento.

## <a name="benefits"></a>Benefícios

O SMB Multichannel dos Arquivos do Azure permite que os clientes usem várias conexões de rede que fornecem maior desempenho e, ao mesmo tempo, reduzem o custo de propriedade. O aumento do desempenho é obtido por meio da agregação de largura de banda em vários adaptadores de rede e o uso do suporte RSS (Receive Side Scaling) para que os adaptadores de rede distribuam a carga de E/S entre várias CPUs.

- **Maior taxa de transferência**: várias conexões permitem que os dados sejam transferidos sobre vários caminhos em paralelo e, portanto, beneficiem de modo significativo as cargas de trabalho que usam tamanhos de arquivo maiores com E/S maiores e que exigem alta taxa de transferência em uma VM ou em um conjunto menor de VMs. Algumas dessas cargas de trabalho incluem mídia e entretenimento para criação de conteúdo ou transcodificação, genômica e análise de risco de serviços financeiros.
- **IOPS mais alta**: a funcionalidade RSS do adaptador de rede permite uma distribuição de carga efetiva entre várias CPUs com várias conexões. Isso ajuda na obtenção de uma escala de IOPS mais alta e na utilização eficaz de CPUs da VM. Isso é útil para cargas de trabalho que têm tamanhos de E/S pequenos, como aplicativos de banco de dados.
- **Tolerância a falhas da rede**: várias conexões reduzem o risco de interrupção, pois os clientes não dependem mais de uma conexão individual.
- **Configuração automática**: quando o SMB Multichannel estiver habilitado em clientes e contas de armazenamento, ele permitirá a descoberta dinâmica de conexões existentes e pode criar caminhos de conexão adicionais conforme necessário.
- **Otimização de custos**: as cargas de trabalho podem alcançar uma escala maior com apenas uma VM ou com um pequeno conjunto de VMs, enquanto se conectam a compartilhamentos premium. Isso pode reduzir o custo total de propriedade, reduzindo o número de VMs necessárias para executar e gerenciar uma carga de trabalho.

Para saber mais sobre o SMB Multichannel, confira a [documentação do Windows](/azure-stack/hci/manage/manage-smb-multichannel).

Esse recurso fornece benefícios de desempenho maiores para aplicativos com vários threads, mas normalmente não ajuda aplicativos de thread único. Confira a seção [Comparação de desempenho](#performance-comparison) para obter mais detalhes.

## <a name="limitations"></a>Limitações

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Configuração

O SMB Multichannel funciona apenas quando o recurso está habilitado no lado do cliente (o seu cliente) e no lado do serviço (a sua conta de armazenamento do Azure).

Em clientes do Windows, o SMB Multichannel é habilitado por padrão. É possível verificar a sua configuração executando o seguinte comando do PowerShell: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Na sua conta de armazenamento do Azure, será necessário habilitar o SMB Multichannel. Confira [Habilitar o SMB Multichannel (versão prévia)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Desabilitar o SMB Multichannel
Na maioria dos cenários, especialmente cargas de trabalho com vários threads, os clientes observam um desempenho aprimorado com o SMB Multichannel. No entanto, em alguns cenários específicos, como em cargas de trabalho de thread único ou para fins de teste, talvez seja vantajoso desabilitar o SMB Multichannel. Confira [Comparação de desempenho](#performance-comparison) para obter mais detalhes.

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Verificar se o SMB Multichannel está configurado corretamente

1. Crie um compartilhamento de arquivo premium ou use um existente.
1. Verifique se o cliente dá suporte ao SMB Multichannel (um ou mais adaptadores de rede estão com a escala do lado do receptor habilitada). Confira a [documentação do Windows](/azure-stack/hci/manage/manage-smb-multichannel) para obter mais detalhes.
1. Monte um compartilhamento de arquivo para o seu cliente.
1. Gere a carga com o seu aplicativo.
    Uma ferramenta de cópia, como robocopy /MT, ou qualquer ferramenta de desempenho, como Diskspd para arquivos de leitura/gravação, pode gerar carga.
1. Abra o PowerShell como um administrador e use o seguinte comando: `Get-SmbMultichannelConnection |fl`
1. Procurar as propriedades **MaxChannels** e **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Captura de tela dos resultados de Get-SMBMultichannelConnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Comparação de desempenho

Há duas categorias de padrões de carga de trabalho de leitura/gravação: thread único e vários threads. A maioria das cargas de trabalho usa vários arquivos, mas pode haver casos de uso específicos em que a carga de trabalho funciona com apenas um arquivo em um compartilhamento. Esta seção aborda diferentes casos de uso e qual impacto no desempenho cada um deles tem. Em geral, a maioria das cargas de trabalho tem vários threads e distribui a carga de trabalho em vários arquivos. Portanto, eles devem observar um aumento significativo no desempenho com o SMB Multichannel.

- **Vários threads/vários arquivos**: dependendo do padrão de carga de trabalho, você deverá ver um aumento significativo no desempenho de E/S (leitura e gravação) em vários canais. Os ganhos de desempenho variam entre um aumento de 2x a 4x em termos de IOPS, taxa de transferência e latência. Nessa categoria, o SMB Multichannel deve estar habilitado para obter o melhor desempenho.
- **Vários threads/arquivo único**: na maioria dos casos de uso nessa categoria, as cargas de trabalho serão beneficiadas pela habilitação do SMB Multichannel, especialmente se a carga de trabalho tiver um tamanho médio de E/S maior do que cerca de 16k. Alguns cenários de exemplo que se beneficiam do SMB Multichannel são o backup ou a recuperação de um arquivo grande. Uma exceção em que você talvez queira desabilitar o SMB Multichannel é se a sua carga de trabalho for pequena com E/S pesada. Nesse caso, você poderá observar uma pequena perda de desempenho de aproximadamente 10%. Dependendo do caso de uso, considere a propagação da carga entre vários arquivos ou desabilite o recurso. Confira a seção [Configuração](#configuration) para obter detalhes.
- **Thread único/vários arquivos ou um arquivo**: na maioria das cargas de trabalho de thread único, há benefícios mínimos de desempenho devido à falta de paralelismo, geralmente haverá uma pequena degradação de desempenho de aproximadamente 10% se o SMB Multichannel estiver habilitado. Nesse caso, é ideal desabilitar o SMB Multichannel com uma exceção. Se a carga de trabalho de thread único puder distribuir a carga entre vários arquivos e usar um tamanho de E/S em média maior (maior que aproximadamente 16k), o uso do SMB Multichannel resultará em um ligeiro benefício de desempenho.

### <a name="performance-test-configuration"></a>Configuração de teste de desempenho

Para os gráficos neste artigo, a seguinte configuração foi usada: uma VM Standard D32s v3 com um adaptador de rede habilitado para RSS com quatro canais. A carga foi gerada usando o diskspd.exe, vários threads com uma profundidade de E/S de 10 e usando E/Ss aleatórias de vários tamanhos.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário e de armazenamento em cache: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Captura de tela que mostra a configuração de teste de desempenho." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Vários threads/vários arquivos com SMB Multichannel

A carga foi gerada em dez arquivos com vários tamanhos de E/S. Os resultados de teste de escala vertical mostraram aprimoramentos significativos nos resultados de teste de taxa de transferência e de IOPS com o SMB Multichannel habilitado. Os seguintes diagramas descrevem os resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagrama de desempenho" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama de desempenho da taxa de transferência." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Em um adaptador de rede houve um aumento de desempenho de leitura de 2x a 3x e um aumento de 3x a 4x no desempenho das gravações, tanto na IOPS e como na taxa de transferência.
- O SMB Multichannel permitiu que a IOPS e a taxa de transferência alcançassem os limites de VM mesmo com apenas um adaptador de rede e com um limite de quatro canais.
- Como a saída (ou leituras no armazenamento) não é limitada, a taxa de transferência de leitura conseguiu de exceder o limite publicado da VM de 16.000 Mbps (2 GiB/s). O teste obteve um resultado de mais de 2,7 GiB/s. A entrada (ou gravações no armazenamento) ainda está sujeita aos limites de VM.
- A distribuição de carga em vários arquivos é permitida para a obtenção de aprimoramentos substanciais.

Um comando de exemplo que foi usado neste teste é: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Cargas de trabalho de vários threads/arquivo único com o SMB Multichannel

A carga foi gerada em um arquivo de 128 GiB. Com o SMB Multichannel habilitado, o teste de escala vertical com vários threads/arquivos únicos mostrou aprimoramentos na maioria dos casos. Os seguintes diagramas descrevem os resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagrama de desempenho de IOPS." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama de desempenho da taxa de transferência de arquivo único." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Em um adaptador de rede com o tamanho médio de E/S maior (> ~16k), houve aprimoramentos significativos em leituras e gravações.
- Em tamanhos de E/S menores, houve um pequeno impacto de aproximadamente 10% no desempenho quando o SMB Multichannel foi habilitado. Isso pode ser reduzido por meio da disseminação da carga em vários arquivos ou desabilitando o recurso.
- O desempenho ainda tem [limites de arquivo único](storage-files-scale-targets.md#file-scale-targets).

## <a name="optimizing-performance"></a>Otimizando o desempenho

As seguintes dicas podem ajudar você a otimizar o seu desempenho:

- Verifique se a sua conta de armazenamento e o seu cliente estão localizados na mesma região do Azure para reduzir a latência de rede.
- Use aplicativos com vários threads e distribua a carga em vários arquivos.
- Os benefícios de desempenho do SMB Multichannel aumentam em paralelo com o número de arquivos de distribuição de carga.
- O desempenho de compartilhamento premium é limitado pelo tamanho do compartilhamento provisionado (IOPS/saída/entrada) e por limites de arquivo único. Para obter detalhes, confira [Noções básicas sobre o provisionamento de compartilhamentos de arquivos premium](understanding-billing.md#provisioned-model).
- O desempenho máximo de um cliente com apenas uma VM ainda é atrelado aos limites da VM. Por exemplo, [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) pode dar suporte a uma largura de banda máxima de 16.000 MBps (ou 2 GBps), a saída da VM (gravações no armazenamento) é limitada e a entrada (leituras do armazenamento) não. O desempenho do compartilhamento de arquivo está sujeito a limites de rede do computador, CPUs, largura de banda de rede disponível para armazenamento interno, tamanhos de E/S, paralelismo, além de outros fatores.
- O teste inicial é geralmente um aquecimento, descarte os resultados e repita-o.
- Se o desempenho for limitado por um cliente individual e a carga de trabalho ainda estiver abaixo dos limites de compartilhamento provisionados, será possível obter um desempenho melhor com a distribuição da carga em vários clientes.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>A relação entre IOPS, taxa de transferência e tamanhos de E/S

**Taxa de transferência = Tamanho de E/S * IOPS**

Tamanhos de E/S maiores geram uma taxa de transferência mais alta e terão latências mais altas, resultando em um número menor de IOPS de rede. Tamanhos de E/S menores geram uma IOPS maior, mas resultam em latências e taxa de transferência líquida menores.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar o SMB Multichannel em uma conta do FileStorage (versão prévia)](storage-files-enable-smb-multichannel.md)
- Confira a [documentação do Windows](/azure-stack/hci/manage/manage-smb-multichannel) para saber mais sobre o SMB Multichannel.

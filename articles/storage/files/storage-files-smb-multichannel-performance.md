---
title: Desempenho de Multichannel do SMB-arquivos do Azure
description: Saiba mais sobre o desempenho de multicanal do SMB.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: cbded0a9c905bb488e1bae0f92d777e2e7ed7441
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630846"
---
# <a name="smb-multichannel-performance"></a>Desempenho do SMB Multichannel

O SMB de arquivos do Azure Multichannel (versão prévia) permite que um cliente SMB 3. x estabeleça várias conexões de rede com os compartilhamentos de arquivos Premium em uma conta de armazenamento de arquivo. O protocolo SMB 3,0 introduziu o recurso de Fibre Channel SMB nos clientes do Windows Server 2012 e Windows 8. Por isso, qualquer cliente SMB 3. x de arquivos do Azure que dá suporte a SMB multicanal pode aproveitar o recurso para seus compartilhamentos de arquivos premium do Azure. Não há nenhum custo adicional para habilitar o SMB multicanal em uma conta de armazenamento.

## <a name="benefits"></a>Benefícios

O SMB Multichannel do Azure files permite que os clientes usem várias conexões de rede que fornecem maior desempenho, ao mesmo tempo que reduz o custo de propriedade. O aumento do desempenho é obtido por meio da agregação de largura de banda em várias NICs e o uso do suporte RSS (Receive Side Scaling) para que as NICs distribuam a carga de e/s entre várias CPUs.

- **Maior taxa de transferência**: várias conexões permitem que os dados sejam transferidos por vários caminhos em paralelo e, portanto, beneficiam de forma significativa as cargas de trabalho que usam tamanhos de arquivo maiores com tamanhos de e/s maiores e exigem alta taxa de transferência de uma única VM ou de um conjunto menor de VMs. Algumas dessas cargas de trabalho incluem mídia e entretenimento para criação de conteúdo ou transcodificação, genoma e análise de risco de serviços financeiros.
- **IOPS mais alto**: o recurso RSS de NIC permite uma distribuição de carga efetiva entre várias CPUs com várias conexões. Isso ajuda a alcançar uma escala de IOPS mais alta e utilização eficaz de CPUs de VM. Isso é útil para cargas de trabalho que têm tamanhos de e/s pequenos, como aplicativos de banco de dados.
- **Tolerância a falhas de rede**: várias conexões reduzem o risco de interrupção, pois os clientes não dependem mais de uma conexão individual.
- **Configuração automática**: quando o Fibre Channel do SMB está habilitado em clientes e contas de armazenamento, ele permite a descoberta dinâmica de conexões existentes e pode criar caminhos de conexão adicionais conforme necessário.
- **Otimização de custos**: as cargas de trabalho podem alcançar uma escala maior de uma única VM ou um pequeno conjunto de VMs, ao mesmo tempo em que se conectam a compartilhamentos Premium. Isso pode reduzir o custo total de propriedade, reduzindo o número de VMs necessárias para executar e gerenciar uma carga de trabalho.

Para saber mais sobre o SMB multicanal, consulte a [documentação do Windows](/azure-stack/hci/manage/manage-smb-multichannel).

Esse recurso fornece mais benefícios de desempenho para aplicativos multi-threaded, mas normalmente não ajuda aplicativos de thread único. Consulte a seção [comparação de desempenho](#performance-comparison) para obter mais detalhes.

## <a name="limitations"></a>Limitações

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Configuração

O SMB Multichannel funciona apenas quando o recurso está habilitado no lado do cliente (seu cliente) e no lado do serviço (sua conta de armazenamento do Azure).

Em clientes Windows, o SMB multicanal é habilitado por padrão. Você pode verificar sua configuração executando o seguinte comando do PowerShell: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Em sua conta de armazenamento do Azure, será necessário habilitar o SMB multicanal. Consulte [habilitar o SMB multicanal (versão prévia)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Desabilitar o SMB multicanal
Na maioria dos cenários, especialmente cargas de trabalho multi-threaded, os clientes devem ver um desempenho aprimorado com o SMB multicanal. No entanto, alguns cenários específicos, como cargas de trabalho de thread único ou para fins de teste, talvez você queira desabilitar o SMB multicanal. Consulte [comparação de desempenho](#performance-comparison) para obter mais detalhes.

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Verificar se o Fibre Channel do SMB está configurado corretamente

1. Crie um compartilhamento de arquivo Premium ou use um existente.
1. Verifique se o cliente dá suporte a SMB Multichannel (um ou mais adaptadores de rede têm o dimensionamento do lado de recebimento habilitado). Consulte a [documentação do Windows](/azure-stack/hci/manage/manage-smb-multichannel) para obter mais detalhes.
1. Monte um compartilhamento de arquivos em seu cliente.
1. Gere carga com seu aplicativo.
    Uma ferramenta de cópia, como Robocopy/MT, ou qualquer ferramenta de desempenho, como Diskspd para arquivos de leitura/gravação, pode gerar carga.
1. Abra o PowerShell como administrador e use o seguinte comando: `Get-SmbMultichannelConnection |fl`
1. Procurar propriedades **MaxChannels** e **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Captura de tela de resultados de Get-SMBMultichannelConnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Comparação de desempenho

Há duas categorias de padrões de carga de trabalho de leitura/gravação-thread único e vários threads. A maioria das cargas de trabalho usa vários arquivos, mas pode haver casos de uso específicos em que a carga de trabalho funciona com um único arquivo em um compartilhamento. Esta seção aborda diferentes casos de uso e o impacto no desempenho de cada um deles. Em geral, a maioria das cargas de trabalho tem vários threads e distribui a carga de trabalho em vários arquivos para que eles devam observar melhorias significativas de desempenho com o SMB multicanal.

- Vários **threads/vários arquivos**: dependendo do padrão de carga de trabalho, você deve ver uma melhoria significativa no desempenho em Ios de leitura e gravação em vários canais. Os ganhos de desempenho variam de qualquer lugar entre 2x e 4x em termos de IOPS, taxa de transferência e latência. Para essa categoria, o SMB multicanal deve ser habilitado para o melhor desempenho.
- **Multithread/arquivo único**: para a maioria dos casos de uso nessa categoria, as cargas de trabalho se beneficiarão de ter o SMB multicanal habilitado, especialmente se a carga de trabalho tiver um tamanho médio de e/s > ~ 16K. Alguns cenários de exemplo que se beneficiam do SMB multicanal são o backup ou a recuperação de um único arquivo grande. Uma exceção em que você talvez queira desabilitar o SMB multicanal é se sua carga de trabalho for de pequeno IOs pesada. Nesse caso, você pode observar uma pequena perda de desempenho de aproximadamente 10%. Dependendo do caso de uso, considere a propagação da carga entre vários arquivos ou desabilite o recurso. Consulte a seção de [configuração](#configuration) para obter detalhes.
- Um **único thread/vários arquivos ou um único arquivo**: para a maioria das cargas de trabalho de thread único, há benefícios mínimos de desempenho devido à falta de paralelismo, geralmente haverá uma pequena degradação de desempenho de aproximadamente 10% se o SMB multicanal estiver habilitado. Nesse caso, é ideal desabilitar o SMB multicanal, com uma exceção. Se a carga de trabalho de thread único puder distribuir a carga entre vários arquivos e usar em um tamanho médio maior de e/s (> ~ 16K), deve haver ligeiras benefícios de desempenho do SMB multicanal.

### <a name="performance-test-configuration"></a>Configuração de teste de desempenho

Para os gráficos neste artigo, a seguinte configuração foi usada: uma única VM Standard D32s v3 com uma única NIC habilitada para RSS com quatro canais. O carregamento foi gerado usando diskspd.exe, com vários threads com profundidade de e/s de 10 e IOs aleatório com vários tamanhos de e/s.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Captura de tela que mostra a configuração de teste de desempenho." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Multifator-threaded/vários arquivos com SMB Multichannel

A carga foi gerada em 10 arquivos com vários tamanhos de e/s. Os resultados do teste de escala vertical mostraram melhorias significativas nos resultados do teste de taxa de transferência e IOPS com o SMB multicanal habilitado. Os seguintes diagramas descrevem os resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagrama de desempenho" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama de desempenho da taxa de transferência." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Em uma única NIC, para leituras, o aumento de desempenho de 2x-3x foi observado e para gravações, ganhos de 3x-4x em termos de IOPS e taxa de transferência.
- O SMB Multichannel permitia IOPS e taxa de transferência para alcançar limites de VM mesmo com uma única NIC e o limite de quatro canais.
- Como a saída (ou leituras no armazenamento) não é limitada, a taxa de transferência de leitura foi capaz de exceder o limite publicado da VM de 16.000 Mbps (2 GiB/s). O teste obteve >2,7 GiB/s. A entrada (ou gravações no armazenamento) ainda está sujeita aos limites de VM.
- Distribuição de carga em vários arquivos permitidos para melhorias substanciais.

Um comando de exemplo que foi usado neste teste é: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Cargas de trabalho de arquivo único/multithread com SMB de vários canais

A carga foi gerada em um único arquivo 128 GiB. Com o SMB multicanal habilitado, o teste de escala vertical com arquivos multithread/únicos mostrou aprimoramentos na maioria dos casos. Os seguintes diagramas descrevem os resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagrama de desempenho de IOPS." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama de desempenho de taxa de transferência de arquivo único." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Em uma única NIC com tamanho médio de e/s maior (> ~ 16K), havia melhorias significativas em leituras e gravações.
- Para tamanhos de e/s menores, houve um pequeno impacto de aproximadamente 10% no desempenho quando o SMB multicanal foi habilitado. Isso pode ser mitigado pela disseminação da carga sobre vários arquivos ou pela desabilitação do recurso.
- O desempenho ainda está associado a  [limites de arquivo único](storage-files-scale-targets.md#file-level-limits).

## <a name="optimizing-performance"></a>Otimizando o desempenho

As dicas a seguir podem ajudá-lo a otimizar seu desempenho:

- Verifique se sua conta de armazenamento e seu cliente estão colocalizados na mesma região do Azure para reduzir a latência de rede.
- Use aplicativos multi-threaded e espalhe a carga em vários arquivos.
- Benefícios de desempenho de aumento de vários canais do SMB com o número de arquivos de distribuição de carga.
- O desempenho de compartilhamento Premium é associado pelo tamanho do compartilhamento provisionado (IOPS/egresso/entrada) e limites de arquivo único. Para obter detalhes, consulte [noções básicas sobre provisionamento de compartilhamentos de arquivos Premium](understanding-billing.md#provisioned-model).
- O desempenho máximo de um único cliente de VM ainda está associado aos limites de VM. Por exemplo, [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) pode dar suporte a uma largura de banda máxima de 16.000 Mbps (ou 2 Gbps), a saída da VM (gravações no armazenamento) é medida, a entrada (leituras do armazenamento) não é. O desempenho do compartilhamento de arquivos está sujeito a limites de rede do computador, CPUs, largura de banda de rede disponível para armazenamento interno, tamanhos de e/s, paralelismo, bem como outros fatores.
- O teste inicial é geralmente um aquecimento, descarta seus resultados e repete o teste.
- Se o desempenho for limitado por um único cliente e a carga de trabalho ainda estiver abaixo dos limites de compartilhamento provisionados, um melhor desempenho poderá ser obtido pela distribuição da carga em vários clientes.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>A relação entre IOPS, taxa de transferência e tamanhos de e/s

**Produtividade = IOPS tamanho de e/s ***

Tamanhos de e/s maiores impulsionam uma taxa de transferência mais alta e terão latências mais altas, resultando em um número menor de IOPS de rede. Tamanhos de e/s menores gerarão IOPS maiores, mas resultarão em latência e taxa de transferência líquida menores.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar o SMB multicanal em uma conta de armazenamento de File(versão prévia)](storage-files-enable-smb-multichannel.md)
- Consulte a [documentação do Windows](/azure-stack/hci/manage/manage-smb-multichannel) para saber mais sobre o SMB multicanal.

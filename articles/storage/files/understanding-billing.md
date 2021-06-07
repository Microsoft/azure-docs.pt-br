---
title: Entender a cobrança de arquivos do Azure | Microsoft Docs
description: Saiba como interpretar os modelos de cobrança provisionados e pagos conforme o uso para compartilhamentos de arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bb608492327baae958c32be05d8f2a1bb4dbfbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226634"
---
# <a name="understand-azure-files-billing"></a>Entender a cobrança de arquivos do Azure
Os arquivos do Azure fornecem dois modelos de cobrança distintos: provisionados e pagos conforme o uso. O modelo provisionado só está disponível para compartilhamentos de arquivos premium, que são compartilhamentos de arquivos implantados no tipo de conta de armazenamento **FileStorage** . O modelo pago conforme o uso está disponível somente para compartilhamentos de arquivos padrão, que são compartilhamentos de arquivos implantados no tipo de conta de armazenamento **GPv2 (de uso geral 2)** . Este artigo explica como os dois modelos funcionam para ajudá-lo a entender sua fatura mensal dos arquivos do Azure.

Para obter informações sobre preços de arquivos do Azure, consulte a [página de preços dos arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Unidades de armazenamento    
Os arquivos do Azure usam unidades base 2 de medida para representar a capacidade de armazenamento: KiB, MiB, GiB e TiB. Seu sistema operacional pode ou não usar a mesma unidade de medição ou sistema de contagem.

### <a name="windows"></a>Windows

O sistema operacional Windows e os arquivos do Azure medem a capacidade de armazenamento usando o sistema de contagem de base 2, mas há uma diferença ao rotular unidades. Os arquivos do Azure rotulam sua capacidade de armazenamento com as unidades base 2 de medida, enquanto o Windows rotula sua capacidade de armazenamento em unidades de medida de 10 bases. Ao relatar a capacidade de armazenamento, o Windows não converte sua capacidade de armazenamento de base 2 para base-10.

|Acrônimo  |Definição  |Unidade  |O Windows é exibido como  |
|---------|---------|---------|---------|
|KiB     |1.024 bytes         |kibibyte         |KB (quilobytes)         |
|MiB     |1.024 KiB (1.048.576 bytes)         |mebibyte         |MB (megabyte)         |
|GiB     |1024 MiB (1.073.741.824 bytes)         |Gibibyte         |GB (Gigabyte)         |
|TiB     |1024 GiB (1.099.511.627.776 bytes)         |tebibyte         |TB (Terabyte)         |

### <a name="macos"></a>macOS

Veja [como o Ios e MacOS reportam a capacidade de armazenamento](https://support.apple.com/HT201402) no site da Apple para determinar qual sistema de contagem é usado.

### <a name="linux"></a>Linux

Um sistema de contagem diferente poderia ser usado por cada sistema operacional ou parte de software individual. Consulte sua documentação para determinar como eles relatam a capacidade de armazenamento.

## <a name="provisioned-model"></a>Modelo provisionado
Os arquivos do Azure usam um modelo provisionado para compartilhamentos de arquivos premium. Em um modelo de negócios provisionado, você especifica de forma proativa o serviço de arquivos do Azure quais são seus requisitos de armazenamento, em vez de serem cobrados com base no que você usa. Isso é semelhante à compra de hardware local, quando você provisiona um compartilhamento de arquivos do Azure com uma determinada quantidade de armazenamento, você paga pelo armazenamento, independentemente de usá-lo ou não, assim como não começa a pagar os custos de mídia física local quando você começa a usar o espaço. Ao contrário da compra de mídia física local, os compartilhamentos de arquivos provisionados podem ser dimensionados ou reduzidos dinamicamente, dependendo de suas características de desempenho de e/s de armazenamento.

Ao provisionar um compartilhamento de arquivos premium, você especifica quantas GiBs sua carga de trabalho exige. Cada GiB que você provisiona fornece direito a IOPS e taxa de transferência adicionais em uma taxa fixa. Além do IOPS de linha de base para o qual você está garantido, cada compartilhamento de arquivos Premium dá suporte à intermitência em uma base de melhor esforço. As fórmulas para IOPS e taxa de transferência são as seguintes:

- IOPS de linha de base = 400 + 1 * GiB provisionado. (Até um máximo de 100.000 IOPS).
- Limite de intermitência = MAX (4000, 3 * IOPS de linha de base).
- Taxa de egresso = 60 MiB/s + 0, 6 * provisionamento GiB.
- Taxa de entrada = 40 MiB/s + 0, 4 * GiB provisionados.

O tamanho provisionado do compartilhamento de arquivos pode ser aumentado a qualquer momento, mas só pode ser reduzido após 24 horas desde o último aumento. Depois de aguardar 24 horas sem um aumento de cota, você pode diminuir a cota de compartilhamento quantas vezes desejar, até aumentá-la novamente. As alterações de escala de taxa de transferência/IOPS entrarão em vigor em alguns minutos após a alteração do tamanho provisionado.

É possível diminuir o tamanho do compartilhamento provisionado abaixo do GiB usado. Se você fizer isso, não perderá dados, mas ainda será cobrado pelo tamanho usado e receberá o desempenho (IOPS de linha de base, taxa de transferência e IOPS de intermitência) do compartilhamento provisionado, não o tamanho usado.

A tabela a seguir ilustra alguns exemplos dessas fórmulas para os tamanhos de compartilhamento provisionados:

|Capacidade (GiB) | IOPS de linha de base | IOPS de intermitência | Saída (MiB/s) | Entrada (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Até 4 mil     | 66   | 44   |
|500         | 900     | Até 4 mil  | 90   | 60   |
|1\.024       | 1.424   | Até 4 mil   | 122   | 81   |
|5.120       | 5.520   | Até 15.360  | 368   | 245   |
|10.240      | 10.640  | Até 30.720  | 675   | 450   |
|33.792      | 34.192  | Até 100.000 | 2.088 | 1.392   |
|51.200      | 51.600  | Até 100.000 | 3.132 | 2.088   |
|102.400     | 100.000 | Até 100.000 | 6.204 | 4.136   |

O desempenho efetivo do compartilhamento de arquivos está sujeito aos limites de rede da máquina, largura de banda de rede disponível, tamanhos de e/s, paralelismo, entre muitos outros fatores. Por exemplo, com base no teste interno com 8 tamanhos de e/s de leitura/gravação de KiB, uma única máquina virtual do Windows sem o SMB com Fibre Channel habilitado, *Standard F16s_v2*, conectada ao compartilhamento de arquivos Premium por SMB poderia alcançar IOPS de leitura de 20 mil e IOPS de gravação de 15.000. Com tamanhos de e/s de leitura/gravação de MiB 512, a mesma VM pode atingir a saída de 1,1 GiB/s e a taxa de transferência de entrada de 370 MiB/s. O mesmo cliente pode alcançar um desempenho de até \~ três vezes se o SMB multicanal estiver habilitado nos compartilhamentos Premium. Para obter a escala de desempenho máxima, [habilite o SMB multicanal](storage-files-enable-smb-multichannel.md) e espalhe a carga entre várias VMS. Consulte o guia de desempenho e [solução de problemas](storage-troubleshooting-files-performance.md) do [SMB Multichannel](storage-files-smb-multichannel-performance.md) para alguns problemas comuns de desempenho e soluções alternativas.

### <a name="bursting"></a>Bursting
Se sua carga de trabalho precisar de desempenho extra para atender à demanda de pico, seu compartilhamento poderá usar créditos de intermitência para ir acima do limite de IOPS de linha de base de compartilhamento para oferecer o desempenho de compartilhamento necessário para atender à demanda. Os compartilhamentos de arquivos Premium podem estourar seu IOPS de até 4.000 ou até um fator de três, o que for um valor mais alto. A intermitência é automatizada e opera com base em um sistema de crédito. A intermitência funciona em uma base de melhor esforço e o limite de intermitência não é uma garantia. os compartilhamentos de arquivos podem *aumentar até* o limite por uma duração máxima de 60 minutos.

Os créditos se acumulam em um Bucket de intermitência sempre que o tráfego para o compartilhamento de arquivos está abaixo do IOPS de linha de base. Por exemplo, um compartilhamento de GiB 100 tem 500 IOPS de linha de base. Se o tráfego real no compartilhamento era de 100 IOPS para um intervalo específico de 1 segundo, o IOPS de 400 não utilizado será creditado em um Bucket de intermitência. Da mesma forma, um compartilhamento de 1 TiB ocioso, acumula crédito intermitente em 1.424 IOPS. Esses créditos serão então usados mais tarde, quando as operações excederem o IOPS de linha de base.

Sempre que um compartilhamento exceder o IOPS de linha de base e tiver créditos em um Bucket de intermitência, ele será intermitente na taxa máxima de intermitência de pico permitida. Os compartilhamentos podem continuar a intermitência, desde que os créditos permaneçam, até a duração máxima de 60 minutos, mas isso se baseia no número de créditos de intermitência acumulados. Cada e/s além do IOPS de linha de base consome um crédito e quando todos os créditos são consumidos, o compartilhamento retornaria para o IOPS de linha de base.

Os créditos de compartilhamento têm três Estados:

- A acumulação, quando o compartilhamento de arquivos está usando menos do que o IOPS de linha de base.
- Recusando, quando o compartilhamento de arquivos está usando mais do que o IOPS de linha de base e no modo de intermitência.
- Constante, quando o compartilhamento de arquivos está usando exatamente o IOPS de linha de base, não há créditos acumulados ou usados.

Novos compartilhamentos de arquivos começam com o número total de créditos em seu Bucket de intermitência. Os créditos de intermitência não serão acumulados se o IOPS de compartilhamento cair abaixo do IOPS de linha de base devido à limitação pelo servidor.

## <a name="pay-as-you-go-model"></a>Modelo pago conforme o uso
Os arquivos do Azure usam um modelo de negócios pago conforme o uso para compartilhamentos de arquivos padrão. Em um modelo de negócios pago conforme o uso, o valor pago é determinado pelo quanto você realmente usa, em vez de com base em um valor provisionado. Em um alto nível, você paga um custo para a quantidade de dados armazenados em disco e, em seguida, um conjunto adicional de transações com base no uso desses dados. Um modelo pago conforme o uso pode ser econômico, pois você não precisa provisionar para considerar os requisitos futuros de crescimento ou desempenho ou desprovisionar se a carga de trabalho for de dados varia ao longo do tempo. Por outro lado, um modelo pago conforme o uso também pode ser difícil de planejar como parte de um processo de orçamento, pois o modelo de cobrança pré-pago é orientado pelo consumo do usuário final do.

### <a name="differences-in-standard-tiers"></a>Diferenças nas camadas padrão
Ao criar um compartilhamento de arquivos padrão, você escolhe entre as camadas otimizadas, quentes e frias da transação. Todas as três camadas são armazenadas no mesmo hardware de armazenamento padrão. A principal diferença dessas três camadas são os preços de armazenamento de dados em repouso, que são menores nas camadas mais frias, e os preços de transação, que são mais altos nas camadas mais frias. Isso significa:

- A opção com transação otimizada, como o próprio nome indica, otimiza o preço das cargas de trabalho com grandes volumes de transações. A camada com transação otimizada tem o preço mais alto de armazenamento de dados em repouso, porém, os preços de transação mais baixos.
- A camada quente destina-se a cargas de trabalho ativas que não envolvem um grande número de transações e tem um preço de armazenamento de dados em repouso um pouco menor, porém, preços de transação ligeiramente mais altos comparado à camada com transação otimizada. Imagine-o como o meio-termo intermediário entre as camadas otimizadas e transacionadas pela transação.
- O frio otimiza o preço de cargas de trabalho que não têm muita atividade, oferecendo o preço de armazenamento de dados em repouso mais baixo, mas os preços de transação mais altos.

Se você colocar uma carga de trabalho acessada com pouca frequência na camada otimizada para transação, pagará quase nada por algumas vezes em um mês em que você faz transações em relação ao seu compartilhamento, mas você pagará um alto valor para os custos de armazenamento de dados. Se você mover esse mesmo compartilhamento para a camada fria, ainda não pagará quase nada pelos custos de transação, simplesmente porque você está com muita frequência para fazer transações para essa carga de trabalho, mas a camada fria tem um preço de armazenamento de dados muito mais barato. A seleção da camada apropriada para seu caso de uso permite que você Reduza consideravelmente os custos. A seleção da camada apropriada para seu caso de uso permite que você Reduza consideravelmente os custos.

Da mesma forma, se você colocar uma carga de trabalho altamente acessada na camada fria, você pagará muito mais em custos de transações, mas menos para os custos de armazenamento de dados. Isso pode levar a uma situação em que os custos aumentados do aumento dos preços da transação superam a economia do preço de armazenamento de dados reduzido, levando você a pagar mais dinheiro em vez do que teria em transações otimizadas. É possível para alguns níveis de uso que, enquanto a camada quente for a camada mais econômica, a camada fria será mais cara do que a transação otimizada.

Seu nível de atividade e carga de trabalho determinará a camada mais econômica para o compartilhamento de arquivo padrão. Na prática, a melhor maneira de escolher a camada mais econômica envolve a análise do consumo de recursos real do compartilhamento (dados armazenados, transações de gravação, etc.).

### <a name="what-are-transactions"></a>O que são transações?
As transações são operações ou solicitações em relação aos arquivos do Azure para carregar, baixar ou manipular o conteúdo do compartilhamento de arquivos. Cada ação executada em um compartilhamento de arquivos se traduz em uma ou mais transações e em compartilhamentos padrão que usam o modelo de cobrança pré-pago, que se traduz em custos de transações.

Há cinco categorias de transação básicas: gravação, lista, leitura, outras e excluir. Todas as operações realizadas por meio da API REST ou SMB são segmentadas em uma destas 4 categorias da seguinte maneira:

| Tipo de operação | Transações de gravação | Listar transações | Transações de leitura | Outras transações | Excluir transações |
|-|-|-|-|-|-|
| Operações de gerenciamento | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Operações de dados | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> O NFS 4,1 está disponível somente para compartilhamentos de arquivos premium, que usam o modelo de cobrança provisionado, as transações não afetam a cobrança de compartilhamentos de arquivos premium.

## <a name="see-also"></a>Veja também
- [Página de preços dos arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).
- [Planejamento de uma implantação de arquivos do Azure](./storage-files-planning.md) e [planejamento para uma implantação de sincronização de arquivos do Azure](./storage-sync-files-planning.md).
- [Crie um compartilhamento de arquivos](./storage-how-to-create-file-share.md) e [implante sincronização de arquivos do Azure](./storage-sync-files-deployment-guide.md).
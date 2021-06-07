---
title: Redundância de dados
titleSuffix: Azure Storage
description: Entenda a redundância de dados no armazenamento do Azure. Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d01e125ef3e905191d76c9f4888e790958211dff
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607313"
---
# <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure

A redundância do Armazenamento do Azure sempre armazena várias cópias dos seus dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. A redundância garante que sua conta de armazenamento atenda às suas metas de disponibilidade e durabilidade mesmo diante de falhas.

Ao decidir qual opção de redundância é melhor para seu cenário, considere as compensações entre custos menores e maior disponibilidade. Os fatores que ajudam a determinar qual opção de redundância você deve escolher incluem:  

- Como os dados são replicados na região primária
- Se seus dados são replicados para uma segunda região que está geograficamente distante para a região primária, para proteger contra desastres regionais
- Se o aplicativo requer acesso de leitura aos dados replicados na região secundária se a região primária ficar indisponível por qualquer motivo

## <a name="redundancy-in-the-primary-region"></a>Redundância na região primária

Os dados em uma conta de Armazenamento do Azure são sempre replicados três vezes na região primária. O Armazenamento do Azure oferece duas opções de como os dados são replicados na região primária:

- O **armazenamento com redundância local (LRS)** copia seus dados de forma síncrona três vezes em um único local físico na região primária. LRS é a opção de replicação menos dispendiosa, mas não é recomendada para aplicativos que exigem alta disponibilidade.
- O **Armazenamento com redundância de zona (ZRS)** copia seus dados de forma síncrona em três zonas de disponibilidade do Azure na região primária. Para aplicativos que exigem alta disponibilidade, a Microsoft recomenda usar o ZRS na região primária e também replicar para uma região secundária.

> [!NOTE]
> A Microsoft recomenda usar ZRS na região primária para cargas de trabalho de Azure Data Lake Storage Gen2.

### <a name="locally-redundant-storage"></a>Armazenamento com redundância local

O LRS (armazenamento com redundância local) Replica seus dados três vezes em um único data center na região primária. O armazenamento com redundância local (LRS) fornece pelo menos 99,999999999% (11 noves) de durabilidade dos objetos em um determinado ano.

O LRS é a opção de redundância de menor custo e oferece a menor durabilidade em comparação com outras opções. O LRS protege seus dados contra falhas de unidade e rack do servidor. No entanto, caso ocorra um desastre no data center, como um incêndio ou uma inundação, todas as réplicas de uma conta de armazenamento que use o LRS poderão ser perdidas ou se tornarem irrecuperáveis. Para atenuar esse risco, a Microsoft recomenda usar o [armazenamento com redundância de zona](#zone-redundant-storage) (ZRS), o [armazenamento com redundância geográfica](#geo-redundant-storage) (GRS) ou o [armazenamento com redundância de zona geográfica](#geo-zone-redundant-storage) (GZRS).

Uma solicitação de gravação para uma conta de armazenamento que está usando o LRS ocorre de forma síncrona. A operação de gravação retorna com êxito somente depois que os dados são gravados em todas as três réplicas.

O diagrama a seguir mostra como os dados são replicados em um único data center com LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Diagrama mostrando como os dados são replicados em um único data center com LRS":::

O LRS é uma boa opção para os seguintes cenários:

- Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.
- Se seu aplicativo estiver restrito a replicar dados somente em um país ou região devido a requisitos de governança de dados, você poderá optar pelo LRS. Em alguns casos, as regiões emparelhadas entre as quais os dados são replicados geograficamente podem estar em outro país ou região. Para obter mais informações sobre pares de regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

O armazenamento com redundância de zona (ZRS) replica seus dados do Armazenamento do Azure de forma síncrona em três zonas de disponibilidade do Azure na região primária. Cada zona de disponibilidade é um local físico separado com energia, resfriamento e rede independentes. O ZRS oferece durabilidade para objetos de dados do Armazenamento do Azure de, pelo menos, 99,9999999999% (doze noves) em um dado ano.

Com o ZRS, seus dados ainda podem ser acessados por operações de leitura e de gravação, mesmo em caso de não disponibilidade de uma zona. Se uma zona se tornar indisponível, o Azure realizará atualizações da rede, como a reposição de DNS. Essas atualizações podem afetar seu aplicativo se você estiver acessando os dados antes que as atualizações sejam concluídas. Ao criar aplicativos para ZRS, siga práticas para manipulação de falha transitórias, incluindo a implementação de políticas de novas tentativas com retirada exponencial.

Uma solicitação de gravação para uma conta de armazenamento que está usando o ZRS ocorre de forma síncrona. A operação de gravação retorna com êxito somente depois que os dados são gravados em todas as réplicas nas três zonas de disponibilidade.

A Microsoft recomenda usar o ZRS na região primária para cenários que exigem consistência, durabilidade e alta disponibilidade. O ZRS também é recomendado para restringir a replicação de dados para dentro de um país ou região para atender aos requisitos de governança de dados.

O diagrama a seguir mostra como os dados são replicados entre as zonas de disponibilidade na região primária com ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Diagrama mostrando como os dados são replicados na região primária com ZRS":::

O ZRS fornece desempenho excelente, baixa latência e resiliência para seus dados se eles ficarem temporariamente indisponíveis. Entretanto, por si só, o ZRS não pode proteger seus dados contra um desastre regional em que várias zonas permanentemente são afetadas. Para proteção contra desastres regionais, a Microsoft recomenda o uso do [armazenamento com redundância de zona geográfica](#geo-zone-redundant-storage) (GZRS), que usa o ZRS na região primária e também replica geograficamente seus dados para uma região secundária.

A tabela a seguir mostra quais tipos de contas de armazenamento dão suporte ao ZRS em quais regiões:

| Tipo de conta de armazenamento | Regiões com suporte | Serviços com suporte |
|--|--|--|
| Uso geral v2<sup>1</sup> | Oriental Norte da África do Sul<br /> (Pacífico Asiático) Ásia Oriental<br /> (Pacífico Asiático) Sudeste da Ásia<br /> (Pacífico Asiático) Leste da Austrália<br /> (Pacífico Asiático) Índia central<br /> (Pacífico Asiático) Leste do Japão<br /> (Pacífico Asiático) Centro da Coreia<br /> Canadá Central do Canadá<br /> Européia Europa Setentrional<br /> Européia Europa Ocidental<br /> Européia França central<br /> Européia Centro-oeste da Alemanha<br /> Européia Leste da Noruega<br /> Européia Norte da Suíça<br /> Européia Sul do Reino Unido<br /> (Oriente Médio) Norte dos EAU<br /> (América do Sul) Sul do Brasil<br /> Digamos EUA Central<br /> (EUA) Leste dos EUA<br /> Digamos Leste dos EUA 2<br /> (EUA) Centro-Norte dos EUA<br />Digamos EUA Central do Sul<br /> Digamos Oeste dos EUA<br /> Digamos Oeste dos EUA 2 | Blobs de bloco<br /> Blobs de páginas<sup>2</sup><br /> Compartilhamentos de arquivos (padrão)<br /> Tabelas<br /> Filas<br /> |
| BlockBlobStorage<sup>1</sup> | Oriental Norte da África do Sul<br /> (Pacífico Asiático) Ásia Oriental<br /> (Pacífico Asiático) Sudeste da Ásia<br /> (Pacífico Asiático) Leste da Austrália<br /> (Pacífico Asiático) Índia central<br /> (Pacífico Asiático) Leste do Japão<br /> (Pacífico Asiático) Centro da Coreia<br /> Canadá Central do Canadá<br /> Européia Europa Setentrional<br /> Européia Europa Ocidental<br /> Européia França central<br /> Européia Centro-oeste da Alemanha<br /> Européia Leste da Noruega<br /> Européia Norte da Suíça<br /> Européia Sul do Reino Unido<br /> (Oriente Médio) Norte dos EAU<br /> (América do Sul) Sul do Brasil<br /> Digamos EUA Central<br /> (EUA) Leste dos EUA<br /> Digamos Leste dos EUA 2<br /> (EUA) Centro-Norte dos EUA<br />Digamos EUA Central do Sul<br /> Digamos Oeste dos EUA<br /> Digamos Oeste dos EUA 2 | Somente blobs de blocos Premium |
| FileStorage | Oriental Norte da África do Sul<br /> (Pacífico Asiático) Ásia Oriental<br /> (Pacífico Asiático) Sudeste da Ásia<br /> (Pacífico Asiático) Leste da Austrália<br /> (Pacífico Asiático) Índia central<br /> (Pacífico Asiático) Leste do Japão<br /> (Pacífico Asiático) Centro da Coreia<br /> Canadá Central do Canadá<br /> Européia Europa Setentrional<br /> Européia Europa Ocidental<br /> Européia França central<br /> Européia Centro-oeste da Alemanha<br /> Européia Leste da Noruega<br /> Européia Norte da Suíça<br /> Européia Sul do Reino Unido<br /> (Oriente Médio) Norte dos EAU<br /> (América do Sul) Sul do Brasil<br /> Digamos EUA Central<br /> (EUA) Leste dos EUA<br /> Digamos Leste dos EUA 2<br /> (EUA) Centro-Norte dos EUA<br />Digamos EUA Central do Sul<br /> Digamos Oeste dos EUA<br /> Digamos Oeste dos EUA 2 | Somente compartilhamentos de arquivos Premium |

<sup>1</sup> A camada de arquivamento não tem suporte atualmente para contas de ZRS.<br />
<sup>2</sup> Contas de armazenamento que contêm discos gerenciados do Azure para máquinas virtuais sempre usam LRS. Os discos não gerenciados do Azure também devem usar LRS. É possível criar uma conta de armazenamento para discos não gerenciados do Azure que usam GRS, mas isso não é recomendável devido a possíveis problemas de consistência em relação à replicação geográfica assíncrona. Os discos gerenciados ou não gerenciados dão suporte a ZRS ou GZRS. Para saber mais sobre discos gerenciados, confira [Preços para discos gerenciados do Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

Para obter informações sobre quais regiões dão suporte ao ZRS, confira **Suporte a serviços por região** em [O que são as Zonas de Disponibilidade do Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundância em uma região secundária

Para aplicativos que exigem alta disponibilidade, você pode optar por também copiar os dados em sua conta de armazenamento para uma região secundária que esteja a centenas de quilômetros de distância da região primária. Se sua conta de armazenamento for copiada para uma região secundária, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária emparelhada é determinada com base na região primária e não pode ser alterada. Para saber mais sobre regiões com suporte do Azure, confira [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).

O Armazenamento do Azure oferece duas opções para copiar seus dados para uma região secundária:

- O **armazenamento com redundância geográfica (GRS)** copia seus dados de forma síncrona três vezes em um único local físico na região primária usando o LRS. Em seguida, ele copia os dados de forma assíncrona para um único local físico na região secundária. Na região secundária, seus dados são copiados de forma síncrona três vezes usando LRS.
- O **armazenamento com redundância de zona geográfica (GZRS)** copia seus dados de forma síncrona em três zonas de disponibilidade do Azure na região primária usando o ZRS. Em seguida, ele copia os dados de forma assíncrona para um único local físico na região secundária. Na região secundária, seus dados são copiados de forma síncrona três vezes usando LRS.

> [!NOTE]
> A principal diferença entre o GRS e o GZRS é como os dados são replicados na região primária. Na região secundária, os dados são sempre replicados três vezes de forma síncrona, usando LRS. O LRS na região secundária protege seus dados contra falhas de hardware.

Com GRS ou GZRS, os dados na região secundária não estão disponíveis para acesso de leitura ou gravação, a menos que haja um failover para a região secundária. Para acesso de leitura para a região secundária, configure sua conta de armazenamento para usar o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou o armazenamento com redundância de acesso de leitura (RA-GZRS). Para saber mais, confira [Acesso de leitura aos dados na região secundária](#read-access-to-data-in-the-secondary-region).

Se a região primária ficar indisponível, você poderá optar por fazer failover para a região secundária. Após a conclusão do failover, a região secundária se tornará a região primária e você poderá ler e gravar os dados novamente. Para saber mais sobre a recuperação de desastres e sobre como fazer failover para a região secundária, confira [Recuperação de desastres e failover da conta de armazenamento](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Como os dados são replicados para a região secundária de forma assíncrona, uma falha que afeta a região primária poderá resultar na perda de dados se a região primária não puder ser recuperada. O intervalo entre as gravações mais recentes na região primária e a última gravação na região secundária é conhecido como objetivo de ponto de recuperação (RPO). O RPO indica o ponto no tempo em que os dados podem ser recuperados. Normalmente, o Armazenamento do Microsoft Azure tem um RPO inferior a 15 minutos, embora atualmente não exista nenhum SLA sobre quanto tempo é preciso para replicar os dados para a região secundária.

### <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

O armazenamento com redundância geográfica (GRS) copia seus dados de forma síncrona três vezes em um único local físico na região primária usando o LRS. Em seguida, ele copia seus dados de forma assíncrona para um único local físico em uma região secundária que está a centenas de quilômetros de distância da região primária. O RS oferece durabilidade para objetos de dados do Armazenamento do Azure de, pelo menos, 99,99999999999999% (dezesseis noves) em um dado ano.

Uma operação de gravação primeiro é confirmada para o local primário e replicados usando o LRS. A atualização, em seguida, é replicada assincronamente para a região secundária. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS.

O diagrama a seguir mostra como os dados são replicados com GRS ou RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Diagrama mostrando como os dados são replicados com GRS ou RA-GRS":::

### <a name="geo-zone-redundant-storage"></a>Armazenamento com redundância de zona geográfica

O armazenamento com redundância de zona geográfica (GZRS) combina a alta disponibilidade fornecida pela redundância entre zonas de disponibilidade com a proteção contra interrupções regionais fornecidas pela replicação geográfica. Os dados em uma conta de armazenamento GZRS são copiados entre três [zonas de disponibilidade do Azure](../../availability-zones/az-overview.md) na região primária e também são replicados para uma região geográfica secundária para proteção contra desastres regionais. A Microsoft recomenda o uso do GZRS para aplicativos que exigem consistência, durabilidade e disponibilidade máximas, excelente desempenho e resiliência para recuperação de desastres.

Com uma conta de armazenamento GZRS, você pode continuar lendo e gravando dados se uma zona de disponibilidade ficar indisponível ou não puder ser recuperada. Além disso, seus dados também serão duráveis no caso de uma interrupção regional completa ou um desastre no qual a região primária não possa ser recuperada. O GZRS foi projetado para fornecer pelo menos 99,99999999999999% (dezesseis noves) de durabilidade dos objetos durante um determinado ano.

O diagrama a seguir mostra como os dados são replicados com GZRS ou RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Diagrama mostrando como os dados são replicados com GZRS ou RA-GZRS":::

Somente contas de armazenamento para uso geral v2 dão suporte ao GZRS e ao RA-GZRS. Para obter mais informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento do Azure](storage-account-overview.md). O GZRS e o RA-GZRS dão suporte a blobs de blocos, blobs de páginas (exceto discos VHD), arquivos, tabelas e filas.

Há suporte para o GZRS e o RA-GZRS nas seguintes regiões:

- Oriental Norte da África do Sul
- (Pacífico Asiático) Ásia Oriental
- (Pacífico Asiático) Sudeste da Ásia
- (Pacífico Asiático) Leste da Austrália
- (Pacífico Asiático) Índia central
- (Pacífico Asiático) Leste do Japão
- (Pacífico Asiático) Centro da Coreia
- Canadá Central do Canadá
- Européia Europa Setentrional
- Européia Europa Ocidental
- Européia França central
- Européia Centro-oeste da Alemanha
- Européia Leste da Noruega
- Européia Norte da Suíça
- Européia Sul do Reino Unido
- (Oriente Médio) Norte dos EAU
- (América do Sul) Sul do Brasil
- Digamos EUA Central
- (EUA) Leste dos EUA
- Digamos Leste dos EUA 2
- (EUA) Centro-Norte dos EUA
- Digamos EUA Central do Sul
- Digamos Oeste dos EUA
- Digamos Oeste dos EUA 2

Para obter informações sobre preços, confira detalhes de preços para [Blobs](https://azure.microsoft.com/pricing/details/storage/blobs), [Arquivos](https://azure.microsoft.com/pricing/details/storage/files/), [Filas](https://azure.microsoft.com/pricing/details/storage/queues/)e [Tabelas](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="read-access-to-data-in-the-secondary-region"></a>Acesso de leitura aos dados na região secundária

O armazenamento com redundância geográfica (com GRS ou GZRS) replica seus dados para outro local físico na região secundária para proteger contra interrupções regionais. No entanto, esses dados estarão disponíveis para serem lidos somente se o cliente ou a Microsoft iniciar um failover da região primária para a secundária. Quando você habilita o acesso de leitura para a região secundária, seus dados ficam disponíveis para serem lidos em todos os momentos, incluindo em uma situação em que a região primária fica indisponível. Para obter acesso de leitura para o local secundário, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou o armazenamento com redundância de zona com acesso de leitura (RA-GZRS).

> [!NOTE]
> Os arquivos do Azure não dão suporte ao armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e ao armazenamento com redundância de acesso de leitura (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Projete seus aplicativos para ter acesso de leitura na região secundária

Se sua conta de armazenamento estiver configurada para o acesso de leitura na região secundária, você poderá projetar seus aplicativos para alternar diretamente para a leitura de dados na região secundária se a região primária ficar indisponível por qualquer motivo. 

A região secundária está disponível para acesso de leitura depois que você habilita RA-GRS ou RA-GZRS, para que você possa testar seu aplicativo com antecedência para certificar-se de que ele será lido corretamente do secundário no caso de uma interrupção. Para saber mais sobre como projetar seus aplicativos para alta disponibilidade, confira [Usar redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md).

Quando o acesso de leitura ao secundário está habilitado, seu aplicativo pode ser lido do ponto de extremidade secundário, bem como do ponto de extremidade primário. O ponto de extremidade secundário acrescenta o sufixo *–secondary* ao nome da conta. Por exemplo, se o ponto de extremidade primário para o blob de armazenamento for `myaccount.blob.core.windows.net`, seu ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para o seu armazenamento são as mesmas para os pontos de extremidade primário e secundário.

### <a name="check-the-last-sync-time-property"></a>Verificar a propriedade Horário da Última Sincronização

Como os dados são replicados para a região secundária de forma assíncrona, a região secundária geralmente está atrás da região primária. Se ocorrer uma falha na região primária, é provável que todas as gravações na primária ainda não tenham sido replicadas para a secundária.

Para determinar quais operações de gravação foram replicadas para a região secundária, seu aplicativo pode verificar a propriedade **Horário da Última Sincronização** da sua conta de armazenamento. Todas as operações de gravação gravadas na região primária antes da hora da última sincronização foram replicadas com êxito para a região secundária, o que significa que elas estão disponíveis para serem lidas na secundária. Qualquer operação de gravação gravada na região primária após a hora da última sincronização pode ou não ter sido replicada para a região secundária, o que significa que elas podem não estar disponíveis para operações de leitura.

Você pode consultar o valor da propriedade **Horário da Última Sincronização** usando o Azure PowerShell, a CLI do Azure ou uma das bibliotecas do cliente do Armazenamento do Azure. A propriedade **Horário da Última Sincronização** é um valor de data/hora em GMT. Para saber mais, confira [Verificar a propriedade Horário da Última Sincronização de uma conta de armazenamento](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Resumo das opções de redundância

As tabelas nas seções a seguir resumem as opções de redundância disponíveis para o Armazenamento do Azure

### <a name="durability-and-availability-parameters"></a>Parâmetros de durabilidade e disponibilidade

A tabela a seguir descreve os principais parâmetros para cada opção de redundância:

| Parâmetro | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Porcentagem de durabilidade dos objetos em um determinado ano | no mínimo 99,999999999% (11 9's) | no mínimo 99,9999999999% (12 9's) | no mínimo 99,99999999999999% (16 9's) | no mínimo 99,99999999999999% (16 9's) |
| Disponibilidade para solicitações de leitura | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) para o GRS<br /><br />Pelo menos 99,99% (99,9% para a camada de acesso esporádico) para o RA-GRS | Pelo menos 99,9% (99% para a camada de acesso esporádico) para o GZRS<br /><br />Pelo menos 99,99% (99.9% para a camada de acesso esporádico) para o RA-GZRS |
| Disponibilidade para solicitações de gravação | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) |
| Número de cópias de dados mantidos em nós separados | Três cópias em uma única região | Três cópias em zonas de disponibilidade separadas em uma única região | Total de seis cópias, incluindo três na região primária e três na região secundária | Total de seis cópias, incluindo três em zonas de disponibilidade separadas na região primária e três cópias localmente redundantes na região secundária |

### <a name="durability-and-availability-by-outage-scenario"></a>Cenário de durabilidade e disponibilidade durante interrupções

A tabela a seguir indica se os dados são duráveis e se estão disponíveis em um determinado cenário, dependendo de qual tipo de redundância estiver em vigor para sua conta de armazenamento:

| Cenário de interrupção | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Um nó dentro de um data center se torna indisponível | Sim | Sim | Sim | Sim |
| Um data center inteiro (zonal ou não zonal) fica indisponível | Não | Sim | Sim<sup>1</sup> | Sim |
| Uma interrupção ocorre em toda a região primária | Não | Não | Sim<sup>1</sup> | Sim<sup>1</sup> |
| O acesso de leitura na região secundária estará disponível se a região primária ficar indisponível | Não | Não | Sim (com RA-GRS) | Sim (com RA-GZRS) |

<sup>1</sup> O failover da conta é necessário para restaurar a disponibilidade de gravação se a região primária ficar indisponível. Para saber mais, confira [Recuperação de desastre e failover da conta de armazenamento](storage-disaster-recovery-guidance.md).

### <a name="supported-azure-storage-services"></a>Serviços de armazenamento do Azure com suporte

A tabela a seguir mostra quais opções de redundância têm suporte em cada serviço de armazenamento do Azure.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Armazenamento de blob<br />Armazenamento de filas<br />Armazenamento de tabela<br />Arquivos do Azure<br />Discos gerenciados do Azure | Armazenamento de blob<br />Armazenamento de filas<br />Armazenamento de tabela<br />Arquivos do Azure | Armazenamento de blob<br />Armazenamento de filas<br />Armazenamento de tabela<br />Arquivos do Azure<br /> | Armazenamento de blob<br />Armazenamento de filas<br />Armazenamento de tabela<br />Arquivos do Azure<br /> |

### <a name="supported-storage-account-types"></a>Tipos de conta de armazenamento suportados

A tabela a seguir mostra quais opções de redundância têm suporte em cada tipo de conta de armazenamento. Para obter informações sobre os tipos de conta de armazenamento, confira [Visão geral da conta de armazenamento](storage-account-overview.md).

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Uso geral v2<br /> Uso geral v1<br /> BlockBlobStorage<br /> BlobStorage<br /> FileStorage | Uso geral v2<br /> BlockBlobStorage<br /> FileStorage | Uso geral v2<br /> Uso geral v1<br /> BlobStorage | Uso geral v2 |

Todos os dados de todas as contas de armazenamento são copiados de acordo com a opção de redundância para a conta de armazenamento. Os objetos, incluindo blobs de blocos, blobs de acréscimo, blobs de páginas, filas, tabelas e arquivos são copiados. Os dados em todas as camadas, incluindo a camada de arquivamento, são copiados. Para saber mais sobre as camadas de blobs, confira [Armazenamento de Blobs do Azure: camadas de acesso quente, frio e de arquivamento](../blobs/storage-blob-storage-tiers.md).

Para obter informações sobre cada opção de redundância de preços, confira [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> O Armazenamento em Disco Premium do Azure atualmente dá suporte apenas ao armazenamento com redundância local (LRS). As contas de armazenamento de blobs de blocos dão suporte ao armazenamento com redundância local (LRS) e ao armazenamento com redundância de zona (ZRS) em determinadas regiões.

## <a name="data-integrity"></a>Integridade de dados

O Armazenamento do Azure verifica regularmente a integridade dos dados armazenados usando verificações de redundância cíclica (CRCs). Caso seja detectada uma corrupção dos dados, ela será reparada usando dados redundantes. O Armazenamento do Azure também calcula somas de verificação em todo o tráfego de rede para detectar corrupção de pacotes de dados ao armazenar ou recuperar dados.

## <a name="see-also"></a>Confira também

- [Verificar a propriedade Horário da Última Sincronização de uma conta de armazenamento](last-sync-time-get.md)
- [Alterar a opção de redundância de uma conta de armazenamento](redundancy-migration.md)
- [Usar a redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md)
- [Recuperação de desastres e failover da conta de armazenamento](storage-disaster-recovery-guidance.md)

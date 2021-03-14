---
title: Recuperação de desastres e failover da conta de armazenamento
titleSuffix: Azure Storage
description: O armazenamento do Azure dá suporte ao failover de conta para contas de armazenamento com redundância geográfica. Com o failover de conta, é possível iniciar o processo de failover da conta de armazenamento, se o ponto de extremidade primário ficar indisponível.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f556c7acd903c108193f9c12a2849500645b119b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506694"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>Recuperação de desastres e failover da conta de armazenamento

A Microsoft se empenha em garantir que os serviços do Azure estejam sempre disponíveis. No entanto, podem ocorrer interrupções imprevistas do serviço. Se seu aplicativo exigir resiliência, a Microsoft recomenda usar o armazenamento com redundância geográfica, para que os dados sejam copiados para uma segunda região. Além disso, os clientes devem dispor de um plano de recuperação de desastre para lidar com uma interrupção regional do serviço. Uma parte importante de um plano de recuperação de desastre é a preparação de um failover para o ponto de extremidade secundário caso o ponto de extremidade primário fique indisponível.

O armazenamento do Azure dá suporte ao failover de conta para contas de armazenamento com redundância geográfica. Com o failover de conta, é possível iniciar o processo de failover da conta de armazenamento, se o ponto de extremidade primário ficar indisponível. O failover atualiza o ponto de extremidade secundário para torná-lo um ponto de extremidade primário de sua conta de armazenamento. Quando o failover estiver concluído, os clientes poderão começar a gravar no novo ponto de extremidade primário.

O failover de conta está disponível para os tipos de conta de armazenamento de uso geral v1, de uso geral v2 e de blobs com as implantações do Azure Resource Manager. O failover de conta tem suporte para todas as regiões públicas, mas não está disponível em nuvens soberanas ou nacionais no momento.

Este artigo descreve os conceitos e o processo envolvidos em um failover de conta e mostra como preparar sua conta de armazenamento para recuperação com o mínimo de impacto para o cliente. Para saber como iniciar um failover de conta no portal do Azure ou no PowerShell, consulte [Iniciar um failover de conta](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Escolhendo a opção de redundância correta

O armazenamento do Azure mantém várias cópias da sua conta de armazenamento para garantir durabilidade e alta disponibilidade. A opção de redundância que será escolhida para sua conta dependerá do grau de resiliência que você precisa. Para proteção contra interrupções regionais, configure sua conta para armazenamento com redundância geográfica, com ou sem a opção de acesso de leitura da região secundária:  

O **grs (armazenamento com redundância geográfica) ou o GZRS (armazenamento com redundância de zona geográfica)** copia seus dados de forma assíncrona em duas regiões geográficas que têm pelo menos centenas de quilômetros de distância. Se a região primária sofrer uma interrupção, a região secundária funcionará como uma fonte redundante para seus dados. É possível iniciar um failover para transformar o ponto de extremidade secundário no ponto de extremidade primário.

O **armazenamento com redundância geográfica com acesso de leitura (ra-grs) ou armazenamento com redundância de zona geográfica com acesso de leitura (ra-GZRS)** fornece armazenamento com redundância geográfica com o benefício adicional de acesso de leitura para o ponto de extremidade secundário. Se ocorrer uma interrupção no ponto de extremidade primário, os aplicativos configurados para acesso de leitura para o secundário e projetados para alta disponibilidade poderão continuar a ler do ponto de extremidade secundário. A Microsoft recomenda RA-GZRS para obter a máxima disponibilidade e durabilidade para seus aplicativos.

Para obter mais informações sobre redundância no armazenamento do Azure, consulte [redundância de armazenamento do Azure](storage-redundancy.md).

> [!WARNING]
> O Armazenamento com redundância geográfica envolve risco de perda de dados. Os dados são copiados para a região secundária de forma assíncrona, o que significa que há um atraso entre o momento em que os dados gravados na região primária são gravados na região secundária. No caso de uma interrupção, as operações de gravação para o ponto de extremidade primário que ainda não foram copiadas para o ponto de extremidade secundário serão perdidas.

## <a name="design-for-high-availability"></a>Projetando para a alta disponibilidade

É importante projetar seu aplicativo para ter uma alta disponibilidade desde o início. Consulte estes recursos do Azure para obter orientações sobre como projetar seu aplicativo e sobre o planejamento para a recuperação de desastre:

- [Criando aplicativos resilientes para o Azure](/azure/architecture/framework/resiliency/app-design): uma visão geral dos principais conceitos para a arquitetura de aplicativos altamente disponíveis no Azure.
- [Lista de verificação de resiliência](/azure/architecture/checklist/resiliency-per-service): uma lista de verificação para verificar se o aplicativo implementa as melhores práticas de design para alta disponibilidade.
- [Use a redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md): diretrizes de design para a criação de aplicativos para tirar proveito do armazenamento com redundância geográfica.
- [Tutorial: criar um aplicativo altamente disponível com o armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md): um tutorial que mostra como criar um aplicativo altamente disponível que alterna automaticamente entre pontos de extremidade como falhas e recuperações são simuladas. 

Além disso, tenha em mente essas práticas recomendadas a fim de manter a alta disponibilidade de seus dados de armazenamento no Azure:

- **Discos:** Use o [backup do Azure](https://azure.microsoft.com/services/backup/) para fazer backup dos discos de VM usados por suas máquinas virtuais do Azure. Considere também usar o [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) para proteger suas VMs em caso de desastre regional.
- **Blobs de blocos:** Ative a [exclusão reversível](../blobs/soft-delete-blob-overview.md) para proteger contra exclusões em nível de objeto e substituições ou copie blobs de blocos para outra conta de armazenamento em uma região diferente usando [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)ou a [biblioteca de movimentação de dados do Azure](storage-use-data-movement-library.md).
- **Arquivos:** Use o [backup do Azure](../../backup/azure-file-share-backup-overview.md) para fazer backup de seus compartilhamentos de arquivos. Habilite também a [exclusão reversível](../files/storage-files-prevent-file-share-deletion.md) para proteger contra exclusões de compartilhamento de arquivos acidentais. Para redundância geográfica quando GRS não estiver disponível, use [AzCopy](./storage-use-azcopy-v10.md) ou [Azure PowerShell](/powershell/module/az.storage/) para copiar os arquivos para outra conta de armazenamento em uma região diferente.
- **Tabelas:** use o [AzCopy](./storage-use-azcopy-v10.md) para exportar os dados de tabela para outra conta de armazenamento em uma região diferente.

## <a name="track-outages"></a>Acompanhar interrupções

Os clientes podem assinar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/) para acompanhar a integridade e o status do armazenamento do Azure e de outros serviços do Azure.

A Microsoft também recomenda que você projete seu aplicativo de forma a se preparar para a possibilidade de falhas de gravação. Seu aplicativo deve expor falhas de gravação de forma que o alerte para a possibilidade de uma interrupção na região primária.

## <a name="understand-the-account-failover-process"></a>Entendendo o processo de failover de conta

O failover de conta gerenciada pelo cliente permite que você falhe toda a conta de armazenamento para a região secundária se a primária ficar indisponível por qualquer motivo. Quando você força um failover para a região secundária, os clientes podem começar a gravar os dados no ponto de extremidade secundário depois que o failover estiver concluído. O failover normalmente leva cerca de uma hora.

### <a name="how-an-account-failover-works"></a>Como um failover de conta funciona

Em circunstâncias normais, um cliente grava dados em uma conta de armazenamento do Azure na região primária e esses dados são copiados assincronamente para a região secundária. A imagem a seguir mostra o cenário quando a região primária está disponível:

![Os clientes gravam os dados na conta de armazenamento na região primária](media/storage-disaster-recovery-guidance/primary-available.png)

Se o ponto de extremidade primário ficar indisponível por algum motivo, o cliente não poderá mais gravar os dados na conta de armazenamento. A imagem a seguir mostra o cenário em que o ponto de extremidade primário ficou indisponível, mas nenhuma recuperação aconteceu ainda:

![O ponto de extremidade primário está indisponível, portanto, os clientes não podem gravar dados](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

O cliente inicia o failover da conta no ponto de extremidade secundário. O processo de failover atualiza a entrada DNS fornecida pelo armazenamento do Azure para que o ponto de extremidade secundário se torne o novo ponto de extremidade primário de sua conta de armazenamento, conforme mostrado na imagem a seguir:

![O cliente inicia o failover da conta no ponto de extremidade secundário](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

O acesso de gravação é restaurado para contas com redundância geográfica depois que a entrada DNS é atualizada e as solicitações são direcionadas para o novo ponto de extremidade primário. Os pontos de extremidade de armazenamento existentes para blobs, tabelas, filas e arquivos permanecem os mesmos após o failover.

> [!IMPORTANT]
> Depois que o failover estiver concluído, a conta de armazenamento é configurada para ser localmente redundante no novo ponto de extremidade primário. Para retomar a replicação para o novo secundário, configure a conta para redundância geográfica novamente.
>
> Tenha em mente que a conversão de uma conta LRS para usar a redundância geográfica incorre em um custo. Esse custo se aplica à atualização da conta de armazenamento na nova região primária após um failover.  

### <a name="anticipate-data-loss"></a>Prevendo a perda de dados

> [!CAUTION]
> Um failover de conta geralmente envolve alguma perda de dados. É importante entender as implicações de iniciar um failover de conta.  

Como os dados são gravados de forma assíncrona da região primária para a região secundária, sempre há um atraso antes que uma gravação na região primária seja copiada para a região secundária. Se a região primária ficar indisponível, as gravações mais recentes talvez ainda não tenham sido copiadas para a região secundária.

Ao forçar um failover, todos os dados na região primária são perdidos uma vez que a região secundária se torna a nova região primária e a conta de armazenamento é configurada para ser localmente redundante. Todos os dados já copiados para o secundário são mantidos quando o failover ocorre. No entanto, todos os dados gravados no primário que também não foram copiados para o secundário são perdidos permanentemente.

A propriedade **Hora da última sincronização** indica o momento mais recente em que os dados da região primária foram certamente gravados na região secundária. Todos os dados gravados antes da hora da última sincronização estarão disponíveis na região secundária, enquanto os dados gravados após esse momento podem não terem sido gravados na região secundária e terem sido perdidos. Use essa propriedade caso ocorra uma interrupção para estimar a quantidade de perda de dados que poderá incorrer ao iniciar um failover de conta.

Como prática recomendada, projete seu aplicativo de modo que seja possível usar a hora da última sincronização para avaliar a estimativa de perda de dados. Por exemplo, se você estiver registrando todas as operações de gravação, será possível comparar o momento das últimas operações de gravação com a hora da última sincronização a fim de determinar quais gravações não foram sincronizadas na região secundária.

Para obter mais informações sobre como verificar a propriedade **hora da última sincronização** , consulte [verificar a propriedade hora da última sincronização de uma conta de armazenamento](last-sync-time-get.md).

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Tendo cuidados ao realizar o fail back para a região primária original

Após realizar o failover da região primária para a região secundária, a conta de armazenamento estará configurada para ser localmente redundante na nova região primária. Em seguida, você pode configurar a conta para redundância geográfica novamente. Quando a conta é configurada para redundância geográfica novamente após um failover, a nova região primária começa imediatamente a copiar dados para a nova região secundária, que era a primária antes do failover original. No entanto, pode levar algum tempo antes que os dados existentes no primário sejam totalmente copiados para o novo secundário.

Depois que a conta de armazenamento for reconfigurada para a redundância geográfica, será possível iniciar outro failover da nova região primária de volta para a nova região secundária. Nesse caso, a região primária original antes do failover se tornará novamente a região primária e estará configurada para ser localmente redundante. Todos os dados na região primária após o failover (a região secundária original) serão perdidos. Se a maioria dos dados na conta de armazenamento não tiver sido copiada para o novo secundário antes de realizar o failback, você poderá sofrer uma grande perda de dados.

Para evitar uma grande perda de dados, verifique o valor da propriedade **Hora da última sincronização** antes de realizar o fail back. Compare a hora da última sincronização com os momentos das últimas vezes que os dados foram gravados na nova região primária a fim de avaliar a perda de dados esperada. 

## <a name="initiate-an-account-failover"></a>Iniciar um failover da conta

É possível iniciar um failover de conta do portal do Azure, no PowerShell, na CLI do Azure ou na API de provedor de recursos do Armazenamento do Azure. Para obter mais informações sobre como iniciar um failover, consulte [Iniciar um failover de conta](storage-initiate-account-failover.md).

## <a name="additional-considerations"></a>Considerações adicionais

Examine as considerações adicionais descritas nesta seção para entender como seus aplicativos e serviços podem ser afetados quando você força um failover.

### <a name="storage-account-containing-archived-blobs"></a>Conta de armazenamento que contém BLOBs arquivados

As contas de armazenamento que contêm BLOBs arquivados dão suporte a failover de conta. Após a conclusão do failover, todos os BLOBs arquivados precisam ser alimentados em uma camada online antes que a conta possa ser configurada para redundância geográfica.

### <a name="storage-resource-provider"></a>Provedor de recursos de armazenamento

Depois que um failover for concluído, os clientes poderão ler novamente e gravar dados do armazenamento do Azure na nova região primária. No entanto, o provedor de recursos de armazenamento do Azure não faz failover, portanto, as operações de gerenciamento de recursos ainda devem ocorrer na região primária. Se a região primária não estiver disponível, você não poderá executar operações de gerenciamento na conta de armazenamento.

Como o provedor de recursos de armazenamento do Azure não faz failover, a propriedade [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) retornará o local primário original após a conclusão do failover.

### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

As máquinas virtuais (VMs) do Azure não realizarão o failover como parte de um failover de conta. Se a região primária ficar indisponível, e você fizer o failover para a região secundária, será preciso recriar todas as VMs após o failover. Além disso, há uma possível perda de dados associada ao failover da conta. A Microsoft recomenda as seguintes diretrizes de [alta disponibilidade](../../virtual-machines/availability.md) e [recuperação de desastres](../../virtual-machines/backup-recovery.md) específicas para máquinas virtuais no Azure.

### <a name="azure-unmanaged-disks"></a>Discos não gerenciados do Azure

Como prática recomendada, a Microsoft aconselha converter os discos não gerenciados em discos gerenciados. No entanto, se você precisar fazer o failover em uma conta que contenha discos não gerenciados anexados a VMs do Azure, será necessário desligar a VM antes de iniciar o failover.

Os discos não gerenciados são armazenadas como blobs de páginas no Armazenamento do Azure. Quando uma VM está em execução no Azure, todos os discos não gerenciados anexados à VM são concedidos. O failover de conta não poderá continuar se houver uma concessão em um blob. Para realizar o failover, siga estas etapas:

1. Antes de começar, anote os nomes de todos os discos não gerenciados, seus números de unidade lógica (LUN) e a VM à qual eles estão anexados. Isso facilitará anexar os discos novamente após o failover.
2. Desligue a VM.
3. Exclua a VM, mas mantenha os arquivos VHD para os discos não gerenciados. Anote a hora que você excluiu a VM.
4. Aguarde até que a **Hora da última sincronização** seja atualizada, ela deve posterior à hora em que você excluiu a VM. Esta etapa é importante porque se o ponto de extremidade secundário não foi totalmente atualizado com os arquivos VHD quando o failover ocorrer, a VM poderá não funcionar corretamente na nova região primária.
5. Inicie o failover da conta.
6. Aguarde até que o failover da conta esteja concluído e a região secundária tenha se tornado a nova região primária.
7. Crie uma VM na nova região primária e anexe novamente os VHDs.
8. Inicie a nova VM.

Tenha em mente que todos os dados armazenados em um disco temporário serão perdidos quando a VM for desligada.

## <a name="unsupported-features-and-services"></a>Recursos e serviços sem suporte

Os seguintes recursos e serviços não têm suporte para o failover de conta:

- A Sincronização de Arquivos do Azure não oferece suporte ao failover de conta de armazenamento. Não deve ser realizado o failover das contas de armazenamento que contêm compartilhamentos de arquivos do Azure que estejam sendo usadas como pontos de extremidade de nuvem na Sincronização de Arquivos do Azure. Se isso for feito, a sincronização deixará de funcionar e poderá causar a perda inesperada de dados no caso de arquivos recentes em camadas.
- ADLS Gen2 contas de armazenamento (contas que têm o namespace hierárquico habilitado) não têm suporte no momento.
- Não é possível realizar failover em uma conta de armazenamento que contenha blob de blocos premium. As contas de armazenamento que dão suporte a blobs de bloco premium atualmente não são compatíveis com a redundância geográfica.
- Não é possível fazer failover de uma conta de armazenamento contendo contêineres habilitados da [política de imutabilidade do worm](../blobs/storage-blob-immutable-storage.md) . A retenção baseada em tempo desbloqueada/bloqueada ou as políticas de manutenção legal impedem o failover para manter a conformidade.

## <a name="copying-data-as-an-alternative-to-failover"></a>Copiando dados como uma alternativa ao failover

Se sua conta de armazenamento estiver configurada para acesso de leitura para o secundário, você poderá projetar seu aplicativo para ler o ponto de extremidade secundário. Se você preferir não realizar o failover no caso de uma interrupção na região primária, será possível usar ferramentas como o [AzCopy](./storage-use-azcopy-v10.md), o [Azure PowerShell](/powershell/module/az.storage/) ou a [Biblioteca de movimentação de dados do Azure](../common/storage-use-data-movement-library.md) para copiar os dados de sua conta de armazenamento na região secundária para outra conta de armazenamento em uma região não afetada. Você poderá, então, direcionar os aplicativos para essa conta de armazenamento para a disponibilidade de leitura e de gravação.

> [!CAUTION]
> Um failover de conta não deve ser usado como parte da sua estratégia de migração de dados.

## <a name="microsoft-managed-failover"></a>Failover gerenciado pela Microsoft

Em circunstâncias extremas em que uma região for perdida devido a um desastre significativo, a Microsoft poderá iniciar um failover regional. Nesse caso, nenhuma ação sua é necessária. Você não terá acesso para gravação na conta de armazenamento até que o failover gerenciado pela Microsoft seja concluído. Seus aplicativos poderão ler a partir da região secundária se sua conta de armazenamento estiver configurada para RA-GRS ou RA-GZRS.

## <a name="see-also"></a>Consulte também

- [Uso da redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md)
- [Iniciar um failover da conta](storage-initiate-account-failover.md)
- [Verificar a propriedade Horário da Última Sincronização de uma conta de armazenamento](last-sync-time-get.md)
- [Tutorial: Criar um aplicativo altamente disponível com o armazenamento de Blobs](../blobs/storage-create-geo-redundant-storage.md)
---
title: Solucionar problemas de replicação de VMs do Azure com Azure Site Recovery
description: Solucionar problemas de replicação na recuperação de desastre de VM do Azure com Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007351"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Solucionar problemas de replicação na recuperação de desastre de VM do Azure

Este artigo descreve problemas comuns no Azure Site Recovery quando você está replicando e recuperando máquinas virtuais (VM) do Azure de uma região para outra região. Ele também explica como solucionar os problemas comuns. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](./azure-to-azure-support-matrix.md).

Azure Site Recovery Replica consistentemente os dados da região de origem para a região de recuperação de desastre. Ele também cria um ponto de recuperação consistente com falhas a cada 5 minutos. Se o Site Recovery não puder criar pontos de recuperação durante 60 minutos, ele alertará você com estas informações:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

As seções a seguir descrevem as causas e soluções.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Alta taxa de alteração de dados na máquina virtual de origem

Azure Site Recovery criará um evento se a taxa de alteração de dados na máquina virtual de origem for maior do que os limites com suporte. Para ver se o problema é devido à alta rotatividade, acesse **itens replicados**  >  **VM**  >  **eventos de VM – últimas 72 horas**.
Você deve ver a taxa de alteração de dados do evento **além dos limites com suporte**:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Azure Site Recovery página que mostra uma alta taxa de alteração de dados que é muito alta.":::

Se você selecionar o evento, deverá ver as informações exatas do disco:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Página que mostra os detalhes do evento de taxa de alteração de dados.":::

### <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery

A tabela a seguir fornece os limites do Azure Site Recovery. Esses limites se baseiam em nossos testes, mas não podem abranger todas as combinações de e/s (entrada e saída) de aplicativos possíveis. Os resultados reais podem variar dependendo da combinação de E/S do aplicativo.

Há dois limites a serem considerados: variação de dados por disco e variação de dados por máquina virtual. Vejamos o disco P20 Premium na tabela a seguir para obter um exemplo. Para uma única VM, Site Recovery pode lidar com 5 MB/s de rotatividade por disco com um máximo de cinco discos. Site Recovery tem um limite de 54 MB/s de rotatividade total por VM.

**Destino de armazenamento de replicação** | **Tamanho médio da E/S do disco de origem** |**Rotatividade média dos dados para o disco de origem** | **Rotatividade total dos dados por dia para o disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1\.684 GB por disco

### <a name="solution"></a>Solução

Azure Site Recovery tem limites de taxas de alteração de dados, dependendo do tipo de disco. Para ver se esse problema é recorrente ou temporário, localize a taxa de alteração de dados da máquina virtual afetada. Acesse a máquina virtual de origem, localize as métricas em **Monitoramento** e adicione-as conforme mostrado nesta captura de tela:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Página que mostra o processo de três etapas para localizar a taxa de alteração de dados.":::

1. Selecione **Adicionar métrica** e adicione **Bytes de gravação de disco do sistema operacional/s** e **Bytes de gravação de disco de dados/s**.
1. Monitore o pico, conforme mostrado na captura de tela.
1. Exiba as operações de gravação totais que estão acontecendo nos discos do sistema operacional e em todos os discos de dados combinados. Essas métricas talvez não forneçam informações por disco, mas elas indicam o padrão total da rotatividade de dados.

Um pico na taxa de alteração de dados pode vir de uma intermitência de dados ocasional. Se a taxa de alteração de dados for maior que 10 MB/s (para Premium) ou 2 MB/s (para o padrão) e ficar inativa, a replicação será atualizada. Se a rotatividade estiver consistentemente bem além do limite com suporte, considere uma destas opções:

- Exclua o disco que está causando uma alta taxa de alteração de dados: primeiro, desabilite a replicação. Em seguida, você pode excluir o disco usando o [PowerShell](azure-to-azure-exclude-disks.md).
- Alterar a camada do disco de armazenamento de recuperação de desastre: essa opção só será possível se a variação de dados do disco for menor que 20 MB/s. Por exemplo, uma VM com um disco P10 tem uma variação de dados maior que 8 MB/s, mas menos de 10 MB/s. Se o cliente puder usar um disco P30 para o armazenamento de destino durante a proteção, o problema poderá ser resolvido. Essa solução só é possível para computadores que estejam usando discos Premium-Managed. Siga estas etapas:

  1. Vá para **discos** do computador replicado afetado e copie o nome do disco de réplica.
  1. Vá para esta réplica do disco gerenciado.
  1. Você pode ver uma faixa em **visão geral** que indica que uma URL SAS foi gerada. Selecione essa faixa e cancele a exportação. Ignore esta etapa se você não vir a faixa.
  1. Assim que a URL SAS for revogada, vá para **configuração** do disco gerenciado. Aumente o tamanho para que Site Recovery suporte a taxa de rotatividade observada no disco de origem.

## <a name="network-connectivity-problems"></a>Problemas de conectividade de rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência de rede para uma conta de armazenamento em cache

O Site Recovery envia dados replicados à conta de armazenamento em cache. Você poderá enfrentar a latência de rede se o carregamento dos dados de uma máquina virtual para a conta de armazenamento de cache for mais lento do que 4 MB em 3 segundos.

Para verificar se há um problema relacionado à latência, use [AzCopy](../storage/common/storage-use-azcopy-v10.md). Você pode usar esse utilitário de linha de comando para carregar dados da máquina virtual para a conta de armazenamento de cache. Se a latência for alta, verifique se você está usando uma solução de virtualização de rede (NVA) para controlar o tráfego de rede de saída das VMs. O appliance pode ser acelerado se todo o tráfego de replicação passar pelo NVA.

Recomendamos criar um ponto de extremidade de serviço de rede em sua rede virtual para "Armazenamento", para que o tráfego da replicação não acesse a NVA. Para saber mais, confira [Network virtual appliance configuration](azure-to-azure-about-networking.md#network-virtual-appliance-configuration) (Configuração da solução de virtualização de rede).

### <a name="network-connectivity"></a>Conectividade de rede

Para que Site Recovery replicação funcione, ela precisa da VM para fornecer conectividade de saída para URLs ou intervalos de IP específicos. Você pode ter sua VM por trás de um firewall ou usar regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída. Nesse caso, você pode ter problemas. Para verificar se todas as URLs estão conectadas, confira [conectividade de saída para URLs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>ID do erro 153006-nenhum ponto de recuperação consistente com o aplicativo disponível para a VM nos últimos "X" minutos

A seguir estão alguns dos problemas mais comuns.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Problema conhecido no SQL Server 2008/2008 R2

**Como corrigir:** Há um problema conhecido com o SQL Server 2008/2008 R2. Consulte o artigo [Azure site Recovery Agent ou outro backup não componente do VSS falha para um servidor que hospeda SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery trabalhos falham em servidores que hospedam qualquer versão do SQL Server instâncias com bancos de AUTO_CLOSE

**Como corrigir:** Consulte o artigo [backups VSS sem componentes, como Azure site Recovery trabalhos falham em servidores que hospedam SQL Server instâncias com bancos de AUTO_CLOSE](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problema conhecido em SQL Server 2016 e 2017

**Como corrigir**: consulte o artigo [atualização cumulativa 16 para SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Você está usando a configuração de Espaços de Armazenamento Diretos do Azure

**Como corrigir**: Azure site Recovery não é possível criar um ponto de recuperação consistente com o aplicativo para espaços de armazenamento diretos configuração. [Configure a política de replicação](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Consistência de aplicativo não habilitada em servidores Linux

**Como corrigir: a** Azure site Recovery para o sistema operacional Linux dá suporte a scripts personalizados de aplicativo para consistência de aplicativo. O script personalizado com as opções pre e post será usado pelo agente de mobilidade Azure Site Recovery para consistência de aplicativo. [Aqui](./site-recovery-faq.md#replication) estão as etapas para habilitá-lo.

### <a name="more-causes-because-of-vss-related-issues"></a>Mais causas por causa de problemas relacionados ao VSS:

Para solucionar os problemas, verifique os arquivos no computador de origem para obter o código de erro exato para a falha:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Para localizar os erros, abra o arquivo _vacp. log_ em um editor de texto pesquise a cadeia de caracteres **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

No exemplo anterior, **2147754994** é o código de erro que informa sobre a falha após esta sentença.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O gravador VSS não está instalado — erro 2147221164

**Como corrigir**: para gerar a marca de consistência do aplicativo, o Azure site Recovery usa o serviço de cópias de sombra de volume (VSS). Site Recovery instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Azure Site Recovery instala este provedor VSS como um serviço. Se o provedor VSS não estiver instalado, a criação do instantâneo de consistência do aplicativo falhará. Ele mostra a **ID do erro 0X80040154 classe não registrada**. Consulte o artigo para [solucionar problemas de instalação do gravador VSS](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O gravador VSS está desabilitado — Erro 2147943458

**Como corrigir**: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS. Site Recovery instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Este provedor do VSS é instalado como um serviço. Se você não tiver o serviço do provedor VSS habilitado, a criação do instantâneo de consistência do aplicativo falhará. Ele mostra o erro: **o serviço especificado está desabilitado e não pode ser iniciado (0x80070422)**.

Se o VSS estiver desabilitado:

- Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **Automático**.
- Reinicie os seguintes serviços:
  - Serviço VSS.
  - Azure Site Recovery provedor de VSS.
  - Serviço VDS.

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Error 2147754756

**Como corrigir**: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS. Verifique se o serviço do provedor VSS Azure Site Recovery está instalado.

Use os seguintes comandos para reinstalar o provedor do VSS:

1. Desinstalar o provedor existente:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Reinstale o provedor do VSS:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **Automático**.

Reinicie os seguintes serviços:

- Serviço VSS.
- Azure Site Recovery provedor de VSS.
- Serviço VDS.

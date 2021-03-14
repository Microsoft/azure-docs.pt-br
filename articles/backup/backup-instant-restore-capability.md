---
title: Funcionalidade de restauração instantânea do Azure
description: Funcionalidade de restauração instantânea do Azure e perguntas frequentes para pilha de backup de VM, modelo de implantação do Gerenciador de Recursos
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 3448b162c17dec2ab5b7637a3527d1c470bd415c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618569"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Obter o melhor backup e restaurar o desempenho com a funcionalidade de restauração instantânea do Backup do Azure

> [!NOTE]
> Com base nos comentários dos usuários, renomeamos a **pilha de backup da VM v2** para a **restauração instantânea** para reduzir a confusão com Azure Stack funcionalidade.
> Todos os usuários do backup do Azure agora foram atualizados para **restauração instantânea**.

O novo modelo para Restauração instantânea oferece os seguintes aprimoramentos de recursos:

* Capacidade de usar instantâneos tirados como parte de um trabalho de backup que está disponível para recuperação sem aguardar a conclusão da transferência de dados para o cofre. Isso reduz o tempo de espera para instantâneos para copiar para o cofre antes de disparar a restauração.
* Reduz os tempos de backup e restauração mantendo os instantâneos localmente. O padrão é de dois dias. Esse valor de retenção de instantâneo padrão é configurável para qualquer valor entre 1 e 5 dias.
* Dá suporte a tamanhos de disco de até 32 TB. O redimensionamento de discos não é recomendado pelo backup do Azure.
* Dá suporte a discos SSD Standard juntamente com discos HDD Standard e SSD Premium discos.
* Capacidade de usar uma conta de armazenamento original de VMs não gerenciadas (por disco) ao restaurar. Essa capacidade existe mesmo quando a VM possui discos distribuídos em contas de armazenamento. Isso acelera as operações de restauração para uma ampla variedade de configurações de VM.
* Para o backup de VMs que estão usando discos Premium não gerenciados em contas de armazenamento, com a restauração instantânea, é recomendável alocar *50%* de espaço livre do espaço de armazenamento total alocado, o que é necessário **apenas** para o primeiro backup. O espaço livre de 50% não é um requisito para backups após a conclusão do primeiro backup.

## <a name="whats-new-in-this-feature"></a>Novidades deste recurso

Hoje, o trabalho de backup consiste em duas fases:

1. Obter instantâneo da VM.
2. Transferir o instantâneo da VM para o cofre dos Serviços de Recuperação.

Um ponto de recuperação é considerado criado somente após as fases 1 e 2 estarem concluídas. Como parte dessa atualização, um ponto de recuperação é criado assim que o instantâneo é concluído, e esse ponto de recuperação do tipo de instantâneo pode ser usado para executar uma restauração usando o mesmo fluxo de restauração. Você pode identificar esse ponto de recuperação no portal do Azure usando "instantâneo" como o tipo de ponto de recuperação e, depois que o instantâneo é transferido para o cofre, o tipo de ponto de recuperação muda para "instantâneo e cofre".

![Trabalho de backup na pilha de backup de VM, modelo de implantação do Gerenciador de Recursos - armazenar e cofre](./media/backup-azure-vms/instant-rp-flow.png)

Por padrão, os instantâneos são retidos por dois dias. Esse recurso permite a operação de restauração desses instantâneos, reduzindo os tempos de restauração. Ele reduz o tempo necessário para transformar e copiar dados de volta do cofre.

## <a name="feature-considerations"></a>Considerações do recurso

* Instantâneos são armazenados com os discos para aumentar a criação do ponto de recuperação e acelerar as operações de restauração. Como resultado, você vê os custos de armazenamento correspondentes aos instantâneos obtidos durante esse período.
* Os instantâneos incrementais são armazenados como blobs de página. Todos os usuários de discos não gerenciados são cobrados pelos instantâneos armazenados na conta de armazenamento local. Como as coleções de pontos de restauração usadas por backups de VM gerenciados usam instantâneos de blob no nível de armazenamento subjacente, para discos gerenciados, você verá os custos correspondentes ao preço do instantâneo de BLOB e eles são incrementais.
* Para contas de armazenamento premium, os instantâneos obtidos para pontos de recuperação instantâneos contam para o limite de 10 TB de espaço alocado.
* Você consegue configurar a retenção de instantâneos com base nas necessidades de restauração. Dependendo do requisito, você pode definir a retenção de instantâneo para um mínimo de um dia no painel de política de backup, conforme explicado abaixo. Isso o ajudará a economizar o custo para retenção de instantâneo se você não executar restaurações com frequência.
* É uma única atualização direcional. Depois de atualizado para a restauração instantânea, você não pode voltar.

>[!NOTE]
>Com o upgrade da restauração instantânea, a duração da retenção de instantâneos para todos os clientes (**tanto novos quanto existentes**) será definida com um valor padrão de dois dias. No entanto, você pode definir a duração de acordo com seu requisito para qualquer valor entre 1 e 5 dias.

## <a name="cost-impact"></a>Impacto de custo

Os instantâneos incrementais são armazenados na conta de armazenamento da VM, que é usada para recuperação instantânea. Instantâneo incremental significa que o espaço ocupado por um instantâneo é igual ao espaço ocupado por páginas escritas depois que o instantâneo foi criado. A cobrança ainda é para o espaço usado por GB ocupado pelo instantâneo e o preço por GB é o mesmo mencionado na página de [preços](https://azure.microsoft.com/pricing/details/managed-disks/). Para VMs que usam discos não gerenciados, os instantâneos podem ser vistos no menu do arquivo VHD de cada disco. Para discos gerenciados, os instantâneos são armazenados em um recurso de coleção de pontos de restauração em um grupo de recursos designado e os instantâneos propriamente ditos não são visíveis diretamente.

>[!NOTE]
> A retenção de instantâneos é fixada em 5 dias para políticas semanais.

## <a name="configure-snapshot-retention"></a>Configurar retenção de instantâneo

### <a name="using-azure-portal"></a>Usando o Portal do Azure

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

No portal do Azure, você pode ver um campo adicionado no painel **política de backup de VM** na seção **restauração instantânea** . Você pode alterar a duração da retenção de instantâneo no painel **política de backup de VM** para todas as VMs associadas à política de backup específica.

![Funcionalidade de restauração instantânea](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Usando o PowerShell

>[!NOTE]
> Em AZ PowerShell Version 1.6.0 em diante, você pode atualizar o período de retenção do instantâneo de restauração instantânea na política usando o PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

A retenção de instantâneo padrão para cada política é definida como dois dias. Você pode alterar o valor para um mínimo de 1 e um máximo de cinco dias. Para as políticas semanais, a retenção de instantâneos é fixada em cinco dias.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quais são as implicações de custo da Restauração instantânea?

Instantâneos são armazenados com os discos para acelerar a criação do ponto de recuperação e as operações de restauração. Como resultado, você vê os custos de armazenamento que correspondem à retenção de instantâneo selecionada como parte da política de backup de VM.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Nas contas de Armazenamento Premium, os instantâneos obtidos para o ponto de Recuperação instantânea ocupam o limite de instantâneos de 10 TB?

Sim, para contas de armazenamento Premium, os instantâneos feitos para o ponto de recuperação instantânea ocupam 10 TB de espaço de instantâneo alocado.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Como funciona o trabalho de retenção de instantâneo durante o período de cinco dias?

Cada dia um novo instantâneo é obtido. Em um determinado momento, há cinco instantâneos incrementais individuais. O tamanho do instantâneo depende da variação de dados, que estão na maioria dos casos em cerca de 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Um instantâneo de Restauração instantânea é um instantâneo incremental ou um instantâneo completo?

Os instantâneos tirados como parte da funcionalidade de Restauração instantânea são instantâneos incrementais.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Como é possível calcular o aumento de custo aproximado devido à funcionalidade da Restauração instantânea?

Isso depende da variação da VM. Em um estado estável, é possível assumir que o aumento do custo seja: = Variação diária do período de retenção de instantâneos pelo custo de armazenamento da VM por GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se o tipo de recuperação para um ponto de restauração é "Instantâneo e cofre" e eu posso executar uma operação de restauração, qual tipo de recuperação será usado?

Se o tipo de recuperação é "instantâneo e cofre", a restauração é feita automaticamente a partir do instantâneo local, o que é muito mais rápido comparado à restauração feita do cofre.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>O que acontece se eu selecionar o período de retenção do ponto de restauração (Tier2) menor que o período de retenção instantâneo (Tier1)?

O novo modelo não permite excluir o ponto de restauração (tier2), a menos que o instantâneo (nível 1) seja excluído. Recomendamos agendar o período de retenção do ponto de restauração (Camada 2) de forma a ser mais longo que o período de retenção de instantâneos.

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>Por que meu instantâneo ainda existe, mesmo após o período de retenção definido na política de backup?

Se o ponto de recuperação tiver um instantâneo e ele for o último ponto de recuperação disponível, ele será retido até o próximo backup bem-sucedido. Isso é de acordo com a política de "coleta de lixo" (GC) designada. Isso exige que pelo menos um ponto de recuperação mais recente sempre esteja presente, no caso de falha em todos os backups subsequentes devido a um problema na VM. Em cenários normais, os pontos de recuperação são limpos no máximo 24 horas após a expiração. Em casos raros, pode haver um ou dois instantâneos adicionais com base na carga mais pesada no GC (coletor de lixo).

### <a name="why-do-i-see-more-snapshots-than-my-retention-policy"></a>Por que vejo mais instantâneos do que minha política de retenção?

Em um cenário em que uma política de retenção é definida como "1", você pode encontrar dois instantâneos. Isso exige que pelo menos um ponto de recuperação mais recente sempre esteja presente, no caso de falha em todos os backups subsequentes devido a um problema na VM. Isso pode causar a presença de dois instantâneos.<br></br>Portanto, se a política for para "n" instantâneos, você poderá encontrar instantâneos "n + 1" às vezes. Além disso, você pode até encontrar instantâneos "n + 1 + 2" se houver um atraso na coleta de lixo. Isso pode ocorrer em raras ocasiões em que:
- Você limpa os instantâneos que estão com retenção passada.
- O coletor de lixo (GC) no back-end está sob carga pesada.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Não preciso da funcionalidade de restauração instantânea. Ele pode ser desabilitado?

O recurso de restauração instantânea está habilitado para todos e não pode ser desabilitado. Você pode reduzir a retenção de instantâneo para um mínimo de um dia.

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>É seguro reiniciar a VM durante o processo de transferência (que pode levar muitas horas)? Reiniciar a interrupção da VM ou retardar a transferência?

Sim, é seguro, e não há nenhum impacto na velocidade de transferência de dados.


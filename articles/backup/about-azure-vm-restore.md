---
title: Sobre o processo de restauração da máquina virtual do Azure
description: Saiba como o serviço de backup do Azure restaura as máquinas virtuais do Azure
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 436859bfb3cee37501e0605465d8e19856a846d6
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806837"
---
# <a name="about-azure-vm-restore"></a>Sobre a restauração da VM do Azure

Este artigo descreve como o [serviço de backup do Azure](./backup-overview.md) restaura as VMs (máquinas virtuais) do Azure. Há várias opções de restauração. Discutiremos os vários cenários que eles dão suporte.

## <a name="concepts"></a>Conceitos

- **Ponto de recuperação** (também conhecido como **ponto de restauração**): um ponto de recuperação é uma cópia dos dados originais cujo backup está sendo feito.

- **Camada (instantâneo versus cofre)**: o backup de VM do Azure ocorre em duas fases:

  - Na fase 1, o instantâneo tirado é armazenado junto com o disco. Isso é conhecido como **camada de instantâneo**. Restaurações de camada de instantâneo são mais rápidas (do que restaurar do cofre) porque eliminam o tempo de espera para que os instantâneos sejam copiados para o cofre antes de disparar a restauração. Portanto, a restauração da camada de instantâneo também é conhecida como [restauração instantânea](./backup-instant-restore-capability.md).
  - Na fase 2, o instantâneo é transferido e armazenado no cofre gerenciado pelo serviço de backup do Azure. Isso é conhecido como **camada de cofre**.

- **OLR (recuperação de local original)**: uma recuperação feita do ponto de restauração para a VM do Azure de origem de onde os backups foram feitos, substituindo-o pelo estado armazenado no ponto de recuperação. Isso substitui o disco do sistema operacional e os discos de dados da VM de origem.

- **ALR (recuperação de local alternativo)**: uma recuperação feita do ponto de recuperação para um servidor que não seja o servidor original em que os backups foram feitos.

- **Restauração em nível de item (ILR):** Restaurando arquivos ou pastas individuais dentro da VM a partir do ponto de recuperação

- **Disponibilidade (tipos de replicação)**: o backup do Azure oferece dois tipos de replicação para manter seu armazenamento/dados altamente disponíveis:
  - O [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy.md#locally-redundant-storage) replica seus dados três vezes (ele cria três cópias dos dados) em uma unidade de escala de armazenamento em um datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger seus dados contra falhas de hardware local.
  - O [GRS (armazenamento com redundância geográfica)](../storage/common/storage-redundancy.md#geo-redundant-storage) é a opção de replicação padrão e recomendada. O GRS replica seus dados para uma região secundária (a centenas de quilômetros da região primária dos dados de origem). O GRS é mais caro do que o LRS, mas fornece um nível mais alto de durabilidade para seus dados, mesmo quando há uma interrupção regional.
  - [O ZRS (armazenamento com redundância de zona)](../storage/common/storage-redundancy.md#zone-redundant-storage) replica seus dados em [zonas de disponibilidade](../availability-zones/az-overview.md#availability-zones), garantindo a residência de dados e a resiliência na mesma região. O ZRS não tem tempo de inatividade. Portanto, suas cargas de trabalho críticas que exigem [residências de dados](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) e não devem ter nenhum tempo de inatividade, podem ter o backup feito em ZRS.

- **CRR (restauração entre regiões)**: como uma das opções de [restauração](./backup-azure-arm-restore-vms.md#restore-options), a CRR (restauração entre regiões) permite que você restaure as VMs do Azure em uma região secundária, que é uma [região emparelhada do Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions) , você pode restaurar seus dados na região secundária a qualquer momento, durante interrupções parciais ou totais ou em qualquer outra hora escolhida. 

## <a name="restore-scenarios"></a>Cenários de restauração

![Cenários de restauração ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Cenário**                                                 | **O que é feito**                                             | **Quando usar**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Restaurar para criar uma nova máquina virtual](./backup-azure-arm-restore-vms.md) | Restaura toda a VM para OLR (se a VM de origem ainda existir) ou ALR | <li> Se a VM de origem for perdida ou corrompida, você poderá restaurar toda a VM  <li> Você pode criar uma cópia da VM  <li> Você pode executar uma análise de restauração para auditoria ou conformidade  <li> Essa opção não funcionará para VMs do Azure criadas a partir de imagens do Marketplace (ou seja, se elas não estiverem disponíveis porque a licença expirou). |
| [Restaurar discos da VM](./backup-azure-arm-restore-vms.md#restore-disks) | Restaurar discos anexados à VM                             |  Todos os discos: essa opção cria o modelo e restaura o disco. Você pode editar esse modelo com configurações especiais (por exemplo, conjuntos de disponibilidade) para atender às suas necessidades e, em seguida, usar o modelo e restaurar o disco para recriar a VM. |
| [Restaurar arquivos específicos dentro da VM](./backup-azure-restore-files-from-vm.md) | Escolha o ponto de restauração, procure, selecione arquivos e restaure-os no mesmo sistema operacional (ou compatível) que a VM de backup. |  Se você souber quais arquivos específicos restaurar, use essa opção em vez de restaurar toda a VM. |
| [Restaurar uma VM criptografada](./backup-azure-vms-encryption.md) | No portal, restaure os discos e, em seguida, use o PowerShell para criar a VM | <li> [VM criptografada com Azure Active Directory](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [VM criptografada sem Azure AD](../virtual-machines/windows/disk-encryption-windows.md) <li> [VM criptografada *com o Azure ad* migrado para *sem o Azure ad*](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Restauração Entre Regiões](./backup-azure-arm-restore-vms.md#cross-region-restore) | Criar uma nova VM ou restaurar discos para uma região secundária (região emparelhada do Azure) | <li> **Interrupção completa**: com o recurso de restauração entre regiões, não há tempo de espera para recuperar dados na região secundária. Você pode iniciar restaurações na região secundária mesmo antes de o Azure declarar uma interrupção. <li> **Interrupção parcial**: o tempo de inatividade pode ocorrer em clusters de armazenamento específicos em que o backup do Azure armazena os dados de backup ou mesmo na rede, conectando os clusters de backup e armazenamento do Azure associados aos seus dados de backup. Com a restauração entre regiões, você pode executar uma restauração na região secundária usando uma réplica de dados de backup na região secundária. <li> **Sem interrupção**: você pode realizar análises de BCDR (continuidade dos negócios e recuperação de desastre) para fins de auditoria ou conformidade com os dados da região secundária. Isso permite que você execute uma restauração dos dados de backup na região secundária, mesmo se não houver uma interrupção completa ou parcial na região primária para a continuidade dos negócios e as análises de recuperação de desastre.  |

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre a restauração da VM](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Métodos de restauração compatíveis](./backup-support-matrix-iaas.md#supported-restore-methods)
- [Solucionar problemas de restauração](./backup-azure-vms-troubleshoot.md#restore)
---
title: Fazer backup e restaurar Active Directory
description: Saiba como fazer backup e restaurar Active Directory controladores de domínio.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733546"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Fazer backup e restaurar Active Directory controladores de domínio

Fazer backup de Active Directory e garantir restaurações bem-sucedidas em casos de corrupção, comprometimento ou desastre é uma parte crítica da manutenção Active Directory.

Este artigo descreve os procedimentos adequados para fazer backup e restaurar Active Directory controladores de domínio com o backup do Azure, sejam eles máquinas virtuais do Azure ou servidores locais. Ele aborda um cenário em que você precisa restaurar um controlador de domínio inteiro para seu estado no momento do backup. Para ver qual cenário de restauração é apropriado para você, consulte [Este artigo](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover).  

>[!NOTE]
> Este artigo não aborda a restauração de itens de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Para obter informações sobre como restaurar Azure Active Directory usuários, consulte [Este artigo](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="best-practices"></a>Práticas recomendadas

- Certifique-se de que é feito o backup de pelo menos um controlador de domínio. Se você fizer backup de mais de um controlador de domínio, certifique-se de que todos os que contêm as [funções FSMO (operação de mestre único flexível)](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) sejam submetidos a backup.

- Fazer backup Active Directory frequentemente. O backup nunca deve ser maior do que o tempo de vida da marca de exclusão (por padrão, 60 dias), porque os objetos mais antigos do que o tempo de vida da marca de exclusão serão "marcados para exclusão" e não são mais considerados válidos.

- Tenha um plano de recuperação de desastres claro que inclua instruções sobre como restaurar seus controladores de domínio. Para preparar a restauração de uma floresta Active Directory, leia o [Guia de recuperação de floresta Active Directory](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide).

- Se você precisar restaurar um controlador de domínio e tiver um controlador de domínio em funcionamento restante no domínio, poderá criar um novo servidor em vez de restaurar a partir do backup. Adicione a função de servidor **Active Directory Domain Services** ao novo servidor para torná-lo um controlador de domínio no domínio existente. Em seguida, os dados de Active Directory serão replicados para o novo servidor. Para remover o controlador de domínio anterior do Active Directory, siga as etapas [neste artigo](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) para executar a limpeza de metadados.

>[!NOTE]
>O backup do Azure não inclui a restauração no nível do item para Active Directory. Se você deseja restaurar objetos excluídos e pode acessar um controlador de domínio, use o [Active Directory Lixeira](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Se esse método não estiver disponível, você poderá usar o backup do controlador de domínio para restaurar os objetos excluídos com a ferramenta **ntdsutil.exe** , conforme explicado [aqui](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).
>
>Para obter informações sobre como executar uma restauração autoritativa do SYSVOL, consulte [Este artigo](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol).

## <a name="backing-up-azure-vm-domain-controllers"></a>Fazendo backup de controladores de domínio da VM do Azure

Se o controlador de domínio for uma VM do Azure, você poderá fazer backup do servidor usando o [backup de VM do Azure](backup-azure-vms-introduction.md).

Leia sobre as [Considerações operacionais para controladores de domínio virtualizados](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) para garantir backups bem-sucedidos (e restaurações futuras) de seus controladores de domínio de VM do Azure.

## <a name="backing-up-on-premises-domain-controllers"></a>Fazendo backup de controladores de domínio locais

Para fazer backup de um controlador de domínio local, você precisa fazer backup dos dados de estado do sistema do servidor.

- Se você estiver usando MARS, siga [estas instruções](backup-azure-system-state.md).
- Se você estiver usando MABS (Servidor de Backup do Azure), siga [estas instruções](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> Não há suporte para a restauração de controladores de domínio locais (a partir do estado do sistema ou de VMs) para a nuvem do Azure. Se você quiser a opção de failover de um ambiente de Active Directory local para o Azure, considere o uso de [Azure site Recovery](../site-recovery/site-recovery-active-directory.md).

## <a name="restoring-active-directory"></a>Restaurando Active Directory

Active Directory dados podem ser restaurados em um dos dois modos: **autoritativo** ou não **autoritativo**. Em uma restauração autoritativa, os dados restaurados Active Directory substituirão os dados encontrados nos outros controladores de domínio na floresta.

No entanto, neste cenário, Estamos recriando um controlador de domínio em um domínio existente, portanto, uma restauração não **autoritativa** deve ser executada.

Durante a restauração, o servidor será iniciado no Modo de Restauração dos Serviços de Diretório (DSRM). Você precisará fornecer a senha de administrador para Modo de Restauração dos Serviços de Diretório.

>[!NOTE]
>Se a senha do DSRM for esquecida, você poderá redefini-la usando [estas instruções](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)).

### <a name="restoring-azure-vm-domain-controllers"></a>Restaurando controladores de domínio de VM do Azure

Para restaurar um controlador de domínio de VM do Azure, consulte [restaurar VMs do controlador de domínio](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Se você estiver restaurando uma única VM do controlador de domínio ou várias VMs do controlador de domínio em um único domínio, restaure-as como qualquer outra VM. O DSRM (Modo de Restauração dos Serviços de Diretório) também está disponível, portanto, todos os cenários de recuperação do Active Directory são viáveis.

Se você precisar restaurar uma única VM do controlador de domínio em uma configuração de vários domínios, restaure os discos e crie uma VM [usando o PowerShell](backup-azure-vms-automation.md#restore-the-disks).

Se você estiver restaurando o último controlador de domínio restante no domínio ou restaurando vários domínios em uma floresta, recomendamos uma [recuperação de floresta](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> Controladores de domínio virtualizados, do Windows 2012 em diante, usam [proteções baseadas em virtualização](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards). Com essas proteções, o Active Directory sabe se a VM restaurada é um controlador de domínio e executa as etapas necessárias para restaurar os dados de Active Directory.

### <a name="restoring-on-premises-domain-controllers"></a>Restaurando controladores de domínio locais

Para restaurar um controlador de domínio local, siga as instruções em para restaurar o estado do sistema para o Windows Server, usando as diretrizes para [considerações especiais sobre a recuperação do estado do sistema em um controlador de domínio](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Próximas etapas

- [Matriz de suporte Backup do Azure](backup-support-matrix.md)
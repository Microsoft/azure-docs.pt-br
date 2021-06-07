---
title: Perguntas frequentes – Azure Disk Encryption para VMs do Windows
description: Este artigo fornece respostas a perguntas frequentes sobre o Microsoft Azure Disk Encryption para VMs de IaaS do Windows.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6371a6bd0c4b8151c97a892ec70789429b4bda75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562190"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Perguntas frequentes sobre Azure Disk Encryption para máquinas virtuais do Windows

Este artigo fornece respostas a perguntas frequentes sobre o Azure Disk Encryption para VMs do Windows. Para obter mais informações sobre esse serviço, consulte [Visão geral do Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>O que é o Azure Disk Encryption para VMs do Windows?

O Azure Disk Encryption para VMs do Windows usa o recurso BitLocker do Windows para fornecer criptografia completa do disco do sistema operacional e dos discos de dados. Além disso, ele fornece criptografia do disco temporário quando o [parâmetro VolumeType é All](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  O conteúdo é transmitido criptografado da VM para ao back-end do Armazenamento. Assim, é fornecida uma criptografia de ponta a ponta com uma chave gerenciada pelo cliente.
 
Consulte [Sistemas operacionais e VMs com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde o Azure Disk Encryption está na GA (disponibilidade geral)?

O Azure Disk Encryption está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

A GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. As diferentes experiências de usuário oferecem flexibilidade. Você tem três opções diferentes para habilitar a criptografia de disco para suas VMs. Para saber mais sobre a experiência do usuário e as orientações passo a passo disponíveis no Azure Disk Encryption, consulte [Cenários do Azure Disk Encryption para Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não há nenhum encargo para criptografar discos de VM com o Azure Disk Encryption, mas há encargos associados ao uso do Azure Key Vault. Para obter mais informações sobre os custos do Cofre de Chaves do Azure, consulte a página de preços [Cofre de chaves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar o Azure Disk Encryption?

Para começar, leia as [visão geral do Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quais tamanhos de VM e sistemas operacionais dão suporte ao Azure Disk Encryption?

O artigo [Visão geral do Azure Disk Encryption](disk-encryption-overview.md) lista os [tamanhos de VM](disk-encryption-overview.md#supported-vms) e os [sistemas operacionais de VM](disk-encryption-overview.md#supported-operating-systems) compatíveis com o Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

Você pode criptografar volumes de dados e de boot, mas não pode criptografar os dados sem primeiro criptografar o volume do sistema operacional (SO).

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso criptografar um volume não montado com o Azure Disk Encryption?

Não, o Azure Disk Encryption criptografa apenas volumes montados.

## <a name="what-is-storage-server-side-encryption"></a>O que é criptografia do lado do servidor do Armazenamento?

A criptografia do lado do servidor do Armazenamento criptografa discos gerenciados do Azure no Armazenamento do Microsoft Azure. Discos gerenciados são criptografados por padrão com criptografia do lado do servidor com chave gerenciada de plataforma (a partir de 10 de junho de 2017). Você pode gerenciar a criptografia de discos gerenciados com suas próprias chaves especificando uma chave gerenciada pelo cliente. Para saber mais, confira [Criptografia do lado do servidor dos discos gerenciados do Azure](../disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Qual é a diferença entre o Azure Disk Encryption e a criptografia do lado do servidor do Armazenamento com chave gerenciada pelo cliente e quando eu devo usar cada solução?

O Azure Disk Encryption fornece criptografia de ponta a ponta para o disco do sistema operacional, discos de dados e o disco temporário com uma chave gerenciada pelo cliente.

- Se seus requisitos incluírem todas as criptografias acima e de ponta a ponta, use o Azure Disk Encryption. 
- Se seus requisitos incluírem a criptografia somente de dados em repouso com a chave gerenciada pelo cliente, use a [criptografia do lado do servidor com chaves gerenciadas pelo cliente](../disk-encryption.md). Você não pode criptografar, ao mesmo tempo, um disco com o Azure Disk Encryption e a criptografia do lado do servidor do Armazenamento com chaves gerenciadas do cliente.
- Se você estiver usando um cenário chamado em [cenários do Windows não compatíveis](disk-encryption-windows.md#unsupported-scenarios), considere [Criptografia do lado do servidor com chaves gerenciadas pelo cliente](../disk-encryption.md). 
- Se a política da sua organização permitir criptografar conteúdo em repouso com uma chave gerenciada pelo Azure, nenhuma ação será necessária. O conteúdo será criptografado por padrão. Para discos gerenciados, o conteúdo no armazenamento é criptografado por padrão com a criptografia do lado do servidor com chave gerenciada por plataforma. A chave é gerenciada pelo serviço de Armazenamento do Microsoft Azure. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como fazer revezar segredos ou chaves de criptografia?

Para revezar segredos, basta chamar o mesmo comando usado originalmente para habilitar a criptografia de disco, especificando um Key Vault diferente. Para revezar a chave de criptografia da chave, chame o mesmo comando usado originalmente para habilitar a criptografia de disco, especificando a nova criptografia de chave. 

>[!WARNING]
> - Se usou anteriormente o [Azure Disk Encryption com aplicativo do Microsoft Azure Active Directory](disk-encryption-windows-aad.md) ao especificar credenciais do Azure Active Directory para criptografar essa VM, você deverá continuar usando essa opção para criptografar a VM. Não é possível usar o Azure Disk Encryption nessa VM criptografada pois esse cenário não tem suporte, o que significa que ainda não é possível alternar o aplicativo AAD por essa VM criptografada.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como fazer para adicionar ou remover uma chave de criptografia da chave senão utilizei originalmente uma?

Para adicionar uma chave de criptografia da chave, chame o comando “enable” novamente passando o parâmetro de chave de criptografia da chave. Para remover uma chave de criptografia da chave, chame o comando “enable” sem passar o parâmetro de chave de criptografia da chave.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite o recurso BYOK (Bring Your Own Key)?

Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre cenários de suporte de chaves de criptografia, consulte [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de criptografia de chave criada pelo Azure?

Sim, você pode usar o Azure Key Vault para gerar uma chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre chave de criptografia, consulte [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso usar um serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia?

Você não pode usar o serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais informações sobre cenários de suporte de chave de criptografia, consulte [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption?

Há pré-requisitos para o Azure Disk Encryption. Consulte o artigo [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md) para criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso da criptografia de disco para habilitar a criptografia e proteger segredos e chaves. Para obter mais informações sobre cenários de suporte de chave de criptografia, consulte [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption com um aplicativo do Azure AD (versão anterior)?

Há pré-requisitos para o Azure Disk Encryption. Consulte o conteúdo [Azure Disk Encryption com o Azure Active Directory](disk-encryption-windows-aad.md) para criar um aplicativo do Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para criptografia de disco para ativar a criptografia e proteger segredos e chaves. Para obter mais informações sobre cenários de suporte de chave de criptografia, consulte [Criar e configurar um cofre de chaves para Azure Disk Encryption com o Azure Active Directory](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Ainda há suporte para o Azure Disk Encryption usando um aplicativo do Azure AD (versão anterior)?
Sim. Ainda há suporte para criptografia de disco usando um aplicativo do Azure AD. No entanto, ao criptografar novas VMs, é recomendável que você use o novo método em vez de criptografar com um aplicativo do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram criptografadas com um aplicativo do Azure AD para a criptografia sem um aplicativo do Azure AD?
  Atualmente, não há um caminho de migração direto para computadores que foram criptografados com um aplicativo do Azure AD para criptografia sem um aplicativo do Azure AD. Além disso, também não há um caminho direto da criptografia sem um aplicativo do Azure AD para a criptografia com o aplicativo do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>À qual versão do Azure PowerShell o Azure Disk Encryption dá suporte?

Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). O Azure Disk Encryption *não* é suportado pela versão 1.1.0 do SDK do Azure.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

O “volume Bek” é um volume de dados locais que armazena com segurança chaves de criptografia para VMs Azure criptografadas.

> [!NOTE]
> Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual método de criptografia é usado pela criptografia de disco do Azure?

O Azure Disk Encryption seleciona o método de criptografia no BitLocker com base na versão do Windows, da seguinte maneira:

| Versões do Windows                 | Versão | Método de criptografia        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 ou superior  | >=1511 |XTS-AES de 256 bits           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES de 256 bits*              |
| Windows Server 2008R2            |        |AES de 256 bits com Difusor |

\* O AES de 256 bits com Difusor não é compatível com o Windows 2012 e posterior.

Para determinar a versão do sistema operacional Windows, execute a ferramenta “winver”' em sua máquina virtual.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer backup e restaurar uma VM criptografada? 

O Backup do Azure fornece um mecanismo para fazer backup e restaurar VMs criptografadas na mesma assinatura e região.  Para obter instruções, consulte [Backup e restauração de máquinas virtuais criptografadas usando o Backup do Azure](../../backup/backup-azure-vms-encryption.md).  Atualmente, não há suporte para a restauração de uma VM criptografada em uma região diferente.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso fazer perguntas ou fornecer comentários?

Você pode fazer perguntas ou comentários na [Página de P e R da Microsoft para Azure Disk Encryption](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para obter mais informações sobre esse serviço, veja os seguintes artigos:

- [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Aplicar a criptografia de disco na Central de Segurança do Azure](../../security-center/asset-inventory.md)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)
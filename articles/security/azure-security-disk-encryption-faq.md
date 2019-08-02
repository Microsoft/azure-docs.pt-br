---
title: 'Perguntas frequentes: Azure Disk Encryption para VMs da IaaS | Microsoft Docs'
description: Este artigo fornece respostas a perguntas frequentes sobre o Microsoft Azure Disk Encryption para VMs IaaS Windows e Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: c28cf4326593897dcbc90902737fc4846356078d
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653392"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Perguntas frequentes do Azure Disk Encryption para VMs de IaaS

Este artigo fornece respostas a perguntas frequentes sobre o Azure Disk Encryption para VMs IaaS Windows e Linux. Para saber mais sobre esse serviço, confira [Azure Disk Encryption para VMs IaaS Windows e Linux](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde o Azure Disk Encryption está na GA (disponibilidade geral)?

O Azure Disk Encryption para VMs IaaS Windows e Linux está na disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

A GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. As diferentes experiências de usuário oferecem flexibilidade. Você tem três opções diferentes para habilitar a criptografia de disco para as VMs IaaS. Para obter mais informações sobre a experiência de usuário e a orientação passo a passo disponível no Azure Disk Encryption, consulte [habilitar o Azure Disk Encryption para Windows](azure-security-disk-encryption-windows.md) e [habilitar o Azure Disk Encryption para Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não há nenhum encargo para criptografar discos de VM com o Azure Disk Encryption, mas há encargos associados ao uso do Azure Key Vault. Para obter mais informações sobre os custos do Cofre de Chaves do Azure, consulte a página de preços [Cofre de chaves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar o Azure Disk Encryption?

Para começar, leia as [visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quais sistemas operacionais e tamanhos de VM dá suporte a Azure Disk Encryption?

O [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) listas do artigo a [tamanhos de VM](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes) e [sistemas operacionais VM](azure-security-disk-encryption-prerequisites.md#supported-operating-systems) que dão suporte a Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

Sim, você pode criptografar volumes de inicialização e de dados para VMs IaaS Windows e Linux. Em VMs Windows, não é possível criptografar os dados sem primeiro criptografar o volume do SO. Para VMs Linux, é possível criptografar o volume de dados sem precisar criptografar o volume do sistema operacional primeiro. Depois de criptografar o volume do SO para Linux, não há suporte para desabilitar a criptografia em um volume do SO para VMs do Linux IaaS. Para VMs do Linux em um conjunto de dimensionamento, somente o volume de dados pode ser criptografado.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Para criptografar um volume desmontado com o Azure Disk Encryption?

Não, o Azure Disk Encryption criptografa apenas volumes montados.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como girar segredos ou chaves de criptografia?

Para girar os segredos, basta chame o mesmo comando que você usou originalmente para habilitar a criptografia de disco, especificando um cofre de chave diferente. Para girar a chave de criptografia de chave, chame o mesmo comando usado originalmente para habilitar a criptografia de disco, especificando a nova chave de criptografia. 

>[!WARNING]
> - Se você já tiver usado [Azure Disk Encryption com o aplicativo do Azure AD](azure-security-disk-encryption-prerequisites-aad.md) especificando as credenciais do AD do Azure para criptografar essa VM, você precisará continuar a usar essa opção para criptografar sua VM. Não é possível usar o [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nessa VM criptografada pois esse cenário não tem suporte, o que significa que ainda não há suporte para alternar o aplicativo AAD por essa VM criptografada.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como adicionar ou remover uma chave de criptografia de chave se originalmente não usar um?

Para adicionar uma chave de criptografia de chave, chame o comando enable novamente passando o parâmetro de chave de criptografia de chave. Para remover uma chave de criptografia de chave, chame o comando enable novamente sem o parâmetro de chave de criptografia de chave.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite o recurso BYOK (Bring Your Own Key)?

Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre os principais cenários de suporte a chaves de criptografia, consulte [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de criptografia de chave criada pelo Azure?

Sim, você pode usar o Azure Key Vault para gerar uma chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre a chave de criptografia, consulte [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso usar um serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia?

Você não pode usar o serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais informações sobre os cenários de suporte a chaves de criptografia de chave, consulte [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption?

Há pré-requisitos para o Azure Disk Encryption. Consulte o artigo [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) para criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso da criptografia de disco para habilitar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte a chave de criptografia, consulte [Visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption com um aplicativo do Azure AD (versão anterior)?

Há pré-requisitos para o Azure Disk Encryption. Consulte o [artigo Pré-requisitos de criptografia de disco do Azure](azure-security-disk-encryption-prerequisites-aad.md) para criar um aplicativo do Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para criptografia de disco para ativar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte a chave de criptografia, consulte [Visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Ainda há suporte para o Azure Disk Encryption usando um aplicativo do Azure AD (versão anterior)?
Sim. Ainda há suporte para criptografia de disco usando um aplicativo do Azure AD. No entanto, ao criptografar novas VMs, é recomendável que você use o novo método em vez de criptografar com um aplicativo do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram criptografadas com um aplicativo do Azure AD para a criptografia sem um aplicativo do Azure AD?
  Atualmente, não há um caminho de migração direto para computadores que foram criptografados com um aplicativo do Azure AD para criptografia sem um aplicativo do Azure AD. Além disso, também não há um caminho direto da criptografia sem um aplicativo do Azure AD para a criptografia com o aplicativo do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>À qual versão do Azure PowerShell o Azure Disk Encryption dá suporte?

Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). O Azure Disk Encryption *não* é suportado pela versão 1.1.0 do SDK do Azure.

> [!NOTE]
> A extensão de visualização de criptografia de disco Linux do Azure "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" foi preterida. Essa extensão foi publicada para versão de visualização do Azure disk encryption. Você não deve usar a versão prévia da extensão em sua implantação de teste ou produção.

> Para cenários de implantação, como do Azure Resource Manager (ARM), em que você tiver uma necessidade de implantar a extensão de criptografia de disco do Azure para VM do Linux habilitar a criptografia na VM de IaaS do Linux, você deve usar a extensão de produção com suporte de criptografia de disco do Azure" Microsoft.Azure.Security.AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Posso aplicar o Azure Disk Encryption à minha imagem personalizada do Linux?

Você não pode aplicar a criptografia de disco do Azure à sua imagem personalizada do Linux. Somente as imagens da galeria Linux para as distribuições suportadas chamadas anteriormente são suportadas. Imagens personalizadas do Linux não são suportadas atualmente.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Posso aplicar atualizações a uma VM Red Hat Linux que usa a atualização do yum?

Sim, você pode executar uma atualização do yum em uma VM do Red Hat Linux.  Para obter mais informações, consulte [gerenciamento de pacotes Linux por trás de um firewall](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>O que é o fluxo de trabalho recomendado de criptografia de disco do Azure recomendado para Linux?

O seguinte fluxo de trabalho é recomendado para ter os melhores resultados no Linux:
* Inicie na imagem da galeria de estoque não modificada que corresponde à distribuição e à versão do sistema operacional necessárias
* Faça backup de todas as unidades montadas que serão criptografadas.  Esse backup permitirá a recuperação se houver uma falha, por exemplo, se a VM for reinicializada antes da conclusão da criptografia.
* Criptografar (pode levar várias horas ou mesmo dias dependendo das características da VM e do tamanho dos discos de dados anexados)
* Personalizar e adicionar o software à imagem conforme necessário.

Se esse fluxo de trabalho não for possível, usar a SSE [(Criptografia do Serviço de Armazenamento)](../storage/common/storage-service-encryption.md) na camada da conta de armazenamento de plataforma poderá ser uma alternativa para a criptografia de disco completo usando dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

"Volume de Bek" para Windows ou "/mnt/azure_bek_disk" para Linux é um volume de dados local que armazena com segurança as chaves de criptografia para VMs de IaaS do Azure criptografadas.
> [!NOTE]
> Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual método de criptografia é usado pela criptografia de disco do Azure?

No Windows, o ADE usa o método de criptografia AES256 do BitLocker (AES256WithDiffuser em versões anteriores ao Windows Server 2012). No Linux, ADE usa o padrão de descriptografia de plain64-xts-aes com uma chave mestra de volume de 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu usar EncryptFormatAll e especificar todos os tipos de volume, ele apagará os dados em unidades de dados que já tivermos criptografado?
Não, os dados não serão apagados de unidades de dados que já tiverem sido criptografadas usando o Azure Disk Encryption. Semelhante ao modo como o EncryptFormatAll não criptografou novamente a unidade do sistema operacional, ele não criptografará novamente a unidade de dados já criptografada. Para obter mais informações, veja os [critérios do EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Há suporte para sistema de arquivos XFS
Volumes XFS têm suporte para criptografia de disco de dados apenas com o EncryptFormatAll. Reformata o volume, apagando todos os dados anteriormente lá. Para obter mais informações, veja os [critérios do EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>É possível fazer backup e restaurar uma VM criptografada? 

O Backup do Azure fornece um mecanismo para fazer backup e restaurar a VM criptografada dentro da mesma assinatura e região.  Para obter instruções, consulte [fazer backup e restaurar máquinas virtuais criptografadas com o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Atualmente, não há suporte para a restauração de uma VM criptografada para uma região diferente.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso fazer perguntas ou fornecer comentários?

Você pode fazer perguntas ou fornecer comentários no [Fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para obter mais informações sobre esse serviço, veja os seguintes artigos:

- [Visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md)
- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografia de dados em repouso do Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

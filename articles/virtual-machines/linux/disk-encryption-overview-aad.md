---
title: Azure Disk Encryption com os pré-requisitos de aplicativo do Azure AD (versão anterior)
description: Este artigo fornece suplementos para Azure Disk Encryption para VMs Linux com requisitos adicionais e pré-requisitos para Azure Disk Encryption com o Azure AD.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b258d499c78aa5fb734cbee01fb753c292bf2678
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970890"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Disk Encryption com Azure Active Directory (AD) (versão anterior)

A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo Azure Active Directory (AD do Azure) para habilitar a criptografia de disco de VM. Com a nova versão, não é mais necessário fornecer credenciais do Azure AD durante a etapa para habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Azure AD usando a nova versão. Para obter instruções sobre como habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption para VMs do Linux](disk-encryption-overview.md). As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.

Este artigo fornece suplementos para [Azure Disk Encryption para VMs Linux](disk-encryption-overview.md) com requisitos e pré-requisitos adicionais para Azure Disk Encryption com o Azure AD (versão anterior).

As informações contidas nessas seções permanecem as mesmas:

- [Sistemas operacionais e VMs com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais da VM](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a> Sistema de rede e a diretiva de grupo

Para habilitar o recurso de Azure Disk Encryption usando a sintaxe de parâmetro AAD mais antiga, as VMs de IaaS (infraestrutura como serviço) devem atender aos seguintes requisitos de configuração de ponto de extremidade de rede: 
  - Para obter um token para se conectar ao cofre de chaves, a VM IaaS deve ser capaz de se conectar a um ponto de extremidade do Azure AD, \[ login.microsoftonline.com \] .
  - Para gravar as chaves de criptografia no cofre de chaves, a VM IaaS deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM IaaS deve ser capaz de se conectar a um ponto de extremidade do armazenamento do Azure que hospeda o repositório de extensão do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se sua política de segurança limitar o acesso de VMs do Azure à Internet, você poderá resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../../key-vault/general/access-behind-firewall.md).
  - No Windows, se o TLS 1,0 estiver explicitamente desabilitado e a versão do .NET não for atualizada para 4,6 ou superior, a seguinte alteração de registro permitirá que Azure Disk Encryption selecione a versão mais recente do TLS:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Política de Grupo
 - A solução de Azure Disk Encryption usa o protetor de chave externa BitLocker para VMs IaaS do Windows. Para VMs ingressadas no domínio, não envie nenhuma política de grupo que imponha protetores de TPM. Para obter informações sobre o Política de Grupo para a opção **permitir BitLocker sem um TPM compatível**, consulte [referência de política de grupo do BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- A política do BitLocker em máquinas virtuais ingressadas no domínio com um Política de Grupo personalizado deve incluir a seguinte configuração: [Configurar o armazenamento do usuário das informações de recuperação do BitLocker-> permitir a chave de recuperação de 256 bits](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption falha quando as configurações de Política de Grupo personalizadas para o BitLocker são incompatíveis. Em computadores que não têm a configuração de política correta, aplique a nova política, force a nova política a ser atualizada (gpupdate.exe/Force) e, em seguida, reinicie se for necessário. 

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia 

Azure Disk Encryption requer Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos. Seu cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para obter mais informações, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Próximas etapas

- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Habilitar Azure Disk Encryption com o Azure AD em VMs do Linux (versão anterior)](disk-encryption-linux-aad.md)
- [Script da CLI dos pré-requisitos do Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell dos pré-requisitos do Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

---
title: Usar o Azure AD Domain Services para autorizar o acesso a dados de arquivo em protocolo SMB
description: Saiba como habilitar a autenticação baseada em identidade em protocolo SMB para os Arquivos do Azure por meio do Azure Active Directory Domain Services. As máquinas virtuais do Windows (VMs) conectadas ao domínio podem acessar os compartilhamentos de arquivo do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 3abca397186572cabb4f7ae99edae8688ea4d9a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499502"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Habilitar a autenticação do Azure Active Directory Domain Services nos Arquivos do Azure

Os [Arquivos do Azure](storage-files-introduction.md) dão suporte à autenticação baseada na identidade em protocolo SMB por meio de dois tipos de serviços de domínio: AD DS (Active Directory Domain Services) local e Azure AD DS (Azure Active Directory Domain Services). É altamente recomendável que você revise a [seção Como funciona](./storage-files-active-directory-overview.md#how-it-works) para selecionar o serviço de domínio certo para autenticação. A configuração será diferente dependendo do serviço de domínio que você escolher. Este artigo se concentra na habilitação e configuração do Azure AD DS para autenticação com compartilhamentos de arquivo do Azure.

Se você for novo nos compartilhamentos de arquivo do Azure, é recomendável que você leia o nosso [guia de plano](storage-files-planning.md) antes de ler a série de artigos a seguir.

> [!NOTE]
> Os Arquivos do Azure dão suporte somente à autenticação Kerberos com o Azure AD DS com RC4-HMAC. A criptografia Kerberos AES ainda não é compatível.
> Os Arquivos do Azure dão suporte à autenticação para o Azure AD DS com sincronização de dados completa com o Azure AD. Se você habilitou a sincronização de escopo no Azure AD DS que sincroniza apenas um conjunto limitado de identidades do Azure Active Directory, não haverá suporte para autenticação e autorização.

## <a name="prerequisites"></a>Pré-requisitos

Antes de habilitar o Azure AD em protocolo SMB para compartilhamentos de arquivo do Azure, verifique se que você concluiu os seguintes pré-requisitos:

1.  **Selecione ou crie um locatário do Azure AD.**

    Você pode usar um inquilino novo ou existente para a autenticação do Azure AD em SMB. O locatário e o compartilhamento de arquivos que você deseja acessar devem estar associados à mesma assinatura.

    Para criar um novo locatário do Azure AD, você pode [adicionar um locatário do Azure AD e uma assinatura do Azure AD](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se você já tem um locatário Azure AD, mas quer criar um locatário para usar com os compartilhamentos de arquivos do Azure, consulte [Criar um locatário do Azure Active Directory](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Ative os Serviços de Domínio do Azure AD no locatário do Azure AD.**

    Para oferecer suporte à autenticação com credenciais do Azure AD, você deve habilitar os Serviços de Domínio do Azure AD para o seu locatário do Azure AD. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    A implantação do Azure AD DS costuma levar cerca de 15 minutos para ser completada. Verifique se o status de integridade do Azure AD DS exibe **Em Execução**, com a sincronização do código hash da senha habilitada, antes de seguir para a próxima etapa.

1.  **Conectar uma VM do Azure ao domínio com o Azure AD DS.**

    Para acessar um compartilhamento de arquivo usando as credenciais do Azure AD de uma VM, ela deve estar conectada ao domínio do Azure AD DS. Para obter mais informações sobre como ingressar no domínio de uma VM, consulte [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > A autenticação do Azure AD DS em protocolo SMB com compartilhamentos de arquivo do Azure é compatível somente em máquinas virtuais do Azure executadas nas versões de sistema operacional acima do Windows 7 ou do Windows Server 2008 R2.

1.  **Selecione ou crie um compartilhamento de arquivos do Azure.**

    Selecione um compartilhamento de arquivos novo ou existente associado à mesma assinatura que o seu locatário do Azure AD. Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [Criar um compartilhamento de arquivos no Azure Files](storage-how-to-create-file-share.md).
    Para um desempenho ideal, recomendamos que o compartilhamento de arquivo esteja na mesma região da VM da qual você planeja acessar o compartilhamento.

1.  **Verifique a conectividade de arquivos do Azure montando compartilhamentos de arquivos do Azure usando a chave da sua conta de armazenamento.**

    Para verificar se sua VM e seu compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação dos Arquivos do Azure com o Azure AD DS está disponível em [todas as regiões do Azure públicas, governamentais e da China](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho

Antes de habilitar a autenticação do Azure AD DS em protocolo SMB para compartilhamentos de arquivo do Azure, verifique se os ambientes do AAD e do armazenamento do Azure estão configurados corretamente. Recomendamos que você analise os [pré-requisitos](#prerequisites) para ter certeza de que completou todas as etapas necessárias.

Em seguida, siga estas etapas para permitir acesso aos recursos de armazenamento dos Arquivos do Azure com credenciais do Azure AD:

1. Habilite a autenticação do Azure AD DS em protocolo SMB para sua conta de armazenamento para registrá-la com a implantação do Azure AD DS associada.
2. Atribuir permissões de acesso para um compartilhamento a uma identidade do Azure AD (um usuário, grupo ou responsável pelo serviço).
3. Configurar permissões NTFS em SMB para diretórios e arquivos.
4. Montar um compartilhamento de arquivos do Azure de uma VM associada ao domínio.

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação do Azure AD DS em protocolo SMB para os Arquivos do Azure.

![Diagrama mostrando o fluxo de trabalho do Azure AD sobre SMB para arquivos do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Habilitar a autenticação do Azure AD DS para a sua conta

Para habilitar a autenticação do Azure AD DS em protocolo SMB para os Arquivos do Azure, você pode definir uma propriedade em contas de armazenamento usando o portal do Azure, o Azure PowerShell ou o CLI do Azure. A configuração dessa propriedade ingresso completamente a conta de armazenamento ao domínio com a implantação do Azure AD DS associado. A autenticação do Azure AD DS em protocolo SMB é habilitada para todos os compartilhamentos de arquivo novos e existentes na conta de armazenamento.

Lembre-se de que você pode habilitar a autenticação do Azure AD DS em protocolo SMB somente após ter implantado com sucesso o Azure AD DS em seu locatário do Azure AD. Para obter mais informações, confira os [pré-requisitos](#prerequisites).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para habilitar a autenticação do Azure AD DS em protocolo SMB usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. No portal do Azure, acesse sua conta de armazenamento existente ou [crie uma conta de armazenamento](../common/storage-account-create.md).
1. Na seção **Configurações**, selecione **Configuração**.
1. Em **acesso baseado em identidade para compartilhamentos de arquivo**, mude o seletor do **AAD DS (Azure Active Directory Domain Services)** para **habilitado**.
1. Selecione **Salvar**.

A imagem a seguir mostra como habilitar a autenticação do Azure AD DS em protocolo SMB para sua conta de armazenamento.

![Habilitar a autenticação do Azure AD DS em protocolo SMB no portal do Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para habilitar a autenticação do Azure AD DS em protocolo SMB com o Azure PowerShell, instale o módulo AZ mais recente (2.4 ou mais recente) ou o módulo AZ.Storage (1.5 ou mais recente). Para obter mais informações sobre como instalar o PowerShell, consulte [Instalar o Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/install-Az-ps).

Para criar uma conta de armazenamento, chame [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) e defina o parâmetro **EnableAzureActiveDirectoryDomainServicesForFile** como **true**. No exemplo a seguir, lembre-se de substituir os valores do espaço reservado por seus próprios valores. (Se você estava usando o módulo anterior da versão prévia, o parâmetro para habilitar o recurso é **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Para habilitar esse recurso em contas de armazenamento já existentes, use o seguinte comando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para habilitar a autenticação do Azure AD em protocolo SMB com o CLI do Azure, instale a versão mais recente da CLI (versão 2.0.70 ou mais recente). Para obter informações sobre como instalar a CLI do Azure, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para criar uma conta de armazenamento, chame [criar conta de armazenamento az](/cli/azure/storage/account#az-storage-account-create) e defina a `--enable-files-aadds` propriedade como **true**. No exemplo a seguir, lembre-se de substituir os valores do espaço reservado por seus próprios valores. (Se você estava usando o módulo anterior da versão prévia, o parâmetro para habilitação de recurso é **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Para habilitar esse recurso em contas de armazenamento já existentes, use o seguinte comando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Você habilitou com êxito a autenticação do Azure AD DS em protocolo SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivo do Azure para uma identidade do Azure AD. Para conceder acesso ao compartilhamento de arquivo para mais usuários, siga as instruções em [Atribuir permissões de acesso](#assign-access-permissions-to-an-identity) para usar uma identidade e [configurar permissões NTFS em partes do protocolo SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os Arquivos do Azure e sobre como usar o Azure AD em protocolo SMB, consulte estes recursos:

- [Visão geral do suporte à autenticação baseada em identidade dos Arquivos do Azure para acesso do SMB](storage-files-active-directory-overview.md)
- [perguntas frequentes](storage-files-faq.md)

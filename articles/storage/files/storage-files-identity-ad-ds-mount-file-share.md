---
title: Montar o compartilhamento de arquivos do Azure para uma VM unida AD DS
description: Saiba como montar um compartilhamento de arquivos para seus computadores ingressados no Active Directory Domain Services local.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 1dd2768c7541312b68e2a44a35fc4260d67c5655
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586991"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>Parte quatro: montar um compartilhamento de arquivos de uma VM ingressada no domínio

Antes de começar este artigo, certifique-se de concluir o artigo anterior, [configurar permissões de diretório e de nível de arquivo sobre SMB](storage-files-identity-ad-ds-configure-permissions.md).

O processo descrito neste artigo verifica se o compartilhamento de arquivos e as permissões de acesso estão configurados corretamente e se você pode acessar um compartilhamento de arquivos do Azure de uma VM ingressada no domínio. A atribuição de função do Azure de nível de compartilhamento pode levar algum tempo para entrar em vigor. 

Entre no cliente usando as credenciais às quais você concedeu permissões, conforme mostrado na imagem a seguir.

![Captura de tela mostrando a tela de entrada do Azure AD para autenticação do usuário](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Pré-requisitos de montagem

Antes de poder montar o compartilhamento de arquivos, verifique se você passou pelos seguintes pré-requisitos:

- Se você estiver montando o compartilhamento de arquivos de um cliente que tenha montado anteriormente o compartilhamento de arquivos usando sua chave de conta de armazenamento, verifique se você desconectou o compartilhamento, removeu as credenciais persistentes da chave da conta de armazenamento e está usando as credenciais AD DS para autenticação. Para obter instruções para limpar o compartilhamento montado com a chave da conta de armazenamento, consulte a [página de perguntas frequentes](./storage-files-faq.md#ad-ds--azure-ad-ds-authentication).
- Seu cliente deve ter uma linha de visão para seu AD DS. Se seu computador ou VM estiver fora da rede gerenciada pelo seu AD DS, você precisará habilitar a VPN para alcançar AD DS para autenticação.

Substitua os valores de espaço reservado pelos seus próprios valores e, em seguida, use o comando a seguir para montar o compartilhamento de arquivos do Azure. Você sempre precisa montar usando o caminho mostrado abaixo. Não há suporte para o uso de CNAME para montagem de arquivo para autenticação baseada em identidade (AD DS ou AD DS do Azure).

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se você tiver problemas para montar com credenciais de AD DS, consulte [não é possível montar os arquivos do Azure com credenciais do AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) para obter diretrizes.

Se a montagem do compartilhamento de arquivos tiver sido bem-sucedida, você terá habilitado e configurado com êxito a autenticação de AD DS local para seus compartilhamentos de arquivos do Azure.

## <a name="next-steps"></a>Próximas etapas

Se a identidade que você criou no AD DS para representar a conta de armazenamento estiver em um domínio ou uma unidade organizacional que impõe a rotação de senha, vá para o próximo artigo para obter instruções sobre como atualizar sua senha:

[Atualize a senha da sua identidade de conta de armazenamento no AD DS](storage-files-identity-ad-ds-update-password.md)
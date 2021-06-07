---
title: Área de trabalho virtual do Windows configurar compartilhamento de arquivos MSIX app Attach versão prévia – Azure
description: Como configurar um compartilhamento de arquivos para o MSIX app attach para a área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930779"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>Configurar um compartilhamento de arquivos para anexação do aplicativo MSIX (versão prévia)

> [!IMPORTANT]
> A anexação do aplicativo MSIX está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Todas as imagens MSIX devem ser armazenadas em um compartilhamento de rede que possa ser acessado por usuários em um pool de hosts com permissões somente leitura.

A anexação do aplicativo MSIX (versão prévia) não tem nenhuma dependência do tipo de malha de armazenamento que o compartilhamento de arquivos usa. As considerações para o compartilhamento de anexação do aplicativo MSIX são as mesmas para um compartilhamento FSLogix. Para saber mais sobre os requisitos de armazenamento, consulte [Opções de armazenamento para contêineres de perfil FSLogix na área de trabalho virtual do Windows](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Requisitos de desempenho

Os limites de tamanho da imagem de anexação do aplicativo MSIX para seu sistema dependem do tipo de armazenamento que você está usando para armazenar os arquivos VHD ou VHDx, bem como as limitações de tamanho dos arquivos VHD, VHSD ou CIM e o sistema de arquivos.

A tabela a seguir fornece um exemplo de quantos recursos uma única imagem MSIX de 1 GB com um aplicativo MSIX dentro dele requer para cada VM:

| Recurso             | Requisitos |
|----------------------|--------------|
| IOPs de estado estável    | 1 IOPs       |
| Entrada de inicialização do computador | 10 IOPs      |
| Latency              | 400 MS       |

Os requisitos podem variar muito dependendo de quantos aplicativos MSIXdos são armazenados na imagem do MSIX. Para imagens MSIX maiores, você precisará alocar mais largura de banda.

### <a name="storage-recommendations"></a>Recomendações de armazenamento

O Azure oferece várias opções de armazenamento que podem ser usadas para anexar o aplicativo MISX. É recomendável usar os arquivos do Azure ou Azure NetApp Files como essas opções oferecem o melhor valor entre custo e sobrecarga de gerenciamento. O artigo [Opções de armazenamento para contêineres de perfil FSLogix na área de trabalho virtual do Windows](store-fslogix-profile.md) compara as diferentes soluções de armazenamento gerenciado que o Azure oferece no contexto da área de trabalho virtual do Windows.

### <a name="optimize-msix-app-attach-performance"></a>Otimizar desempenho de anexação de aplicativo MSIX

Aqui estão algumas outras coisas que recomendamos que você faça para otimizar o desempenho de anexação do aplicativo MSIX:

- A solução de armazenamento usada para anexar o aplicativo MSIX deve estar no mesmo local de datacenter que os hosts de sessão.
- Para evitar gargalos de desempenho, exclua os seguintes arquivos VHD, VHDX e CIM de verificações de antivírus:
   
    - <MSIXAppAttachFileShare \> \* . VHD
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\compartilhamento de storageaccount.file.core.windows.net \\ \* \* . VHD
    - \\\\compartilhamento de storageaccount.file.core.windows.net \\ \* \* . VHDX
    - <MSIXAppAttachFileShare>. CIM
    - \\\\compartilhamento de storageaccount.file.core.windows.net \\ \* \* . CIM

- Separe a malha de armazenamento para anexação do aplicativo MSIX de contêineres de perfil FSLogix.
- Todas as contas de sistema de VM e contas de usuário devem ter permissões somente leitura para acessar o compartilhamento de arquivos.
- Qualquer plano de recuperação de desastre para a área de trabalho virtual do Windows deve incluir a replicação do compartilhamento de arquivos de anexação do aplicativo MSIX em seu local de failover secundário. Para saber mais sobre a recuperação de desastre, consulte [configurar um plano de continuidade de negócios e recuperação de desastre](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Como configurar o compartilhamento de arquivos

O processo de instalação do compartilhamento de arquivos de anexação do aplicativo MSIX é basicamente o mesmo que [o processo de instalação para compartilhamentos de arquivos de perfil FSLogix](create-host-pools-user-profile.md). No entanto, você precisará atribuir permissões diferentes aos usuários. A anexação de aplicativo MSIX requer permissões somente leitura para acessar o compartilhamento de arquivos.

Se você estiver armazenando seus aplicativos MSIX em arquivos do Azure, em seguida, para os hosts de sua sessão, você precisará atribuir todas as VMs de host de sessão, o RBAC (controle de acesso baseado em função) da conta de armazenamento e as permissões do sistema de arquivos de nova tecnologia (NTFS) no compartilhamento.

| Objeto do Azure                      | Função necessária                                     | Função de função                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Host de sessão (objetos de computador VM)| Colaborador de Compartilhamento SMB de Dados do Arquivo de Armazenamento          | Ler e executar, ler, listar conteúdo da pasta  |
| Administradores no compartilhamento de arquivos              | Colaborador com Privilégios Elevados do Compartilhamento SMB de Dados do Arquivo de Armazenamento | Controle total                                  |
| Usuários no compartilhamento de arquivos               | Colaborador de Compartilhamento SMB de Dados do Arquivo de Armazenamento          | Ler e executar, ler, listar conteúdo da pasta  |

Para atribuir permissões de VMs de host de sessão para a conta de armazenamento e o compartilhamento de arquivos:

1. Crie um grupo de segurança Active Directory Domain Services (AD DS).

2. Adicione as contas de computador para todas as VMs de host de sessão como membros do grupo.

3. Sincronize o grupo de AD DS para Azure Active Directory (Azure AD).

4. Criar uma conta de armazenamento.

5. Crie um compartilhamento de arquivos na conta de armazenamento seguindo as instruções em [criar um compartilhamento de arquivos do Azure](../storage/files/storage-how-to-create-file-share.md#create-file-share).

6. Ingresse a conta de armazenamento para AD DS seguindo as instruções na [parte um: habilite a autenticação AD DS para seus compartilhamentos de arquivos do Azure](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Atribua o grupo de AD DS sincronizado ao Azure AD e atribua a conta de armazenamento da função colaborador de compartilhamento SMB de dados de arquivo de armazenamento.

8. Monte o compartilhamento de arquivos em qualquer host de sessão seguindo as instruções na [parte dois: atribuir permissões de nível de compartilhamento a uma identidade](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Conceda permissões NTFS no compartilhamento de arquivos para o grupo de AD DS.

10. Configure as permissões NTFS para as contas de usuário. Você precisará de uma unidade operacional (UO) originada do AD DS ao qual as contas na VM pertencem.

Depois de atribuir a identidade ao seu armazenamento, siga as instruções nos artigos em [próximas etapas](#next-steps) para conceder outras permissões necessárias para a identidade que você atribuiu às VMs.

Você também precisará certificar-se de que suas VMs de host de sessão tenham permissões de sistema de arquivos de tecnologia novas (NTFS). Você deve ter um contêiner de unidade operacional originado de Active Directory Domain Services (AD DS) e os usuários devem ser membros dessa unidade operacional para usar essas permissões.

## <a name="next-steps"></a>Próximas etapas

Aqui estão as outras coisas que você precisará fazer depois de configurar o compartilhamento de arquivos:

- Saiba como configurar Azure Active Directory Domain Services (AD DS) em [criar um contêiner de perfil com arquivos e AD DS do Azure](create-file-share.md).
- Saiba como configurar arquivos do Azure e AD DS do Azure em [criar um contêiner de perfil com arquivos do Azure e AD DS do Azure](create-profile-container-adds.md).
- Saiba como configurar Azure NetApp Files para anexação de aplicativo MSIX em [criar um contêiner de perfil com Azure NetApp Files e AD DS](create-fslogix-profile-container.md).
- Saiba como usar um compartilhamento de arquivos baseado em máquina virtual em [criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivos](create-host-pools-user-profile.md).

Quando tiver terminado, aqui estão alguns outros recursos que podem ser úteis:

- Pergunte a nossas perguntas da Comunidade sobre esse recurso na [área de trabalho virtual do Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Você também pode deixar comentários para a Área de Trabalho Virtual do Windows no [Hub de comentários da Área de Trabalho Virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Glossário de anexação de aplicativo MSIX](app-attach-glossary.md)
- [Perguntas frequentes sobre anexação de aplicativo MSIX](app-attach-faq.md)

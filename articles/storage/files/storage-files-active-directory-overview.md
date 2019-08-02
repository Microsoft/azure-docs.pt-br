---
title: Visão geral da autenticação do Azure Active Directory por SMB para arquivos do Azure (versão prévia) – Armazenamento do Azure
description: Os Arquivos do Azure dão suporte à autenticação baseada em identidade via SMB (versão prévia) por meio do Azure AD (Azure Active Directory) Domain Services. As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: 21087424be1a7a3edfe2dddcbec830bd74559b23
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269375"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Visão geral do suporte de autenticação de serviço (AAD DS) os arquivos do Azure do Azure Active Directory domínio para acesso ao SMB (visualização)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como habilitar a autenticação do AAD DS para arquivos do Azure, consulte [habilitar Azure domínio serviço de autenticação do Active Directory via SMB para arquivos do Azure (visualização)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossário 
É útil entender alguns termos-chave relacionadas à autenticação do serviço de domínio do AD do Azure no SMB para arquivos do Azure:

-   **Azure Active Directory (Azure AD)**  
    O Azure AD (Azure Active Directory) é o serviço de gerenciamento de diretório e identidade multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução. Para obter mais informações, confira [O que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Os Serviços de Domínio do Azure AD fornecem serviços de domínio gerenciado, como ingresso no domínio, políticas de grupo, LDAP e autenticação Kerberos/NTLM. Esses serviços são totalmente compatíveis com o Windows Server Active Directory. Para obter mais informações, consulte [Serviços de Domínio do Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

-   **RBAC (Controle de Acesso Baseado em Função) do Azure**  
    O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode gerenciar o acesso aos recursos concedendo aos usuários o mínimo de permissões necessárias para que eles realizem seus trabalhos. Para obter mais informações sobre o RBAC, consulte [O que é o Controle de acesso baseado em função (RBAC) no Azure?](../../role-based-access-control/overview.md)

-   **Autenticação Kerberos**

    Kerberos é um protocolo de autenticação usado para verificar a identidade de um usuário ou host. Para obter mais informações sobre Kerberos, consulte [Visão geral da autenticação do Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo SMB (Server Message Block)**  
    O SMB é um protocolo de compartilhamento de arquivos de rede padrão do setor. O SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre o SMB, consulte [Protocolo SMB da Microsoft e Visão geral do protocolo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Vantagens da autenticação do serviço de domínio do AD do Azure
Autenticação do serviço de domínio do AD do Azure para arquivos do Azure oferece diversos benefícios em relação ao uso de autenticação de chave compartilhada:

-   **Estender a experiência de acesso de compartilhamento de arquivos tradicional baseado em identidade para a nuvem com o Azure AD e o Azure AD Domain Service**  
    Se você planeja "lift and shift" credenciais de seu aplicativo para a nuvem, substituindo os servidores de arquivos tradicionais com arquivos do Azure, talvez seja melhor seu aplicativo para autenticar com o Azure AD para acessar os dados de arquivo. Do Azure suporta arquivos usando credenciais do Azure AD para acessar arquivos do Azure no SMB do AAD DS ingressado no domínio VMs do Windows. Você também pode optar por sincronizar todos os seus objetos do Active Directory locais ao Azure AD para preservar os nomes de usuário, senhas e outras atribuições de grupo.

-   **Impor o controle de acesso granular em compartilhamentos de arquivos do Azure**  
    Você pode conceder permissões para uma identidade específica no nível de arquivo, diretório ou compartilhamento. Por exemplo, suponha que você tenha várias equipes usando um compartilhamento de arquivos do Azure único para colaboração em projetos. Você pode conceder acesso para todas as equipes a diretórios não confidenciais, ao mesmo tempo em que limita o acesso a diretórios que contenham dados financeiros confidenciais apenas para sua equipe de Finanças. 

-   **Fazer backup das ACLs junto com seus dados**  
    Você pode usar Arquivos do Azure para fazer backup de seus compartilhamentos de arquivos locais existentes. Os Arquivos do Azure preservam suas ACLs junto com seus dados ao fazer backup de um compartilhamento de arquivos para os Arquivos do Azure por SMB.

## <a name="how-it-works"></a>Como ele funciona
Os Arquivos do Azure usam o Azure AD Domain Services para oferecer suporte a autenticação do Kerberos com credenciais do Azure AD das VMs ingressadas por domínio. Antes de poder usar o Azure AD com o Arquivos do Azure, você deve primeiro habilitar o Azure AD Domain Services e ingressar no domínio a partir das VMs das quais você planeja acessar dados de arquivos. Sua VM ingressado no domínio deve residir na mesma rede virtual (VNET) como o Azure AD Domain Services. 

Quando uma identidade associada a um aplicativo em execução em uma VM tenta acessar dados em Arquivos do Azure, a solicitação é enviada ao Azure AD Domain Services para autenticar a identidade. Se a autenticação for bem-sucedida, o Azure AD Domain Services retorna um token do Kerberos. O aplicativo envia uma solicitação que inclui o token do Kerberos e os Arquivos do Azure usam esse token para autorizar a solicitação. Os Arquivos do Azure apenas recebem o token e não persistem em credenciais do Azure AD.

![Diagrama mostrando a captura de tela de autenticação do Azure AD por SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Habilitar a autenticação do serviço de domínio do Azure AD para acesso ao SMB
Você pode habilitar a autenticação do serviço de domínio do AD do Azure para arquivos do Azure em suas contas de armazenamento novas e existentes criadas após 24 de setembro de 2018. 

Antes de habilitar esse recurso, verifique se que o Azure AD Domain Services foi implantado para o primário locatário do Azure AD ao qual sua conta de armazenamento está associada. Se você ainda não configurou o Azure AD Domain Services, siga as orientações passo a passo fornecidas em [Habilitar o Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/create-instance.md).

A implantação do Azure AD Domain Services geralmente leva de 10 a 15 minutos. Após a implantação do Azure AD Domain Services, você pode habilitar a autenticação do Azure AD por SMB para os Arquivos do Azure. Para obter mais informações, consulte [autenticação de habilitar o Azure Active Directory Domain Service via SMB para arquivos do Azure (visualização)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões no nível de compartilhamento para Arquivos do Azure
Depois que a autenticação do serviço de domínio do AD do Azure tiver sido habilitada, você pode configurar funções RBAC personalizadas para identidades do Azure AD e atribuir direitos de acesso a qualquer compartilhamento de arquivos na conta de armazenamento.

Quando um aplicativo em execução em uma VM ingressada no domínio tenta montar um compartilhamento de arquivos do Azure ou acessar um diretório ou arquivo, as credenciais do aplicativo do Azure AD são verificadas para garantir que as permissões no nível de compartilhamento e de NTFS adequadas. Para obter informações sobre como configurar permissões de nível de compartilhamento, consulte [autenticação de habilitar o Azure Active Directory Domain Service via SMB (visualização)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurar permissões no nível do diretório ou do arquivo para Arquivos do Azure 
Os Arquivos do Azure impõem permissões de arquivo NTFS padrão no nível do diretório e do arquivo, incluindo no diretório raiz. A configuração de permissões no nível de diretório ou de arquivo é compatível apenas por SMB. Monte o compartilhamento de arquivo de destino da sua VM e configure as permissões usando o comando [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) do Windows. 

> [!NOTE]
> Não há suporte para configurar permissões NTFS por meio do Explorador de Arquivos do Windows na versão prévia.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Use a chave de conta de armazenamento para permissões de superusuário 
Um usuário que possui a chave de conta de armazenamento pode acessar Arquivos do Azure com permissões de superusuário. As permissões de superusuário ultrapassam todas as restrições de controle de acesso configuradas no nível de compartilhamento com o RBAC e impostas pelo Azure AD. As permissões de superusuário são necessárias para montar um compartilhamento de arquivos do Azure. 

> [!IMPORTANT]
> Como parte das melhores práticas de segurança, evite compartilhar suas chaves de conta de armazenamento e aproveitar as permissões do Azure AD sempre que possível.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Preservar as ACLs de diretório e de arquivo para importação de dados para compartilhamentos de arquivos do Azure
Os arquivos do Azure agora dá suporte a preservação de ACLs de arquivo ou diretório quando você copia dados para compartilhamentos de arquivos do Azure. Você pode copiar as ACLs em um arquivo ou diretório para arquivos do Azure. Por exemplo, você pode usar [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com o sinalizador `/copy:s` para copiar dados e ACLs para um compartilhamento de arquivos do Azure. Preservação de ACL é ativado por padrão e você não precisa explicitamente habilitar o recurso de autenticação de serviço de domínio do AD do Azure em sua conta de armazenamento. 

## <a name="pricing"></a>Preços
Não há nenhum custo adicional de serviço para habilitar a autenticação do Azure AD por SMB em sua conta de armazenamento. Para obter mais informações sobre preços, consulte as páginas [Preços dos Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [Preços do Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a autenticação de Arquivos do Azure e do Azure AD por SMB, consulte estes recursos:

- [Introdução aos Arquivos do Azure](storage-files-introduction.md)
- [Habilitar a autenticação do Azure Active Directory por SMB para Arquivos do Azure (Versão prévia)](storage-files-active-directory-enable.md)
- [Perguntas frequentes](storage-files-faq.md)

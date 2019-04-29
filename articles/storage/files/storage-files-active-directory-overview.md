---
title: Visão geral da autenticação do Azure Active Directory por SMB para arquivos do Azure (versão prévia) – Armazenamento do Azure
description: Os Arquivos do Azure dão suporte à autenticação baseada em identidade via SMB (versão prévia) por meio do Azure AD (Azure Active Directory) Domain Services. As VMs (máquinas virtuais) do Windows ingressadas no domínio podem, então, acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: rogarana
ms.openlocfilehash: 7010425ba8acff4ed223e2a402d7a927a91c06b6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766492"
---
# <a name="overview-of-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Visão geral da autenticação do Azure Active Directory por SMB para arquivos do Azure (versão prévia)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como habilitar a autenticação do Azure AD por SMB para Arquivos do Azure, consulte [Habilitar autenticação do Azure Active Directory por SMB para Arquivos do Azure (versão prévia)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossário 
É útil entender alguns dos termos-chave relacionados à autenticação do Azure AD por SMB para Arquivos do Azure:

-   **Azure Active Directory (Azure AD)**  
    O Azure AD (Azure Active Directory) é o serviço de gerenciamento de diretório e identidade multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução. Para obter mais informações, confira [O que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Os Serviços de Domínio do Azure AD fornecem serviços de domínio gerenciado, como ingresso no domínio, políticas de grupo, LDAP e autenticação Kerberos/NTLM. Esses serviços são totalmente compatíveis com o Windows Server Active Directory. Para obter mais informações, consulte [Serviços de Domínio do Azure Active Directory (AD)](../../active-directory-domain-services/active-directory-ds-overview.md).

-   **RBAC (Controle de Acesso Baseado em Função) do Azure**  
    O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode gerenciar o acesso aos recursos concedendo aos usuários o mínimo de permissões necessárias para que eles realizem seus trabalhos. Para obter mais informações sobre o RBAC, consulte [O que é o Controle de acesso baseado em função (RBAC) no Azure?](../../role-based-access-control/overview.md)

-   **Autenticação Kerberos**

    Kerberos é um protocolo de autenticação usado para verificar a identidade de um usuário ou host. Para obter mais informações sobre Kerberos, consulte [Visão geral da autenticação do Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo SMB (Server Message Block)**  
    O SMB é um protocolo de compartilhamento de arquivos de rede padrão do setor. O SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre o SMB, consulte [Protocolo SMB da Microsoft e Visão geral do protocolo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-authentication"></a>Vantagens da autenticação do Azure AD
O Azure AD por SMB para Arquivos do Azure oferece diversos benefícios em relação ao uso de autenticação de Chave Compartilhada:

-   **Estender a experiência tradicional de acesso de compartilhamento de arquivos baseado em identidade para a nuvem com o Azure AD**  
    Se você planeja fazer um "lift and shift" do seu aplicativo para a nuvem, substituindo servidores de arquivos tradicionais por Arquivos do Azure, seria conveniente que seu aplicativo seja autenticado com o Azure AD para acessar os dados do arquivo. Os Arquivos do Azure oferecem suporte ao uso de credenciais do Azure AZ de VMs ingressadas no domínio por SMB para acessar compartilhamentos de arquivos, diretórios ou arquivos. Você também pode optar por sincronizar todos os seus objetos do Active Directory locais ao Azure AD para preservar os nomes de usuário, senhas e outras atribuições de grupo.

-   **Impor o controle de acesso granular em compartilhamentos de arquivos do Azure**  
    Com a autenticação do Azure AD por SMB, você pode conceder permissões para uma identidade específica no nível de compartilhamento, diretório ou arquivo. Por exemplo, suponha que você tenha várias equipes usando um compartilhamento de arquivos do Azure único para colaboração em projetos. Você pode conceder acesso para todas as equipes a diretórios não confidenciais, ao mesmo tempo em que limita o acesso a diretórios que contenham dados financeiros confidenciais apenas para sua equipe de Finanças. 

-   **Fazer backup das ACLs junto com seus dados**  
    Você pode usar Arquivos do Azure para fazer backup de seus compartilhamentos de arquivos locais existentes. Os Arquivos do Azure preservam suas ACLs junto com seus dados ao fazer backup de um compartilhamento de arquivos para os Arquivos do Azure por SMB.

## <a name="how-it-works"></a>Como ele funciona
Os Arquivos do Azure usam o Azure AD Domain Services para oferecer suporte a autenticação do Kerberos com credenciais do Azure AD das VMs ingressadas por domínio. Antes de poder usar o Azure AD com o Arquivos do Azure, você deve primeiro habilitar o Azure AD Domain Services e ingressar no domínio a partir das VMs das quais você planeja acessar dados de arquivos. Sua VM ingressada no domínio deve residir na mesma rede virtual (VNET) que o Azure AD Domain Services. 

Quando uma identidade associada a um aplicativo em execução em uma VM tenta acessar dados em Arquivos do Azure, a solicitação é enviada ao Azure AD Domain Services para autenticar a identidade. Se a autenticação for bem-sucedida, o Azure AD Domain Services retorna um token do Kerberos. O aplicativo envia uma solicitação que inclui o token do Kerberos e os Arquivos do Azure usam esse token para autorizar a solicitação. Os Arquivos do Azure apenas recebem o token e não persistem em credenciais do Azure AD.

![Diagrama mostrando a captura de tela de autenticação do Azure AD por SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-authentication-over-smb"></a>Habilitar autenticação do Azure AD com SMB
Você pode habilitar a autenticação do AD do Azure em SMB para arquivos do Azure em suas contas de armazenamento novas e existentes criadas após 24 de setembro de 2018. 

Antes de habilitar a autenticação do Azure AD por SMB, verifique se o Azure AD Domain Services foi implantado para o locatário primário do Azure AD ao qual sua conta de armazenamento está associada. Se você ainda não configurou o Azure AD Domain Services, siga as orientações passo a passo fornecidas em [Habilitar o Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

A implantação do Azure AD Domain Services geralmente leva de 10 a 15 minutos. Após a implantação do Azure AD Domain Services, você pode habilitar a autenticação do Azure AD por SMB para os Arquivos do Azure. Para obter mais informações, consulte [Habilitar a autenticação do Azure Active Directory por SMB para Arquivos do Azure (Versão prévia)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões no nível de compartilhamento para Arquivos do Azure
Depois que a autenticação do Azure AD tiver sido habilitada, você pode configurar funções RBAC personalizadas para identidades do Azure AD e atribuir direitos de acesso a qualquer compartilhamento de arquivos na conta de armazenamento.

Quando um aplicativo em execução em uma VM ingressada no domínio tenta montar um compartilhamento de arquivos do Azure ou acessar um diretório ou arquivo, as credenciais do aplicativo do Azure AD são verificadas para garantir que as permissões no nível de compartilhamento e de NTFS adequadas. Para obter informações sobre como configurar permissões no nível de compartilhamento, consulte [Habilitar autenticação do Azure Active Directory por SMB (Versão prévia)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurar permissões no nível do diretório ou do arquivo para Arquivos do Azure 
Os Arquivos do Azure impõem permissões de arquivo NTFS padrão no nível do diretório e do arquivo, incluindo no diretório raiz. A configuração de permissões no nível de diretório ou de arquivo é compatível apenas por SMB. Monte o compartilhamento de arquivo de destino da sua VM e configure as permissões usando o comando [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) do Windows. 

> [!NOTE]
> Não há suporte para configurar permissões NTFS por meio do Explorador de Arquivos do Windows na versão prévia.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Use a chave de conta de armazenamento para permissões de superusuário 
Um usuário que possui a chave de conta de armazenamento pode acessar Arquivos do Azure com permissões de superusuário. As permissões de superusuário ultrapassam todas as restrições de controle de acesso configuradas no nível de compartilhamento com o RBAC e impostas pelo Azure AD. As permissões de superusuário são necessárias para montar um compartilhamento de arquivos do Azure. 

> [!IMPORTANT]
> Como parte das melhores práticas de segurança, evite compartilhar suas chaves de conta de armazenamento e aproveitar as permissões do Azure AD sempre que possível.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Preservar as ACLs de diretório e de arquivo para importação de dados para compartilhamentos de arquivos do Azure
A autenticação do Azure AD por SMB oferece suporte a preservação de ACLs de diretório ou de arquivo quando você copia dados para compartilhamentos de arquivos do Azure. Na versão de prévia, você pode copiar as ACLs em um diretório ou arquivo para Arquivos do Azure. Por exemplo, você pode usar [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com o sinalizador `/copy:s` para copiar dados e ACLs para um compartilhamento de arquivos do Azure.

## <a name="pricing"></a>Preços
Não há nenhum custo adicional de serviço para habilitar a autenticação do Azure AD por SMB em sua conta de armazenamento. Para obter mais informações sobre preços, consulte as páginas [Preços dos Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [Preços do Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a autenticação de Arquivos do Azure e do Azure AD por SMB, consulte estes recursos:

- [Introdução aos Arquivos do Azure](storage-files-introduction.md)
- [Habilitar a autenticação do Azure Active Directory por SMB para Arquivos do Azure (Versão prévia)](storage-files-active-directory-enable.md)
- [Perguntas frequentes](storage-files-faq.md)
---
title: Visão geral-autorização baseada em identidade de arquivos do Azure
description: Os arquivos do Azure oferecem suporte à autenticação baseada em identidade sobre SMB (protocolo de mensagem de servidor) por meio de Azure Active Directory Domain Services (AD DS) e Active Directory. As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 8887243f953a7bb000033a2e56b4655b93cd7ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519785"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Visão geral de opções de autenticação baseadas em identidades do Azure Files para acesso SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como habilitar a autenticação de Active Directory Domain Services local para compartilhamentos de arquivos do Azure, consulte [habilitar a autenticação de Active Directory Domain Services local sobre SMB para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md).

Para saber como habilitar a autenticação de AD DS do Azure para compartilhamentos de arquivos do Azure, consulte [habilitar a autenticação de Azure Active Directory Domain Services em arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glossário 
É útil entender alguns termos-chave relacionados à autenticação do serviço de domínio do Azure AD sobre SMB para compartilhamentos de arquivos do Azure:

-   **Autenticação Kerberos**

    Kerberos é um protocolo de autenticação usado para verificar a identidade de um usuário ou host. Para obter mais informações sobre Kerberos, consulte [Visão geral da autenticação do Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo SMB (Server Message Block)**

    O SMB é um protocolo de compartilhamento de arquivos de rede padrão do setor. O SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre o SMB, consulte [Protocolo SMB da Microsoft e Visão geral do protocolo CIFS](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Active Directory do Azure (Azure AD)**

    O Azure Active Directory (AD do Azure) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução. As VMs (máquinas virtuais) do Windows Unidas pelo Azure AD não podem acessar compartilhamentos de arquivos do Azure com suas credenciais do Azure AD. Para obter mais informações, confira [O que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    O Azure AD DS fornece serviços de domínio gerenciados, como ingresso no domínio, diretivas de grupo, LDAP e autenticação Kerberos/NTLM. Esses serviços são totalmente compatíveis com Active Directory Domain Services. Para obter mais informações, consulte [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services local (AD DS)**

    A integração do AD DS (Active Directory Domain Services local) com os arquivos do Azure fornece os métodos para armazenar dados de diretório ao disponibilizá-los para usuários e administradores de rede. A segurança é integrada com AD DS por meio de autenticação de logon e controle de acesso a objetos no diretório. Com um único logon de rede, os administradores podem gerenciar dados de diretório e organização em toda a rede, e os usuários de rede autorizados podem acessar recursos em qualquer lugar da rede. O AD DS é normalmente adotado por empresas em ambientes locais e AD DS credenciais são usadas como a identidade do controle de acesso. Para obter mais informações, consulte [Active Directory Domain Services visão geral](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **RBAC do Azure (controle de acesso baseado em função do Azure)**

    O Azure RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC do Azure, você pode gerenciar o acesso aos recursos concedendo aos usuários o menor número de permissões necessárias para executar seus trabalhos. Para obter mais informações sobre o RBAC do Azure, consulte [o que é o Azure RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Casos de uso comuns

A autenticação baseada em identidade e o suporte para ACLs do Windows em arquivos do Azure é melhor aproveitado para os seguintes casos de uso:

### <a name="replace-on-premises-file-servers"></a>Substituir servidores de arquivos locais

Reprovar e substituir servidores de arquivos locais dispersos é um problema comum que cada empresa encontra em sua jornada de modernização de ti. Compartilhamentos de arquivos do Azure com autenticação de AD DS local é o melhor ajuste aqui, quando você pode migrar os dados para os arquivos do Azure. Uma migração completa permitirá que você aproveite os benefícios de alta disponibilidade e escalabilidade, minimizando também as alterações no lado do cliente. Ele fornece uma experiência de migração direta aos usuários finais, para que eles possam continuar acessando seus dados com as mesmas credenciais usando seus computadores ingressados no domínio existentes.

### <a name="lift-and-shift-applications-to-azure"></a>Migrar e deslocar aplicativos para o Azure

Ao migrar e deslocar aplicativos para a nuvem, você deseja manter o mesmo modelo de autenticação para seus dados. À medida que estendemos a experiência de controle de acesso com base em identidade para compartilhamentos de arquivos do Azure, ele elimina a necessidade de alterar seu aplicativo para métodos de autenticação modernos e agilizar a adoção da nuvem. Os compartilhamentos de arquivos do Azure fornecem a opção de integração com o Azure AD DS ou AD DS local para autenticação. Se seu plano for ser de 100% na nuvem nativa e minimizar os esforços de gerenciamento de infraestruturas de nuvem, o Azure AD DS seria uma melhor opção como um serviço de domínio totalmente gerenciado. Se precisar de compatibilidade total com recursos de AD DS, convém considerar estender seu ambiente de AD DS para a nuvem por meio da hospedagem automática de controladores de domínio em VMs. De qualquer forma, fornecemos a flexibilidade para escolher os serviços de domínio que atendam às suas necessidades de negócios.

### <a name="backup-and-disaster-recovery-dr"></a>Recuperação de desastres e backup (DR)

Se você estiver mantendo seu armazenamento de arquivos primário local, os compartilhamentos de arquivos do Azure podem servir como um armazenamento ideal para backup ou DR, para melhorar a continuidade dos negócios. Você pode usar compartilhamentos de arquivos do Azure para fazer backup de seus dados de servidores de arquivos existentes, preservando as DACLs do Windows. Para cenários de DR, você pode configurar uma opção de autenticação para dar suporte à imposição de controle de acesso adequada no failover.

## <a name="supported-scenarios"></a>Cenários com suporte

A tabela a seguir resume os cenários de autenticação de compartilhamentos de arquivos do Azure com suporte para o Azure AD DS e AD DS local. É recomendável selecionar o serviço de domínio que você adotou para o seu ambiente de cliente para integração com os arquivos do Azure. Se você já tiver AD DS configurado localmente ou no Azure em que seus dispositivos estão ingressados no domínio do seu anúncio, você deve optar por aproveitar AD DS para a autenticação de compartilhamentos de arquivos do Azure. Da mesma forma, se você já tiver adotado o Azure AD DS, deverá usá-lo para autenticação em compartilhamentos de arquivos do Azure.


|Autenticação de AD DS do Azure  | Autenticação de AD DS local  |
|---------|---------|
|As máquinas Windows ingressadas no AD DS do Azure podem acessar compartilhamentos de arquivos do Azure com as credenciais do Azure AD sobre SMB.     |As máquinas do Windows locais AD DS ou ingressadas no Azure AD DS podem acessar compartilhamentos de arquivos do Azure com as credenciais de Active Directory locais que são sincronizadas com o Azure AD por SMB. Seu cliente deve ter uma linha de visão para seu AD DS.        |

### <a name="restrictions"></a>Restrições

- O Azure AD DS e a autenticação de AD DS local não dão suporte à autenticação em relação às contas de computador. Em vez disso, você pode considerar o uso de uma conta de logon de serviço.
- Nem a autenticação de AD DS do Azure nem a autenticação de AD DS local têm suporte em dispositivos ingressados no Azure AD ou em dispositivos registrados no Azure AD.
- Os compartilhamentos de arquivos do Azure oferecem suporte apenas à autenticação baseada em identidade em um dos seguintes serviços de domínio, seja [Azure Active Directory Domain Services (Azure AD DS)](#azure-ad-ds) ou [Active Directory Domain Services local (AD DS)](#ad-ds).
- Nenhum método de autenticação baseado em identidade é suportado com NFS (sistema de arquivos de rede), que está em versão prévia.

## <a name="advantages-of-identity-based-authentication"></a>Vantagens da autenticação baseada em identidade
A autenticação baseada em identidade para arquivos do Azure oferece vários benefícios em relação ao uso da autenticação de chave compartilhada:

-   **Estenda a experiência tradicional de acesso ao compartilhamento de arquivos baseado em identidade para a nuvem com AD DS local e o Azure AD DS**  
    Se você planeja levantar e deslocar seu aplicativo para a nuvem, substituindo os servidores de arquivos tradicionais por compartilhamentos de arquivos do Azure, convém que seu aplicativo seja autenticado com as credenciais locais AD DS ou do Azure AD DS para acessar os dados do arquivo. Os arquivos do Azure dão suporte ao uso de AD DS locais ou de credenciais do Azure AD DS para acessar compartilhamentos de arquivos do Azure via SMB de AD DS locais ou de VMs ingressadas no domínio do Azure AD DS.

-   **Impor o controle de acesso granular em compartilhamentos de arquivos do Azure**  
    Você pode conceder permissões a uma identidade específica no nível de compartilhamento, diretório ou arquivo. Por exemplo, suponha que você tenha várias equipes usando um compartilhamento de arquivos do Azure único para colaboração em projetos. Você pode conceder acesso para todas as equipes a diretórios não confidenciais, ao mesmo tempo em que limita o acesso a diretórios que contenham dados financeiros confidenciais apenas para sua equipe de Finanças. 

-   **Fazer backup de ACLs do Windows (também conhecido como NTFS) junto com seus dados**  
    Você pode usar compartilhamentos de arquivos do Azure para fazer backup de seus compartilhamentos de arquivos locais existentes. Os arquivos do Azure preservam suas ACLs junto com seus dados quando você faz backup de um compartilhamento de arquivos para compartilhamentos de arquivos do Azure via SMB.

## <a name="how-it-works"></a>Como ele funciona

Os compartilhamentos de arquivos do Azure aproveitam o protocolo Kerberos para autenticação com AD DS local ou AD DS do Azure. Quando uma identidade associada a um usuário ou aplicativo em execução em um cliente tenta acessar dados em compartilhamentos de arquivos do Azure, a solicitação é enviada para o serviço de domínio, seja AD DS ou AD DS do Azure, para autenticar a identidade. Se a autenticação for bem-sucedida, ela retornará um token Kerberos. O cliente envia uma solicitação que inclui o token Kerberos e os compartilhamentos de arquivos do Azure usam esse token para autorizar a solicitação. Os compartilhamentos de arquivos do Azure recebem apenas o token Kerberos, não as credenciais de acesso.

Antes de habilitar a autenticação baseada em identidades em compartilhamentos de arquivos do Azure, primeiro você deve configurar seu ambiente de domínio.

### <a name="ad-ds"></a>AD DS

Para autenticação de AD DS local, você deve configurar seus controladores de domínio do AD e ingressar no domínio em seus computadores ou máquinas virtuais. Você pode hospedar seus controladores de domínio em VMs do Azure ou locais. De qualquer forma, os clientes ingressados no domínio devem ter uma linha de visão para o serviço de domínio, portanto, eles devem estar dentro da rede corporativa ou da rede virtual (VNET) do seu serviço de domínio.

O diagrama a seguir ilustra a autenticação de AD DS local para compartilhamentos de arquivos do Azure via SMB. O AD DS local deve ser sincronizado com o Azure AD usando a sincronização Azure AD Connect. Somente usuários híbridos que existem no AD DS local e no Azure AD podem ser autenticados e autorizados para acesso de compartilhamento de arquivos do Azure. Isso ocorre porque a permissão de nível de compartilhamento é configurada em relação à identidade representada no Azure AD em que a permissão de nível de diretório/arquivo é imposta com isso em AD DS. Certifique-se de configurar as permissões corretamente no mesmo usuário híbrido.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="Diagrama que descreve a autenticação de AD DS local para compartilhamentos de arquivos do Azure por SMB.":::

### <a name="azure-ad-ds"></a>Azure AD DS

Para a autenticação de AD DS do Azure, você deve habilitar Azure AD Domain Services e ingressar no domínio as VMs das quais você planeja acessar dados de arquivo. Sua VM ingressada no domínio deve residir na mesma rede virtual (VNET) que o AD DS do Azure. 

O diagrama a seguir representa o fluxo de trabalho para a autenticação de AD DS do Azure para compartilhamentos de arquivos do Azure via SMB. Ele segue um padrão semelhante para a autenticação de AD DS local para compartilhamentos de arquivos do Azure. Há duas diferenças principais:

- Primeiro, você não precisa criar a identidade no Azure AD DS para representar a conta de armazenamento. Isso é executado pelo processo de habilitação em segundo plano.

- Em segundo lugar, todos os usuários que existem no Azure AD podem ser autenticados e autorizados. O usuário pode ser somente na nuvem ou híbrido. A sincronização do Azure AD para o Azure AD DS é gerenciada pela plataforma sem a necessidade de nenhuma configuração de usuário. No entanto, o cliente deve estar ingressado no domínio para o Azure AD DS, não pode ser ingressado ou registrado no Azure AD. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Diagrama":::

### <a name="enable-identity-based-authentication"></a>Habilitar a autenticação baseada em identidade

Você pode habilitar a autenticação baseada em identidade com o Azure AD DS ou o AD DS local para compartilhamentos de arquivos do Azure em suas contas de armazenamento novas e existentes. Somente um serviço de domínio pode ser usado para autenticação de acesso a arquivos na conta de armazenamento, que se aplica a todos os compartilhamentos de arquivos na conta. Orientações detalhadas sobre como configurar seus compartilhamentos de arquivos para autenticação com o Azure AD DS em nosso artigo [habilitar a autenticação Azure Active Directory Domain Services em arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) e diretrizes para AD DS locais em nosso outro artigo, [habilite a autenticação de Active Directory Domain Services local sobre SMB para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões no nível de compartilhamento para Arquivos do Azure

Quando o Azure AD DS ou a autenticação de AD DS local estiver habilitada, você poderá usar funções internas do Azure ou configurar funções personalizadas para identidades do Azure AD e atribuir direitos de acesso a qualquer compartilhamento de arquivos em suas contas de armazenamento. A permissão atribuída permite que a identidade concedida obtenha acesso somente ao compartilhamento, nada mais, nem mesmo o diretório raiz. Você ainda precisa configurar separadamente as permissões no nível do diretório ou do arquivo para compartilhamentos de arquivos do Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configurar permissões no nível do diretório ou do arquivo para arquivos do Azure

Os compartilhamentos de arquivos do Azure impõem permissões de arquivo padrão do Windows no nível do diretório e do arquivo, incluindo o diretório raiz. A configuração de permissões no nível do diretório ou do arquivo é suportada tanto pelo SMB quanto pelo REST. Monte o compartilhamento de arquivos de destino de sua VM e configure permissões usando o explorador de arquivos do Windows, o Windows [icacls](/windows-server/administration/windows-commands/icacls)ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/get-acl) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Use a chave de conta de armazenamento para permissões de superusuário

Um usuário com a chave da conta de armazenamento pode acessar compartilhamentos de arquivos do Azure com permissões de superusuário. As permissões de superusuário ignoram todas as restrições de controle de acesso.

> [!IMPORTANT]
> Nossa prática recomendada de segurança recomendada é evitar compartilhar suas chaves de conta de armazenamento e aproveitar a autenticação baseada em identidade sempre que possível.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Preservar ACLs de diretório e arquivo ao importar dados para compartilhamentos de arquivos do Azure

Os arquivos do Azure dão suporte à preservação de ACLs de nível de diretório ou arquivo ao copiar dados para compartilhamentos de arquivos do Azure. Você pode copiar ACLs em um diretório ou arquivo para compartilhamentos de arquivos do Azure usando Sincronização de Arquivos do Azure ou conjuntos de ferramentas de movimentação de arquivos comuns. Por exemplo, você pode usar o [Robocopy](/windows-server/administration/windows-commands/robocopy) com o `/copy:s` sinalizador para copiar dados, bem como ACLs para um compartilhamento de arquivos do Azure. As ACLs são preservadas por padrão, não é necessário habilitar a autenticação baseada em identidade em sua conta de armazenamento para preservar ACLs.

## <a name="pricing"></a>Preços
Não há nenhum encargo de serviço adicional para habilitar a autenticação baseada em identidade sobre SMB em sua conta de armazenamento. Para obter mais informações sobre preços, consulte preços de [arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [preços de Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os arquivos do Azure e a autenticação baseada em identidade sobre o SMB, consulte estes recursos:

- [Como planejar uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Habilitar a autenticação de Active Directory Domain Services local sobre SMB para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md)
- [Habilitar a autenticação de Azure Active Directory Domain Services nos arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [perguntas frequentes](storage-files-faq.md)
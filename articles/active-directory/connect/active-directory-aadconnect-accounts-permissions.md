---
title: "Azure AD Connect: contas e permissões | Microsoft Docs"
description: "Este tópico descreve as contas usadas e criadas e as permissões necessárias."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: billmath
ms.openlocfilehash: b45e4096cb68c4b88d2d782427d66a11d1b86b33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: contas e permissões
O assistente de instalação do Azure AD Connect oferece dois caminhos diferentes:

* Nas Configurações Expressas, o assistente exige mais privilégios.  Isso é assim para que ele possa definir a sua configuração facilmente, sem a necessidade de criar usuários ou configurar permissões.
* Nas Configurações Personalizadas, o assistente oferece mais escolhas e opções. No entanto, há algumas situações em que é necessário que você tenha as permissões corretas.

## <a name="related-documentation"></a>documentação relacionada
Se você não leu a documentação em [Integrando suas identidades locais com o Azure Active Directory](../active-directory-aadconnect.md), a tabela a seguir fornece links para tópicos relacionados.

|Tópico |Link|  
| --- | --- |
|Baixar o Azure AD Connect | [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar usando as Configurações expressas | [Instalação expressa do Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalar usando Configurações personalizadas | [Instalação personalizada do Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Atualização do DirSync | [Atualizar a partir da ferramenta de sincronização do AD do Azure (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verifique a instalação e atribua licenças ](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Instalação das configurações expressas
Nas Configurações expressas, o assistente de instalação solicita credenciais de admin corporativo do AD DS.  Isso é assim para que o Active Directory local possa ser configurado com as permissões necessárias para o Azure AD Connect. Se você estiver atualizando o DirSync, as credenciais de administrador corporativo do AD DS serão usadas para redefinir a senha da conta usada pelo DirSync. Você também precisa de credenciais de Administrador Global do Azure AD.

| Página do assistente | Credenciais coletadas | Permissões necessárias | Usadas para |
| --- | --- | --- | --- |
| N/D |Usuário que está executando o assistente de instalação |Administrador do servidor local |<li>Cria a conta local que é usada como a [conta de serviço do mecanismo de sincronização](#azure-ad-connect-sync-service-account). |
| Conecte-se ao AD do Azure |Credenciais de diretório do AD do Azure |Função de administrador global no AD do Azure |<li>Habilite a sincronização no diretório do AD do Azure.</li>  <li>Criação da [conta do Azure AD](#azure-ad-service-account) que é usada para operações de sincronização em andamento no Azure AD.</li> |
| Conectar-se ao AD DS |Credenciais do Active Directory local |Membro do grupo EA (Administradores de Empresa) no Active Directory |<li>Cria uma [conta](#active-directory-account) no Active Directory e concede permissões a ela. Essa conta criada é usada para ler e gravar informações de diretório durante a sincronização.</li> |

### <a name="enterprise-admin-credentials"></a>Credenciais de administrador corporativo
Essas credenciais são usadas apenas durante a instalação e não são usadas após a conclusão da instalação. O Admin Corporativo e não Administrador de Domínio, deve garantir que as permissões no Active Directory possam ser definidas em todos os domínios.

### <a name="global-admin-credentials"></a>Credenciais de administrador global
Essas credenciais são usadas apenas durante a instalação e não são usadas após a conclusão da instalação. São usadas para criar a [conta do AD do Azure](#azure-ad-service-account) usada para sincronizar alterações com o AD do Azure. A conta também habilita a sincronização como um recurso no Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Permissões de configurações expressas para a conta do AD DS criada
A [conta](#active-directory-account) criada para leitura e gravação no AD DS tem as seguintes permissões se é criada pelas configurações expressas:

| Permissão | Usado para |
| --- | --- |
| <li>Replicar alterações de diretório</li><li>Replicar todas as alterações de diretório |Sincronização de senha |
| Ler/Gravar todas as propriedades Usuário |Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades iNetOrgPerson |Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades Grupo |Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades Contato |Importação e Exchange híbrido |
| Redefinir senha |Preparação para habilitar write-back de senha |

## <a name="custom-settings-installation"></a>Instalação de configurações personalizadas
O Azure AD Connect, versão 1.1.524.0 e posteriores, tem a opção para permitir que o assistente do Azure AD Connect crie a conta usada para se conectar ao Active Directory.  As versões anteriores exigem que a conta seja criada antes da instalação. As permissões que você deve conceder a essa conta podem ser encontradas em [Criar a conta do AD DS](#create-the-ad-ds-account). 

| Página do assistente | Credenciais coletadas | Permissões necessárias | Usadas para |
| --- | --- | --- | --- |
| N/D |Usuário que está executando o assistente de instalação |<li>Administrador do servidor local</li><li>Para usar um SQL Server completo, o usuário deve ser o administrador do sistema (SA) no SQL</li> |Por padrão, cria a conta local que é usada como [conta de serviço do mecanismo de sincronização](#azure-ad-connect-sync-service-account). A conta é criada somente quando o administrador não define uma conta específica. |
| Instalar serviços de sincronização, opção de conta de serviço |Credenciais de conta de usuário local ou AD |Usuário, permissões são concedidas pelo assistente de instalação |Se o administrador especificar uma conta, essa conta será usada como a conta de serviço para o serviço de sincronização. |
| Conecte-se ao AD do Azure |Credenciais de diretório do AD do Azure |Função de administrador global no AD do Azure |<li>Habilite a sincronização no diretório do AD do Azure.</li>  <li>Criação da [conta do Azure AD](#azure-ad-service-account) que é usada para operações de sincronização em andamento no Azure AD.</li> |
| Conectar seus diretórios |Credenciais do Active Directory para cada floresta que é conectada ao Azure AD local |As permissões dependem de quais recursos você habilita e podem ser encontradas em [Criar a conta do AD DS](#create-the-ad-ds-account) |Essa conta é usada para ler e gravar informações de diretório durante a sincronização. |
| Servidores do AD FS |Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de logon do usuário que executa o assistente não são suficientes para conectar-se |Administrador de domínio |Instalação e configuração da função de servidor do AD FS. |
| Servidores proxy de aplicativo Web |Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de logon do usuário que executa o assistente não são suficientes para conectar-se |Administrador local no computador de destino |Instalação e configuração da função de servidor WAP. |
| Credenciais de confiança de proxy |As credenciais de confiança do serviço de federação (as credenciais que o proxy usa para se registrar para um certificado de confiança do FS |Conta de domínio que é administrador local do servidor do AD FS |Registro inicial de certificado de confiança FS-WAP. |
| Página da conta de serviço do AD FS, "Usar uma opção de conta de usuário de domínio" |Credenciais de conta de usuário do AD |Usuário de domínio |A conta de usuário do AD cujas credenciais são fornecidas é usada como a conta de logon do serviço do AD FS. |

### <a name="create-the-ad-ds-account"></a>Criar a conta do AD DS
A conta que você especifica na página **Conecte seus diretórios** deve estar presente no Active Directory antes da instalação.  Ela também deve ter as permissões necessárias concedidas. O Assistente de instalação não verifica as permissões, e os problemas são encontrados somente durante a sincronização.

As permissões de que você precisa dependem dos recursos opcionais que habilitar. Se você tiver vários domínios, as permissões devem ser concedidas para todos os domínios na floresta. Se você não habilitar esses recursos, as permissões padrão **Usuário de domínio** serão suficientes.

| Recurso | Permissões |
| --- | --- |
| Recurso msDS-ConsistencyGuid |Permissões de gravação para o atributo msDS-ConsistencyGuid documentados em [Conceitos de design – usando o atributo msDS-ConsistencyGuid como sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| Sincronização de senha |<li>Replicar alterações de diretório</li>  <li>Replicar todas as alterações de diretório |
| Implantação híbrida do Exchange |Permissões de gravação para os atributos documentados em [Write-back híbrido do Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) para usuários, grupos e contatos. |
| Pasta pública do Exchange Mail |Permissões de leitura para os atributos documentados na [Pasta pública do Exchange Mail](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Write-back de senha |Permissões de gravação para os atributos documentados em [Introdução ao gerenciamento de senhas](../active-directory-passwords-writeback.md) para usuários. |
| Write-back de dispositivo |Permissões concedidas com um script do PowerShell, conforme descrito em [Write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md). |
| Write-back de grupo |Ler, criar, atualizar e excluir objetos de grupo para **grupos do Office 365** sincronizados.  Para saber mais, confira [Write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback).|

## <a name="upgrade"></a>Atualizar
Ao atualizar de uma versão do Azure AD Connect para uma nova versão, você precisa das seguintes permissões:

>[!IMPORTANT]
>Começando com o build 1.1.484, o Azure AD Connect introduziu um bug de regressão que requer permissões de sysadmin para atualizar o banco de dados SQL.  Esse bug ainda está presente no build mais recente, 1.1.614.  Se você estiver atualizando para esse build, precisará de permissões de sysadmin.  Permissões de DBO não são suficientes.  Se você tentar atualizar a conexão do Azure AD sem ter permissões de sysadmin, a atualização falhará e o Azure AD Connect deixará de funcionar corretamente depois.  A Microsoft está ciente desse problema e está trabalhando para corrigi-lo.


| Principal | Permissões necessárias | Usadas para |
| --- | --- | --- |
| Usuário que está executando o assistente de instalação |Administrador do servidor local |Binários de atualização. |
| Usuário que está executando o assistente de instalação |Membro do ADSyncAdmins |Fazer alterações em Regras de Sincronização e outras configurações. |
| Usuário que está executando o assistente de instalação |Se você usa um SQL Server completo: DBO (ou semelhante) do banco de dados de mecanismo de sincronização |Fazer alterações no nível de banco de dados, como a atualização de tabelas com novas colunas. |

## <a name="more-about-the-created-accounts"></a>Mais informações sobre as contas criadas
### <a name="active-directory-account"></a>Conta do Active Directory
Se você usar configurações expressas, uma conta será criada no Active Directory e será usada para sincronização. A conta criada está localizada no domínio raiz da floresta no contêiner Usuários e tem seu nome prefixado com **MSOL_**. A conta é criada com uma senha longa complexa que não expira. Se você tiver uma política de senha em seu domínio, verifique se senhas longas e complexas são permitidas para esta conta.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

Se você usar configurações personalizadas, você será responsável por criar a conta antes de iniciar a instalação.

### <a name="azure-ad-connect-sync-service-account"></a>Conta de serviço de sincronização do Azure AD Connect
O serviço de sincronização pode ser executado em contas diferentes. Ele pode ser executado em uma VSA (**conta de serviço virtual**), uma gMSA/sMSA (**conta de serviço gerenciado de grupo**) ou então uma conta de usuário regular. As opções com suporte foram alteradas com a versão de abril de 2017 do Connect ao fazer uma nova instalação. Se você atualizar de uma versão anterior do Azure AD Connect, essas opções adicionais não estarão disponíveis.

| Tipo de conta | Opção de instalação | Descrição |
| --- | --- | --- |
| [Conta de Serviço Virtual](#virtual-service-account) | Expressa e personalizada, abril de 2017 e posterior | Essa é a opção usada para todas as instalações expressas, exceto para instalações em um controlador de domínio. Ela é a opção padrão para instalações personalizadas, a menos que outra opção seja usada. |
| [Conta de Serviço Gerenciado de Grupo](#group-managed-service-account) | Personalizada, abril de 2017 e posterior | Se você usa um SQL Server remoto, é recomendável usar uma conta de serviço gerenciado de grupo. |
| [Conta de usuário](#user-account) | Expressa e personalizada, abril de 2017 e posterior | Uma conta de usuário prefixada com AAD_ só é criada durante a instalação quando instalada no Windows Server 2008 e quando instalada em um controlador de domínio. |
| [Conta de usuário](#user-account) | Expressa e personalizada, março de 2017 e versões anteriores | Uma conta local prefixada com AAD_ é criada durante a instalação. Ao usar a instalação personalizada, outra conta pode ser especificada. |

Se você usar o Connect com um build de março de 2017 ou anterior, a senha da conta de serviço não deverá ser redefinida, pois o Windows destruirá as chaves de criptografia por motivos de segurança. Você não pode alterar a conta para nenhuma outra conta sem reinstalar o Azure AD Connect. Se você atualizar para um build de abril de 2017 ou posterior, haverá suporte para alterar a senha na conta de serviço, mas você não poderá alterar a conta usada.

> [!Important]
> Você só pode definir a conta de serviço na primeira instalação. Não há suporte para alterar a conta de serviço após a conclusão da instalação.

Essa é uma tabela das opções padrão, recomendadas e com suporte para a conta de serviço de sincronização.

Legenda:

- **Negrito** indica a opção padrão e, na maioria dos casos, a opção recomendada.
- *Itálico* indica a opção recomendada quando ela não é a opção padrão.
- 2008 – opção padrão quando instalado no Windows Server 2008
- Não negrito – opção com suporte
- Conta local – conta de usuário local no servidor
- Conta do domínio – conta de usuário do domínio
- sMSA – [conta de serviço gerenciado autônomo](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA – [conta de serviço gerenciado de grupo](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Personalizado | SQL remoto</br>Personalizado |
| --- | --- | --- | --- |
| **computador autônomo/de grupo de trabalho** | Sem suporte | **VSA**</br>Conta local (2008)</br>Conta local |  Sem suporte |
| **computador associado ao domínio** | **VSA**</br>Conta local (2008) | **VSA**</br>Conta local (2008)</br>Conta local</br>Conta do domínio</br>sMSA, gMSA | **gMSA**</br>Conta do domínio |
| **Controlador de domínio** | **Conta do domínio** | *gMSA*</br>**Conta do domínio**</br>sMSA| *gMSA*</br>**Conta do domínio**|

#### <a name="virtual-service-account"></a>Conta de serviço virtual
Uma conta de serviço virtual é um tipo especial de conta que não tem uma senha e é gerenciada pelo Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

O VSA destina-se a ser usado em cenários em que o mecanismo de sincronização e o SQL estão no mesmo servidor. Se você usar um SQL server remoto, é recomendável em vez disso usar uma [conta de serviço gerenciado de grupo](#managed-service-account).

Este recurso requer o Windows Server 2008 R2 ou posterior. Se você instalar o Azure AD Connect no Windows Server 2008, a instalação voltará a usar uma [conta de usuário](#user-account) em vez disso.

#### <a name="group-managed-service-account"></a>Conta de serviço gerenciado de grupo
Se você usa um SQL Server remoto, é recomendável usar uma **conta de serviço gerenciado de grupo**. Para obter mais informações sobre como preparar o Active Directory para a conta de serviço gerenciado de grupo, consulte [Visão geral de contas de serviço gerenciado de grupo](https://technet.microsoft.com/library/hh831782.aspx).

Para usar essa opção, na página [Instalar componentes necessários](active-directory-aadconnect-get-started-custom.md#install-required-components), selecione **Usar uma conta de serviço existente** e selecione **Conta de serviço gerenciado**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
Também há suporte para o uso de uma [conta de serviço gerenciado autônomo](https://technet.microsoft.com/library/dd548356.aspx). No entanto, essas contas podem ser usadas apenas no computador local e não há nenhum benefício em usá-las no lugar da conta de serviço virtual padrão.

Este recurso requer o Windows Server 2012 ou posterior. Se você precisar usar um sistema operacional mais antigo e usar SQL remoto, você deverá usar uma [conta de usuário](#user-account).

#### <a name="user-account"></a>Conta de usuário
Uma conta de serviço local é criada pelo assistente de instalação (a menos que você especifique a conta a ser usada em configurações personalizadas). A conta prefixada com **AAD_** é usada com o serviço de sincronização real para ser executada como. Se você instalar o Azure AD Connect em um controlador de domínio, a conta é criada no domínio. A conta de serviço **AAD_** deve estar localizada no domínio se:
   - você usa um servidor remoto executando o SQL Server
   - você usa um proxy que exija autenticação

![Conta de serviço de sincronização](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma senha longa complexa que não expira.

Essa conta é usada para armazenar as senhas das outras contas de maneira segura. As senhas dessas outras contas são armazenadas criptografadas no banco de dados. As chaves privadas das chaves de criptografia são protegidas com a criptografia de chave secreta dos serviços de criptografia usando a DPAPI (API de Proteção de Dados) do Windows.

Se você usar um SQL Server completo, a conta de serviço será o DBO do banco de dados criado para o mecanismo de sincronização. O serviço não funcionará conforme esperado com qualquer outra permissão. Um logon do SQL também é criado.

A conta também recebe permissões para arquivos, chaves do registro e outros objetos relacionados ao mecanismo de sincronização.

### <a name="azure-ad-service-account"></a>Conta de serviço do AD do Azure
Uma conta no Azure AD é criada para uso do serviço de sincronização. Essa conta pode ser identificada com seu nome de exibição.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

O nome do servidor em que a conta é usada em pode ser identificado na segunda parte do nome do usuário. Na figura, nome do servidor é FABRIKAMCON. Se você tiver servidores de teste, cada servidor terá sua própria conta.

A conta de serviço é criada com uma senha longa e complexa que não expira. Ela recebe uma função especial **Contas de sincronização de diretório** que tem somente permissões para executar tarefas de sincronização de diretório. Essa função interna especial não pode ser concedida fora do assistente do Azure AD Connect. O Portal do Azure mostra essa conta com a função **Usuário**.

Há um limite de 20 contas de serviço de sincronização no Azure AD. Para obter a lista de contas de serviço do Azure AD existentes no seu Azure AD, execute o seguinte cmdlet do PowerShell do Azure AD: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Para remover contas de serviço do Azure AD não utilizadas, execute o seguinte cmdlet do PowerShell do Azure AD:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](../active-directory-aadconnect.md).

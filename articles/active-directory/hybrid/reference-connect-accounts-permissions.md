---
title: 'Azure AD Connect: contas e permissões | Microsoft Docs'
description: Este tópico descreve as contas usadas e criadas e as permissões necessárias.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f7466931537745fb188a3bdb05646bff19912e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466245"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Contas e permissões

## <a name="accounts-used-for-azure-ad-connect"></a>Contas usadas para o Azure AD Connect

![visão geral das contas](media/reference-connect-accounts-permissions/account5.png)

O Azure AD Connect usa 3 contas para sincronizar informações do Active Directory do Windows Server ou local no Azure Active Directory.  Essas contas são:

- **Conta do AD DS Connector**: usada para ler / gravar informações no Active Directory do Windows Server

- **Conta de serviço ADSync**: usado para executar o serviço de sincronização e acessar o banco de dados SQL

- **Conta de conector do AD do Azure**: usado para gravar informações no AD do Azure

Além desses três contas usadas para executar o Azure AD Connect, você precisará também as seguintes contas adicionais para instalar o Azure AD Connect.  Estes são:

- **Conta do administrador local**: o administrador que está instalando o Azure AD Connect e que tem permissões de Administrador local no computador.

- **Conta de administrador corporativo do AD DS**: usada opcionalmente para criar a "conta do Conector AD DS" acima.

- **Conta de administrador global do Azure AD**: usada para criar a conta do Azure AD Connector e configurar o Azure AD.  Você pode exibir contas de administrador global no portal do Azure.  Consulte [listar atribuições de função do Azure ad](../../active-directory/roles/view-assignments.md).

- **Conta SA do SQL (opcional)** : usada para criar o banco de dados do ADSync ao usar a versão completa do SQL Server.  Esse SQL Server pode ser local ou remoto para a instalação do Azure AD Connect.  Essa conta pode ser a mesma conta do administrador corporativo.  O provisionamento do banco de dados agora pode ser realizado fora da banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário do banco de dados.  Para obter informações sobre isso, consulte [instalar o Azure AD Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Do build 1.4.###.# em diante, não há mais suporte para usar um admin corporativo ou uma conta do administrador de domínio como a conta do Conector do AD DS.  Se tentar entrar em uma conta que seja um admin corporativo ou administrador de domínio ao especificar **usar conta existente**, você receberá um erro.

> [!NOTE]
> Há suporte para gerenciar as contas administrativas usadas no Azure AD Connect de uma floresta administrativa ESAE (também conhecida como "floresta vermelha").
> As florestas administrativas dedicadas permitem que as organizações hospedem contas administrativas, estações de trabalho e grupos em um ambiente que tenha controles de segurança mais fortes do que o ambiente de produção.
> Para saber mais sobre florestas administrativas dedicadas, confira [Abordagem de design de floresta administrativa do ESAE](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> A função de Administrador global não é necessária após a configuração inicial e a única conta necessária será a conta de função de **Contas de Sincronização de Diretório**. Isso não significa, necessariamente, que você desejará apenas remover a conta com a função de Administrador global. É melhor alterar a função para uma função menos poderosa, pois a remoção total da conta pode apresentar problemas se você precisar executar o assistente novamente. Ao reduzir o privilégio da função, você sempre poderá elevá-lo novamente se precisar utilizar o assistente do Azure AD Connect outra vez. 

## <a name="installing-azure-ad-connect"></a>Instalando o Azure AD Connect
O assistente de instalação do Azure AD Connect oferece dois caminhos diferentes:

* Nas Configurações Expressas, o assistente exige mais privilégios.  Isso é assim para que ele possa definir a sua configuração facilmente, sem a necessidade de criar usuários ou configurar permissões.
* Nas Configurações Personalizadas, o assistente oferece mais escolhas e opções. No entanto, há algumas situações em que é necessário que você tenha as permissões corretas.



## <a name="express-settings-installation"></a>Instalação das configurações expressas
As configurações expressas, o Assistente de instalação solicita o seguinte:

  - Credenciais de administrador de empresa do AD DS
  - Credenciais de Administrador Global do AD do Azure

### <a name="ad-ds-enterprise-admin-credentials"></a>Credenciais de administrador de empresa do DS do AD
A conta de administrador de empresa do AD DS é usada para configurar o seu Active Directory no local. Essas credenciais são usadas apenas durante a instalação e não são usadas após a conclusão da instalação. O Admin Corporativo e não Administrador de Domínio, deve garantir que as permissões no Active Directory possam ser definidas em todos os domínios.

Se você estiver atualizando o DirSync, as credenciais de administrador corporativo do AD DS serão usadas para redefinir a senha da conta usada pelo DirSync. Você também precisa de credenciais de Administrador Global do Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Credenciais de Administrador Global do Azure AD
Essas credenciais são usadas apenas durante a instalação e não são usadas após a conclusão da instalação. Ele é usado para criar a conta do Conector do Azure AD usada para sincronizar alterações no Azure AD. A conta também habilita a sincronização como um recurso no Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Permissões necessárias para a conta do AD DS conector para configurações expressas
A conta do Conector do AD DS é criada para leitura e gravação no Windows Server AD e possui as seguintes permissões quando criada por configurações expressas:

| Permissão | Usado para |
| --- | --- |
| <li>Replicar alterações de diretório</li><li>Replicar todas as alterações de diretório |Sincronização de hash de senha |
| Ler/Gravar todas as propriedades Usuário |Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades iNetOrgPerson |Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades Grupo |Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades Contato |Importação e Exchange híbrido |
| Redefinir senha |Preparação para habilitar write-back de senha |

### <a name="express-installation-wizard-summary"></a>Resumo do Assistente para instalação expressa

![Instalação expressa](./media/reference-connect-accounts-permissions/express.png)

A seguir está um resumo das páginas do assistente de instalação expressa, as credenciais coletadas e para que são usadas.

| Página do assistente | Credenciais coletadas | Permissões necessárias | Usadas para |
| --- | --- | --- | --- |
| N/D |Usuário que está executando o assistente de instalação |Administrador do servidor local |<li>Cria a conta de serviço ADSync, que é usada para executar o serviço de sincronização. |
| Conecte-se ao AD do Azure |Credenciais de diretório do AD do Azure |Função de administrador global no AD do Azure |<li>Habilite a sincronização no diretório do AD do Azure.</li>  <li>Criação da conta do Conector do Azure AD que é usada para operações de sincronização contínuas no AD do Azure.</li> |
| Conectar-se ao AD DS |Credenciais do Active Directory local |Membro do grupo EA (Administradores de Empresa) no Active Directory |<li>Cria o conta do conector do AD DS no Active Directory e concede permissões a ele. Essa conta criada é usada para ler e gravar informações de diretório durante a sincronização.</li> |


## <a name="custom-installation-settings"></a>Configurações de instalação personalizada

Com a instalação de configurações personalizadas, o assistente oferece mais escolhas e opções. 

### <a name="custom-installation-wizard-summary"></a>Resumo do Assistente de instalação personalizada

A seguir apresentamos um resumo das páginas do assistente de instalação personalizado, as credenciais coletadas e para que são usadas.

![Captura de tela que mostra as páginas do assistente de instalação personalizada.](./media/reference-connect-accounts-permissions/customize.png)

| Página do assistente | Credenciais coletadas | Permissões necessárias | Usadas para |
| --- | --- | --- | --- |
| N/D |Usuário que está executando o assistente de instalação |<li>Administrador do servidor local</li><li>Para usar um SQL Server completo, o usuário deve ser o administrador do sistema (SA) no SQL</li> |Por padrão, cria a conta local que é usada como conta de serviço do mecanismo de sincronização. A conta é criada somente quando o administrador não define uma conta específica. |
| Instalar serviços de sincronização, opção de conta de serviço |Credenciais de conta de usuário local ou AD |Usuário, permissões são concedidas pelo assistente de instalação |Se o administrador especificar uma conta, essa conta será usada como a conta de serviço para o serviço de sincronização. |
| Conecte-se ao AD do Azure |Credenciais de diretório do AD do Azure |Função de administrador global no AD do Azure |<li>Habilite a sincronização no diretório do AD do Azure.</li>  <li>Criação da conta do Conector do Azure AD que é usada para operações de sincronização contínuas no AD do Azure.</li> |
| Conectar seus diretórios |Credenciais do Active Directory para cada floresta que é conectada ao Azure AD local |As permissões dependem de quais recursos você habilita e podem ser encontradas em Criar a conta do Conector do AD DS |Essa conta é usada para ler e gravar informações de diretório durante a sincronização. |
| Servidores do AD FS |Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de conexão do usuário que executa o assistente não são suficientes para conectar-se |Administrador de domínio |Instalação e configuração da função de servidor do AD FS. |
| Servidores proxy de aplicativo Web |Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de conexão do usuário que executa o assistente não são suficientes para conectar-se |Administrador local no computador de destino |Instalação e configuração da função de servidor WAP. |
| Credenciais de confiança de proxy |As credenciais de confiança do serviço de federação (as credenciais que o proxy usa para se registrar para um certificado de confiança do FS |Conta de domínio que é administrador local do servidor do AD FS |Registro inicial de certificado de confiança FS-WAP. |
| Página da conta de serviço do AD FS, "Usar uma opção de conta de usuário de domínio" |Credenciais de conta de usuário do AD |Usuário de domínio |A conta de usuário do Azure AD cujas credenciais são fornecidas é usada como a conta de conexão do serviço do AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Criar a conta do conector do AD DS

>[!IMPORTANT]
>Um novo módulo do PowerShell nomeado ADSyncConfig.psm1 foi introduzido com o build **1.1.880.0** (lançado em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões do Active Directory corretas para a conta do conector do Azure AD.
>
>Para saber mais, confira [Azure AD Connect: configurar permissão da Conta do Conector AD DS](how-to-connect-configure-ad-ds-connector-account.md)

A conta que você especifica na página **Conecte seus diretórios** deve estar presente no Active Directory antes da instalação.  O Azure AD Connect versão 1.1.524.0 e posterior tem a opção de permitir que o assistente do Azure AD Connect crie a **conta do Conector AD DS** usada para se conectar ao Active Directory.  

Ela também deve ter as permissões necessárias concedidas. O Assistente de instalação não verifica as permissões, e os problemas são encontrados somente durante a sincronização.

As permissões de que você precisa dependem dos recursos opcionais que habilitar. Se você tiver vários domínios, as permissões devem ser concedidas para todos os domínios na floresta. Se você não habilitar esses recursos, as permissões padrão **Usuário de domínio** serão suficientes.

| Recurso | Permissões |
| --- | --- |
| recurso ms-DS-ConsistencyGuid |Permissões de gravação para o atributo ms-DS-ConsistencyGuid documentado em [Conceitos de Design - Usar ms-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de senha |<li>Replicar alterações de diretório</li>  <li>Replicar todas as alterações de diretório |
| Implantação híbrida do Exchange |Permissões de gravação para os atributos documentados em [Write-back híbrido do Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para usuários, grupos e contatos. |
| Pasta pública do Exchange Mail |Permissões de leitura para os atributos documentados na [Pasta pública do Exchange Mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| write-back de senha |Permissões de gravação para os atributos documentados em [Introdução ao gerenciamento de senhas](../authentication/tutorial-enable-sspr-writeback.md) para usuários. |
| Write-back de dispositivo |Permissões concedidas com um script do PowerShell, conforme descrito em [Write-back do dispositivo](how-to-connect-device-writeback.md). |
| Write-back de grupo |Permite o Write-back de **Microsoft 365 grupos** para uma floresta com o Exchange instalado.|

## <a name="upgrade"></a>Atualizar
Ao atualizar de uma versão do Azure AD Connect para uma nova versão, você precisa das seguintes permissões:

>[!IMPORTANT]
>Começando com o build 1.1.484, o Azure AD Connect introduziu um bug de regressão que requer permissões de sysadmin para atualizar o banco de dados SQL.  Esse bug foi corrigido no build 1.1.647.  Se você estiver atualizando para esse build, precisará de permissões de sysadmin.  Permissões de DBO não são suficientes.  Se você tentar atualizar a conexão do Azure AD sem ter permissões de sysadmin, a atualização falhará e o Azure AD Connect deixará de funcionar corretamente depois.  A Microsoft está ciente desse problema e está trabalhando para corrigi-lo.


| Principal | Permissões necessárias | Usado para |
| --- | --- | --- |
| Usuário que está executando o assistente de instalação |Administrador do servidor local |Binários de atualização. |
| Usuário que está executando o assistente de instalação |Membro do ADSyncAdmins |Fazer alterações em Regras de Sincronização e outras configurações. |
| Usuário que está executando o assistente de instalação |Se você usar um SQL Server completo: DBO (ou semelhante) do banco de dados do mecanismo de sincronização |Fazer alterações no nível de banco de dados, como a atualização de tabelas com novas colunas. |

## <a name="more-about-the-created-accounts"></a>Mais informações sobre as contas criadas
### <a name="ad-ds-connector-account"></a>Conta do AD DS conector
Se você usar configurações expressas, uma conta será criada no Active Directory e será usada para sincronização. A conta criada está localizada no domínio raiz da floresta no contêiner Usuários e tem seu nome prefixado com **MSOL_** . A conta é criada com uma senha longa complexa que não expira. Se você tiver uma política de senha em seu domínio, verifique se senhas longas e complexas são permitidas para esta conta.

![Conta do AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Se você usar configurações personalizadas, você será responsável por criar a conta antes de iniciar a instalação.  Confira Criar a conta do conector do AD DS.

### <a name="adsync-service-account"></a>Conta do serviço ADSync
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
- sMSA – [conta de serviço gerenciado autônomo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA – [conta de serviço gerenciado de grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Personalizado | SQL remoto</br>Personalizado |
| --- | --- | --- | --- |
| **computador associado ao domínio** | **VSA**</br>Conta local (2008) | **VSA**</br>Conta local (2008)</br>Conta local</br>Conta do domínio</br>sMSA, gMSA | **gMSA**</br>Conta do domínio |
| **Controlador de domínio** | **Conta do domínio** | *gMSA*</br>**Conta do domínio**</br>sMSA| *gMSA*</br>**Conta do domínio**|

#### <a name="virtual-service-account"></a>Conta de serviço virtual
Uma conta de serviço virtual é um tipo especial de conta que não tem uma senha e é gerenciada pelo Windows.

![Captura de tela que mostra a VSA (conta de serviço virtual).](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

O VSA destina-se a ser usado em cenários em que o mecanismo de sincronização e o SQL estão no mesmo servidor. Se você usar um SQL server remoto, é recomendável usar uma conta de serviço gerenciado de grupo.

Este recurso requer o Windows Server 2008 R2 ou posterior. Se você instalar o Azure AD Connect no Windows Server 2008, a instalação voltará a usar uma [conta de usuário](#user-account) em vez disso.

#### <a name="group-managed-service-account"></a>Conta de serviço gerenciado de grupo
Se você usa um SQL Server remoto, é recomendável usar uma **conta de serviço gerenciado de grupo**. Para obter mais informações sobre como preparar o Active Directory para a conta de serviço gerenciado de grupo, consulte [Visão geral de contas de serviço gerenciado de grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

Para usar essa opção, na página [Instalar componentes necessários](how-to-connect-install-custom.md#install-required-components), selecione **Usar uma conta de serviço existente** e selecione **Conta de serviço gerenciado**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Também há suporte para o uso de uma [conta de serviço gerenciado autônomo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)). No entanto, essas contas podem ser usadas apenas no computador local e não há nenhum benefício em usá-las no lugar da conta de serviço virtual padrão.

Este recurso requer o Windows Server 2012 ou posterior. Se você precisar usar um sistema operacional mais antigo e usar SQL remoto, você deverá usar uma [conta de usuário](#user-account).

#### <a name="user-account"></a>Conta de usuário
Uma conta de serviço local é criada pelo assistente de instalação (a menos que você especifique a conta a ser usada em configurações personalizadas). A conta prefixada com **AAD_** é usada com o serviço de sincronização real para ser executada como. Se você instalar o Azure AD Connect em um controlador de domínio, a conta é criada no domínio. A conta de serviço **AAD_** deve estar localizada no domínio se:
   - você usa um servidor remoto executando o SQL Server
   - você usa um proxy que exija autenticação

![Conta de serviço de sincronização](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma senha longa complexa que não expira.

Essa conta é usada para armazenar as senhas das outras contas de maneira segura. As senhas dessas outras contas são armazenadas criptografadas no banco de dados. As chaves privadas das chaves de criptografia são protegidas com a criptografia de chave secreta dos serviços de criptografia usando a DPAPI (API de Proteção de Dados) do Windows.

Se você usar um SQL Server completo, a conta de serviço será o DBO do banco de dados criado para o mecanismo de sincronização. O serviço não funcionará conforme esperado com qualquer outra permissão. Um logon do SQL também é criado.

A conta também recebe permissões para arquivos, chaves do registro e outros objetos relacionados ao mecanismo de sincronização.

### <a name="azure-ad-connector-account"></a>Conta de conector do AD do Azure
Uma conta no Azure AD é criada para uso do serviço de sincronização. Essa conta pode ser identificada com seu nome de exibição.

![Captura de tela que mostra a conta do Azure AD.](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

O nome do servidor em que a conta é usada em pode ser identificado na segunda parte do nome do usuário. Na imagem, o nome do servidor é DC1. Se você tiver servidores de teste, cada servidor terá sua própria conta.

A conta é criada com uma senha longa complexa que não expira. Ela recebe uma função especial **Contas de sincronização de diretório** que tem somente permissões para executar tarefas de sincronização de diretório. Essa função interna especial não pode ser concedida fora do assistente do Azure AD Connect. O Portal do Azure mostra essa conta com a função **Usuário**.

Há um limite de 20 contas de serviço de sincronização no Azure AD. Para obter a lista de contas de serviço do Azure AD existentes no seu Azure AD, execute o seguinte cmdlet do PowerShell do Azure AD: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Para remover contas de serviço do Azure AD não utilizadas, execute o seguinte cmdlet do PowerShell do Azure AD:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Para usar os comandos do PowerShell acima, instale o [módulo do Azure Active Directory PowerShell para Graph](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) e conecte-se à sua instância do Azure AD usando o [Connect-AzureAD](/powershell/module/azuread/connect-azuread)

Para obter informações adicionais sobre como gerenciar ou redefinir a senha para a conta do Azure AD Connector, confira [Gerenciar a conta do Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>documentação relacionada
Se você não leu a documentação em [Integrando suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md), a tabela a seguir fornece links para tópicos relacionados.

|Tópico |Link|  
| --- | --- |
|Baixar o Azure AD Connect | [Baixar o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar usando as Configurações expressas | [Instalação expressa do Azure AD Connect](how-to-connect-install-express.md)|
|Instalar usando Configurações personalizadas | [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)|
|Atualizar do DirSync | [Atualizar a partir da ferramenta de sincronização do AD do Azure (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verifique a instalação e atribua licenças](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).

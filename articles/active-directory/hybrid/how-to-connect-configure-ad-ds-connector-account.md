---
title: 'Azure AD Connect: Configurar as Permissões de Conta do AD DS | Microsoft Docs'
description: Este documento fornece detalhes sobre como configurar a conta do conector do AD DS Connect com o novo módulo do ADSyncConfig PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62bfc528886767bc09159ca2a2696c8c9264b307
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349932"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Configurar as permissões de Conta do AD DS Connector 

O novo módulo do PowerShell chamado [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) foi introduzido com o build 1.1.880.0 (lançado em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões do Active Directory corretas para sua implantação do Azure AD Connect. 

## <a name="overview"></a>Visão geral 
Os seguintes cmdlets do PowerShell pode ser usado para configurar as permissões do Active Directory da conta do conector do AD DS, para cada recurso que você seleciona habilitar no Azure AD Connect. Para evitar problemas, você deve preparar permissões do Active Directory com antecedência sempre desejar instalar o Azure AD Connect usando uma conta de domínio personalizado para se conectar à sua floresta. Esse módulo ADSyncConfig também pode ser usado para configurar permissões após a implantação do Azure AD Connect.

![visão geral da conta do ad ds](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Para a instalação do Azure AD Connect Express, uma conta gerada automaticamente (MSOL_nnnnnnnnnn) é criada no Active Directory com todas as permissões necessárias, portanto, não há necessidade de usar este módulo ADSyncConfig, a menos que você bloqueou permissões herdadas em unidades organizacionais ou em objetos específicos do Active Directory que você deseja sincronizar com o Azure AD. 
 
### <a name="permissions-summary"></a>Resumo de permissões 
A tabela a seguir fornece um resumo das permissões necessárias nos objetos do AD: 

| Recurso | Permissões |
| --- | --- |
| recurso ms-DS-ConsistencyGuid |Permissões de leitura e gravação para o atributo ms-DS-ConsistencyGuid documentado em [Conceitos de Design – Usar ms-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de senha |<li>Replicar alterações de diretório</li>  <li>Replicar todas as alterações de diretório |
| Implantação híbrida do Exchange |Permissões de leitura e gravação para os atributos documentados em [Write-back híbrido do Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para usuários, grupos e contatos. |
| Pasta pública do Exchange Mail |Permissões de leitura para os atributos documentados na [Pasta pública do Exchange Mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| write-back de senha |Permissões de leitura e gravação para os atributos documentados em [Introdução ao gerenciamento de senhas](../authentication/tutorial-enable-sspr-writeback.md) para usuários. |
| Write-back de dispositivo |Permissões de leitura e gravação para objetos de dispositivo e contêineres documentados em [write-back de dispositivo](how-to-connect-device-writeback.md). |
| Write-back de grupo |Ler, criar, atualizar e excluir objetos de grupo para **grupos do Office 365** sincronizados.|

## <a name="using-the-adsyncconfig-powershell-module"></a>Usando o módulo ADSyncConfig PowerShell 
O módulo ADSyncConfig requer o [Ferramentas de Administração de Servidor Remoto (RSAT) para o AD DS](/windows-server/remote/remote-server-administration-tools), pois ele depende do módulo do PowerShell do AD DS e ferramentas. Para instalar o RSAT para o AD DS, abra uma janela do Windows PowerShell com “Executar como administrador” e execute: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configurar](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Você também pode copiar o arquivo **C:\Arquivos de Programas\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** para um controlador de domínio que já tem as Ferramentas de Administração de Servidor Remoto para o AD DS instalado e usar este módulo do PowerShell de lá.

Para começar a usar o ADSyncConfig, você precisará carregar o módulo em uma janela do Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Para verificar todos os cmdlets incluídos neste módulo, você pode digitar:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Verificação](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Cada cmdlet tem os mesmos parâmetros de entrada de conta de conector do AD DS e um comutador de AdminSDHolder. Para especificar sua conta do conector do AD DS, você pode fornecer o nome da conta e o domínio ou apenas a conta de nome diferenciado (DN),

por exemplo,

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

ou,

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Substitua `<ADAccountName>`, `<ADDomainName>` e `<ADAccountDN>` pelos valores adequados para seu ambiente.

Caso você não quiser modificar permissões no contêiner AdminSDHolder, use a opção `-SkipAdminSdHolders`. 

Por padrão, todos os cmdlets de permissões do conjunto de tentar configurar permissões do AD DS na raiz de cada domínio na floresta, que significa que o usuário que executa a sessão do PowerShell requer direitos de administrador de domínio em cada domínio na floresta.  Devido a esse requisito, é recomendável usar um administrador de empresa da raiz da floresta. Se sua implantação do Azure AD Connect tem vários conectores do AD DS, ele será necessário para executar o mesmo cmdlet em cada floresta que tenha um conector do AD DS. 

Você também pode definir permissões em um objeto específico de UO ou o AD DS usando o parâmetro `-ADobjectDN` seguido o DN do objeto de destino no qual você deseja definir permissões. Ao usar um destino ADobjectDN, o cmdlet definirá permissões nesse objeto e não no domínio raiz ou contêiner AdminSDHolder. Esse parâmetro pode ser útil quando você tiver determinadas UOs ou objetos do AD DS que a herança de permissão tiver desabilitada (consulte Localizar objetos AD DS com a herança de permissão desabilitada) 

As exceções para esses parâmetros comuns são as `Set-ADSyncRestrictedPermissions` cmdlet que são usadas para definir as permissões na conta de conector do AD DS em si, e o `Set-ADSyncPasswordHashSyncPermissions` cmdlet, pois as permissões necessárias para a sincronização de Hash de senha só são definidas na raiz do domínio, portanto, esse cmdlet não inclui o `-ObjectDN` ou `-SkipAdminSdHolders` parâmetros.

### <a name="determine-your-ad-ds-connector-account"></a>Determine sua conta do Conector AD DS 
No caso do Azure AD Connect já estar instalado e você desejar verificar o que é a conta de conector do AD DS atualmente em uso pelo Azure AD Connect, você pode executar o cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Localize os objetos do AD DS com a herança de permissão desabilitada 
Caso você deseje verificar se há qualquer objeto do AD DS com herança de permissão desabilitado, você pode executar: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Por padrão, esse cmdlet só procurará as UOs com herança desabilitada, mas você pode especificar outras classes de objeto do AD DS no `-ObjectClass` parâmetro ou use ' *' para todos os objeto classes, da seguinte maneira: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Exibir permissões do AD DS de um objeto 
Você pode usar o cmdlet abaixo para exibir a lista de permissões definidas no momento em um objeto do Active Directory fornecendo seu DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Configurar as permissões de Conta do AD DS Connector 
 
### <a name="configure-basic-read-only-permissions"></a>Configurar permissões de somente leitura básicas 
Para definir permissões de somente leitura básicas para a conta do conector do AD DS quando não estiver usando qualquer recurso do Azure AD Connect, execute: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Esse cmdlet definirá as seguintes permissões: 
 

|Type |Nome |Acesso |Aplica-se A| 
|-----|-----|-----|-----|
|Allow |Conta do AD DS conector |Leia todas as propriedades |Objetos de dispositivo descendente| 
|Allow |Conta do AD DS conector|Leia todas as propriedades |Objetos descendentes de InetOrgPerson| 
|Allow |Conta do AD DS conector |Leia todas as propriedades |Objetos de computador descendentes| 
|Allow |Conta do AD DS conector |Leia todas as propriedades |Objetos descendentes de foreignSecurityPrincipal| 
|Allow |Conta do AD DS conector |Leia todas as propriedades |Objetos de grupo descendentes| 
|Allow |Conta do AD DS conector |Leia todas as propriedades |Objetos de usuário descendentes| 
|Allow |Conta do AD DS conector |Leia todas as propriedades |Objetos de contato descendentes| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Configurar permissões do MS-DS-Consistency-Guid 
Para definir permissões para a conta do conector de AD DS ao usar o atributo ms-DS-Consistency-GUID como a âncora de origem (também conhecida como a opção "permitir que o Azure gerencie a âncora de origem para mim"), execute: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

ou; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Esse cmdlet definirá as seguintes permissões: 

|Type |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Allow|Conta do AD DS conector|Propriedade de leitura/gravação|Objetos de usuário descendentes|

### <a name="permissions-for-password-hash-synchronization"></a>Permissões para sincronização de hash de senha 
Para definir permissões para a conta do conector do AD DS ao usar a sincronização de Hash de senha, execute: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Esse cmdlet definirá as seguintes permissões: 

|Type |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Allow |Conta do AD DS conector |Replicar alterações de diretório |Apenas este objeto (raiz de domínio)| 
|Allow |Conta do AD DS conector |Replicar todas as alterações de diretório |Apenas este objeto (raiz de domínio)| 
  
### <a name="permissions-for-password-writeback"></a>Permissões para write-back de senha 
Para definir permissões para a conta do conector do AD DS ao usar a sincronização de Write-back de senha, execute: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Esse cmdlet definirá as seguintes permissões: 

|Type |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Allow |Conta do AD DS conector |Redefinir senha |Objetos de usuário descendentes| 
|Allow |Conta do AD DS conector |Gravar lockoutTime de propriedade |Objetos de usuário descendentes| 
|Allow |Conta do AD DS conector |Gravar pwdLastSet de propriedade |Objetos de usuário descendentes| 

### <a name="permissions-for-group-writeback"></a>Permissões para write-back de grupo 
Para definir permissões para a conta do conector do AD DS ao usar a executar Grupo Writeback: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
ou; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Esse cmdlet definirá as seguintes permissões: 

|Type |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Allow |Conta do AD DS conector |Leitura/gravação genérica |Todos os atributos do grupo do tipo de objeto e subobjetos| 
|Allow |Conta do AD DS conector |Excluir também objetos filho |Todos os atributos do grupo do tipo de objeto e subobjetos| 
|Allow |Conta do AD DS conector |Excluir/Excluir objetos da árvore|Todos os atributos do grupo do tipo de objeto e subobjetos|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Permissões para Exchange híbrido 
Para definir permissões para a conta do conector do AD DS ao usar a implantação híbrida do Exchange, execute: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Esse cmdlet definirá as seguintes permissões:  
 

|Type |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Allow |Conta do AD DS conector |Leitura/gravação de todas as propriedades |Objetos de usuário descendentes| 
|Allow |Conta do AD DS conector |Leitura/gravação de todas as propriedades |Objetos descendentes de InetOrgPerson| 
|Allow |Conta do AD DS conector |Leitura/gravação de todas as propriedades |Objetos de grupo descendentes| 
|Allow |Conta do AD DS conector |Leitura/gravação de todas as propriedades |Objetos de contato descendentes| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Permissões para pastas públicas do Exchange Mail (visualização) Permissions para Exchange Mail Public Folders (Preview) 
Para definir permissões para a conta do conector do AD DS ao usar o recurso pastas públicas do Exchange Mail, execute: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Esse cmdlet definirá as seguintes permissões: 

|Type |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Allow |Conta do AD DS conector |Leia todas as propriedades |Objetos PublicFolder descendentes| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Restringir as permissões de Conta do AD DS Connector 
Este script do PowerShell apertará as permissões para a conta de conector do AD fornecido como um parâmetro. Reforçar as permissões envolve as seguintes etapas: 

- Desabilite herança no objeto especificado 
- Remova todas as ACEs no objeto específico, exceto as ACEs específicas ao SELF, pois queremos manter as permissões padrão intactas quando se trata de para si mesmo. 
 
  O parâmetro -ADConnectorAccountDN é a conta do AD cujas permissões precisam ser reforçadas. Isso normalmente é a conta de domínio MSOL_nnnnnnnnnnnn que está configurada no conector do AD DS (consulte a determinar sua conta do conector do AD DS). O parâmetro de credenciamento é necessário para especificar a conta de administrador que tem os privilégios necessários para restringir as permissões do Active Directory no objeto de destino do AD. Esse normalmente é o administrador Corporativo ou de Domínio.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Por exemplo: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Esse cmdlet definirá as seguintes permissões: 

|Type |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Allow |SYSTEM |Controle Total |Este objeto 
|Allow |Administradores Corporativos |Controle Total |Este objeto 
|Allow |Administradores de Domínio |Controle Total |Este objeto 
|Allow |Administradores |Controle Total |Este objeto 
|Allow |Controladores de Domínio Corporativo |Listar Conteúdo |Este objeto 
|Allow |Controladores de Domínio Corporativo |Ler Todas as Propriedades |Este objeto 
|Allow |Controladores de Domínio Corporativo |Permissões de Leitura |Este objeto 
|Allow |Usuários Autenticados |Listar Conteúdo |Este objeto 
|Allow |Usuários Autenticados |Ler Todas as Propriedades |Este objeto 
|Allow |Usuários Autenticados |Permissões de Leitura |Este objeto 

## <a name="next-steps"></a>Próximas etapas
- [Azure AD Connect: Contas e permissões](reference-connect-accounts-permissions.md)
- Instalação expressa[Instalação expressa](how-to-connect-install-express.md)
- [Personalização Instalada](how-to-connect-install-custom.md)
- [Referência de ADSyncConfig](reference-connect-adsyncconfig.md)
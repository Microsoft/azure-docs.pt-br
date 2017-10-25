---
title: "Definir configurações de grupo usando PowerShell no Azure Active Directory | Microsoft Docs"
description: "Como gerenciar as configurações de grupos usando cmdlets do Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 06384d1a1fb7fcc36e9ab97e38c6524a7e260140
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para definir configurações de grupo

> [!IMPORTANT]
> Este conteúdo se aplica apenas a grupos do Office 365. Para saber mais sobre como permitir que os usuários criem grupos de segurança, defina `Set-MSOLCompanySettings -UsersPermissionToCreateGroupsEnabled $True` conforme descrito em [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

As configurações de Grupos do Office 365 são definidas usando um objeto Settings e um SettingsTemplate. Inicialmente, você não verá objetos Settings no seu diretório, pois o diretório foi configurado com as definições padrão. Para alterar as configurações padrão, você deve criar um novo objeto de configurações usando um modelo de configurações. Modelos de configurações são definidos pela Microsoft. Há vários modelos de configurações diferentes. Para definir as configuração de grupo do Office 365 para seu diretório, use o modelo chamado "Group.Unified". Para definir as configurações de grupo do Office 365 em um único grupo, use o modelo chamado "Group.Unified.Guest". Esse modelo é usado para gerenciar o acesso de convidado a um grupo do Office 365. 

Os cmdlets fazem parte do Módulo do Azure Active Directory PowerShell V2. Para obter instruções de como baixar e instalar o módulo no seu computador, confira o artigo [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/) (PowerShell Versão 2 do Azure Active Directory). Você pode instalar a versão 2 do módulo da [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Recuperar um valor de configurações específico
Se você souber o nome da configuração que deseja recuperar, será possível usar o cmdlet abaixo para recuperar o valor atual das configurações. Neste exemplo, recuperaremos o valor de uma configuração chamada "UsageGuidelinesUrl". Você pode ler que mais sobre configurações de diretório e seus nomes mais abaixo neste artigo.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Criar configurações no nível do diretório
Estas etapas criam configurações no nível do diretório, que se aplicam a todos os grupos do Office 365 (Grupos unificados) no diretório.

1. Nos cmdlets DirectorySettings, você precisa especificar a ID de SettingsTemplate que deseja usar. Se você não souber essa ID, esse cmdlet retornará a lista com todos os modelos de configurações:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Essa chamada do cmdlet retorna todos os modelos disponíveis:
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Para adicionar uma URL de diretrizes de uso, primeiro você precisa obter o objeto SettingsTemplate que define o valor de URL da diretriz de uso; ou seja, o modelo de Group.Unified:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Em seguida, crie um novo objeto de configurações com base no modelo:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Então, atualize o valor de diretriz de uso:
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. Por fim, aplique as configurações:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Após a conclusão bem-sucedida, o cmdlet retornará a ID do novo objeto de configuração:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
Aqui estão as configurações definidas no Group.Unified SettingsTemplate.

| **Configuração** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Tipo: booliano<li>Padrão: True |O sinalizador que indica se é permitida a criação de Grupo Unificado no diretório por usuários que não são administradores. |
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: String<li>Padrão: “” |O GUID do grupo de segurança para o qual os membros têm permissão para criar Grupos Unificados, mesmo quando EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: String<li>Padrão: “” |Um link para as Diretrizes de Uso do Grupo. |
|  <ul><li>ClassificationDescriptions<li>Tipo: String<li>Padrão: “” | Uma lista delimitada por vírgulas de descrições de classificação. |
|  <ul><li>DefaultClassification<li>Tipo: String<li>Padrão: “” | A classificação que deve ser usada como a classificação padrão para um grupo, se nenhuma for especificada.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Tipo: String<li>Padrão: “” | Não use. Não implementado. |
| <ul><li>CustomBlockedWordsList<li>Tipo: String<li>Padrão: “” | Não use. Não implementado. |
| <ul><li>EnableMSStandardBlockedWords<li>Tipo: booliano<li>Padrão: “False” | Não use
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tipo: booliano<li>Padrão: False | Booliano que indica se um usuário convidado pode ser um proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tipo: booliano<li>Padrão: True | Booliano que indica se um usuário convidado pode ter acesso ao conteúdo de Grupos Unificados. |
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: String<li>Padrão: “” | A url de um link para as diretrizes de uso do convidado. |
|  <ul><li>AllowToAddGuests<li>Tipo: booliano<li>Padrão: True | Um booliano que indica se há permissão para adicionar convidados a este diretório.|
|  <ul><li>ClassificationList<li>Tipo: String<li>Padrão: “” |Uma lista delimitada por vírgulas de valores de classificação válidos que podem ser aplicados a Grupos Unificados. |


## <a name="read-settings-at-the-directory-level"></a>Ler configurações no nível do diretório
Estas etapas leem configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Leia todas as configurações existentes do diretório:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Esse cmdlet retorna uma lista de todas as configurações de diretório:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Leia todas as configurações para um grupo específico:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Leia todos os valores de configurações de diretório de um objeto de configurações de diretório específico, usando o GUID da ID de Configurações:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Esse cmdlet retorna os nomes e valores nesse objeto de configurações para esse grupo específico:
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Atualizar as configurações para um grupo específico

1. Procurar o modelo de configurações denominado "Groups.Unified.Guest"
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Recupere o objeto de modelo para o modelo Groups.Unified.Guest:
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Crie um novo objeto de configurações do modelo:
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Defina a configuração com o valor necessário:
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Crie a nova configuração para o grupo exigido no diretório:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Atualizar configurações no nível do diretório

Estas etapas atualizam as configurações no nível do diretório, que se aplicam a todos os grupos Unificados no diretório. Estes exemplos pressupõem que já exista um objeto Configurações em seu diretório.

1. Encontre o objeto Configurações existente:
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Atualize o valor:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Atualize a configuração:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Remover configurações no nível do diretório
Esta etapa remove configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Referência de sintaxe de cmdlet
Você pode encontrar mais documentação do PowerShell do Azure Active Directory em [Cmdlets do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Leitura adicional

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

---
title: Definir configurações de grupo usando o PowerShell-Azure AD | Microsoft Docs
description: Como gerenciar as configurações de grupos usando cmdlets do Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ad70aff7e1673681541a1b6b37a0bd50d822fed
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954390"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para definir configurações de grupo

Este artigo contém instruções sobre como usar cmdlets do PowerShell do Azure Active Directory (Azure AD) para criar e atualizar grupos. Esse conteúdo se aplica somente a grupos de Microsoft 365 (às vezes chamados de grupos unificados).

> [!IMPORTANT]
> Algumas configurações exigem uma licença do Azure Active Directory Premium P1. Para obter mais informações, consulte a tabela [Configurações de modelo](#template-settings).

Para saber mais sobre como evitar que usuários não administradores criem grupos de segurança, defina `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` conforme descrito em [Set-MSOLCompanySettings](/powershell/module/msonline/set-msolcompanysettings).

Configurações de grupos de Microsoft 365 são definidas usando um objeto de configurações e um objeto Settingstemplate. Inicialmente, você não verá objetos Settings no seu diretório, pois o diretório foi configurado com as definições padrão. Para alterar as configurações padrão, você deve criar um novo objeto de configurações usando um modelo de configurações. Modelos de configurações são definidos pela Microsoft. Há vários modelos de configurações diferentes. Para definir Microsoft 365 configurações de grupo para seu diretório, use o modelo chamado "Group. Unified". Para definir Microsoft 365 configurações de grupo em um único grupo, use o modelo chamado "Group. Unified. Guest". Este modelo é usado para gerenciar o acesso de convidado a um grupo de Microsoft 365. 

Os cmdlets fazem parte do Módulo do Azure Active Directory PowerShell V2. Para obter instruções de como baixar e instalar o módulo no seu computador, confira o artigo [Azure Active Directory PowerShell Version 2](/powershell/azure/active-directory/overview) (PowerShell Versão 2 do Azure Active Directory). Você pode instalar a versão 2 do módulo da [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de desinstalar qualquer versão mais antiga do módulo Azure Active Directory PowerShell for Graph para Windows PowerShell e instale [Azure Active Directory PowerShell para Graph-versão de visualização pública (posterior a 2.0.0.137) antes de](https://www.powershellgallery.com/packages/AzureADPreview) executar os comandos do PowerShell.

1. Abra o aplicativo Windows PowerShell como um administrador.
2. Desinstale qualquer versão anterior do AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Instale a versão mais recente do AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Criar configurações no nível do diretório
Essas etapas criam configurações no nível do diretório, que se aplicam a todos os grupos de Microsoft 365 no diretório. O cmdlet Get-AzureADDirectorySettingTemplate está disponível apenas no [módulo de versão prévia do Azure AD PowerShell para Graph](https://www.powershellgallery.com/packages/AzureADPreview).

1. Nos cmdlets DirectorySettings, você precisa especificar a ID de SettingsTemplate que deseja usar. Se você não souber essa ID, esse cmdlet retornará a lista com todos os modelos de configurações:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Essa chamada do cmdlet retorna todos os modelos disponíveis:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Microsoft 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Para adicionar uma URL de diretrizes de uso, primeiro você precisa obter o objeto SettingsTemplate que define o valor de URL da diretriz de uso; ou seja, o modelo de Group.Unified:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Em seguida, crie um novo objeto de configurações com base no modelo:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Em seguida, atualize o objeto de configurações com um novo valor. Os dois exemplos abaixo alteram o valor de diretriz de uso e habilitam rótulos de sensibilidade. Defina essas configurações ou qualquer outra configuração no modelo, conforme necessário:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   $Setting["EnableMIPLabels"] = "True"
   ```  
5. Em seguida, aplique a configuração:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Você pode ler os valores usando:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Atualizar configurações no nível do diretório
Para atualizar o valor de UsageGuideLinesUrl no modelo de configuração, leia as configurações atuais do Azure AD, caso contrário, poderíamos acabar substituindo as configurações existentes que não sejam a UsageGuideLinesUrl.

1. Obtenha as configurações atuais do grupo. configurações unificadas:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Verifique as configurações atuais:
   
   ```powershell
   $Setting.Values
   ```
   
   Saída:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               True
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Para remover o valor de UsageGuideLinesUrl, edite a URL para que seja uma cadeia de caracteres vazia:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Salve a atualização no diretório:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Configurações de modelo
Aqui estão as configurações definidas no Group.Unified SettingsTemplate. A menos que indicado o contrário, esses recursos exigem uma licença do Azure Active Directory Premium P1. 

| **Configuração** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Tipo: booliano<li> Padrão: True |O sinalizador que indica se Microsoft 365 criação de grupo é permitida no diretório por usuários não administradores. Essa configuração não requer uma licença do Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: String<li>Padrão: "" |GUID do grupo de segurança para o qual os membros têm permissão para criar grupos de Microsoft 365 mesmo quando EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: String<li>Padrão: "" |Um link para as Diretrizes de Uso do Grupo. |
|  <ul><li>ClassificationDescriptions<li>Tipo: String<li>Padrão: "" | Uma lista delimitada por vírgulas de descrições de classificação. O valor de ClassificationDescriptions somente é válido neste formato:<br>$setting ["ClassificationDescriptions"] = "classificação: descrição, classificação: Descrição"<br>em que a classificação corresponde a uma entrada na classificação.<br>Essa configuração não se aplica quando EnableMIPLabels = = true.|
|  <ul><li>DefaultClassification<li>Tipo: String<li>Padrão: "" | A classificação que deve ser usada como a classificação padrão para um grupo, se nenhuma for especificada.<br>Essa configuração não se aplica quando EnableMIPLabels = = true.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Tipo: String<li>Padrão: "" | Cadeia de caracteres de um comprimento máximo de 64 caracteres que define a Convenção de nomenclatura configurada para grupos de Microsoft 365. Para obter mais informações, consulte [impor uma política de nomenclatura para grupos de Microsoft 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Tipo: String<li>Padrão: "" | Cadeia de caracteres de frases separadas por vírgulas que os usuários não serão autorizados a usar em nomes de grupos ou alias. Para obter mais informações, consulte [impor uma política de nomenclatura para grupos de Microsoft 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Tipo: booliano<li>Padrão: "false" | Não usar
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tipo: booliano<li> Padrão: False | Booliano que indica se um usuário convidado pode ser um proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tipo: booliano<li> Padrão: True | Booliano que indica se um usuário convidado pode ou não ter acesso ao conteúdo de grupos de Microsoft 365.  Essa configuração não requer uma licença do Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: String<li>Padrão: "" | A url de um link para as diretrizes de uso do convidado. |
|  <ul><li>AllowToAddGuests<li>Tipo: booliano<li> Padrão: True | Um booliano que indica se há permissão para adicionar convidados a este diretório. <br>Essa configuração poderá ser substituída e se tornará somente leitura se *EnableMIPLabels* estiver definido como *true* e uma política de convidado estiver associada ao rótulo de sensibilidade atribuído ao grupo.<br>Se a configuração AllowToAddGuests for definida como false no nível da organização, qualquer configuração de AllowToAddGuests no nível do grupo será ignorada. Se você quiser habilitar o acesso de convidado para apenas alguns grupos, deverá definir AllowToAddGuests como true no nível da organização e, em seguida, desabilitá-lo seletivamente para grupos específicos. |
|  <ul><li>ClassificationList<li>Tipo: String<li>Padrão: "" | Uma lista delimitada por vírgulas de valores de classificação válidos que podem ser aplicados a grupos de Microsoft 365. <br>Essa configuração não se aplica quando EnableMIPLabels = = true.|
|  <ul><li>EnableMIPLabels<li>Tipo: booliano<li>Padrão: "false" |O sinalizador que indica se os rótulos de sensibilidade publicados no centro de conformidade Microsoft 365 podem ser aplicados a grupos de Microsoft 365. Para obter mais informações, consulte [atribuir rótulos de sensibilidade para grupos de Microsoft 365](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Exemplo: configurar a política de convidado para grupos no nível do diretório
1. Obtenha todos os modelos de configuração:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Para definir a política de convidado para grupos no nível do diretório, você precisa de grupo. modelo unificado
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Em seguida, crie um novo objeto de configurações com base no modelo:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Em seguida, atualize a configuração AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Em seguida, aplique a configuração:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Você pode ler os valores usando:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Ler configurações no nível do diretório

Se você souber o nome da configuração que deseja recuperar, será possível usar o cmdlet abaixo para recuperar o valor atual das configurações. Neste exemplo, recuperaremos o valor de uma configuração chamada "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Estas etapas leem configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Leia todas as configurações existentes do diretório:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Esse cmdlet retorna uma lista de todas as configurações de diretório:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Leia todas as configurações para um grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Leia todos os valores de configurações de diretório de um objeto de configurações de diretório específico, usando o GUID de ID de configurações:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Esse cmdlet retorna os nomes e valores nesse objeto de configurações para esse grupo específico:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Remover configurações no nível do diretório
Esta etapa remove configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Criar configurações para um grupo específico

1. Procurar o modelo de configurações denominado "Groups.Unified.Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Microsoft 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Recupere o objeto de modelo para o modelo Groups.Unified.Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Crie um novo objeto de configurações do modelo:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Defina a configuração com o valor necessário:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Obtenha a ID do grupo ao qual você deseja aplicar essa configuração:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Crie a nova configuração para o grupo exigido no diretório:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Para verificar as configurações, execute este comando:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Atualizar as configurações para um grupo específico
1. Obtenha a ID do grupo cuja configuração você deseja atualizar:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Recupere a configuração do Grupo:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Atualize a configuração do grupo conforme necessário, por exemplo,
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Em seguida, obtenha a ID da configuração para este grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Você receberá uma resposta semelhante a esta:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Em seguida, você pode definir o novo valor para essa configuração:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Você pode ler o valor da configuração para certificar-se de que ele foi atualizado corretamente:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Referência de sintaxe de cmdlet
Você pode encontrar mais documentação do PowerShell do Azure Active Directory em [Cmdlets do Azure Active Directory](/powershell/azure/active-directory/install-adv2).

## <a name="additional-reading"></a>Leituras adicionais

* [Gerenciando o acesso a recursos com grupos de Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integração de suas identidades locais com o Active Directory do Azure](../hybrid/whatis-hybrid-identity.md)

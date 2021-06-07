---
title: Aplicar uma política de nomenclatura de grupo no Azure Active Directory | Microsoft Docs
description: Como configurar uma política de nomenclatura para grupos do Microsoft 365 no Azure Active Directory
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
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00291907ddea75ba4507779d1226502dc8343aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365694"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Aplicar uma política de nomenclatura em grupos do Microsoft 365 no Azure Active Directory

Para impor convenções de nomenclatura consistentes aos grupos do Microsoft 365 criados ou editados pelos usuários, defina uma política de nomenclatura de grupos para suas organizações no Azure Active Directory (Azure AD). Por exemplo, você pode usar a política de nomenclatura para comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo. Você também pode usar a política de nomenclatura para ajudar a categorizar grupos no catálogo de endereços. Você pode usar a política para bloquear palavras específicas de serem usadas em nomes de grupo e alias.

> [!IMPORTANT]
> Para usar uma política de nomenclatura do Azure AD nos grupos do Microsoft 365, é necessário ter (mas não necessariamente atribuir) uma licença P1 do Azure Active Directory Premium ou uma licença do Azure Active Directory Basic EDU para cada usuário único que é membro de um ou mais daqueles grupos.

A política de nomenclatura é aplicada para criar ou editar grupos criados em cargas de trabalho (por exemplo, Outlook, Microsoft Teams, SharePoint, Exchange ou Planner). Ela é aplicada para o nome do grupo e o alias de grupo. Se você definir uma política de nomenclatura no Azure AD e tiver uma política de nomenclatura de grupos do Exchange, a política do Azure AD será aplicada na sua organização.

Quando a política de nomenclatura de grupo for configurada, será aplicada a novos grupos do Microsoft 365 criados pelos usuários finais. A política de nomenclatura não se aplica a determinadas funções de diretório, como Administrador global ou Administrador de usuários (veja abaixo a lista completa de funções isentadas da política de nomenclatura de grupo). Para os grupos atuais do Microsoft 365, a política não será aplicada imediatamente no momento da configuração. A política será aplicada nesses grupos após os proprietários alterarem os nomes deles.

## <a name="naming-policy-features"></a>Recursos da política de nomenclatura

Você pode impor uma política de nomenclatura para grupos de duas maneiras:

- **Política de nomenclatura de prefixo-sufixo** você pode definir os prefixos ou sufixos que são adicionados automaticamente ao impor uma convenção de nomenclatura nos seus grupos (por exemplo, no nome do grupo "GRP\_JAPAN\_My Group\_Engineering", GRP\_JAPAN\_ é o prefixo e \_Engineering é o sufixo). 

- **Personalizar palavras bloqueadas** você pode carregar um conjunto de palavras bloqueadas específicas para sua organização para que sejam bloqueadas em grupos criados por usuários (por exemplo, "CEO, folha de pagamento, RH").

### <a name="prefix-suffix-naming-policy"></a>Política de nomenclatura de prefixo-sufixo

A estrutura geral de convenção de nomenclatura é 'Prefixo[GroupName]Sufixo'. Embora você possa definir vários prefixos e sufixos, você só pode ter uma instância do [GroupName] na configuração. Os prefixos ou sufixos podem ser cadeias de caracteres fixas ou atributos de usuário como \[Departamento\] que são substituídos com base no usuário que está criando o grupo. O número total permitido para as cadeias de caracteres de prefixo e sufixo combinadas é de 53 caracteres. 

Prefixos e sufixos podem conter caracteres especiais que têm suporte no nome do grupo e o alias de grupo. Caracteres de prefixo ou sufixo que não tenham suporte para o alias de grupo também são aplicados no nome do grupo, mas removidos do alias de grupo. Devido a essa restrição, os prefixos e sufixos aplicados para o nome do grupo podem ser diferentes daquelas aplicado ao alias de grupo. 

#### <a name="fixed-strings"></a>Cadeias de caracteres fixas

Você pode usar cadeias de caracteres para facilitar a verificação e diferenciar os grupos na lista de endereços global e nos links de navegação à esquerda de cargas de trabalho de grupo. Alguns dos prefixos comuns são palavras-chave como "Grp\_Nome", "\#Nome", "\_Nome"

#### <a name="user-attributes"></a>Atributos de usuário

Você pode usar os atributos que podem ajudar você e seus usuários a identificar para qual departamento, escritório ou região geográfica o grupo foi criado. Por exemplo, se você definir sua política de nomenclatura como `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`, e `User’s department = Engineering`, um nome de grupo imposto pode ser "GRP My Group Engineering". Atributos do Azure AD com suporte são \[Departamento\], \[Empresa\], \[Escritório\], \[EstadoOuProvíncia\], \[PaísOuRegião\], \[Título\]. Atributos de usuário sem suporte são tratados como cadeias de caracteres fixas; por exemplo, "\[CódigoPostal\]". Atributos de extensão e atributos personalizados não são suportados.

É recomendável que você use atributos que têm valores preenchidos para todos os usuários em sua organização e não use atributos que têm valores longos.

### <a name="custom-blocked-words"></a>Palavras bloqueadas personalizadas

Uma lista de palavras bloqueadas é uma lista separada por vírgulas de frases a serem bloqueadas em alias e nomes de grupo. Nenhuma pesquisa de subcadeia de caracteres é executada. Uma correspondência exata entre o nome do grupo e uma ou mais das palavras bloqueadas personalizadas é necessária para acionar uma falha. Pesquisa subcadeia de caracteres não é executada de forma que os usuários podem usar palavras comuns como 'Class', mesmo que 'lass' seja uma palavra bloqueada.

Regras de lista de palavras bloqueadas:

- As palavras bloqueadas não diferenciam maiúsculas de minúsculas.
- Quando um usuário digita uma palavra bloqueada como parte de um nome de grupo, ele recebe uma mensagem de erro com a palavra bloqueada.
- Não há nenhuma restrição de caracteres em palavras bloqueadas.
- Há um limite de 5000 frases que podem ser configuradas na lista de palavras bloqueadas. 

### <a name="roles-and-permissions"></a>Funções e permissões

Para configurar a política de nomenclatura, uma das seguintes funções é necessária:
- Administrador global
- Administrador de grupo
- Gravador de diretório


Administradores selecionados podem ser isentos dessas políticas, em todas as cargas de trabalho de grupo e pontos de extremidade, para que eles possam criar grupos usando palavras bloqueadas e com suas próprias convenções de nomenclatura. A seguir estão a lista de funções de administrador isentos da política de nomenclatura de grupos.

- Administrador global
- Administrador de usuários

## <a name="configure-naming-policy-in-azure-portal"></a>Configurar a política de nomenclatura no portal do Azure

1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta de administrador de grupo.
1. Para abrir a página da política de nomenclatura, escolha **Grupos** e **Política de nomenclatura**.

    ![Abrir a página da política de nomenclatura no centro de administração](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Exibir ou editar a política de nomenclatura de prefixo/sufixo

1. Na página **Política de nomenclatura**, escolha **Política de nomenclatura de grupos**.
1. Para exibir ou editar individualmente as políticas de nomenclatura de prefixo ou sufixo atuais, escolha os atributos ou cadeias de caracteres que você deseja aplicar como parte da política de nomenclatura.
1. Para remover um prefixo ou sufixo da lista, escolha o prefixo ou sufixo e, em seguida, escolha **Excluir**. É possível excluir vários itens ao mesmo tempo.
1. Selecione **Salvar** para salvar as alterações e colocar a nova política em vigor.

### <a name="edit-custom-blocked-words"></a>Editar palavras bloqueadas personalizadas

1. Na página **Política de nomenclatura**, escolha **Palavras bloqueadas**.

    ![Editar e carregar uma lista de palavras bloqueadas da política de nomenclatura](./media/groups-naming-policy/blockedwords.png)

1. Para exibir ou editar a lista atual de palavras bloqueadas personalizadas, escolha **Baixar**.
1. Carregue a nova lista de palavras bloqueadas personalizadas escolhendo o ícone de arquivo.
1. Selecione **Salvar** para salvar as alterações e colocar a nova política em vigor.

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de desinstalar qualquer versão anterior do Azure Active Directory PowerShell para o módulo de gráfico para o Windows PowerShell e instale [Azure Active Directory PowerShell para gráfico - versão de visualização pública 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) antes de executar os comandos do PowerShell.

1. Abra o aplicativo Windows PowerShell como um administrador.
2. Desinstale qualquer versão anterior do AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Instale a versão mais recente do AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Se aparecer uma mensagem sobre o acesso a um repositório não confiável, insira **S**. A instalação do novo módulo pode levar alguns minutos.

## <a name="configure-naming-policy-in-powershell"></a>Configurar a política de nomenclatura no PowerShell

1. Abra uma janela do Windows PowerShell em seu computador. Você pode abri-lo sem privilégios elevados.

1. Execute os comandos a seguir para preparar para executar os cmdlets.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Na tela **Entrar na sua conta** que abre, digite sua conta de administrador e senha para conectar-se ao serviço e selecione **Entrar**.

1. Siga as etapas em [cmdlets do Azure Active Directory para definir as configurações de grupo](../enterprise-users/groups-settings-cmdlets.md) para criar configurações de grupo para esta organização.

### <a name="view-the-current-settings"></a>Exiba as configurações atuais

1. Busque a atual política de nomenclatura para exibir as configurações atuais.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Exiba as configurações do grupo atual.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Defina a política de nomenclatura e palavras bloqueadas personalizadas

1. Defina os prefixos e sufixos de nome de grupo no Azure AD PowerShell. Para o recurso funcionar corretamente, [GroupName] deve ser incluído na configuração.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Defina as palavras bloqueadas personalizadas que você deseja restringir. O exemplo a seguir ilustra como você pode adicionar suas próprias palavras personalizadas.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Salve as configurações para a nova política ser efetivada, como no exemplo a seguir.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
É isso. Você definiu sua política de nomenclatura e adicionou suas palavras bloqueadas.

## <a name="export-or-import-custom-blocked-words"></a>Exportar ou importar palavras bloqueadas personalizadas

Para saber mais, consulte o artigo [Cmdlets do Azure Active Directory para definir as configurações de grupo](../enterprise-users/groups-settings-cmdlets.md).

Aqui está um exemplo de script do PowerShell para exportar várias palavras bloqueadas:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Aqui está um exemplo de script do PowerShell para importar várias palavras bloqueadas:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Remover a política de nomenclatura

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remover a política de nomenclatura usando o portal do Azure

1. Na página **Política de nomenclatura**, escolha **Excluir Política**.
1. Depois de confirmar a exclusão, a política de nomenclatura será removida, incluindo toda a política de nomenclatura de prefixo-sufixo e palavras bloqueadas personalizadas.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Remover a política de nomenclatura usando o PowerShell do Azure AD

1. Esvazie os prefixos e sufixos de nome de grupo no PowerShell do Azure AD.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Esvazie as palavras bloqueadas personalizadas.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Salve as configurações.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-microsoft-365-apps"></a>Experiência nos aplicativos Microsoft 365

Após a definição da política de nomenclatura de grupo no Azure AD, quando um usuário cria um grupo em um aplicativo do Microsoft 365, ele vê:

- Uma visualização de nome de acordo com a política de nomenclatura (com prefixos e sufixos) assim que o usuário digitar o nome do grupo
- Se o usuário inserir palavras bloqueadas, ele verá uma mensagem de erro para que possa remover as palavras bloqueadas.

Carga de trabalho | Conformidade
----------- | -------------------------------
Portais do Azure Active Directory | O portal do Azure AD e o portal do Painel de Acesso mostrará nome imposto da política de nomenclatura quando o usuário digita um nome de grupo ao criar ou editar um grupo. Quando um usuário digita uma palavra bloqueada personalizada, uma mensagem de erro com a palavra bloqueada é exibida para que o usuário possa removê-la.
OWA (Outlook Web Access) | O Outlook Web Access mostra o nome imposto da política de nomenclatura quando o usuário digita um nome de grupo ou alias de grupo. Quando um usuário digita uma palavra bloqueada personalizada, uma mensagem de erro é exibida no UI junto com a palavra bloqueada para que o usuário possa removê-la.
Outlook Desktop | Grupos criados no Outlook Desktop são compatíveis com as configurações de política de nomenclatura. O aplicativo Outlook Desktop ainda não mostra a visualização do nome do grupo de imposto e não retorna os erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo. No entanto, a política de nomenclatura é aplicada automaticamente ao criar ou editar um grupo e os usuários veem mensagens de erro se houver palavras bloqueadas personalizadas no nome do grupo ou alias.
Microsoft Teams | Equipes da Microsoft mostra o nome imposto da política de nomenclatura de grupo quando o usuário insere um nome de equipe. Quando um usuário digita uma palavra bloqueada personalizada, uma mensagem de erro é exibida junto com a palavra bloqueada para que o usuário possa removê-la.
SharePoint  |  O SharePoint mostra o nome imposto da política de nomenclatura quando o usuário digita um nome de site ou endereço de email de grupo. Quando um usuário digita uma palavra bloqueada personalizada, uma mensagem de erro é exibida junto com a palavra bloqueada para que o usuário possa removê-la.
Microsoft Stream | O Microsoft Stream mostra o nome imposto da política de nomenclatura de grupo quando o usuário digita um nome de grupo ou alias de email de grupo. Quando um usuário digita uma palavra bloqueada personalizada, uma mensagem de erro é exibida com a palavra bloqueada para que o usuário possa removê-la.
Aplicativo Android e iOS do Outlook | Grupos criados nos aplicativos do Outlook são compatíveis com a política de nomenclatura configurada. O aplicativo móvel do Outlook ainda não mostra a visualização do nome imposto da política de nomenclatura e não retorna erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo. No entanto, a política de nomenclatura é aplicada automaticamente ao clicar em criar/editar e os usuários veem mensagens de erro se houver palavras bloqueadas personalizadas no nome do grupo ou alias.
Aplicativo móvel de grupos | Grupos criados no aplicativo móvel de grupos são compatíveis com a política de nomenclatura. O aplicativo móvel de grupos ainda não mostra a visualização da política de nomenclatura e não retorna erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo. No entanto, a política de nomenclatura é aplicada automaticamente ao criar ou editar um grupo e os usuários veem erros apropriados se houver palavras bloqueadas personalizadas no nome do grupo ou alias.
Planner | Planejador é compatível com a política de nomenclatura. Planejador mostra a visualização de política de nomenclatura ao inserir o nome do plano. Quando um usuário digita uma palavra bloqueada personalizada, uma mensagem de erro é mostrada ao criar o plano.
Dynamics 365 for Customer Engagement | O Dynamics 365 for Customer Engagement é compatível com a política de nomenclatura. O Dynamics 365 for Customer Engagement mostra o nome imposto da política de nomenclatura quando o usuário digita um nome de grupo ou alias de email de grupo. Quando o usuário digita uma palavra bloqueada personalizada, uma mensagem de erro é exibida com a palavra bloqueada para que o usuário possa removê-la.
School Data Sync (SDS) | Grupos criados por meio de SDS estão em conformidade com a política de nomenclatura, mas a política de nomenclatura não é aplicada automaticamente. Os administradores de SDS têm que acrescentar os prefixos e sufixos para nomes de classe para os quais grupos precisam ser criados e, em seguida, carregados em SDS. Caso contrário, a criação ou edição de grupos falhará.
Aplicativo de sala de aula | Grupos criados no aplicativo de sala de aula estão em conformidade com a política de nomenclatura, mas a política de nomenclatura não é aplicada automaticamente, e a visualização de política de nomenclatura não é mostrada para os usuários ao inserir um nome de grupo de sala de aula. Os usuários devem digitar o nome imposto do grupo de sala de aula com prefixos e sufixos. Caso contrário, a operação para criar ou editar o grupo de sala de aula falhará com erros.
Power BI | Os workspaces do Power BI estão em conformidade com a política de nomenclatura.    
Yammer | Quando um usuário conectado ao Yammer com sua conta do Azure Active Directory criar um grupo ou editar o nome de um grupo, o nome do grupo estará em conformidade com a política de nomenclatura. Isso se aplica aos grupos conectados do Microsoft 365 e a todos os outros grupos do Yammer.<br>Se um grupo conectado do Microsoft 365 tiver sido criado antes da política de nomenclatura ter entrado em vigor, o nome do grupo não seguirá automaticamente as políticas de nomenclatura. Quando um usuário editar o nome do grupo, será solicitado que adicione o prefixo e o sufixo.
StaffHub  | As equipes de StaffHub não seguem a política de nomenclatura, mas o grupo do Microsoft 365 subjacente a segue. O nome da equipe StaffHub não aplica os prefixos e sufixos e não verifica as palavras bloqueadas personalizadas. Mas o StaffHub aplica os prefixos e os sufixos e remove as palavras bloqueadas do grupo do Microsoft 365 subjacente.
Exchange PowerShell | Cmdlets do Exchange PowerShell estão em conformidade com a política de nomenclatura. Os usuários recebem mensagens de erro apropriado com prefixos e sufixos sugeridos e palavras bloqueadas personalizadas se eles não seguem a política de nomenclatura no nome do grupo e o alias de grupo (mailNickname).
Cmdlets do Azure Active Directory PowerShell | Cmdlets do Azure Active Directory PowerShell estão em conformidade com a política de nomenclatura. Os usuários recebem mensagens de erro apropriado com prefixos e sufixos sugeridos e palavras bloqueadas personalizadas se eles não seguem a convenção de nomenclatura nos nomes do grupo e o alias de grupo.
Centro de Administração do Exchange | Centro de Administração do Exchange está em conformidade com a política de nomenclatura. Os usuários recebem mensagens de erro apropriado com prefixos e sufixos sugeridos e palavras bloqueadas personalizadas se eles não seguem a convenção de nomenclatura no nome do grupo e o alias de grupo.
Centro de administração do Microsoft 365 | O centro de administração do Microsoft 365 está em conformidade com a política de nomenclatura. Quando um usuário cria ou edita nomes de grupo, a política de nomenclatura é aplicada automaticamente e os usuários recebem erros apropriados quando eles inserem palavras bloqueadas personalizadas. O centro de administração do Microsoft 365 ainda não mostra uma visualização da política de nomenclatura e não retorna erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo.

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre os grupos do Azure AD.

- [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Política de término para grupos do Microsoft 365](groups-lifecycle.md)
- [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)

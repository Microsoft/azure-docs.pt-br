---
title: Adicionar um repositório de artefatos ao laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um repositório de artefatos ao laboratório no Azure DevTest labs.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: c391aa157e35bdc389bd30efe48fa380d06c193e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508363"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Adicionar um repositório de artefatos ao laboratório no DevTest Labs
DevTest Labs permite que você especifique um artefato a ser adicionado a uma VM no momento da criação da VM ou depois que a VM é criada. Este artefato pode ser uma ferramenta ou um aplicativo que você deseja instalar na VM. Artefatos são definidos em um arquivo JSON carregado de um repositório Git do GitHub ou do Azure DevOps. 

O [repositório do artefato público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mantidos pelo DevTest Labs, fornece várias ferramentas comuns para Windows e Linux. Um link para esse repositório é adicionado automaticamente ao seu laboratório. Você pode criar seu próprio repositório de artefato com ferramentas específicas que não estão disponíveis no repositório de artefato público. Para saber mais sobre como criar artefatos personalizados, consulte [criar artefatos personalizados](devtest-lab-artifact-author.md).

Este artigo fornece informações sobre como adicionar seu repositório de artefato personalizado usando o portal do Azure, modelos de gerenciamento de recursos do Azure e Azure PowerShell. Você pode automatizar a adição de um repositório de artefatos a um laboratório escrevendo scripts do PowerShell ou CLI. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar um repositório de artefatos ao laboratório, você deve primeiro obter as informações de chave do seu repositório. As seções a seguir descrevem como obter as informações necessárias para repositórios hospedados no **GitHub** ou **DevOps do Azure**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório GitHub e token de acesso pessoal

1. Vá para a página inicial do repositório GitHub que contém o artefato ou definições de modelo do Resource Manager.
2. Selecione **Clonar ou baixar**.
3. Para copiar a URL para a área de transferência, selecione o botão **URL de clone HTTPS**. Salve a URL para uso posterior.
4. Selecione a imagem de perfil no canto superior direito do GitHub e, em seguida, selecione **Configurações**.
5. No **configurações pessoais** menu à esquerda, selecione **configurações do desenvolvedor**.
6. Selecione **tokens de acesso pessoal** no menu à esquerda.
7. Selecione **Gerar novo token**.
8. No **Novo token de acesso pessoal**, insira uma **Descrição do token**. Aceite os itens padrão em **Selecionar escopos** e, em seguida, selecione **Gerar Token**.
9. Salve o token gerado. Você usará o token posteriormente.
10. Feche o GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obter a URL de clone do Azure Repos e o token de acesso pessoal
1. Acesse a página inicial de sua coleção de equipe (por exemplo, https://contoso-web-team.visualstudio.com) ) e selecione o projeto.
2. Na home page do projeto, selecione **Código**.
3. Para exibir a URL de clone, na página **Código** do projeto, selecione **Clone**.
4. Salve a URL. Você usará a URL posteriormente.
5. Para criar um token de acesso pessoal, selecione **Meu perfil** no menu suspenso da conta de usuário.
6. Na página de informações de perfil, selecione **Segurança**.
7. Na guia **Segurança**, selecione **Adicionar**.
8. Na página **Criar um token de acesso pessoal**:
   1. Insira uma **Descrição** para o token.
   2. Na lista **Expira em**, selecione **180 dias**.
   3. Na lista **Contas**, selecione **Todas as contas acessíveis**.
   4. Selecione a opção **Todos os escopos**.
   5. Selecione **Criar Token**.
9. O novo token será exibido na lista de **Tokens de Acesso Pessoal**. Selecione **Copiar Token**e salve o valor do token para uso posterior.
10. Continue a conectar seu laboratório para a seção do repositório.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção fornece etapas para adicionar um repositório de artefatos a um laboratório no portal do Azure. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Mais Serviços** e, em seguida, selecione **DevTest Labs** na lista de serviços.
3. Na lista de laboratórios, selecione o laboratório. 
4. Selecione **configuração e políticas** no menu à esquerda.
5. Selecione **repositórios** sob **recursos externos** seção no menu à esquerda.
6. Selecione **+ adicionar** na barra de ferramentas.

    ![O botão Adicionar repositório](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Sobre o **repositórios** , especifique as seguintes informações:
   1. **Nome**. Insira um nome para o repositório.
   2. **URL de clone do Git**. Insira a URL HTTPS de clone de Git que você copiou anteriormente do GitHub ou do Azure DevOps Services.
   3. **Ramificação**. Insira a ramificação para obter as suas definições.
   4. **Token de acesso pessoal**. Insira o token de acesso pessoal obtido anteriormente do GitHub ou do Azure DevOps Services.
   5. **Caminhos de pasta**. Insira, pelo menos, um caminho de pasta em relação a URL de clone que contém o artefato ou definições de modelo do Resource Manager. Ao especificar um subdiretório, verifique se você incluiu a barra "/" no caminho da pasta.

        ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Clique em **Salvar**.

## <a name="use-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
Modelos de gerenciamento de recursos (Azure Resource Manager) do Azure são arquivos JSON que descrevem os recursos no Azure que você deseja criar. Para obter mais informações sobre esses modelos, consulte [Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md).

Esta seção fornece etapas para adicionar um repositório de artefatos a um laboratório usando um modelo do Azure Resource Manager.  O modelo cria o laboratório, se ele ainda não existir. 

### <a name="template"></a>Modelo
O modelo de exemplo usado neste artigo reúne as informações a seguir por meio de parâmetros. A maioria dos parâmetros tem padrões inteligentes, mas há alguns valores que devem ser especificada. Você deve especificar o nome do laboratório, URI para o repositório de artefato e o token de segurança para o repositório. 

- Nome do laboratório.
- Nome de exibição para o repositório de artefato na interface do usuário de DevTest Labs (UI). O valor padrão é: `Team Repository`.
- O URI para o repositório (exemplo: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Branch no repositório que contém os artefatos. O valor padrão é: `master`.
- Nome da pasta que contém os artefatos. O valor padrão é: `/Artifacts`.
- Tipo de repositório. Valores permitidos são `VsoGit` ou `GitHub`.
- Token de acesso para o repositório. 

    ```json
    {
    
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Implantar o modelo
Há algumas maneiras de implantar o modelo no Azure e ter o recurso criado, caso ele não exista ou atualizado, se ele existir. Para obter detalhes, consulte os seguintes artigos:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Implantar recursos com modelos do Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)

Vamos continuar e ver como implantar o modelo no PowerShell. Os cmdlets usados para implantar o modelo são específicos ao contexto, para que o locatário atual e a assinatura atual são usadas. Use [AzContext conjunto](/powershell/module/az.accounts/set-azcontext) antes de implantar o modelo, se necessário, para alterar o contexto.

Primeiro, crie um grupo de recursos usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Se o grupo de recursos que você deseja usar já existir, ignore esta etapa.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Em seguida, crie uma implantação para o grupo de recursos usando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Este cmdlet aplica as alterações de recurso para o Azure. Várias implantações de recursos podem ser feitas para qualquer grupo de recursos determinado. Se você estiver implantando várias vezes ao mesmo grupo de recursos, verifique se que o nome de cada implantação é exclusivo.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Depois que o novo AzResourceGroupDeployment executar com êxito, o comando gera informações importantes, como o estado de provisionamento (deve ser bem-sucedida) e as saídas para o modelo.
 
## <a name="use-azure-powershell"></a>Usar PowerShell do Azure 
Esta seção fornece um exemplo de script do PowerShell que pode ser usado para adicionar um repositório de artefatos a um laboratório. Se você não tiver o Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) para obter instruções detalhadas para instalá-lo.

### <a name="full-script"></a>Script completo
Aqui está o script completo, incluindo algumas mensagens detalhadas e comentários:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab. 

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>

 
[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',
    
    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,
    
    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"
 
Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell
O exemplo a seguir mostra como executar o script: 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>parâmetros
O script do PowerShell de exemplo neste artigo usa os seguintes parâmetros:

| Parâmetro | DESCRIÇÃO | 
| --------- | ----------- | 
| LabName | O nome do laboratório. |
| ArtifactRepositoryName | Nome para o novo repositório de artefato. O script cria um nome aleatório para o repositório se não for especificado. |
| ArtifactRepositoryDisplayName | Nome de exibição para o repositório de artefato. Esse é o nome que aparece no portal do Azure (https://portal.azure.com) ao exibir todos os repositórios de artefato para um laboratório. |
| RepositoryUri | URI para o repositório. Exemplos: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Branch no qual o artefato arquivos podem ser encontrados. O padrão é 'mestre'. | 
| FolderPath | Pasta na qual os artefatos podem ser encontrados. O padrão é ' / artefatos |
| PersonalAccessToken | Token de segurança para acessar o repositório GitHub ou VSOGit. Consulte a seção pré-requisitos para obter instruções para obter token de acesso pessoal |
| SourceType | Se o artefato é repositório VSOGit ou GitHub. |

O próprio repositório precisa interno nome de identificação, que é diferente do que o nome de exibição que é visto no portal do Azure. Você não vir o nome interno usando o portal do Azure, mas você vê-lo ao usar APIs REST do Azure ou o Azure PowerShell. O script fornece um nome, se nenhum for especificado pelo usuário do nosso script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Comandos do PowerShell usados no script

| Comando do PowerShell | Observações |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Esse comando é usado para obter detalhes sobre o laboratório, como seu local. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Não há nenhum comando específico para a adição de repositórios de artefato. Genérica [New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet faz o trabalho. Esse cmdlet necessárias a **ResourceId** ou o **ResourceName** e **ResourceType** par saber o tipo de recurso para criar. Esse script de exemplo usa o nome do recurso e o par de tipo de recurso. <br/><br/>Observe que você está criando a fonte do repositório de artefato no mesmo local e no mesmo grupo de recursos que o laboratório.|

O script adiciona um novo recurso para a assinatura atual. Use [Get-AzContext](/powershell/module/az.accounts/get-azcontext) ver essas informações. Use [AzContext conjunto](/powershell/module/az.accounts/set-azcontext) para definir o locatário atual e a assinatura.

A melhor maneira de descobrir o nome do recurso e informações de tipo de recurso é usar o [APIs REST do teste de unidade Azure](https://azure.github.io/projects/apis/) site. Confira a [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) provedor para ver as APIs REST disponíveis para o provedor do DevTest Labs. Os usuários de script ID do recurso. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
O tipo de recurso é tudo o que listado depois de 'providers' no URI, com exceção dos itens listados entre colchetes. O nome do recurso é tudo o que viu o entre colchetes. Se mais de um item é esperado para o nome do recurso, separe cada item com uma barra "/" como fizemos. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Próximas etapas
- [Especifique os artefatos obrigatórios para seu laboratório no Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Criar artefatos personalizados para sua máquina de virtual do DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefato no laboratório](devtest-lab-troubleshoot-artifact-failure.md)


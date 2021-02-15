---
title: Criar ambientes de várias VMs e recursos de PaaS com modelos
description: Saiba como criar ambientes de várias VMs e recursos de PaaS no Azure DevTest Labs com base em um modelo do Azure Resource Manager
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88182401"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager

Os ambientes de Azure DevTest Labs permitem que os usuários implantem rapidamente infraestruturas complexas de forma consistente dentro dos limites do laboratório. Você pode usar [modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) para criar ambientes com conjuntos de recursos no DevTest Labs. Esses ambientes podem conter recursos do Azure que os modelos do Resource Manager podem criar.

Você pode [adicionar facilmente uma VM (máquina virtual) de cada vez](devtest-lab-add-vm.md) a um laboratório usando o [portal do Azure](https://portal.azure.com). No entanto, cenários como aplicativos Web de várias camadas ou um farm do SharePoint precisam de um mecanismo para criar várias VMs em uma única etapa. Usando modelos de Azure Resource Manager, você pode definir a infraestrutura e a configuração de sua solução do Azure e implantar várias VMs repetidamente em um estado consistente.

Os modelos de Azure Resource Manager também fornecem os seguintes benefícios:

- Azure Resource Manager modelos são carregados diretamente do seu repositório do GitHub ou do Azure Repos controle do código-fonte.
- Os usuários podem criar um ambiente escolhendo um modelo de Azure Resource Manager configurado do portal do Azure, assim como fazem com outros tipos de bases de [VM](devtest-lab-comparing-vm-base-image-types.md).
- Você pode provisionar recursos de PaaS do Azure, bem como VMs de IaaS em um ambiente de um modelo de Azure Resource Manager.
- Você pode acompanhar o custo dos ambientes no laboratório, além de VMs individuais criadas por outros tipos de bases. Recursos de PaaS são criados e aparecerão no controle de custos. No entanto, o desligamento automático de VM não se aplica aos recursos de PaaS.

Para saber mais sobre os benefícios de usar modelos do Resource Manager para implantar, atualizar ou excluir muitos recursos de laboratório em uma única operação, consulte [benefícios do uso de modelos do Resource Manager](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Quando você usa um modelo do Resource Manager como base para criar VMs de laboratório, há algumas diferenças entre a criação de várias VMs ou uma única VM. Para obter mais informações, consulte [usar o modelo de Azure Resource Manager de uma máquina virtual](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Usar ambientes públicos do DevTest Labs
O Azure DevTest Labs tem um [repositório público de modelos de Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que você pode usar para criar ambientes sem precisar se conectar a uma fonte externa do GitHub por conta própria. Esse repositório público é semelhante ao repositório público de artefatos que está disponível no portal do Azure para cada laboratório que você criar. O repositório de ambiente permite que você comece rapidamente com modelos de ambiente pré-criados que têm poucos parâmetros de entrada. Esses modelos fornecem uma experiência de introdução tranqüila para recursos de PaaS em laboratórios.

No repositório público, a equipe do DevTest Labs e outras pessoas criaram e compartilharam modelos usados com frequência, como aplicativos Web do Azure, Service Fabric cluster e um ambiente de farm de desenvolvimento do SharePoint. Você pode usar esses modelos diretamente ou personalizá-los para atender às suas necessidades. Para obter mais informações, veja [Configurar e usar ambientes públicos no DevTest Labs](devtest-lab-configure-use-public-environments.md). Depois de criar seus próprios modelos, você pode armazená-los nesse repositório para compartilhá-los com outras pessoas ou configurar seu próprio repositório git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Criar seus próprios repositórios de modelo

Como uma das práticas recomendadas com infraestrutura como código e configuração como código, você deve gerenciar modelos de ambiente no controle do código-fonte. Azure DevTest Labs segue essa prática e carrega todos os modelos de Azure Resource Manager diretamente de seus repositórios GitHub ou Azure Repos. Como resultado, você pode usar modelos do Resource Manager em todo o ciclo de lançamento, do ambiente de teste para o ambiente de produção.

Há várias regras a serem seguidas para organizar seus modelos de Azure Resource Manager em um repositório:

- Você deve nomear o arquivo de modelo mestre *azuredeploy.jsem*.

- Se você quiser usar valores de parâmetro definidos em um arquivo de parâmetro, o arquivo de parâmetro deverá ser nomeado *azuredeploy.parameters.jsem*.

  Você pode usar os parâmetros `_artifactsLocation` e `_artifactsLocationSasToken` para construir o valor do URI parametersLink, permitindo ao DevTest Labs gerenciar automaticamente os modelos aninhados. Para obter mais informações, consulte [implantar modelos de Azure Resource Manager aninhados para ambientes de teste](deploy-nested-template-environments.md).

- Você pode definir metadados para especificar o nome de exibição do modelo e a descrição em um arquivo chamado *metadata.jsem*, da seguinte maneira:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Principais arquivos de modelo do Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Adicionar repositórios de modelo ao laboratório

Depois de criar e configurar seu repositório, você pode adicioná-lo ao laboratório usando o portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado.
1. No painel **visão geral** do laboratório, selecione **configuração e políticas**.

   ![Configuração e políticas](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Na lista configurações **e políticas de configuração** , selecione **repositórios**. O repositório **público de artefatos** é gerado automaticamente para todos os laboratórios e se conecta ao [repositório GitHub público do DevTest Labs](https://github.com/Azure/azure-devtestlab).

1. Para adicionar seu repositório de modelos de Azure Resource Manager, selecione **Adicionar**.

   ![Repositório público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. No painel **repositórios** , insira as seguintes informações:

   - **Nome**: Insira um nome de repositório a ser usado no laboratório.
   - **URL de clone do git**: Insira a URL de clone HTTPS do git no GitHub ou Azure repos.
   - **Branch** (opcional): Insira o nome do Branch para acessar suas definições de modelo de Azure Resource Manager.
   - **Token de acesso pessoal**: Insira o token de acesso pessoal que é usado para acessar com segurança seu repositório.
     - Para obter o token do Azure Repos, em seu perfil, selecione **configurações do usuário**  >  **Security**  >  **tokens de acesso pessoal**.
     - Para obter seu token do GitHub, em seu perfil, selecione **configurações**  >  **configurações do desenvolvedor**  >  **tokens de acesso pessoal**.
   - **Caminhos de pasta**: Insira o caminho de pasta que é relativo ao URI de clone do git para suas definições de artefato ou suas definições de modelo de Azure Resource Manager.

1. Selecione **Salvar**.

   ![Adicionar novo repositório](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Depois de adicionar um modelo de Azure Resource Manager ao laboratório, os usuários do laboratório podem criar ambientes usando o modelo.

## <a name="configure-access-rights-for-lab-users"></a>Configurar direitos de acesso para usuários do laboratório

Os usuários do laboratório têm a função **leitor** por padrão, portanto, não podem alterar os recursos em um grupo de recursos de ambiente. Por exemplo, eles não podem parar nem iniciar seus recursos.

Para fornecer à função **colaborador** dos usuários do laboratório para que eles possam editar os recursos em seus ambientes, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), no painel **visão geral** do laboratório, selecione **configuração e políticas**e, em seguida, selecione **configurações do laboratório**.

1. No painel **configurações do laboratório** , selecione **colaborador**e, em seguida, selecione **salvar** para conceder permissões de gravação aos usuários do laboratório.

   ![Configurar direitos de acesso de usuário de laboratório](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

A próxima seção orienta a criação de ambientes a partir de um modelo de Azure Resource Manager.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Criar ambientes a partir de modelos no portal do Azure

Depois de adicionar um modelo de Azure Resource Manager ao laboratório, os usuários do laboratório podem criar ambientes na portal do Azure seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Na página do laboratório, selecione **Adicionar**.

1. O painel **escolher uma base** exibe as imagens base que você pode usar, com os modelos de Azure Resource Manager listados primeiro. Selecione o modelo de Azure Resource Manager desejado.

   ![Escolher uma base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. No painel **Adicionar** , insira um valor de **nome de ambiente** a ser exibido para os usuários do ambiente.

   O modelo de Azure Resource Manager define o restante dos campos de entrada. Se o modelo *azuredeploy.parameter.jsno* arquivo definir valores padrão, os campos de entrada mostrarão esses valores.

   Para parâmetros do tipo *cadeia de caracteres segura*, você pode usar segredos do seu Azure Key Vault. Para saber mais sobre como armazenar segredos em um cofre de chaves e usá-los ao criar recursos de laboratório, confira [armazenar segredos em Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Adicionar painel](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Os valores de parâmetro a seguir não aparecem nos campos de entrada, mesmo que o modelo os especifique. Em vez disso, o formulário mostra os campos de entrada em branco em que os usuários do laboratório devem inserir valores ao criar o ambiente.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Selecione **Adicionar** para criar o ambiente.

   O ambiente inicia o provisionamento imediatamente, com o status sendo exibido na lista **minhas máquinas virtuais** . O laboratório cria automaticamente um novo grupo de recursos para provisionar todos os recursos definidos no modelo de Azure Resource Manager.

1. Depois que o ambiente for criado, selecione o ambiente na lista **minhas máquinas virtuais** para abrir o painel grupo de recursos e procurar todos os recursos que o ambiente provisionou.

   ![Recursos de ambiente](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Você também pode expandir o ambiente para exibir apenas a lista de VMs que o ambiente provisionou.

   ![Lista Minhas máquinas virtuais](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selecione qualquer um dos ambientes para exibir as ações disponíveis, como aplicar artefatos, anexar discos de dados, alterar o tempo de desligamento automático e muito mais.

   ![Ações do ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatizar a criação de ambiente com o PowerShell

É viável usar o portal do Azure para adicionar um único ambiente a um laboratório, mas quando um cenário de desenvolvimento ou teste deve criar vários ambientes, a implantação automatizada é uma experiência melhor.

Antes de prosseguir, verifique se você tem um modelo de Azure Resource Manager que define os recursos a serem criados. [Adicione e configure o modelo em um repositório git](#configure-your-own-template-repositories)e [adicione o repositório ao laboratório](#add-template-repositories-to-the-lab).

O script de exemplo a seguir cria um ambiente em seu laboratório. Os comentários ajudam você a entender melhor o script.

1. Salve o exemplo de script do PowerShell a seguir no disco rígido como *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Execute o script da seguinte maneira, usando seus valores específicos para SubscriptionId, LabName, ResourceGroupName, Repositoryname, TemplateName (pasta no repositório git) e Environmentname.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Você também pode usar CLI do Azure para implantar recursos com modelos do Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Somente um usuário com permissões de proprietário de laboratório pode criar VMs de um modelo do Resource Manager usando o Azure PowerShell. Se você quiser automatizar a criação de VM usando um modelo do Resource Manager e tiver apenas permissões de usuário, poderá usar o comando da CLI [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Limitações do modelo do Resource Manager no DevTest Labs

Considere estas limitações ao usar modelos do Resource Manager no DevTest Labs:

- Você não pode criar fórmulas ou imagens personalizadas de VMs de laboratório que foram criadas a partir de um modelo do Resource Manager.

- A maioria das políticas não é avaliada quando você implanta modelos do Resource Manager.

Por exemplo, você pode ter uma política de laboratório que um usuário pode criar apenas cinco VMs. No entanto, um usuário pode implantar um modelo do Resource Manager que crie dezenas de VMs. As políticas que não são avaliadas incluem:

  - Número de VMs por usuário

  - Número de VMs premium por usuário do laboratório

  - Número de discos premium por usuário do laboratório

## <a name="next-steps"></a>Próximas etapas
- Depois de criar uma VM, você pode se conectar à VM selecionando **conectar** no painel de gerenciamento da VM.
- Exiba e gerencie recursos em um ambiente selecionando-o na lista **Minhas máquinas virtuais** no seu laboratório.
- Explore os [modelos de Azure Resource Manager da Galeria de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

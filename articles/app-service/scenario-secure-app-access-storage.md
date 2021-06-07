---
title: Tutorial – O aplicativo Web acessa o armazenamento usando identidades gerenciadas | Azure
description: Neste tutorial, você aprenderá a acessar o Armazenamento do Azure para um aplicativo usando identidades gerenciadas.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurecli
ms.openlocfilehash: 7d84b3f8e654940a8f2c36075b92d630505e88b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482305"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Tutorial: Acessar o Armazenamento do Azure em um aplicativo Web

Saiba como acessar o Armazenamento do Azure para um aplicativo Web (não um usuário conectado) em execução no Serviço de Aplicativo do Azure usando identidades gerenciadas.

:::image type="content" alt-text="Diagrama que mostra como acessar o armazenamento." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Você deseja adicionar o acesso ao plano de dados do Azure (Armazenamento do Azure, Banco de Dados SQL do Azure, Azure Key Vault ou outros serviços) no aplicativo Web. Você pode usar uma chave compartilhada, mas precisa se preocupar com a segurança operacional de quem pode criar, implantar e gerenciar o segredo. Também é possível que a chave possa ser verificada no GitHub, pelo qual os hackers sabem como procurar. Um modo mais seguro de permitir o acesso do aplicativo Web aos dados é pelo uso de [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md).

Uma identidade gerenciada do Azure AD (Azure Active Directory) permite que o Serviço de Aplicativo acesse recursos por meio do RBAC (controle de acesso baseado em função), sem a necessidade de credenciais do aplicativo. Depois de atribuir uma identidade gerenciada ao seu aplicativo Web, o Azure cuida da criação e da distribuição de um certificado. As pessoas não precisam se preocupar em gerenciar segredos nem credenciais de aplicativo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar uma identidade gerenciada atribuída ao sistema em um aplicativo Web.
> * Criar uma conta de armazenamento e um contêiner do Armazenamento de Blobs do Azure.
> * Acessar o armazenamento por meio de um aplicativo Web usando identidades gerenciadas.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo Web em execução no Serviço de Aplicativo do Azure que tem o [módulo de autenticação/autorização do Serviço de Aplicativo habilitado](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Habilitar a identidade gerenciada em um aplicativo

Se você criar e publicar o aplicativo Web por meio do Visual Studio, a identidade gerenciada foi habilitada no aplicativo para você. No serviço de aplicativo, selecione **Identidade** no painel esquerdo e escolha **Atribuído ao sistema**. Verifique se o **Status** está definido como **Ativado**. Caso contrário, selecione **Salvar** e **Sim** para habilitar a identidade gerenciada atribuída ao sistema. Quando a identidade gerenciada é habilitada, o status é definido como **Ativado** e a ID do objeto fica disponível.

:::image type="content" alt-text="Captura de tela que mostra a opção Identidade atribuída ao sistema." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Essa etapa cria uma ID do objeto diferente da ID do aplicativo criada no painel **Autenticação/Autorização**. Copie a ID do objeto da identidade gerenciada atribuída ao sistema. Você precisará dela mais tarde.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Criar uma conta de armazenamento e um contêiner do Armazenamento de Blobs

Agora você está pronto para criar uma conta de armazenamento e um contêiner do Armazenamento de Blobs.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um grupo de recursos.

Uma conta de armazenamento de uso geral v2 fornece acesso a todos os serviços de Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. As etapas descritas aqui criam uma conta de armazenamento de uso geral v2, mas as etapas para a criação de qualquer tipo de conta de armazenamento são semelhantes.

Os blobs no Armazenamento do Azure são organizados em contêineres. Para carregar um blob posteriormente neste tutorial, crie primeiro um contêiner.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar uma conta de armazenamento de uso geral v2 no portal do Azure, siga estas etapas.

1. No menu do portal do Azure, selecione **Todos os serviços**. Na lista de recursos, insira **Contas de Armazenamento**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Armazenamento**.

1. Na janela **Contas de Armazenamento** exibida, escolha **Adicionar**.

1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.

1. No campo **Grupo de recursos**, selecione o grupo de recursos que contém o aplicativo Web no menu suspenso.

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também precisa ter entre 3 e 24 caracteres e só pode incluir números e letras minúsculas.

1. Selecione um local para sua conta de armazenamento ou use o local padrão.

1. Deixe esses campos definidos como seus valores padrão:

    |Campo|Valor|
    |--|--|
    |Modelo de implantação|Gerenciador de Recursos|
    |Desempenho|Standard|
    |Tipo de conta|StorageV2 (uso geral v2)|
    |Replicação|RA-GRS (armazenamento com redundância geográfica com acesso de leitura)|
    |Camada de acesso|Frequente|

1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.

1. Selecione **Criar**.

Para criar um contêiner do Armazenamento de Blobs no Armazenamento do Azure, siga estas etapas.

1. Acesse sua nova conta de armazenamento no portal do Azure.

1. No menu à esquerda da conta de armazenamento, role a página até a seção **Serviço Blob** e selecione **Contêineres**.

1. Selecione o botão **+ Contêiner**.

1. Digite um nome para o novo contêiner. O nome do contêiner deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-).

1. Defina o nível de acesso público ao contêiner. O nível padrão é **Privado (sem acesso anônimo)** .

1. Selecione **OK** para criar o contêiner.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma conta de armazenamento de uso geral v2 e um contêiner do Armazenamento de Blobs, execute o script a seguir. Especifique o nome do grupo de recursos que contém o aplicativo Web. Insira um nome para a conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também precisa ter entre 3 e 24 caracteres e só pode incluir números e letras minúsculas.

Especifique o local da conta de armazenamento. Para ver uma lista de localizações válidas para a sua assinatura, execute ```Get-AzLocation | select Location```. O nome do contêiner deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-).

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma conta de armazenamento de uso geral v2 e um contêiner do Armazenamento de Blobs, execute o script a seguir. Especifique o nome do grupo de recursos que contém o aplicativo Web. Insira um nome para a conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também precisa ter entre 3 e 24 caracteres e só pode incluir números e letras minúsculas. 

Especifique o local da conta de armazenamento. O nome do contêiner deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-).

O exemplo a seguir usa sua conta do Azure AD para autorizar a operação para criar o contêiner. Antes de criar o contêiner, atribua a função Colaborador de Dados do Blob de Armazenamento a si mesmo. Mesmo que você seja o proprietário da conta, precisará ter permissões explícitas para executar operações de dados na conta de armazenamento.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Permitir acesso à conta de armazenamento

Você precisa permitir ao aplicativo Web acesso à conta de armazenamento antes de poder criar, ler ou excluir blobs. Em uma etapa anterior, você configurou o aplicativo Web em execução no Serviço de Aplicativo com uma identidade gerenciada. Usando o RBAC do Azure, você pode fornecer à identidade gerenciada o acesso a outro recurso, assim como qualquer entidade de segurança. A função Colaborador de Dados do Storage Blob fornece ao aplicativo Web (representado pela identidade gerenciada atribuída ao sistema) acesso de leitura, gravação e exclusão no contêiner de blob e nos dados.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal do Azure](https://portal.azure.com), acesse a conta de armazenamento para permitir acesso ao aplicativo Web. Selecione **Controle de acesso (IAM)** no painel esquerdo e escolha **Atribuições de função**. Você verá uma lista de quem tem acesso à conta de armazenamento. Agora você deseja adicionar uma atribuição de função a um robô, o serviço de aplicativo que precisa de acesso à conta de armazenamento. Selecione **Adicionar** > **Adicionar atribuição de função**.

Em **Função**, selecione **Colaborador de dados de blob de armazenamento** para permitir ao aplicativo Web acesso de leitura a blobs de armazenamento. Em **Atribuir acesso a**, selecione **Serviço de Aplicativo**. Em **Assinatura**, selecione a assinatura. Em seguida, selecione o serviço de aplicativo ao qual deseja fornecer acesso. Selecione **Salvar**.

:::image type="content" alt-text="Captura de tela que mostra a tela Adicionar atribuição de função." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

O aplicativo Web agora tem acesso à conta de armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Execute o script a seguir para atribuir a função Colaborador de Dados do Storage Blob ao aplicativo Web (representado por uma identidade gerenciada atribuída ao sistema) na conta de armazenamento.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Execute o script a seguir para atribuir a função Colaborador de Dados do Storage Blob ao aplicativo Web (representado por uma identidade gerenciada atribuída ao sistema) na conta de armazenamento.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Acessar o Armazenamento de Blobs (.NET)

A classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) é usada para obtenção de uma credencial de token para o código destinada à autorização de solicitações ao Armazenamento do Azure. Crie uma instância da classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), que usa a identidade gerenciada para buscar tokens e anexá-los ao cliente do serviço. O exemplo de código a seguir obtém a credencial de token autenticada e a usa para criar um objeto de cliente de serviço que, por sua vez, carrega um novo blob.

Para ver esse código como parte de um aplicativo de exemplo, confira o [exemplo no GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity).

### <a name="install-client-library-packages"></a>Instalar os pacotes da biblioteca de clientes

Instale o [Pacote NuGet do Armazenamento de Blobs](https://www.nuget.org/packages/Azure.Storage.Blobs/) para trabalhar com o Armazenamento de Blobs e o [pacote NuGet da biblioteca de clientes da Identidade do Azure para .NET](https://www.nuget.org/packages/Azure.Identity/) para se autenticar com as credenciais do Azure AD. Instale as bibliotecas de clientes usando a interface de linha de comando do .NET Core ou o Console do Gerenciador de Pacotes no Visual Studio.

# <a name="command-line"></a>[Linha de comando](#tab/command-line)

Abra uma linha de comando e alterne para o diretório que contém o arquivo de projeto.

Execute os comandos de instalação.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Gerenciador de Pacotes](#tab/package-manager)

Abra o projeto ou a solução no Visual Studio e abra o console usando o comando **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

Execute os comandos de instalação.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Exemplo

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você concluiu este tutorial e não precisa mais do aplicativo Web nem dos recursos associados, [limpe os recursos que você criou](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Criar uma identidade gerenciada atribuída ao sistema.
> * Criar uma conta de armazenamento e um contêiner do Armazenamento de Blobs.
> * Acessar o armazenamento por meio de um aplicativo Web usando identidades gerenciadas.

> [!div class="nextstepaction"]
> [O Serviço de Aplicativo acessa o Microsoft Graph em nome do usuário](scenario-secure-app-access-microsoft-graph-as-user.md)

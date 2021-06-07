---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Armazenamento do Azure usando uma credencial SAS – Azure AD
description: Um tutorial que mostra como usar uma identidade gerenciada atribuída pelo sistema VM do Windows para acessar o Armazenamento do Azure, usando uma credencial SAS em vez de uma chave de acesso à conta de armazenamento.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f8b23d8f717e430e865e391a40692773f0beace
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776385"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Tutorial: Use uma identidade gerenciada atribuída pelo sistema VM do Windows para acessar o Armazenamento do Microsoft Azure por meio de uma credencial SAS

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade atribuída pelo sistema para uma máquina virtual do Windows (VM) para obter uma credencial SAS (Storage Access Signature) de armazenamento. Especificamente, uma [credencial SAS de serviço](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Uma SAS de Serviço permite conceder acesso limitado a objetos em uma conta de armazenamento por tempo limitado e para um serviço específico (em nosso caso, o serviço blob) sem expor uma chave de acesso de conta. Você pode usar a credencial SAS normalmente ao realizar operações de armazenamento, por exemplo, ao usar o SDK de Armazenamento. Para este tutorial, vamos demonstrar o upload e o download de um blob usando o PowerShell do Armazenamento do Azure. Você saberá como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Conceda à sua VM acesso a SAS de conta de armazenamento no Resource Manager 
> * Obter um token de acesso usando a identidade da VM e usá-lo para recuperar SAS do Resource Manager 

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre Identidades Gerenciadas. Se você não estiver familiarizado com as identidades gerenciadas para funcionalidades de recursos do Azure, veja esta [visão geral](overview.md). 
- Uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/).
- Permissões de "Proprietário" no escopo apropriado (sua assinatura ou grupo de recursos) para executar a criação de recursos e o gerenciamento de função necessários. Se precisar de ajuda com a atribuição de função, confira [Atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).
- Você também precisa de uma máquina virtual do Windows com identidades gerenciadas atribuídas ao sistema habilitadas.
  - Se precisar criar uma máquina virtual para este tutorial, siga o artigo intitulado [Criar uma máquina virtual com a identidade atribuída pelo sistema habilitada](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Se você ainda não tiver uma, agora você criará uma conta de armazenamento. Você também pode pular esta etapa e conceder acesso de identidade gerenciada atribuído pelo sistema da VM à credencial SAS de uma conta de armazenamento existente. 

1. Clique no botão **+/Criar novo serviço** encontrado no canto superior esquerdo do portal do Azure.
2. Clique em **Armazenamento**, então **Conta de Armazenamento** e um novo painel "Criar conta de armazenamento" será exibido.
3. Insira um nome para a conta de armazenamento, que você usará mais tarde.  
4. **Modelo de implantação** e **Tipo de conta** devem ser definidos como "Resource Manager" e "Uso geral", respectivamente. 
5. Verifique se a **Assinatura** e o **Grupo de Recursos** correspondem ao que você especificou quando criou sua VM na etapa anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Criar um contêiner de blobs na conta de armazenamento

Mais tarde vamos carregar e baixar um arquivo para a nova conta de armazenamento. Como arquivos exigem armazenamento de blobs, é preciso criar um contêiner de blobs para armazenar o arquivo.

1. Navegue de volta para sua conta de armazenamento criado recentemente.
2. Clique no link **Contêineres** no painel à esquerda, em "Serviço Blob".
3. Clique em **+ Contêiner** na parte superior da página e um painel "Novo contêiner" deslizará para fora.
4. Dê um nome ao contêiner, selecione um nível de acesso, então clique em **OK**. O nome especificado será usado posteriormente no tutorial. 

    ![Criar um contêiner de armazenamento](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Conceder à identidade gerenciada atribuída pelo sistema da VM acesso para usar um SAS de armazenamento 

O Armazenamento do Azure não dá suporte nativo a autenticação do Azure AD.  No entanto, você pode usar uma identidade gerenciada para recuperar um SAS de armazenamento do Resource Manager e usar o SAS para acessar o armazenamento.  Nesta etapa, você concede o acesso à identidade gerenciada atribuída pelo sistema da VM à sua conta de armazenamento SAS.   

1. Navegue de volta para sua conta de armazenamento criado recentemente.   
2. Clique no link do **Controle de acesso (IAM)** no painel à esquerda.  
3. Clique em **+ Adicionar atribuição de função** na parte superior da página para adicionar uma nova atribuição de função à sua VM
4. Defina **Função** como "Colaborador da Conta de Armazenamento" no lado direito da página.  
5. No próximo menu suspenso, escolha **Atribuir acesso a** ao recurso “Máquina Virtual”.  
6. Em seguida, verifique se a assinatura correta é listada no menu suspenso **Assinatura** e, em seguida, defina **Grupo de Recursos** como "Todos os grupos de recursos".  
7. Por fim, em **Selecionar**, escolha sua Máquina Virtual do Windows na lista suspensa e clique em **Salvar**. 

    ![Texto Alt da imagem](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso usando a identidade da máquina virtual e usá-la para chamar o Azure Resource Manager 

Para o restante do tutorial, trabalharemos em sua VM.

Você precisará usar os cmdlets do PowerShell do Azure Resource Manager nesta parte.  Se ele não estiver instalado, [baixe a versão mais recente](/powershell/azure/) antes de continuar.

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a Máquina Virtual do Windows e, na página **Visão geral**, clique em **Conectar** na parte superior.
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows. 
3. Agora você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual.
4. Abra o PowerShell na sessão remota e use Invoke-WebRequest para obter um token do Azure Resource Manager da identidade gerenciada local para o ponto de extremidade de recursos do Azure.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > O valor do parâmetro "resource" deve ser uma correspondência exata para o que é esperado pelo Azure AD. Ao usar a ID de recurso do Azure Resource Manager, você deve incluir a barra à direita no URI.
    
    Em seguida, extraia o elemento “Conteúdo”, que está armazenado como uma cadeia de caracteres formatada em JSON (JavaScript Object Notation) no objeto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Em seguida, extraia o token de acesso da resposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Obter uma credencial SAS do Azure Resource Manager para fazer chamadas de armazenamento 

Agora use o PowerShell para chamar o Resource Manager usando o token de acesso recuperado da seção anterior para criar uma credencial SAS de armazenamento. Quando tivermos a credencial SAS, poderemos chamar as operações de armazenamento.

Para esta solicitação, vamos usar os parâmetros de solicitação HTTP a seguir para criar a credencial SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Esses parâmetros são incluídos no corpo da POSTAGEM da solicitação para a credencial SAS. Para obter mais informações sobre os parâmetros para a criação de uma credencial SAS, consulte [Referência REST de SAS do Serviço de Lista](/rest/api/storagerp/storageaccounts/listservicesas).

Primeiro, converta os parâmetros em JSON e, em seguida, chame o ponto de extremidade `listServiceSas` de armazenamento para criar a credencial SAS:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> A URL diferencia maiúsculas de minúsculas, portanto, certifique-se de usar letras maiúsculas e minúsculas exatamente da mesma forma que você usou anteriormente ao nomear o Grupo de Recursos, incluindo a letra maiúscula "G" em "resourceGroups". 

Agora podemos pode extrair a credencial SAS da resposta:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Se você inspecionar as credenciais SAS, verá algo assim:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Em seguida, criamos um arquivo chamado "test.txt". Em seguida, use a credencial SAS para autenticar-se com o cmdlet `New-AzStorageContent`, carregar o arquivo para nosso contêiner de blob e então baixar o arquivo.

```bash
echo "This is a test text file." > test.txt
```

Certifique-se instalar os cmdlets do Armazenamento do Azure primeiro, usando `Install-Module Azure.Storage`. Então carregue o blob recém-criado usando o cmdlet do PowerShell `Set-AzStorageBlobContent`:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Você também pode baixar o blob que acabou de criar, usando o cmdlet do PowerShell `Get-AzStorageBlobContent`:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar a identidade gerenciada atribuída pelo sistema de uma VM do Windows para acessar o Armazenamento do Azure usando uma credencial SAS.  Para saber mais sobre SAS de Armazenamento do Azure, confira:

> [!div class="nextstepaction"]
>[Como usar SAS (assinaturas de acesso compartilhado)](../../storage/common/storage-sas-overview.md)

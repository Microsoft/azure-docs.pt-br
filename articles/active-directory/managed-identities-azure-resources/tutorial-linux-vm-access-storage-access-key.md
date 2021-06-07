---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Armazenamento do Azure por meio da chave de acesso – Linux – Azure AD
description: Um tutorial que o conduz pelo processo de usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Armazenamento do Azure usando uma chave de acesso.
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
ms.date: 03/04/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99a199fcc5f43f3710fe2e2fcfe55b7e624987b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317456"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Armazenamento do Azure por meio da chave de acesso

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema de uma VM (máquina virtual) do Linux para recuperar as chaves de acesso da conta de armazenamento. Você pode usar a chave de acesso de armazenamento normalmente ao realizar operações de armazenamento, por exemplo, ao usar o SDK de Armazenamento. Para este tutorial, vamos carregar e baixar blobs usando a CLI do Azure. Você saberá como:

> [!div class="checklist"]
> * Conceder acesso à VM para chaves de acesso da conta de armazenamento no Resource Manager 
> * Obter um token de acesso usando a identidade da VM e usá-la para recuperar as chaves de acesso de armazenamento do Resource Manager  

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Se você ainda não tiver uma, agora você criará uma conta de armazenamento.  Você também pode ignorar esta etapa e conceder acesso de identidade gerenciada atribuída pelo sistema da VM às chaves de uma conta de armazenamento existente. 

1. Clique no botão **+/Criar novo serviço** encontrado no canto superior esquerdo do portal do Azure.
2. Clique em **Armazenamento**, então **Conta de Armazenamento** e um novo painel "Criar conta de armazenamento" será exibido.
3. Insira um **Nome** para a conta de armazenamento, que você usará mais tarde.  
4. **Modelo de implantação** e **Tipo de conta** devem ser definidos como "Resource manager" e "Finalidade geral", respectivamente. 
5. Verifique se a **Assinatura** e o **Grupo de Recursos** correspondem ao que você especificou quando criou sua VM na etapa anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Criar um contêiner de blobs na conta de armazenamento

Mais tarde vamos carregar e baixar um arquivo para a nova conta de armazenamento. Como arquivos exigem armazenamento de blobs, é preciso criar um contêiner de blobs para armazenar o arquivo.

1. Navegue de volta para sua conta de armazenamento criado recentemente.
2. Clique no link **Contêineres** à esquerda, em "Serviço Blob".
3. Clique em **+ Contêiner** na parte superior da página e um painel "Novo contêiner" deslizará para fora.
4. Dê um nome ao contêiner, selecione um nível de acesso, então clique em **OK**. O nome especificado será usado posteriormente no tutorial. 

    ![Criar um contêiner de armazenamento](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Conceder acesso de identidade gerenciada atribuído pelo sistema da VM para usar as chaves de acesso da conta de armazenamento

Nesta etapa, você concede à identidade gerenciada atribuída pelo sistema da VM acesso às chaves para a sua conta de armazenamento.   

1. Navegue de volta para sua conta de armazenamento criado recentemente.
2. Clique no link do **Controle de acesso (IAM)** no painel à esquerda.  
3. Clique em **+ Adicionar atribuição de função** na parte superior da página para adicionar uma nova atribuição de função à sua VM
4. Defina **Função** como "Função do Serviço de Operador da Chave de Conta de Armazenamento", no lado direito da página. 
5. No próximo menu suspenso, escolha **Atribuir acesso a** ao recurso “Máquina Virtual”.  
6. Em seguida, verifique se a assinatura correta é listada no menu suspenso **Assinatura** e, em seguida, defina **Grupo de Recursos** como "Todos os grupos de recursos".  
7. Por fim, em **Selecionar**, escolha sua Máquina Virtual do Linux no menu suspenso e clique em **Salvar**. 

    ![Texto Alt da imagem](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso usando a identidade da máquina virtual e usá-la para chamar o Azure Resource Manager

Para o restante do tutorial, trabalharemos na VM criada anteriormente.

Para concluir essas etapas, você precisará de cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](/windows/wsl/install-win10). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a máquina virtual do Linux e na página **Visão geral**, clique em **Conectar** na parte superior. Copie a cadeia de caracteres para conectar-se à VM. 
2. Conecte-se à VM usando um cliente SSH.  
3. Em seguida, você receberá uma solicitação para inserir sua **Senha**, adicionada durante a criação da **VM do Linux**. Assim, você deve se conectar com êxito.  
4. Use o CURL para obter um token de acesso para o Azure Resource Manager.  

    A solicitação CURL e a resposta para o token de acesso está abaixo:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
    ```
    
    > [!NOTE]
    > Na solicitação anterior, o valor do parâmetro "resource" deve ser uma correspondência exata do que é esperado pelo Azure AD. Ao usar a ID de recurso do Azure Resource Manager, você deve incluir a barra à direita no URI.
    > Na resposta seguinte, o elemento access_token foi reduzido para fins de brevidade.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obter chaves de acesso da conta de armazenamento do Azure Resource Manager para realizar chamadas de armazenamento  

Agora, usaremos CURL para fazer uma chamada ao Resource Manager usando o token de acesso recuperado na seção anterior para recuperar a chave de acesso de armazenamento. Assim que tivermos a chave de acesso de armazenamento, podemos chamar as operações de upload/download de armazenamento. Substitua os parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<STORAGE ACCOUNT NAME>` pelos seus próprios valores. Substitua o valor `<ACCESS TOKEN>` pelo token de acesso que você recuperou anteriormente:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> O texto na URL anterior diferencia maiúsculas de minúsculas, portanto, verifique se você está usando letras maiúsculas e minúsculas para os Grupos de Recursos refletirem-nas adequadamente. Além disso, é importante saber que se trata de uma solicitação POST, não uma solicitação GET, e passe um valor para capturar um limite de comprimento com -d que pode ser NULL.  

A resposta de CURL fornece a lista de chaves:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Crie um arquivo de blob de exemplo para carregar o contêiner de armazenamento de blobs. Em uma VM do Linux, você pode fazer isso com o seguinte comando. 

```bash
echo "This is a test file." > test.txt
```

Em seguida, autentique com o comando `az storage` da CLI usando a chave de acesso de armazenamento e carregue o arquivo para o contêiner de blob. Nesta etapa, você precisará [instalar a CLI do Azure mais recente](/cli/azure/install-azure-cli) na sua VM, se ainda não o fez.
 

```azurecli
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Resposta: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Além disso, baixe o arquivo usando a CLI do Azure e autentique com a chave de acesso de armazenamento. 

Solicitação: 

```azurecli
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Resposta: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar a identidade gerenciada atribuída pelo sistema de uma VM do Linux para acessar o Armazenamento do Azure usando uma chave de acesso.  Para saber mais sobre as chaves de acesso de Armazenamento do Azure, confira:

> [!div class="nextstepaction"]
>[Gerenciar suas chaves de acesso de armazenamento](../../storage/common/storage-account-create.md)

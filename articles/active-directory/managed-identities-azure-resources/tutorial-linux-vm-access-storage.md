---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Armazenamento do Azure – Linux – Azure AD
description: Um tutorial que o conduz pelo processo de usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Armazenamento do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4c7612188043be070ead92c88838b567b22787d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98131262"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Armazenamento do Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema para uma VM (máquina virtual) do Linux para acessar o Armazenamento do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contêiner de blobs em uma conta de armazenamento
> * Conceder acesso de Identidade Gerenciada de Linux VM em um contêiner de Armazenamento do Azure
> * Obter um token de acesso e usá-lo para chamar o Armazenamento do Azure

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Para executar os exemplos de script CLI neste tutorial, você tem duas opções:

- Usar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md), seja pelo Portal do Azure ou por meio do botão **Experimentar**, localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](/cli/azure/install-azure-cli) (2.0.23 ou posterior) se preferir usar um console local da CLI.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Nesta seção, você criará uma conta de armazenamento. 

1. Clique no botão **+ Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.
2. Clique em **Armazenamento** e, em seguida, **Conta de armazenamento – blob, arquivo, tabela, fila**.
3. Sob **Nome**, insira um nome para a conta de armazenamento.  
4. **Modelo de implantação** e **Tipo de conta** devem ser definidos como **Resource manager** e **Armazenamento (uso geral v1)** . 
5. Verifique se a **Assinatura** e o **Grupo de Recursos** correspondem ao que você especificou quando criou sua VM na etapa anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Criar um contêiner de blob e carregar um arquivo na conta de armazenamento

Arquivos exigem armazenamento de blobs, então é preciso criar um contêiner de blobs para armazenar o arquivo. Em seguida, você carrega um arquivo no contêiner de blobs na nova conta de armazenamento.

1. Navegue de volta para sua conta de armazenamento criado recentemente.
2. Em **Serviço Blob**, clique em **Contêineres**.
3. No topo da página, clique em **+ Contêiner**.
4. Em **Novo contêiner**, insira um nome para o contêiner e, sob **Nível de acesso público**, mantenha o valor padrão.

    ![Criar um contêiner de armazenamento](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Usando um editor de sua escolha, crie um arquivo denominado *hello world.txt* em seu computador local.  Abra o arquivo e adicione o texto (sem as aspas) "Hello world! :)" e salve-o. 

6. Carregue o arquivo no contêiner recém-criado clicando no nome do contêiner, depois em **Carregar**
7. No painel **Carregar blob**, em **Arquivos**, clique no ícone de pasta e navegue até o arquivo **hello_world.txt** em seu computador local, selecione-o e clique em **Carregar**.

    ![Carregar arquivo de texto](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Conceder à sua VM acesso a um contêiner do Armazenamento do Azure 

Você pode usar a identidade gerenciada da VM para recuperar os dados no Azure Storage Blob.

>[!NOTE]
> Para obter mais informações sobre as várias funções que você pode usar para conceder permissões para o armazenamento, examine [Autorizar o acesso a blobs e filas usando o Azure Active Directory](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)

1. Navegue de volta para sua conta de armazenamento criado recentemente.  
2. Clique no link do **Controle de acesso (IAM)** no painel à esquerda.  
3. Clique em **+ Adicionar atribuição de função** na parte superior da página para adicionar uma nova atribuição de função à sua VM.
4. Em **Função**, no menu suspenso, selecione **Leitor de Dados de Blob de Armazenamento**. 
5. No menu suspenso seguinte, em **Atribuir acesso a**, escolha **Máquina Virtual**.  
6. Depois, verifique se a assinatura correta está listada no menu suspenso **Assinatura** e defina **Grupo de Recursos** como **Todos os grupos de recursos**.  
7. Em **Selecionar**, escolha sua VM e clique em **Salvar**.

    ![Atribuir permissões](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Obter um token de acesso e usá-lo para chamar o Armazenamento do Azure

O Armazenamento do Azure tem suporte nativo para autenticação do Azure AD, de modo que ele pode aceitar diretamente os tokens de acesso obtidos por meio da Identidade Gerenciada. Isso faz parte da integração do Armazenamento do Azure com o Azure AD, e é diferente de fornecer as credenciais na cadeia de conexão.

Para concluir as etapas a seguir, você precisará trabalhar da VM criada anteriormente e precisará de um cliente SSH para se conectar a ela. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a máquina virtual do Linux e na página **Visão geral**, clique em **Conectar**. Copie a cadeia de caracteres para conectar-se à VM.
2. **Conecte-se** à VM com um cliente SSH de sua escolha. 
3. Na janela de terminal, usando CURL, faça uma solicitação para o ponto de extremidade da Identidade Gerenciada local para obter um token de acesso para o Armazenamento do Azure.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Agora, use o token de acesso para acessar o Armazenamento do Microsoft Azure, por exemplo, para ler o conteúdo do arquivo de exemplo que você carregou anteriormente para o contêiner. Substitua os valores `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` e `<FILE NAME>` pelos valores que você especificou anteriormente e `<ACCESS TOKEN>` pelo token retornado na etapa anterior.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   A resposta tem o conteúdo do arquivo:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a habilitar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Armazenamento do Azure.  Para saber mais sobre o Armazenamento do Azure, confira:

> [!div class="nextstepaction"]
> [Armazenamento do Azure](../../storage/common/storage-introduction.md)

---
title: Acesse o Armazenamento do Azure usando uma identidade gerenciada atribuída pelo sistema da VM do Windows | Microsoft Docs
description: Um tutorial que o conduz pelo processo de usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Armazenamento do Azure.
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
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1cc69b3cfdac307edf6dfe999a5d538c2cb811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89263171"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Armazenamento do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema para uma VM (máquina virtual) do Windows para acessar o Armazenamento do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Criar um contêiner de blobs em uma conta de armazenamento
> * Conceder à identidade gerenciada atribuída pelo sistema da VM do Windows acesso a uma conta de armazenamento
> * Obter um acesso e usá-lo para chamar o Armazenamento do Azure

> [!NOTE]
> A autenticação do Azure Active Directory para Armazenamento do Azure está em versão prévia.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>Habilitar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Conceder acesso


### <a name="create-storage-account"></a>Criar Conta de Armazenamento

Nesta seção, você criará uma conta de armazenamento.

1. Clique no botão **+ Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.
2. Clique em **Armazenamento** e, em seguida, **Conta de armazenamento – blob, arquivo, tabela, fila**.
3. Sob **Nome**, insira um nome para a conta de armazenamento.
4. **Modelo de implantação** e **Tipo de conta** devem ser definidos como **Resource manager** e **Armazenamento (uso geral v1)** .
5. Verifique se a **Assinatura** e o **Grupo de Recursos** correspondem ao que você especificou quando criou sua VM na etapa anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

### <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Criar um contêiner de blob e carregar um arquivo na conta de armazenamento

Arquivos exigem armazenamento de blobs, então é preciso criar um contêiner de blobs para armazenar o arquivo. Em seguida, você carrega um arquivo no contêiner de blobs na nova conta de armazenamento.

1. Navegue de volta para sua conta de armazenamento criado recentemente.
2. Em **Serviço Blob**, clique em **Contêineres**.
3. No topo da página, clique em **+ Contêiner**.
4. Em **Novo contêiner**, insira um nome para o contêiner e, sob **Nível de acesso público**, mantenha o valor padrão.

    ![Criar um contêiner de armazenamento](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Usando um editor de sua escolha, crie um arquivo denominado *hello world.txt* em seu computador local. Abra o arquivo e adicione o texto (sem as aspas) "Hello world! :)" e salve-o.
6. Carregue o arquivo no contêiner recém-criado clicando no nome do contêiner, depois em **Carregar**
7. No painel **Carregar blob**, em **Arquivos**, clique no ícone de pasta e navegue até o arquivo **hello_world.txt** em seu computador local, selecione-o e clique em **Carregar**.
    ![Carregar arquivo de texto](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

### <a name="grant-access"></a>Conceder acesso

Esta seção mostra como conceder à sua VM acesso a um contêiner do Armazenamento do Azure. Você pode usar a identidade gerenciada atribuída pelo sistema da VM para recuperar os dados no Azure Storage Blob.

1. Navegue de volta para sua conta de armazenamento criado recentemente.
2. Clique no link do **Controle de acesso (IAM)** no painel à esquerda.
3. Clique em **+ Adicionar atribuição de função** na parte superior da página para adicionar uma nova atribuição de função à sua VM.
4. Em **Função**, no menu suspenso, selecione **Leitor de Dados de Blob de Armazenamento**.
5. No menu suspenso seguinte, em **Atribuir acesso a**, escolha **Máquina Virtual**.
6. Depois, verifique se a assinatura correta está listada no menu suspenso **Assinatura** e defina **Grupo de Recursos** como **Todos os grupos de recursos**.
7. Em **Selecionar**, escolha sua VM e clique em **Salvar**.

    ![Atribuir permissões](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="access-data"></a>Acessar dados 

O Armazenamento do Azure tem suporte nativo para autenticação do Azure AD, de modo que ele pode aceitar diretamente os tokens de acesso obtidos por meio da identidade gerenciada. Isso faz parte da integração do Armazenamento do Azure com o Azure AD, e é diferente de fornecer as credenciais na cadeia de conexão.

Confira um exemplo de código .NET da abertura de uma conexão com o Armazenamento do Azure usando um token de acesso e, em seguida, da leitura do conteúdo do arquivo criado anteriormente. Esse código deve ser executado na VM para poder acessar o ponto de extremidade da identidade gerenciada da VM. É necessário ter o .NET Framework 4.6 ou superior para usar o método de token de acesso. Substitua o valor de `<URI to blob file>` adequadamente. Você pode obter esse valor navegando até o arquivo criado e carregado no armazenamento de blobs e copiando a **URL** sob **Propriedades**, na página **Visão geral**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");

            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);

            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }
    }
}
```

A resposta tem o conteúdo do arquivo:

`Hello world! :)`


## <a name="disable"></a>Desabilitar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a habilitar uma identidade atribuída pelo sistema da VM do Windows para acessar o Armazenamento do Azure.  Para saber mais sobre o Armazenamento do Azure, confira:

> [!div class="nextstepaction"]
> [Armazenamento do Azure](../../storage/common/storage-introduction.md)
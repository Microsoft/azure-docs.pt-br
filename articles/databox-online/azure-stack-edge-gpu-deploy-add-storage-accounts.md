---
title: Tutorial para transferir dados para uma conta de armazenamento com uma GPU do Azure Stack Edge Pro | Microsoft Docs
description: Saiba como adicionar e se conectar a contas de armazenamento local e do Edge em dispositivo de GPU do Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: d20ade0fdf6262c08a4ae961d17b1ec8a9ba39f2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065796"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Tutorial: Transferir dados por meio de contas de armazenamento com uma GPU do Azure Stack Edge Pro 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este tutorial descreve como adicionar e conectar-se a contas de compartilhamento no dispositivo do Azure Stack Edge Pro. Depois de adicionar as contas de armazenamento, o Azure Stack Edge Pro poderá transferir dados para o Azure.

Esse procedimento pode levar cerca de 30 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma conta de armazenamento
> * Conecte-se à conta de armazenamento

 
## <a name="prerequisites"></a>Pré-requisitos

Para adicionar contas de armazenamento ao Azure Stack Edge Pro, verifique se:

- Você instalou o seu dispositivo físico conforme descrito em [Instalar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).

- Você ativou o dispositivo físico conforme descrito em [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="add-an-edge-storage-account"></a>Adicionar uma conta de armazenamento do Edge

Para criar uma conta de armazenamento do Edge, execute o seguinte procedimento:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Conecte-se à conta de armazenamento do Edge

Agora você pode se conectar às APIs REST de armazenamento do Edge por meio de *HTTP* ou *HTTPS*.

- O *HTTPS* é a maneira segura e recomendada.
- O *HTTP* é usado para a conexão em redes confiáveis.

## <a name="connect-via-http"></a>Conectar-se via HTTP

A conexão às APIs REST do Edge via HTTP exige as seguintes etapas:

- Adicionar o VIP de serviço consistente do Azure e o ponto de extremidade de serviço Blob ao host remoto
- Verificar conexão 

Cada uma dessas etapas é descrita nas seções a seguir.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Adicionar o endereço IP do dispositivo e o ponto de extremidade de serviço Blob ao cliente remoto

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Verificar a conexão

Para verificar a conexão, geralmente você precisa das seguintes informações (pode variar) coletadas na etapa anterior:

- Nome da conta de armazenamento.
- Chave de acesso da conta de armazenamento.
- Ponto de extremidade de serviço Blob.

Você já tem o nome da conta de armazenamento e o ponto de extremidade de serviço Blob. Obtenha a chave de acesso da conta de armazenamento conectando-se ao dispositivo por meio do Azure Resource Manager usando um cliente do Azure PowerShell.

Siga as etapas em [Conectar-se ao dispositivo por meio do Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md). Depois de entrar nas APIs de dispositivo local por meio do Azure Resource Manager, obtenha a lista de contas de armazenamento no dispositivo. Execute o cmdlet a seguir:

`Get-AzureRMStorageAccount`

Na lista de contas de armazenamento no dispositivo, identifique a conta de armazenamento para a qual você precisa da chave de acesso. Observe o nome da conta de armazenamento e o grupo de recursos.

Um exemplo de saída é mostrado abaixo:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Para obter a chave de acesso, execute o seguinte cmdlet:

`Get-AzureRmStorageAccountAccessKey`

Um exemplo de saída é mostrado abaixo:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Copie e salve essa chave. Você usará essa chave para verificar a conexão usando Gerenciador de Armazenamento do Azure.

Para verificar se a conexão foi estabelecida com êxito, use o Gerenciador de Armazenamento para se anexar a uma conta de armazenamento externo. Caso não tenha o Gerenciador de Armazenamento, [baixe-o](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Conectar-se via HTTPS

A conexão às APIs REST do Armazenamento de Blobs do Azure via HTTPS exige as seguintes etapas:

- Obter seu certificado de ponto de extremidade de blob
- Importar o certificado no host remoto ou do cliente
- Adicionar o IP do dispositivo e o ponto de extremidade de serviço Blob ao host remoto ou do cliente
- Configurar e verificar a conexão

Cada uma dessas etapas é descrita nas seções a seguir.

### <a name="get-certificate"></a>Obter certificado

Acessar o Armazenamento de Blobs via HTTPS requer um certificado SSL para o dispositivo. Você também carregará esse certificado no dispositivo do Azure Stack Edge Pro como um arquivo *.pfx* com uma chave privada anexada a ele. Para obter mais informações sobre como criar (apenas para fins de teste e desenvolvimento) e carregar esses certificados em seu dispositivo do Azure Stack Edge Pro, acesse:

- [Criar o certificado do ponto de extremidade do blob](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional).
- [Carregar o certificado do ponto de extremidade do blob](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).
- [Importar certificados no cliente que está acessando o dispositivo](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

### <a name="import-certificate"></a>Importar certificado

Se estiver usando o Gerenciador de Armazenamento do Azure para se conectar às contas de armazenamento no dispositivo, você também precisará importar o certificado para o Gerenciador de Armazenamento no formato PEM. No ambiente do Windows, o *.cer* codificado em Base 64 é o mesmo que o formato PEM.

Execute as seguintes etapas para importar os certificados no Gerenciador de Armazenamento do Azure:

1. Verifique se Gerenciador de Armazenamento do Azure está direcionando às APIs do Azure Stack. Acesse **Editar > Definir APIs do Azure Stack como Destino**. Quando solicitado, reinicie o Gerenciador de Armazenamento para que a alteração entre em vigor.

2. Para importar certificados SSL, Acesse **Editar > Certificados SSL > Importar certificados**.

  
   ![Importar o certificado para o Gerenciador de Armazenamento](./media/azure-stack-edge-gpu-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Navegue e forneça a cadeia de assinatura e os certificados de blob. A cadeia de assinatura e o certificado de blob devem estar no formato PEM, que é o mesmo que o formato codificado em Base64 no sistema Windows. Você será notificado de que os certificados foram importados com êxito.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicionar o endereço IP do dispositivo e o ponto de extremidade de serviço Blob

Siga as mesmas etapas para [adicionar endereço IP do dispositivo e ponto de extremidade de serviço Blob ao se conectar via *HTTP*](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>Configurar e verificar a conexão

Siga as etapas para [Configurar e verificar a conexão que você usou ao se conectar via *HTTP*](#verify-connection). A única diferença é que você deve deixar a *opção Usar HTTP* desmarcada.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu os seguintes tópicos sobre o Azure Stack Edge Pro:

> [!div class="checklist"]
> * Adicionar uma conta de armazenamento
> * Conectar-se a uma conta de armazenamento

Para saber como transformar seus dados usando o Azure Stack Edge Pro, prossiga para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar dados com o Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)

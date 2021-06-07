---
title: Tutorial para transferir dados a compartilhar com uma GPU do Azure Stack Edge Pro | Microsoft Docs
description: Saiba como adicionar e conectar-se a compartilhamentos no dispositivo de GPU do Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 5f4a40e5b4e0dbced10f9d4a19609d9a0a49a1db
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058758"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>Tutorial: Transferir dados por meio de compartilhamentos com uma GPU do Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este tutorial descreve como adicionar e conectar-se a compartilhamentos no dispositivo Azure Stack Edge Pro. Depois de adicionar os compartilhamentos, o Azure Stack Edge Pro pode transferir dados para o Azure.

Esse procedimento leva cerca de 10 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Adicionar um compartilhamento
> * Conectar-se ao compartilhamento

## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar compartilhamentos ao Azure Stack Edge Pro, verifique o seguinte:

* Você instalou o seu dispositivo físico conforme descrito em [Instalar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).

* Você ativou o dispositivo físico conforme descrito em [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

## <a name="add-a-share"></a>Adicionar um compartilhamento

Para criar um compartilhamento, siga o procedimento a seguir:

1. No [portal do Azure](https://portal.azure.com/), selecione o recurso do Azure Stack Edge e, em seguida, acesse **Visão Geral**. O dispositivo deve estar online. Selecione **Gateway de armazenamento em nuvem**.

   ![Dispositivo online](./media/azure-stack-edge-gpu-deploy-add-shares/device-online-1.png)

2. Selecione **+ Adicionar compartilhamento** na barra de comandos do dispositivo.

   ![Adicionar um compartilhamento](./media/azure-stack-edge-gpu-deploy-add-shares/select-add-share-1.png)

3. No painel **Adicionar compartilhamento**, siga estas etapas:

    a. Na caixa **Nome**, forneça um nome exclusivo para seu compartilhamento.  
    O nome do compartilhamento pode ter apenas letras, números e hifens. Ele deve entre 3 e 63 caracteres e começar com uma letra ou um número. Hifens devem ser precedidos e seguidos por uma letra ou um número.
    
    b. Escolha um **Tipo** para o compartilhamento.  
    O tipo pode ser **SMB** ou **NFS**, sendo SMB o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux.  
    O restante das opções varia levemente dependendo se você escolher compartilhamentos SMB ou NFS. 

    c. Forneça uma conta de armazenamento na qual o compartilhamento residirá.

    d. Na lista suspensa **Serviço de armazenamento**, selecione **Blob de Blocos**, **Blob de Páginas** ou **Arquivos**.  
    O tipo de serviço selecionado depende de qual formato você deseja que os dados usem no Azure. Neste exemplo, porque queremos armazenar os dados como blobs de blocos no Azure, selecionamos **Blob de Blocos**. Se você selecionar o **Blob de Páginas**, deverá garantir que seus dados sejam alinhados com 512 bytes. Por exemplo, um VHDX sempre é alinhado com 512 bytes.

   > [!IMPORTANT]
   > Se você estiver usando a conta de Armazenamento do Azure com um dispositivo do Azure Stack Edge Pro ou do Data Box Gateway, assegure que não existam políticas de imutabilidade definidas nessa conta. Para obter mais informações, confira [Definir e gerenciar políticas de imutabilidade para o armazenamento de blobs](../storage/blobs/storage-blob-immutability-policies-manage.md).

    e. Crie um contêiner de blob ou use um existente na lista suspensa. Se for criar um contêiner de blob, forneça um nome de contêiner. Se um contêiner ainda não existir, ele será criado na conta de armazenamento com o nome do compartilhamento criado recentemente.
   
    f. Dependendo de se você criou um compartilhamento SMB ou um compartilhamento NFS, execute uma das seguintes etapas: 
     
    - **Compartilhamento SMB**: Em **Todos os usuários locais com privilégios**, selecione **Criar novo** ou **Usar existente**. Se você criar um novo usuário local, digite um nome de usuário e a senha e, em seguida, confirme a senha. Essa ação atribui permissões para o usuário local. No momento, não há suporte para a modificação de permissões no nível de compartilhamento. Se você marcar a caixa de seleção **Permitir somente operações de leitura** para esses dados de compartilhamento, poderá especificar usuários somente leitura.
    
        ![Adicionar compartilhamento SMB](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-smb-1.png)
   
    - **Compartilhamento NFS**: Insira os endereços IP dos clientes permitidos que podem acessar o compartilhamento.

        ![Adicionar compartilhamento NFS](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-nfs-1.png)
   
4. Selecione **Criar** para criar o compartilhamento.
    
    Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, o bloco **Compartilhamentos** será atualizado para refletir o novo compartilhamento.
    

## <a name="connect-to-the-share"></a>Conectar-se ao compartilhamento

Agora, você pode se conectar a um ou mais compartilhamentos que criou na etapa anterior. As etapas podem ser diferentes dependendo de seu compartilhamento ser SMB ou NFS.

A primeira etapa é garantir que o nome do dispositivo possa ser resolvido quando você estiver usando o compartilhamento SMB ou NFS.

### <a name="modify-host-file-for-name-resolution"></a>Modificar arquivo de host para resolução de nomes

Agora, você adicionará o IP do dispositivo e o nome amigável do dispositivo que definiu na IU da Web local do dispositivo para:

- O arquivo de host no cliente OU
- O arquivo de host no servidor DNS

> [!IMPORTANT]
> Recomendamos que você modifique o arquivo de host no servidor DNS para a resolução de nome do dispositivo.

No cliente Windows que você está usando para se conectar ao dispositivo, execute as seguintes etapas:

1. Inicie o **Bloco de notas** como administrador e, em seguida, abra o arquivo **hosts** localizado em `C:\Windows\System32\Drivers\etc`.

    ![Arquivo de hosts do Windows Explorer](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-1.png)


2. Adicione a seguinte entrada ao arquivo de **hosts** substituindo-a por valores apropriados para o dispositivo: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    Você pode obter o IP do dispositivo da página **Rede** e o nome amigável do dispositivo da página **Dispositivo** na IU da Web local. A seguinte captura de tela do arquivo de hosts mostra a entrada:

    ![Arquivo de hosts do Windows Explorer 2](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>Conectar-se a um compartilhamento SMB

No seu cliente do Windows Server conectado ao seu dispositivo Azure Stack Edge Pro, conecte-se a um compartilhamento SMB inserindo os comandos:


1. Em uma janela Comando, digite:

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > É possível se conectar a um compartilhamento SMB somente com o nome do dispositivo e não por meio do endereço IP do dispositivo.

2. Quando você for solicitado a fazer isso, insira a senha para o compartilhamento.  
   O exemplo de saída desse comando é apresentado aqui.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. No teclado, selecione Windows + R.

4. Na janela **Executar**, especifique `\\<device name>` e, em seguida, selecione **OK**.  

    ![Caixa de diálogo Executar do Windows](media/azure-stack-edge-gpu-deploy-add-shares/run-window-1.png)

   O Explorador de Arquivos é aberto. Agora você deverá ser capaz de exibir os compartilhamentos que criou como pastas. No Explorador de Arquivos, clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.
 
    ![Conectar-se ao compartilhamento SMB](./media/azure-stack-edge-gpu-deploy-add-shares/file-explorer-smbshare-1.png)

    Os dados são gravados nesses compartilhamentos da forma em que são gerados e o dispositivo envia os dados para a nuvem.

### <a name="connect-to-an-nfs-share"></a>Conectar-se a um compartilhamento NFS

No cliente Linux conectado ao seu dispositivo Azure Stack Edge Pro, siga o procedimento abaixo:

1. Verifique se o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, use o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, vá para [Instalar cliente NFSv4](https://help.ubuntu.com/community/NFSv4Howto).

2. Após o cliente NFS ser instalado, monte o compartilhamento NFS criado em seu dispositivo Azure Stack Edge Pro usando o seguinte comando:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    É possível obter o IP do dispositivo na página **Rede** da IU da Web local.

    > [!IMPORTANT]
    > O uso da opção `sync` ao montar compartilhamentos melhora as taxas de transferência de arquivos grandes.
    > Antes de montar os compartilhamentos, verifique se os diretórios que atuarão como pontos de montagem no computador local já foram criados. Esses diretórios não devem conter arquivos nem subpastas.

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento em seu dispositivo Azure Stack Edge Pro. O IP do dispositivo é `10.10.10.60`. O compartilhamento `mylinuxshare2` é montado no ubuntuVM. O ponto de montagem do compartilhamento é `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> As seguintes condições são aplicáveis a esta versão:
> - Depois que um arquivo é criado no compartilhamento, não há suporte para a renomeação do arquivo. 
> - Excluir um arquivo de um compartilhamento não exclui a entrada na conta de Armazenamento do Azure.
> - Quando você usar o `rsync` para copiar por NFS, use o sinalizador `--inplace`. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu os seguintes tópicos sobre o Azure Stack Edge Pro:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar

Para saber como transformar seus dados usando o Azure Stack Edge Pro, prossiga para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar dados com o Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)
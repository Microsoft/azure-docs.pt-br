---
title: Tutorial para transferir dados a compartilhar com um Azure Stack Edge Pro | Microsoft Docs
description: Neste tutorial, saiba como adicionar e conectar-se a compartilhamentos em seu dispositivo Azure Stack Edge Pro para que Azure Stack Edge Pro possa transferir dados para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 8c9ad00a8910562e1a3a53af5120030bc482c927
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060203"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge-pro"></a>Tutorial: Transferir dados com o Azure Stack Edge Pro

Este tutorial descreve como adicionar e conectar-se a compartilhamentos no dispositivo Azure Stack Edge Pro. Depois de adicionar os compartilhamentos, o Azure Stack Edge Pro pode transferir dados para o Azure.

Esse procedimento leva cerca de 10 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se ao compartilhamento

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar compartilhamentos ao Azure Stack Edge Pro, verifique o seguinte:

- Você instalou o seu dispositivo físico conforme descrito em [Instalar o Azure Stack Edge Pro](azure-stack-edge-deploy-install.md).

- Você já ativou o dispositivo físico conforme descrito em [Conectar, configurar e ativar o Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Adicionar um compartilhamento

Para criar um compartilhamento, siga o procedimento a seguir:

1. No [portal do Azure](https://portal.azure.com/), selecione o recurso do Azure Stack Edge e, em seguida, acesse **Visão Geral**. O dispositivo deve estar online. Selecione **Gateway de armazenamento em nuvem**.

   ![Dispositivo online](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. Selecione **+ Adicionar compartilhamento** na barra de comandos do dispositivo.

   ![Adicionar um compartilhamento](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. No painel **Adicionar compartilhamento**, siga o procedimento a seguir:

    a. Na caixa **Nome**, forneça um nome exclusivo para seu compartilhamento.  
    O nome do compartilhamento pode ter apenas letras minúsculas, números e hifens. Ele deve entre 3 e 63 caracteres e começar com uma letra ou um número. Hifens devem ser precedidos e seguidos por uma letra ou um número.
    
    b. Escolha um **Tipo** para o compartilhamento.  
    O tipo pode ser **SMB** ou **NFS**, sendo SMB o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux.  
    O restante das opções varia levemente dependendo se você escolher compartilhamentos SMB ou NFS. 

    c. Forneça uma conta de armazenamento na qual o compartilhamento residirá. 

    > [!IMPORTANT]
    > Se você estiver usando a conta de Armazenamento do Azure com um dispositivo do Azure Stack Edge Pro ou do Data Box Gateway, assegure que não existam políticas de imutabilidade definidas nessa conta. Para obter mais informações, confira [Definir e gerenciar políticas de imutabilidade para o armazenamento de blobs](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    d. Na lista suspensa **Serviço de armazenamento**, selecione **Blob de Blocos**, **Blob de Páginas** ou **Arquivos**.  
    O tipo de serviço selecionado depende de qual formato você deseja que os dados usem no Azure. Neste exemplo, porque queremos armazenar os dados como blobs de blocos no Azure, selecionamos **Blob de Blocos**. Se você selecionar o **Blob de Páginas**, deverá garantir que seus dados sejam alinhados com 512 bytes. Por exemplo, um VHDX sempre é alinhado com 512 bytes.

    e. Crie um contêiner de blob ou use um existente na lista suspensa. Se for criar um contêiner de blob, forneça um nome de contêiner. Se um contêiner ainda não existir, ele será criado na conta de armazenamento com o nome do compartilhamento criado recentemente.
   
    f. Dependendo de se você criou um compartilhamento SMB ou um compartilhamento NFS, execute uma das seguintes etapas: 
     
    - **Compartilhamento SMB**: Em **Todos os usuários locais com privilégios**, selecione **Criar novo** ou **Usar existente**. Se você criar um novo usuário local, digite um nome de usuário e a senha e, em seguida, confirme a senha. Essa ação atribui permissões para o usuário local. Depois de atribuir as permissões aqui, você pode usar o Explorador de Arquivos para modificá-las.

        Se você marcar a caixa de seleção **Permitir somente operações de leitura** para esses dados de compartilhamento, poderá especificar usuários somente leitura.

        ![Adicionar compartilhamento SMB](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Compartilhamento NFS**: Insira os endereços IP dos clientes permitidos que podem acessar o compartilhamento.

        ![Adicionar compartilhamento NFS](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Selecione **Criar** para criar o compartilhamento.
    
    Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, o bloco **Compartilhamentos** será atualizado para refletir o novo compartilhamento.
    

## <a name="connect-to-the-share"></a>Conectar-se ao compartilhamento

Agora, você pode se conectar a um ou mais compartilhamentos que criou na etapa anterior. As etapas podem ser diferentes dependendo de seu compartilhamento ser SMB ou NFS.

### <a name="connect-to-an-smb-share"></a>Conectar-se a um compartilhamento SMB

No seu cliente do Windows Server conectado ao seu dispositivo Azure Stack Edge Pro, conecte-se a um compartilhamento SMB inserindo os comandos:


1. Em uma janela Comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Quando você for solicitado a fazer isso, insira a senha para o compartilhamento.  
   O exemplo de saída desse comando é apresentado aqui.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. No teclado, selecione Windows + R.

4. Na janela **Executar**, especifique `\\<device IP address>` e, em seguida, selecione **OK**.  
   O Explorador de Arquivos é aberto. Agora você deverá ser capaz de exibir os compartilhamentos que criou como pastas. No Explorador de Arquivos, clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.
 
    ![Conectar-se ao compartilhamento SMB](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    Os dados são gravados nesses compartilhamentos da forma em que são gerados e o dispositivo envia os dados para a nuvem.

### <a name="connect-to-an-nfs-share"></a>Conectar-se a um compartilhamento NFS

No cliente Linux conectado ao seu dispositivo Azure Stack Edge Pro, siga o procedimento abaixo:

1. Verifique se o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, use o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, vá para [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Após o cliente NFS ser instalado, monte o compartilhamento NFS criado em seu dispositivo Azure Stack Edge Pro usando o seguinte comando:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > O uso da opção `sync` ao montar compartilhamentos melhora as taxas de transferência de arquivos grandes.
    > Antes de montar os compartilhamentos, verifique se os diretórios que atuarão como pontos de montagem no computador local já foram criados. Esses diretórios não devem conter arquivos nem subpastas.

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento em seu dispositivo Azure Stack Edge Pro. O IP do dispositivo é `10.10.10.60`. O compartilhamento `mylinuxshare2` é montado no ubuntuVM. O ponto de montagem do compartilhamento é `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> As seguintes condições são aplicáveis a esta versão:
> - Depois que um arquivo é criado no compartilhamento, não há suporte para a renomeação do arquivo. 
> - Excluir um arquivo de um compartilhamento não exclui a entrada na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu os seguintes tópicos sobre o Azure Stack Edge Pro:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar

Para saber como transformar seus dados usando o Azure Stack Edge Pro, prossiga para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar dados com o Azure Stack Edge Pro](./azure-stack-edge-deploy-configure-compute.md)
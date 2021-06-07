---
title: Transferir dados com o Azure Data Box Gateway | Microsoft Docs
description: Saiba como adicionar e se conectar a compartilhamentos no Azure Data Box Gateway, então o dispositivo Gateway do Data Box poderá transferir dados para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 0c0ef6157ebf70c896fbac5ff692246e4fad2c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937198"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Tutorial: Transferir dados com o Azure Data Box Gateway


## <a name="introduction"></a>Introdução

Este artigo descreve como adicionar e conectar-se aos compartilhamentos no Data Box Gateway. Depois de adicionar os compartilhamentos, o dispositivo pode transferir dados para o Azure.

Esse procedimento leva cerca de 10 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar

## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar compartilhamentos ao Data Box Gateway, verifique se:

- Você provisionou um dispositivo virtual e se conectou a ele, conforme detalhado em [Provisionar um Data Box Gateway no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [Provisionar um Data Box Gateway no VMware](data-box-gateway-deploy-provision-vmware.md).

- Você ativou o dispositivo virtual descrito em [Conectar e ativar o Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- O dispositivo está pronto para você criar compartilhamentos e transferir dados.

## <a name="add-a-share"></a>Adicionar um compartilhamento

Para criar um compartilhamento, realize o seguinte procedimento:

1. No [portal do Azure](https://portal.azure.com/), selecione o recurso do Data Box Gateway e, em seguida, acesse **Visão Geral**. O dispositivo deve estar online. Selecione **+ Adicionar compartilhamento** na barra de comandos do dispositivo.
   
   ![Adicionar um compartilhamento](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Em **Adicionar Compartilhamento**, realize o seguinte procedimento:

    1. Forneça um nome exclusivo para seu compartilhamento. Os nomes de compartilhamentos podem conter apenas letras minúsculas, números e hifens. O nome do compartilhamento precisa ter entre 3 e 63 caracteres e começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen.
    
    2. Escolha um **Tipo** para o compartilhamento. O tipo pode ser SMB ou NFS, em que SMB é o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux. Dependendo da escolha entre compartilhamentos SMB ou NFS, as opções apresentadas serão ligeiramente diferentes.

    3. Forneça uma conta de armazenamento na qual o compartilhamento residirá. Se um contêiner ainda não existir, ele será criado na conta de armazenamento com o nome do compartilhamento criado recentemente. Se o contêiner já existir, esse contêiner será usado.
       > [!IMPORTANT]
       > Verifique se a conta do Armazenamento do Azure que você usa não tem políticas de imutabilidade definidas se você estiver a usando com um dispositivo do Data Box Gateway. Para obter mais informações, confira [Definir e gerenciar políticas de imutabilidade para o armazenamento de blobs](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    4. Escolha o **Serviço de armazenamento** entre blob de blocos, blobs de página ou arquivos. O tipo do serviço escolhido depende do formato escolhido para os dados que residirão no Azure. Por exemplo, nesta instância, queremos que os dados residam como blocos de blob no Azure e, portanto, vamos escolher o Blob de Blocos. Se escolher o Blob de Páginas, você deverá fazer com que seus dados sejam alinhados com 512 bytes. Por exemplo, um VHDX sempre é alinhado com 512 bytes.
   
    5. Esta etapa depende do tipo de compartilhamento criado, SMB ou NFS.
     
    - **Compartilhamento SMB** – em **Todos os usuários locais com privilégios**, selecione **Criar** ou **Usar existente**. Se você criar um usuário local, insira um **nome de usuário** e uma **senha** e, em seguida, **confirme a senha**. Essa ação atribui as permissões ao usuário local. No momento, não há suporte para a modificação de permissões no nível de compartilhamento.
    
        ![Adicionar compartilhamento SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Se você marcar a caixa de seleção **Permitir operações somente leitura** para esses dados de compartilhamento, poderá especificar usuários somente leitura.
        
    - **Compartilhamento NFS** – insira os endereços IP dos clientes permitidos que podem acessar o compartilhamento.

        ![Adicionar compartilhamento NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Selecione **Criar** para criar o compartilhamento.
    
    Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, o bloco **Compartilhamentos** será atualizado para refletir o novo compartilhamento.
    
    ![Bloco Compartilhamentos atualizado](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Conectar-se ao compartilhamento

Agora, você pode se conectar a um ou mais compartilhamentos que criou na etapa anterior. As etapas podem ser diferentes dependendo de seu compartilhamento ser SMB ou NFS.

### <a name="connect-to-an-smb-share"></a>Conectar-se a um compartilhamento SMB

No cliente do Windows Server conectado ao Data Box Gateway, conecte-se a um compartilhamento SMB inserindo os comandos:


1. Em uma janela Comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando solicitado, digite a senha do compartilhamento. O exemplo de saída desse comando é apresentado aqui.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. No teclado, selecione Windows + R. 
3. Na janela **Executar**, especifique `\\<device IP address>` e, em seguida, selecione **OK**. O Explorador de Arquivos é aberto. Agora você deverá ser capaz de exibir os compartilhamentos que criou como pastas. No Explorador de Arquivos, clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.
 
    ![Conectar-se ao compartilhamento SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Os dados são gravados nesses compartilhamentos da forma em que são gerados e o dispositivo envia os dados para a nuvem.

### <a name="connect-to-an-nfs-share"></a>Conectar-se a um compartilhamento NFS

No cliente Linux conectado ao seu dispositivo, realize o seguinte procedimento:

1. Verifique se o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, use o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, vá para [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Depois que o cliente NFS for instalado, use o seguinte comando para montar o compartilhamento NFS criado em seu dispositivo do Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Antes de configurar as montagens, verifique se os diretórios que atuarão como pontos de montagem em seu computador local já foram criados e, também, se eles não contêm arquivos ou subpastas.

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento no dispositivo do Gateway. O IP do dispositivo virtual é `10.10.10.60`, o compartilhamento `mylinuxshare2` é montado no ubuntuVM e `/home/databoxubuntuhost/gateway` é o ponto de montagem.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> As seguintes condições são aplicáveis a esta versão:
> - Depois que um arquivo é criado nos compartilhamentos, não há suporte para a renomeação do arquivo.
> - A exclusão de um arquivo de um compartilhamento não exclui a entrada na conta de armazenamento.
> - Se estiver usando `rsync` para copiar dados, a opção `rsync -a` não é compatível.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar


Avance para o próximo tutorial para aprender a administrar seu Data Box Gateway.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Data Box Gateway](https://aka.ms/dbg-docs)
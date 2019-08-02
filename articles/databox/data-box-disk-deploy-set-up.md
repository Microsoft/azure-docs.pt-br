---
title: Tutorial para desempacotar, se conectar a, desbloquear o Azure Data Box Disk | Microsoft Docs
description: Use este tutorial para aprender a configurar o Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 49f3f608ff34847905b219047af843db00da78c4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480041"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Tutorial: Desempacotar, conectar e desbloquear o Azure Data Box Disk

Este tutorial descreve como desempacotar, conectar e desbloquear o Azure Data Box Disk.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Desempacotar o Data Box Disk
> * Conectar-se a discos e obter a chave de acesso
> * Desbloquear discos no cliente Windows
> * Desbloquear discos no cliente Linux

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Solicitar o Azure Data Box Disk](data-box-disk-deploy-ordered.md).
2. Você recebeu os discos e o status do trabalho o portal foi atualizado para **Entregue**.
3. Você tem um computador cliente no qual pode instalar a ferramenta de desbloqueio do Data Box Disk. Computador cliente deve:
    - Executar um [Sistema operacional com suporte](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Ter outro [software obrigatório](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) instalado se for um cliente do Windows.  

## <a name="unpack-your-disks"></a>Desempacotar os discos

 Conclua as etapas a seguir para desempacotar os discos.

1. Os discos do Data Box Disks são enviados pelo correio em uma caixa pequena. Abra a caixa e remova o seu conteúdo. Verifique se a caixa possui de 1 a 5 discos de estado sólido (SSDs) e um cabo de conexão USB por disco. Inspecione a caixa e verifique se há algum sinal de adulteração ou qualquer outro dano visível. 

    ![Pacote de envio do Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Se a caixa estiver adulterada ou extremamente danificada, não abra-a. Entre em contato com o Suporte da Microsoft para obter ajuda e avaliar se os discos estão em boas condições de funcionamento e se é necessário enviar novos discos.
3. Verifique se a caixa possui um compartimento transparente com uma etiqueta de remessa (abaixo da etiqueta atual) para devolução. Se essa etiqueta for perdida ou danificada, você pode baixar e imprimir uma nova no portal do Azure. 

    ![Etiqueta de remessa do Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Guarde a caixa e a espuma da embalagem para devolver os discos.

## <a name="connect-to-disks-and-get-the-passkey"></a>Conectar-se a discos e obter a chave de acesso 

1. Use o cabo incluído para conectar o disco ao computador cliente executando um SO com suporte conforme especificado nos pré-requisitos. 

    ![Conectar o Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. No portal do Azure, vá até **Geral > Detalhes do Dispositivo**. Use o ícone de cópia para copiar a chave de acesso. Essa chave de acesso será usada para desbloquear os discos.

    ![Chave de acesso de desbloqueio do Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

As etapas para desbloquear os discos são diferentes de acordo com o cliente Windows ou Linux ao qual você está conectado.

## <a name="unlock-disks-on-windows-client"></a>Desbloquear discos no cliente Windows

Execute as seguintes etapas para conectar e desbloquear os discos.
     
1. No portal do Azure, vá até **Geral > Detalhes do Dispositivo**. 
2. Baixe o conjunto de ferramentas do Data Box Disk correspondente ao cliente do Windows. Esse conjunto de ferramentas contém 3 ferramentas: Ferramenta de Desbloqueio de Data Box Disk, a ferramenta de Validação de Data Box Disk e a ferramenta de Cópia Dividida de Data Box Disk. 

    Neste procedimento, você usará somente a ferramenta de Desbloqueio de Data Box Disk. As duas ferramentas serão usadas posteriormente.

    > [!div class="nextstepaction"]
    > [Baixe o conjunto de ferramentas do Data Box Disk para Windows](https://aka.ms/databoxdisktoolswin)         

3. Extraia o conjunto de ferramentas no mesmo computador que você usará para copiar os dados. 
4. Abra uma janela do prompt de comando ou execute o Windows PowerShell como administrador no mesmo computador.
5. (Opcional) Para verificar se o computador que você está usando para desbloquear o disco atende aos requisitos de sistema operacional, execute o comando de verificação do sistema. Um exemplo de saída é mostrado abaixo. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Execute `DataBoxDiskUnlock.exe` e forneça a chave de acesso que você obteve em [Conectar-se a discos e obter a chave de acesso](#connect-to-disks-and-get-the-passkey). A letra da unidade atribuída ao disco é exibida. Um exemplo de saída é mostrado abaixo.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Repita as etapas de desbloqueio para todas as futuras reinserções de disco. Use o comando `help` se você precisar de ajuda com a ferramenta de desbloqueio do Data Box Disk.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Depois que o disco estiver desbloqueado, você pode exibir o conteúdo do disco.    

    ![Conteúdo do Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

Se você tiver algum problema ao desbloquear os discos, veja como [solucionar problemas de desbloqueio](data-box-disk-troubleshoot-unlock.md). 

## <a name="unlock-disks-on-linux-client"></a>Desbloquear discos no cliente Linux

1. No portal do Azure, vá até **Geral > Detalhes do Dispositivo**. 
2. Baixe o conjunto de ferramentas do Data Box Disk correspondente ao cliente do Linux.  

    > [!div class="nextstepaction"]
    > [Baixe o conjunto de ferramentas do Data Box Disk para Linux](https://aka.ms/databoxdisktoolslinux) 

3. No cliente Linux, abra um terminal. Navegue até a pasta para a qual você baixou o software. Altere as permissões de arquivo para executar esses arquivos. Digite o seguinte comando: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Um exemplo de saída é mostrado abaixo. Após executar o comando chmod, é possível verificar se as permissões de arquivo foram alteradas executando o comando `ls`. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Execute o script para que ele instale todos os binários necessários para o software de desbloqueio do Data Box Disk. Use `sudo` para executar o comando como raiz. Após a instalação bem-sucedida dos binários, você verá uma observação sobre isso no terminal.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Primeiro, o script verificará se o computador cliente está executando um sistema operacional compatível. Um exemplo de saída é mostrado abaixo. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Digite `y` para continuar a instalação. Os pacotes que o script instala são: 
   - **epel-release**: repositório que contém os três pacotes a seguir. 
   - **dislocker e fuse-dislocker** – esses utilitários ajudam a descriptografar discos criptografados do BitLocker. 
   - **ntfs-3g**: pacote que ajuda a montar volumes NTFS. 
 
     Após a instalação bem-sucedida dos pacotes, o terminal exibirá uma notificação sobre isso.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Execute a ferramenta de desbloqueio do Data Box Disk. Forneça a chave de acesso do portal do Azure que você obteve em [Conectar-se a discos e obter a chave de acesso](#connect-to-disks-and-get-the-passkey). Como opção, especifique uma lista de volumes criptografados do BitLocker a serem desbloqueados. A lista de chaves de acesso e de volumes precisa ser especificada entre aspas simples. 

    Digite o seguinte comando.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’          

    O exemplo de saída é mostrado abaixo. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    O ponto de montagem do volume para o qual você pode copiar seus dados é exibido.

7. Repita as etapas de desbloqueio para quaisquer futuras reinserções de disco. Use o comando `help` se você precisar de ajuda com a ferramenta de desbloqueio do Data Box Disk. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    O exemplo de saída é mostrado abaixo. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Depois que o disco estiver desbloqueado, você pode ir para o ponto de montagem e exibir o conteúdo do disco. Agora você está pronto para copiar os dados para as pastas *BlockBlob* ou *PageBlob*. 

    ![Conteúdo do Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


Se você tiver algum problema ao desbloquear os discos, veja como [solucionar problemas de desbloqueio](data-box-disk-troubleshoot-unlock.md). 

::: zone-end

::: zone target="chromeless"

1. Descompacte discos e use o cabo incluído para conectar o disco ao computador cliente.
2. Baixe e extraia o conjunto de ferramentas do Data Box Disk no mesmo computador que você usará para copiar os dados.

    > [!div class="nextstepaction"]
    > [Baixe o conjunto de ferramentas do Data Box Disk para Windows](https://aka.ms/databoxdisktoolswin)

    ou o
    > [!div class="nextstepaction"]
    > [Baixe o conjunto de ferramentas do Data Box Disk para Linux](https://aka.ms/databoxdisktoolslinux) 

3. Para desbloquear os discos em um cliente Windows, abra uma janela do prompt de comando ou execute o Windows PowerShell como administrador no mesmo computador:

    - Digite o comando a seguir na mesma pasta em que a ferramenta de Desbloqueio do Data Box Disk está instalada.

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  Forneça a chave de acesso obtida em **Geral > Detalhes do dispositivo** no portal do Azure. A letra da unidade atribuída ao disco é exibida. 
4. Para desbloquear os discos em um cliente Linux, abra um terminal. Acesse a pasta na qual você baixou o software. Digite os seguintes comandos para alterar as permissões de arquivo, de modo que você possa executar esses arquivos: 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    Execute o script para instalar todos os binários necessários.

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Execute a ferramenta de desbloqueio do Data Box Disk. Forneça a chave de acesso no portal do Azure acessando **Geral > Detalhes do dispositivo**. Opcionalmente, especifique uma lista de volumes criptografados do BitLocker em aspas simples a serem desbloqueados.

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’
    ```      
5. Repita as etapas de desbloqueio para todas as futuras reinserções de disco. Use o comando de ajuda se você precisar de ajuda com a ferramenta de desbloqueio do Data Box Disk.

Depois que o disco for desbloqueado, você poderá exibir o conteúdo do disco.

Para obter mais informações sobre como configurar e desbloquear os discos, acesse [Tutorial: Desempacotar, conectar e desbloquear o Azure Data Box Disk](data-box-disk-deploy-set-up.md).

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Desempacotar o Data Box Disk
> * Conectar-se a discos e obter a chave de acesso
> * Desbloquear discos no cliente Windows
> * Desbloquear discos no cliente Linux


Avance para o próximo tutorial para saber como copiar dados para o Data Box Disk.

> [!div class="nextstepaction"]
> [Copiar dados para o seu Data Box Disk](./data-box-disk-deploy-copy-data.md)

::: zone-end


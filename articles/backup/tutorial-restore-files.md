---
title: Restaurar arquivos para uma VM com o Backup do Azure
description: Saiba como executar restaurações em nível de arquivo em uma VM do Azure com os serviços de backup e recuperação.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 553f19a2cb621698cb5b47e56d7cb30d529cc243
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467050"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Restaurar arquivos para uma máquina virtual no Azure
O Backup do Azure cria pontos de recuperação que são armazenados em cofres de recuperação com redundância geográfica. Ao restaurar de um ponto de recuperação, você pode restaurar a VM inteira ou arquivos individuais. Este artigo fornece detalhes de como restaurar arquivos individuais. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Liste e selecione os pontos de recuperação
> * Conecte um ponto de recuperação a uma VM
> * Restaurar arquivos de um ponto de recuperação

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.18 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Pré-requisitos
Este tutorial requer uma VM do Linux que tenha sido protegida com o Backup do Azure. Para simular um processo de exclusão acidental e recuperação de arquivo, você exclui uma página de um servidor Web. Se você precisar de uma VM do Linux que execute um servidor Web e tenha sido protegida com o Backup do Azure, consulte [Back up a virtual machine in Azure with the CLI](quick-backup-vm-cli.md) (Fazer backup de uma máquina virtual no Azure com a CLI).


## <a name="backup-overview"></a>Visão geral do backup
Quando o Azure inicia um backup, a extensão de backup na VM cria um instantâneo pontual. A extensão de backup é instalada na VM quando o primeiro backup é solicitado. O Backup do Azure também poderá criar um instantâneo do armazenamento subjacente se a VM não estiver em execução quando o backup ocorrer.

Por padrão, o Backup do Azure usa um backup consistente com o sistema de arquivos. Depois que o Backup do Azure cria o instantâneo, os dados são transferidos para o cofre dos Serviços de Recuperação. Para maximizar a eficiência, o Backup do Azure identifica e transfere apenas os blocos de dados que foram alterados desde o backup anterior.

Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.


## <a name="delete-a-file-from-a-vm"></a>Excluir um arquivo de uma VM
Se você excluir ou fizer alterações em um arquivo acidentalmente, será possível restaurar os arquivos individuais de um ponto de recuperação. Esse processo permite procurar os arquivos de backup em um ponto de recuperação e restaurar apenas os arquivos necessários. Neste exemplo, vamos excluir um arquivo de um servidor Web para demonstrar o processo de recuperação em nível de arquivo.

1. Para conectar-se à VM, obter o endereço IP da VM com [az vm show](/cli/azure/vm?view=azure-cli-latest#az-vm-show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Para confirmar se seu site da Web está funcionando no momento, abra um navegador da Web com o endereço IP público da VM. Deixe a janela do navegador da Web aberta.

    ![Página da Web NGINX padrão](./media/tutorial-restore-files/nginx-working.png)

3. Conecte-se à VM com SSH. Substitua *publicIpAddress* pelo endereço IP público que você obteve em um comando anterior:

    ```bash
    ssh publicIpAddress
    ```

4. Exclua a página padrão do servidor Web em */var/www/html/index.nginx-debian.html* da seguinte maneira:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. No navegador da Web, atualize a página da Web. O site da Web não carrega a página, conforme é mostrado no exemplo a seguir:

    ![O site NGINX não carrega mais a página padrão](./media/tutorial-restore-files/nginx-broken.png)

6. Feche a sessão SSH da VM da seguinte maneira:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Gerar script de recuperação de arquivo
Para restaurar os arquivos, o Backup do Azure fornece um script para ser executado na VM que conecta o ponto de recuperação a uma unidade local. Você pode procurar essa unidade local, restaurar arquivos para a própria VM e desconectar o ponto de recuperação. O Backup do Azure continua a fazer backup dos dados com base na política atribuída para agendamento e retenção.

1. Para listar os pontos de recuperação da VM, use [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). Neste exemplo, selecionamos o ponto de recuperação mais recente da VM denominada *myVM* que está protegido em *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Para obter o script que conecta ou monta o ponto de recuperação da VM, use [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp). O exemplo a seguir obtém o script para a VM denominada *myVM* que está protegida em *myRecoveryServicesVault*.

    Substitua *myRecoveryPointName* pelo nome do ponto de recuperação que você obteve no comando anterior:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    O script é baixado e uma senha é exibida, como no exemplo a seguir:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Para transferir o script para sua VM, use cópia segura (SCP). Forneça o nome do script baixado e substitua *publicIpAddress* pelo endereço IP público da VM. Lembre-se de incluir `:` no final do comando do SCP da seguinte maneira:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Restaurar arquivos na VM
Com o script de recuperação copiado para a VM, você pode conectar o ponto de recuperação e restaurar arquivos.

1. Conecte-se à VM com SSH. Substitua *publicIpAddress* pelo endereço IP público da VM da seguinte maneira:

    ```bash
    ssh publicIpAddress
    ```

2. Para permitir que o script seja executado corretamente, adicione permissões de execução com **chmod**. Insira o nome do seu próprio script:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Para montar o ponto de recuperação, execute o script. Insira o nome do seu próprio script:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Conforme o script for executado, você precisará inserir uma senha para acessar o ponto de recuperação. Insira a senha mostrada na saída do comando [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp) anterior que gerou o script de recuperação.

    A saída do script fornece o caminho para o ponto de recuperação. A saída de exemplo a seguir mostra que o ponto de recuperação está montado em */home/azureuser/myVM-20170919213536/Volume1*:

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. Use **cp** para copiar a página da Web NGINX padrão do ponto de recuperação montado de volta para o local do arquivo original. Substitua o ponto de montagem */home/azureuser/myVM-20170919213536/Volume1* pelo seu próprio local:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. No navegador da Web, atualize a página da Web. O site agora é carregado corretamente novamente, conforme é mostrado no exemplo a seguir:

    ![O site NGINX agora é carregado corretamente](./media/tutorial-restore-files/nginx-restored.png)

7. Feche a sessão SSH da VM da seguinte maneira:

    ```bash
    exit
    ```

8. Desmonte o ponto de recuperação da VM com [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-unmount-rp). O exemplo a seguir desmonta o ponto de recuperação da VM denominada *myVM* em *myRecoveryServicesVault*.

    Substitua *myRecoveryPointName* pelo nome do ponto de recuperação que você obteve nos comandos anteriores:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você conectou um ponto de recuperação a uma VM e restaurou arquivos para um servidor Web. Você aprendeu como:

> [!div class="checklist"]
> * Liste e selecione os pontos de recuperação
> * Conecte um ponto de recuperação a uma VM
> * Restaurar arquivos de um ponto de recuperação

Avance para o próximo tutorial para saber mais sobre como fazer backup do Windows Server para o Azure.

> [!div class="nextstepaction"]
> [Fazer backup do Windows Server para o Azure](tutorial-backup-windows-server-to-azure.md)


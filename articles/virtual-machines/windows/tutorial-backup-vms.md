---
title: Fazer backup de VMs do Windows do Azure | Microsoft Docs
description: Proteja as VMs do Windows fazendo backup delas com o Backup do Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8e58a2290e5034ef393f65cbcddb86e18cf4a6ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Fazer backup de máquinas virtuais do Windows no Azure

Você pode proteger seus dados fazendo backups em intervalos regulares. O Backup do Azure cria pontos de recuperação que são armazenados em cofres de recuperação com redundância geográfica. Ao restaurar de um ponto de recuperação, você pode restaurar a VM inteira ou apenas arquivos específicos. Este artigo explica como restaurar um único arquivo para uma VM que executa o Windows Server e o IIS. Se você ainda não tem uma VM para usar, você pode criar uma usando o [início rápido do Windows](quick-create-portal.md). Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um backup de uma VM
> * Agendar um backup diário
> * Restaurar um arquivo de um backup




## <a name="backup-overview"></a>Visão geral do backup

Quando o serviço de Backup do Azure inicia um trabalho de backup, ele dispara a extensão de backup para obter um instantâneo pontual. O serviço de Backup do Azure usa a extensão _VMSnapshot_. A extensão é instalada durante o primeiro backup de VM se a VM está em execução. Se a VM não estiver em execução, o serviço de Backup criará um instantâneo do armazenamento subjacente (já que nenhuma gravação de aplicativo ocorre enquanto a VM está parada).

Ao gerar um instantâneo de VMs do Windows, o Serviço de backup coordena com o VSS (Serviço de Cópias de Sombra de Volume) para obter um instantâneo consistente dos discos da máquina virtual. Depois que o serviço de Backup do Azure gera o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde o backup anterior.

Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.


## <a name="create-a-backup"></a>Criar um backup
Crie um backup diário agendado simples em um Cofre de Serviços de Recuperação. 

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **Máquinas virtuais**. 
3. Na lista, selecione uma VM da qual fazer backup.
4. Na folha da VM, na seção **Configurações**, clique em **Backup**. A folha **Habilitar backup** é aberta.
5. Em **Cofre de Serviços de Recuperação**, clique em **Criar novo** e forneça o nome para o novo cofre. Um novo cofre é criado no mesmo Grupo de Recursos e na mesma localização que a máquina virtual.
6. Clique em **Política de backup**. Para este exemplo, mantenha os padrões e clique em **OK**.
7. Na folha **Habilitar backup**, clique em **Habilitar Backup**. Isso cria um backup diário com base no agendamento padrão.
10. Para criar um ponto de recuperação inicial, na folha **Backup**, clique em **Fazer backup agora**.
11. Na folha **Fazer Backup Agora**, clique no ícone de calendário, use o controle de calendário para selecionar o último dia de retenção desse ponto de recuperação e clique em **Fazer Backup**.
12. Na folha **Backup** de sua VM, você verá o número de pontos de recuperação completos.

    ![Pontos de Recuperação](./media/tutorial-backup-vms/backup-complete.png)
    
O primeiro backup leva aproximadamente 20 minutos. Prossiga para a próxima parte deste tutorial depois que o backup for concluído.

## <a name="recover-a-file"></a>Recuperar um arquivo

Se você acidentalmente excluir ou fizer alterações em um arquivo, você poderá usar a recuperação de arquivo para recuperar o arquivo de seu cofre de backup. A Recuperação de Arquivo utiliza um script que é executado na VM para montar o ponto de recuperação como uma unidade local. Essas unidades permanecerão montadas por 12 horas para que você possa copiar arquivos do ponto de recuperação e restaurá-los para a VM.  

Neste exemplo, mostraremos como recuperar o arquivo de imagem que é usado na página da Web padrão para o IIS. 

1. Abra um navegador e conecte-se ao endereço IP da VM para mostrar a página padrão do IIS.

    ![Página da Web padrão do IIS](./media/tutorial-backup-vms/iis-working.png)

2. Conecte-se à VM.
3. Na VM, abra o **Explorador de Arquivos**, navegue até \inetpub\wwwroot e exclua o arquivo **iisstart.png**.
4. No computador local, atualize o navegador para ver se a imagem na página padrão do IIS foi removida.

    ![Página da Web padrão do IIS](./media/tutorial-backup-vms/iis-broken.png)

5. No computador local, abra uma nova guia e vá para o [Portal do Azure](https://portal.azure.com).
6. No menu à esquerda, selecione **Máquinas virtuais** e selecione a VM da lista.
8. Na folha da VM, na seção **Configurações**, clique em **Backup**. A folha **Backup** é aberta. 
9. No menu na parte superior da folha, selecione **Recuperação de Arquivo**. A folha **Recuperação de arquivo** será aberta.
10. Em **Etapa 1: selecionar um ponto de recuperação**, selecione um ponto de recuperação do menu suspenso.
11. Em **Etapa 2: baixar o script para procurar e recuperar arquivos**, clique no botão **Baixar Executável**. Salve o arquivo para a pasta **Downloads**.
12. No computador local, abra o **Explorador de Arquivos** e navegue até a pasta **Downloads** e copie o arquivo .exe baixado. O nome do arquivo será prefixado pelo nome da VM. 
13. Na sua VM (sobre a conexão RDP), cole o arquivo .exe na Área de Trabalho da VM. 
14. Navegue até a área de trabalho da sua VM e clique duas vezes no .exe. Isso iniciará um prompt de comando e, em seguida, montará o ponto de recuperação como um compartilhamento de arquivo que você poderá acessar. Quando a criação do compartilhamento for concluída, digite **p** para fechar o prompt de comando.
15. Na VM, abra o **Explorador de Arquivos** e navegue até a letra da unidade que foi usada para o compartilhamento de arquivos.
16. Navegue até \inetpub\wwwroot e copie **iisstart.png** do compartilhamento de arquivos e cole-o em \inetpub\wwwroot. Por exemplo, copie F:\inetpub\wwwroot\iisstart.png e cole-o em c:\inetpub\wwwroot para recuperar o arquivo.
17. No computador local, abra a guia do navegador em que você está conectado ao endereço IP da VM mostrando a página padrão do IIS. Pressione CTRL + F5 para atualizar a página do navegador. Agora, você verá que a imagem foi restaurada.
18. No computador local, volte para a guia do navegador para o Portal do Azure e, na **Etapa 3: desmontar discos após a recuperação**, clique no botão **Desmontar Discos**. Se você esquecer de fazer isso, a conexão para o ponto de montagem será fechada automaticamente após 12 horas. Após essas 12 horas, você precisa baixar um novo script para criar um novo ponto de montagem.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um backup de uma VM
> * Agendar um backup diário
> * Restaurar um arquivo de um backup

Avance para o próximo tutorial para saber mais sobre o monitoramento de máquinas virtuais.

> [!div class="nextstepaction"]
> [Monitorar máquinas virtuais](tutorial-monitoring.md)










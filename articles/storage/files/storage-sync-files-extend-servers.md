---
title: Tutorial – Estender servidores de arquivos do Windows com a Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como estender servidores de arquivos do Windows com a Sincronização de Arquivos do Azure, do início ao fim.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f71a27ea4da6bce5832287e948e0731672280196
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699497"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutorial: Estender servidores de arquivos do Windows com a Sincronização de Arquivos do Azure

Este artigo demonstra as etapas básicas para estender a capacidade de armazenamento de um Windows Server usando a Sincronização de Arquivos do Azure. Embora o tutorial apresente o Windows Server como uma VM (máquina virtual) do Azure, você normalmente fará esse processo para os servidores locais. Encontre instruções sobre como implantar a Sincronização de Arquivos do Azure em seu próprio ambiente no artigo [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Implantar o Serviço de Sincronização de Armazenamento
> * Preparar Servidores Windows para uso com Sincronização de arquivos do Azure
> * Instalar o agente de Sincronização de Arquivo do Azure
> * Registrar o Windows Server no Serviço de Sincronização de Armazenamento
> * Criar um grupo de sincronização e um ponto de extremidade de nuvem
> * Criar um ponto de extremidade do servidor

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

Para este tutorial, você precisará fazer o seguinte antes de implantar a Sincronização de Arquivos do Azure:

- Criar uma conta de armazenamento do Microsoft Azure e um compartilhamento de arquivo
- Configurar uma VM do Windows Server 2016 Datacenter
- Preparar a VM do Windows Server para a Sincronização de Arquivos do Azure

### <a name="create-a-folder-and-txt-file"></a>Criar uma pasta e um arquivo .txt

No computador local, crie uma nova pasta chamada _FilesToSync_ e adicione um arquivo de texto chamado _mytestdoc.txt_. Você carregará esse arquivo no compartilhamento de arquivo mais adiante neste tutorial.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos

Depois de implantar uma conta de armazenamento do Azure, você criará um compartilhamento de arquivo.

1. No portal do Azure, selecione **Ir para o recurso**.
1. Selecione **Arquivos** no painel da conta de armazenamento.

    ![Selecionar Arquivos](./media/storage-sync-files-extend-servers/click-files.png)

1. Selecione **+ Compartilhamento de Arquivo**.

    ![Selecionar o botão Adicionar compartilhamento de arquivo](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nomeie o novo compartilhamento de arquivo _afsfileshare_. Insira "1" para a **Cota** e, em seguida, selecione **Criar**. A cota pode ter um máximo de 5 TiB, mas você só precisa de 1 GB para este tutorial.

    ![Fornecer um nome e uma cota para o novo compartilhamento de arquivos](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selecione o novo compartilhamento de arquivo. Na localização do compartilhamento de arquivo, selecione **Carregar**.

    ![Carregar um arquivo](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Procure a pasta _FilesToSync_ em que você criou o arquivo .txt, selecione _mytestdoc.txt_ e **Carregar**.

    ![Procurar o compartilhamento de arquivos](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Nesta altura, você já criou uma conta de armazenamento e um compartilhamento de arquivo contendo um arquivo. Em seguida, você implantará uma VM do Azure com o Windows Server 2016 Datacenter para representar o servidor local neste tutorial.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Implantar uma VM e anexar um disco de dados

1. Acesse o portal do Azure e expanda o menu à esquerda. Escolha **Criar um recurso** no canto superior esquerdo.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace**, pesquise **Windows Server 2016 Datacenter** e selecione-o nos resultados. Escolha **Criar**.
1. Acesse a guia **Informações Básicas**. Em **Detalhes do projeto**, selecione o grupo de recursos criado para este tutorial.

   ![Inserir informações básicas sobre a VM na folha do portal](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Em **Detalhes da instância**, forneça um nome de VM. Por exemplo, use _myVM_.
1. Não altere as configurações padrão para **Região**, **Opções de disponibilidade**, **Imagem** e **Tamanho**.
1. Em **Conta de administrador**, forneça um **Nome de usuário** e uma **Senha** para a VM.
1. Em **Regras de porta de entrada**, escolha **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** no menu suspenso.

1. Antes de criar a VM, você precisará criar um disco de dados.

   1. Selecione **Avançar:Discos**.

      ![Adicionar discos de dados](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Na guia **Discos**, em **Opções de disco**, mantenha os padrões.
   1. Em **DISCOS DE DADOS**, selecione **Criar e anexar um novo disco**.

   1. Use as configurações padrão, exceto **Tamanho (GiB)** , que poderá ser alterado para **1 GB** para este tutorial.

      ![Detalhes do disco de dados](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Selecione **OK**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

   Selecione o ícone **Notificações** para inspecionar o **Progresso da implantação**. A criação de uma VM poderá levar alguns minutos para ser concluída.

1. Após a conclusão da implantação da VM, selecione **Ir para o recurso**.

   ![Ir para o recurso](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Nesta altura, você já criou uma nova máquina virtual e anexou um disco de dados. Em seguida, você se conectará à VM.

### <a name="connect-to-your-vm"></a>Conectar-se à sua VM

1. No portal do Azure, selecione **Conectar** na página de propriedades da máquina virtual.

   ![Conecte-se a uma VM do Azure no portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na página **Conectar-se à máquina virtual**, mantenha as opções padrão para se conectar pelo **endereço IP** na porta 3389. Selecione **Baixar Arquivo RDP**.

   ![Baixe o arquivo RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Abra o arquivo RDP baixado e selecione **Conectar** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite o nome de usuário como *localhost\nome de usuário*, insira a senha criada para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-sync-files-extend-servers/local-host2.png)

1. Você pode receber um aviso de certificado durante o processo de entrada. Selecione **Sim** ou **Continuar** para criar a conexão.

### <a name="prepare-the-windows-server"></a>Preparar o Windows Server

Para o servidor Windows Server 2016 Datacenter, desabilite a opção Configuração de Segurança Aprimorada do Internet Explorer. Essa etapa é necessária apenas para o registro inicial do servidor. Você pode habilitá-la novamente depois que o servidor foi registrado.

Na VM do Windows Server 2016 Datacenter, o Gerenciador do Servidor será aberto automaticamente.  Se o Gerenciador do Servidor não for aberto por padrão, pesquise-o no Explorador de Arquivos.

1. Em **Gerenciador do Servidor**, selecione **Servidor Local**.

   !["Servidor Local" no lado esquerdo da interface do usuário do Gerenciador do Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. No painel **Propriedades**, selecione o link de **Configuração de Segurança Aprimorada do IE**.  

    ![O painel "Configuração de Segurança Reforçada do IE" na interface do usuário do Gerenciador do Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Na caixa de diálogo **Configuração de Segurança Reforçada do Internet Explorer**, selecione **Desativado** para **Administradores** e **Usuários**.

    ![A janela pop-up da Configuração de Segurança Reforçada do Internet Explorer com "Desativado" selecionado](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Agora, você pode adicionar o disco de dados à VM.

### <a name="add-the-data-disk"></a>Adicionar o disco de dados

1. Enquanto estiver na VM do **Windows Server 2016 Datacenter**, selecione **Serviços de arquivos e armazenamento** > **Volumes** > **Discos**.

    ![Disco de dados](media/storage-sync-files-extend-servers/your-disk.png)

1. Clique com o botão direito do mouse no disco de 1 GB chamado **Disco Virtual Msft** e selecione **Novo volume**.
1. Conclua o assistente. Use as configurações padrão e anote a letra da unidade atribuída.
1. Selecione **Criar**.
1. Selecione **Fechar**.

   Nesta altura, você já colocou o disco online e criou um volume. Abra o Explorador de Arquivos na VM do Windows Server para confirmar a presença do disco de dados recém-adicionado.

1. No Explorador de Arquivos na VM, expanda **Este computador** e abra a nova unidade. É a unidade F: neste exemplo.
1. Clique com botão direito do mouse e selecione **Nova** > **Pasta**. Nomeie a pasta _FilesToSync_.
1. Abra a pasta **FilesToSync**.
1. Clique com botão direito do mouse e selecione **Novo** > **Documento de Texto**. Nomeie o arquivo de texto _MyTestFile_.

    ![Adicionar um novo arquivo de texto](media/storage-sync-files-extend-servers/new-file.png)

1. Feche o **Explorador de Arquivos** e o **Gerenciador do Servidor**.

### <a name="download-the-azure-powershell-module"></a>Baixar o módulo do Azure PowerShell

Em seguida, na VM do Windows Server 2016 Datacenter, instale o módulo do Azure PowerShell no servidor.

1. Na VM, abra uma janela do PowerShell com privilégios elevados.
1. Execute o comando a seguir:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Caso você tenha uma versão do NuGet que seja mais antiga do que 2.8.5.201, você deverá baixar e instalar a última versão do NuGet.

   Por padrão, a galeria do PowerShell não está configurada como um repositório confiável para o PowerShellGet. Na primeira vez que usar a PSGallery, você verá o seguinte prompt:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Responda **Sim** ou **Sim para Todos** para continuar com a instalação.

O módulo `Az` é um módulo de rollup para os cmdlets do Azure PowerShell. A instalação dele baixa todos os módulos disponíveis do Azure Resource Manager e disponibiliza seus cmdlets para uso.

Nesta altura, você já configurou seu ambiente para o tutorial. Você está pronto para implantar o Serviço de Sincronização de Armazenamento.

## <a name="deploy-the-service"></a>Implantar o serviço

Para implantar a Sincronização de Arquivos do Azure, primeiro coloque um recurso do **Serviço de Sincronização de Armazenamento** em um grupo de recursos da assinatura selecionada. O Serviço de Sincronização de Armazenamento herda as permissões de acesso da assinatura e do grupo de recursos.

1. No portal do Azure, selecione **Criar um recurso** e, em seguida, pesquise **Sincronização de Arquivos do Azure**.
1. Nos resultados da pesquisa, selecione **Sincronização de Arquivos do Azure**.
1. Selecione **Criar** para abrir a guia **Implantar Sincronização de Armazenamento**.

   ![Implantar Sincronização de Armazenamento](media/storage-sync-files-extend-servers/afs-info.png)

   No novo painel que será aberta, insira as seguintes informações:

   | Valor | DESCRIÇÃO |
   | ----- | ----- |
   | **Nome** | Um nome exclusivo (por assinatura) para o Serviço de Sincronização de Armazenamento.<br><br>Use _afssyncservice02_ para este tutorial. |
   | **Assinatura** | A assinatura do Azure usada para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que contém o Serviço de Sincronização de Armazenamento.<br><br>Use _afsresgroup101918_ para este tutorial. |
   | **Localidade** | Leste dos EUA |

1. Quando terminar, selecione **Criar** para implantar o **Serviço de Sincronização de Armazenamento**.
1. Selecione a guia **Notificações** > **Ir para o recurso**.

## <a name="install-the-agent"></a>Instalar o agente

O agente de Sincronização de arquivos do Azure é um pacote baixável que permite que o Windows Server seja sincronizado com um compartilhamento de arquivos do Azure.

1. Na VM do **Windows Server 2016 Datacenter**, abra o **Internet Explorer**.
1. Vá para o [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Role a tela para baixo até a seção **Agente de Sincronização de Arquivos do Azure** e selecione **Baixar**.

   ![Download do agente de sincronização](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Marque a caixa de seleção **StorageSyncAgent_V3_WS2016.EXE** e selecione **Avançar**.

   ![Selecionar agente](media/storage-sync-files-extend-servers/select-agent.png)

1. Selecione **Permitir uma vez** > **Executar** > **Abrir**.
1. Se você ainda não fez isso, feche a janela do PowerShell.
1. Aceite os padrões do **Assistente de Instalação do Agente de Sincronização de Armazenamento**.
1. Selecione **Instalar**.
1. Selecione **Concluir**.

Você implantou o Serviço de Sincronização do Azure e instalou o agente na VM do Windows Server 2016 Datacenter. Agora você precisa registrar a VM no Serviço de Sincronização de Armazenamento.

## <a name="register-windows-server"></a>Registrar o Windows Server

O registro do Windows Server em um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou o cluster) e o Serviço de Sincronização de Armazenamento. Um servidor só pode ser registrado em um único Serviço de Sincronização de Armazenamento. Ele pode ser sincronizado com outros servidores e compartilhamentos de arquivos do Azure associados a esse Serviço de Sincronização de Armazenamento.

A interface do usuário do Registro do Servidor deverá ser aberta automaticamente após a instalação do agente de Sincronização de Arquivos do Azure. Caso contrário, abra-a manualmente na localização do arquivo: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Quando a interface do usuário do Registro do Servidor for aberta na VM, selecione **OK**.
1. Selecione **Entrar** para começar.
1. Entre com suas credenciais de conta do Azure e selecione **Entrar**.
1. Forneça as seguintes informações:

   ![Uma captura de tela da interface do usuário de Registro do Servidor](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Valor | DESCRIÇÃO |
   | **Assinatura do Azure** | A assinatura que contém o Serviço de Sincronização de Armazenamento para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que contém o Serviço de Sincronização de Armazenamento. Use _afsresgroup101918_ para este tutorial. |
   | **Serviço de Sincronização de Armazenamento** | O nome do Serviço de Sincronização de Armazenamento. Use _afssyncservice02_ para este tutorial. |

1. Selecione **Registrar** para concluir o registro do servidor.
1. Como parte do processo de registro, você deverá entrar novamente. Entre e selecione **Avançar**.
1. Selecione **OK**.

## <a name="create-a-sync-group"></a>Criar um Grupo de Sincronização

Um grupo de sincronização define a topologia de sincronização para um conjunto de arquivos. Um grupo de sincronização precisa conter um ponto de extremidade da nuvem, que representa um compartilhamento de arquivo do Azure. Um grupo de sincronização também precisa conter um ou mais pontos de extremidade de servidor. Um ponto de extremidade do servidor representa um caminho em um servidor registrado. Para criar um grupo de sincronização:

1. No [portal do Azure](https://portal.azure.com/), selecione **+ Grupo de sincronização** no Serviço de Sincronização de Armazenamento. Use *afssyncservice02* para este tutorial.

   ![Criar um novo Grupo de Sincronização no Portal do Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Insira as seguintes informações para criar um grupo de sincronização com um ponto de extremidade da nuvem:

   | Valor | DESCRIÇÃO |
   | ----- | ----- |
   | **Nome do grupo de sincronização** | Esse nome deve ser exclusivo dentro do Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome lógico para você. Use *afssyncgroup* para este tutorial.|
   | **Assinatura** | A assinatura onde você implantou o Serviço de Sincronização de Armazenamento para este tutorial. |
   | **Conta de armazenamento** | Escolha **Selecionar conta de armazenamento**. No painel exibido, selecione a conta de armazenamento que contém o compartilhamento de arquivo do Azure criado. Use *afsstoracct101918* para este tutorial. |
   | **Compartilhamento de arquivos do Azure** | O nome do compartilhamento de arquivo do Azure criado. Use *afsfileshare* para este tutorial. |

1. Selecione **Criar**.

Se você selecionar o grupo de sincronização, verá que possui agora um **ponto de extremidade de nuvem**.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto de extremidade do servidor

Um ponto de extremidade de servidor representa uma localização específica em um servidor registrado. Por exemplo, uma pasta em um volume do servidor. Para adicionar um ponto de extremidade de servidor:

1. Selecione o grupo de sincronização recém-criado e, em seguida, **Adicionar ponto de extremidade de servidor**.

   ![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. No painel **Adicionar ponto de extremidade de servidor**, insira as seguintes informações para criar um ponto de extremidade de servidor:

   | | |
   | ----- | ----- |
   | Valor | DESCRIÇÃO |
   | **Servidor registrado** | O nome do servidor criado. Use *afsvm101918* para este tutorial. |
   | **Caminho** | O caminho do Windows Server para a unidade criada. Use *f:\filestosync* neste tutorial. |
   | **Disposição em camadas de nuvem** | Deixe desabilitada para este tutorial. |
   | **Espaço Livre do Volume** | Deixe em branco para este tutorial. |

1. Selecione **Criar**.

Os arquivos agora estão sincronizados entre o compartilhamento de arquivos do Azure e o Windows Server.

![Armazenamento do Azure sincronizado com êxito](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu as etapas básicas para estender a capacidade de armazenamento de um Windows Server usando a Sincronização de Arquivos do Azure. Para obter uma visão mais completa do planejamento de uma implantação da Sincronização de Arquivos do Azure, confira:

> [!div class="nextstepaction"]
> [Planejar a implantação da Sincronização de Arquivos do Azure](./storage-sync-files-planning.md)

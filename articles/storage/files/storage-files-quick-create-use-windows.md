---
title: Início Rápido do Azure – criar e usar um compartilhamento do serviço Arquivos do Azure em VMs do Windows | Microsoft Docs
description: Neste início rápido, você configura um compartilhamento do serviço Arquivos do Azure no portal do Azure e o conecta a uma máquina virtual do Windows. Você se conecta ao compartilhamento do serviço Arquivos e carrega um arquivo nele. Em seguida, tira um instantâneo do compartilhamento do Arquivos, modifica o arquivo no compartilhamento do Arquivos e restaura um instantâneo anterior do compartilhamento do Arquivos.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 626a6a5e0449d267b67653c2f63303226dd0c99d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699747"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Início Rápido: Criar e gerenciar compartilhamento do Armazenamento de Arquivos do Azure com máquinas de virtuais do Windows

O artigo demonstra as etapas básicas para criar e usar um compartilhamento do Arquivos do Azure. Neste início rápido, a ênfase é como configurar rapidamente um compartilhamento do Arquivos do Azure para que você possa experimentar o funcionamento do serviço. Se precisar de instruções mais detalhadas para criar e usar compartilhamentos de arquivo do Azure em seu próprio ambiente, consulte [Usar um compartilhamento de arquivos do Azure com o Windows](storage-how-to-use-files-windows.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

Neste início rápido, você configura os seguintes itens:

- Uma conta de armazenamento e um compartilhamento de arquivo do Azure
- Uma VM do Windows Server 2016 Datacenter

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Antes de poder trabalhar com um compartilhamento de arquivo do Azure, você precisa criar uma conta de armazenamento do Azure. Uma conta de armazenamento v2 de uso geral fornece acesso a todos os serviços do Armazenamento do Azure: blobs, arquivos, filas e tabelas. O guia de início rápido cria uma conta de armazenamento de uso geral v2, mas as etapas para criar qualquer tipo de conta de armazenamento são semelhantes. Uma conta de armazenamento pode conter uma quantidade ilimitada de compartilhamentos. Um compartilhamento pode conter uma quantidade ilimitada de arquivos, até os limites de capacidade da conta de armazenamento.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

Em seguida, crie um compartilhamento de arquivos.

1. Quando a implantação da conta de armazenamento do Azure for concluída, selecione **Ir para o recurso**.
1. Selecione **Arquivos** no painel da conta de armazenamento.

    ![Selecionar Arquivos](./media/storage-files-quick-create-use-windows/click-files.png)

1. Selecione **+ Compartilhamento de Arquivo**.

    ![Selecionar o botão Adicionar compartilhamento de arquivo](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nomeie o novo compartilhamento de arquivo *qsfileshare* > digite "1" para a **Cota** > selecione **Criar**. A cota pode ter um máximo de 5 TiB, mas você só precisa de 1 GiB para este início rápido.
1. Crie um novo arquivo txt chamado *qsTestFile* no computador local.
1. Selecione o novo compartilhamento de arquivo e, no local do compartilhamento de arquivo, selecione **Carregar**.

    ![Carregar um arquivo](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Navegue até o local onde você criou o arquivo .txt > selecione *qsTestFile.txt* > selecione **Carregar**.

Até agora, você criou uma conta de armazenamento do Azure e um compartilhamento de arquivo contendo um arquivo no Azure. Em seguida, você criará a VM do Azure com o Windows Server 2016 Datacenter para representar o servidor local neste início rápido.

### <a name="deploy-a-vm"></a>Implantar uma máquina virtual

1. Em seguida, expanda o menu no lado esquerdo do portal e escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace**, procure e selecione **Windows Server 2016 Datacenter**, em seguida, escolha **Criar**.
1. Na guia **Básico**, em **Detalhes do projeto**, selecione o grupo de recursos que você criou para este início rápido.

   ![Insira as informações básicas sobre sua VM na folha do portal](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Em **Detalhes da instância**, nomeie a VM *qsVM*.
1. Deixe as configurações padrão para **Região**, **Opções de disponibilidade**, **Imagem** e **Tamanho**.
1. Em **Conta de administrador**, adicione *VMadmin* como **Nome de usuário** e insira uma **Senha** para a VM.
1. Em **Regras de porta de entrada**, escolha **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** na lista suspensa.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**. A criação de uma nova VM levará alguns minutos para ser concluída.

1. Após a conclusão da implantação da VM, selecione **Ir para o recurso**.

Nesta altura, você já criou uma nova máquina virtual e anexou um disco de dados. Agora, você precisa se conectar à VM.

### <a name="connect-to-your-vm"></a>Conectar-se à sua VM

1. Selecione **Conectar** na página de propriedades da máquina virtual.

   ![Conecte-se a uma VM do Azure no portal](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na página **Conectar-se à máquina virtual**, mantenha as opções padrão para se conectar por **endereço IP** pela **porta número** *3389* e clique em **Baixar arquivo RDP**.
1. Abra o arquivo RDP baixado e selecione **Conectar** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite o nome de usuário como *localhost\nome de usuário*,em que &lt;nome de usuário&gt; é o nome do usuário administrador da VM que você criou para a máquina virtual. Insira a senha que você criou para a máquina virtual e selecione **OK**.

   ![Mais opções](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** ou **Continuar** para criar a conexão.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapeie o compartilhamento de arquivo do Azure para uma unidade do Windows

1. No portal do Azure, navegue até o compartilhamento de arquivo *qsfileshare* e selecione **Conectar**.
1. Copie o conteúdo da segunda caixa e cole-o no **Bloco de notas**.

   ![O caminho UNC do painel Conectar dos Arquivos do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Na VM, abra **Explorador de Arquivos** e selecione **Este PC** na janela. Esta seleção alterará os menus disponíveis na faixa de opções. No menu **Computador**, selecione **Mapear unidade de rede**.
1. Selecione a letra da unidade e digite o caminho UNC. Se você seguiu as sugestões de nomenclatura deste início rápido, copie *\\qsstorageacct.file.core.windows.net\qsfileshare* do **Bloco de notas**.

   Verifique se as duas caixas de seleção estão marcadas.

   ![Uma captura de tela da caixa de diálogo "Mapear unidade de rede"](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Selecione **Concluir**.
1. Na caixa de diálogo **Segurança do Windows**:

   - No Bloco de notas, copie o nome da conta de armazenamento iniciada por AZURE\ e cole-o na caixa de diálogo **Segurança do Windows** como o nome de usuário. Se você seguiu as sugestões de nomenclatura neste início rápido, copie *AZURE\qsstorageacct*.
   - No Bloco de notas, copie a chave da conta de armazenamento e cole-a na caixa de diálogo **Segurança do Windows** como a senha.

      ![O caminho UNC do painel Conectar dos Arquivos do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de compartilhamento

Agora que mapeou a unidade, você pode criar um instantâneo.

1. No portal, navegue até o compartilhamento de arquivo e selecione **Criar instantâneo**.

   ![Criar um instantâneo](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Na VM, abra *qstestfile.txt* e digite "Este arquivo foi modificado" > salve e feche o arquivo.
1. Crie outro instantâneo.

## <a name="browse-a-share-snapshot"></a>Procurar um instantâneo de compartilhamento

1. Em seu compartilhamento de arquivo, selecione **Exibir instantâneos**.
1. No painel **Instantâneos de compartilhamento de arquivo**, selecione o primeiro instantâneo da lista.

   ![Instantâneo selecionado na lista de carimbos de data/hora](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. No painel do instantâneo, selecione *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Restaurar de um instantâneo

1. Na folha de instantâneo do compartilhamento de arquivo, clique com o botão direito do mouse em *qsTestFile* e selecione o botão **Restaurar**.
1. Selecione **Substituir arquivo original**.

   ![Botões Baixar e Restaurar](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Na VM, abra o arquivo. A versão não modificada foi restaurada.

## <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento

1. Em seu compartilhamento de arquivo, selecione **Exibir instantâneos**.
1. No painel **Instantâneos de compartilhamento de arquivo**, selecione o último instantâneo da lista e clique em **Excluir**.

   ![Botão Excluir](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Usar um instantâneo de compartilhamento no Windows

Assim como acontece com instantâneos locais do VSS, você pode exibir os instantâneos de seu compartilhamento de arquivo do Azure montado usando a guia Versões Anteriores.

1. No Explorador de Arquivos, localize o compartilhamento montado.

   ![Compartilhamento montado no Explorador de Arquivos](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Selecione *qsTestFile.txt* e > clique com o botão direito do mouse e selecione **Propriedades** no menu.

   ![Clique com o botão direito do mouse no menu para um diretório selecionado](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Selecione **Versões Anteriores** para ver a lista de instantâneos de compartilhamento para esse diretório.

1. Selecione **Abrir** para abrir o instantâneo.

   ![Guia Versões Anteriores](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior

1. Selecione **Restaurar**. Esta ação copia o conteúdo de todo o diretório recursivamente para o local original no momento da criação do instantâneo.

   ![Botão Restaurar na mensagem de aviso](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar um compartilhamento de arquivos do Azure com o Windows](storage-how-to-use-files-windows.md)

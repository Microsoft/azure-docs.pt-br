---
title: Carregar o arquivo VHD no Azure DevTest Labs usando o Explorer do Armazenamento do Microsoft Azure | Microsoft Docs
description: Carregar o arquivo VHD na conta de armazenamento do laboratório usando o Explorer do Armazenamento do Microsoft Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3c187d104334fe75ec9e0ce41a3fdc14b508dfb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623256"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Carregar o arquivo VHD na conta de armazenamento do laboratório usando o Explorer do Armazenamento do Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os arquivos VHD podem ser usados para criar imagens personalizadas, que são usadas para provisionar máquinas virtuais. Este artigo ilustra como usar o [Explorer do Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar um arquivo VHD em uma conta de armazenamento do laboratório. Depois de carregar seu arquivo VHD, a [seção Próximas etapas](#next-steps) lista alguns dos artigos que ilustram como criar uma imagem personalizada do arquivo VHD carregado. Para saber mais sobre os discos e VHDs do Azure, confira [Introdução aos discos gerenciados](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As etapas a seguir mostram como carregar um arquivo VHD no DevTest Labs usando o [Explorer do Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Baixe e instale a versão mais recente do Explorer do Armazenamento do Microsoft Azure](https://www.storageexplorer.com).

1. Obtenha o nome da conta de armazenamento do laboratório usando o Portal do Azure:

    1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
    
    1. Na lista de laboratórios, selecione o laboratório desejado.  
    
    1. Na folha do laboratório, selecione **Configuração**. 
    
    1. Na folha **Configuração** do laboratório, selecione **Imagens personalizadas (VHDs)**.
    
    1. Na folha **Imagens personalizadas**, selecione **+Adicionar**. 
    
    1. Na folha **Imagem personalizada**, selecione **VHD**.
    
    1. Na folha **VHD**, selecione **Carregar um VHD usando o PowerShell**.
    
        ![Carregar o VHD usando o PowerShell][0]
    
    1. A folha **Carregar uma imagem usando o PowerShell** exibe uma chamada ao cmdlet **Add-AzureVhd**. O primeiro parâmetro (*Destino*) contém o nome da conta de armazenamento do laboratório no seguinte formato:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Anote o nome da conta de armazenamento, pois ele será usado em etapas posteriores.
    
1. Conecte-se a uma conta de assinatura do Azure usando o Explorer do Armazenamento.

    > [!TIP] 
    > 
    > O Explorer do Armazenamento dá suporte a várias opções de conexão. Esta seção ilustra como se conectar a uma conta de armazenamento associada à sua assinatura do Azure. Para ver as outras opções de conexão compatíveis com o Explorer do Armazenamento, veja o artigo [Introdução ao Explorer do Armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Abra o Explorer do Armazenamento.
    
    1. No Explorer do Armazenamento, selecione **Configurações de Conta do Azure**. 
    
        ![Configurações de conta do Azure][1]
    
    1. O painel esquerdo exibe as contas da Microsoft em que você fez logon. Para se conectar a outra conta, selecione **Adicionar uma conta**e siga os diálogos para entrar com uma conta da Microsoft associada a pelo menos uma assinatura ativa do Azure.
    
        ![Adicionar uma conta][2]
    
    1. Depois de entrar com êxito usando uma conta da Microsoft, o painel esquerdo é preenchido com as assinaturas do Azure associadas à conta. Selecione as assinaturas do Azure com as quais você deseja trabalhar e selecione **Aplicar**. (A seleção de **Todas as assinaturas** alterna a seleção de todas ou de nenhuma das assinaturas listadas do Azure.)
    
        ![Selecionar assinaturas do Azure][3]
    
    1. O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.
    
        ![Assinaturas do Azure selecionadas][4]

1. Localize a conta de armazenamento do laboratório:

    1. No painel esquerdo do Explorer do Armazenamento, localize e expanda o nó da assinatura do Azure que possui o laboratório.
    
    1. No nó da assinatura, expanda **Contas de Armazenamento**.

    1. Expanda o nó da conta de armazenamento do laboratório para revelar nós de **Contêineres de Blob**, **Compartilhamentos de Arquivos**, **Filas** e **Tabelas**.
    
    1. Expanda o nó **Contêineres de Blob**.
    
    1. Escolha o contêiner de blobs de carregamentos para exibir seu conteúdo no painel direito.
        
        ![Diretório de carregamento][5]

1. Carregue o arquivo VHD usando o Explorer do Armazenamento:

    1. No painel direito do Explorer do Armazenamento, você deve ver uma listagem dos blobs no contêiner de blob **carregamentos** da conta de armazenamento do laboratório. Na barra de ferramentas do editor de blobs, escolha **Carregar** 
        
        ![Botão Carregar][6]
    
    1. No menu suspenso **Carregar**, escolha **Carregar arquivos...**.
    
    1. Na caixa de diálogo **Carregar arquivos**, escolha as reticências.
        
        ![Escolher arquivo][8]  

    1. Na caixa de diálogo **Selecionar arquivo para carregar**, navegue até o arquivo VHD desejado, selecione-o e escolha **Abrir**.
    
    1. Quando retornar para a caixa de diálogo **Carregar arquivos**, altere **Tipo de blob** para **Blob de páginas**.
    
    1. Escolha **Carregar**.

        ![Escolher arquivo][9]  
    
    1. O painel **Log de Atividades** do Explorer do Armazenamento mostra o status de download (com os links para cancelar o carregamento). O processo de carregamento de um arquivo VHD pode ser demorado, dependendo do tamanho do arquivo VHD e da velocidade de conexão. 

        ![Status de carregamento do arquivo][10]  

## <a name="next-steps"></a>Próximas etapas

- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o Portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png

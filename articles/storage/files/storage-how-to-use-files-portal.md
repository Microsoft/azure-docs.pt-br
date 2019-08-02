---
title: Início Rápido para gerenciar compartilhamentos de arquivos do Azure com o portal do Azure
description: Use esse início rápido para saber como usar o portal do Azure para gerenciar os Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d7618d2542b1c1d83ed7328c8a32d80f2d0e66a
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699521"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Início Rápido: Criar e gerenciar compartilhamentos de arquivos do Azure com o portal do Azure 
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados no Windows, no Linux e no macOS. Este guia percorre os fundamentos de trabalhar com compartilhamentos de arquivos do Azure usando o [Portal do Azure](https://portal.azure.com/).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Para criar um compartilhamento de arquivos do Azure:

1. Selecione a conta de armazenamento no seu painel.
2. Na página da conta de armazenamento, na seção **Serviços**, selecione **Arquivos**.
    ![Uma captura de tela da seção de serviços da conta de armazenamento; selecione o Serviço de arquivos](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. No menu na parte superior da página **Serviço de arquivos**, clique em **+ Compartilhamento de arquivos**. O menu suspenso da página **Novo compartilhamento de arquivos**.
4. Em **Nome**, digite *myshare*.
5. Clique em **OK** para criar o compartilhamento de arquivos do Azure.

Os nomes de compartilhamento precisam ter somente letras em minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Usar o compartilhamento de arquivos do Azure
Os Arquivos do Azure fornecem dois métodos para trabalhar com arquivos e pastas dentro do seu compartilhamento de arquivos do Azure: o padrão do setor [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) e o [protocolo REST de arquivo](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar um compartilhamento de arquivos com SMB, consulte o documento abaixo com base em seu sistema operacional:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Usando um compartilhamento de arquivo do Azure no portal do Azure
Todas as solicitações feitas por meio do Portal do Azure são feitas com a API REST do arquivo, permitindo a você criar, modificar e excluir arquivos e diretórios nos clientes sem acesso ao SMB. É possível trabalhar diretamente com o protocolo REST de Arquivo (ou seja, criar manualmente chamadas REST HTTP por conta própria), mas a maneira mais comum (além de usar o portal do Azure) para usar o protocolo REST de Arquivo é usar o [módulo do Azure PowerShell](storage-how-to-use-files-powershell.md), a [CLI do Azure](storage-how-to-use-files-cli.md) ou um SDK do Armazenamento do Azure, que fornecem um bom wrapper do protocolo REST de Arquivo na linguagem de scripts/programação de sua escolha. 

Acreditamos que a maioria dos usos dos arquivos do Azure será com o compartilhamento em vez do protocolo SMB, pois isso permite usar os aplicativos e as ferramentas existentes que se espera poder usar, mas há vários motivos que mostram como é vantajoso usar a API REST de arquivo em vez de SMB, a saber:

- Você precisa fazer uma alteração rápida ao seu compartilhamento de arquivo do Azure em trânsito, como um laptop sem acesso ao SMB, tablet ou dispositivo móvel.
- Você precisa executar um script ou aplicativo de um cliente que não consegue montar um compartilhamento de SMB, como clientes locais que não têm a porta 445 desbloqueada.
- Você está aproveitando as vantagens dos recursos sem servidor, como o [Azure Functions](../../azure-functions/functions-overview.md). 

Os exemplos a seguir mostram como usar o portal do Azure para manipular o compartilhamento de arquivos do Azure com o protocolo REST de arquivo. 

Agora que você criou um compartilhamento de arquivos do Azure, pode montar o compartilhamento de arquivos com SMB no [Windows](storage-how-to-use-files-windows.md), no [Linux](storage-how-to-use-files-linux.md) ou no [macOS](storage-how-to-use-files-mac.md). Como alternativa, você pode trabalhar com o compartilhamento de arquivos do Azure com o Portal do Azure. 

#### <a name="create-a-directory"></a>Criar um diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure:

1. Na página **Serviço de arquivos**, selecione o compartilhamento de arquivos **myshare**. A página do compartilhamento de arquivos é aberta.
2. No menu na parte superior da página, selecione **+ Adicionar diretório**. Menu suspenso da página **Novo diretório**.
3. Digite *myDirectory* e clique em **OK**.

#### <a name="upload-a-file"></a>Carregar um arquivo 
Para demonstrar o upload de um arquivo, primeiro crie ou selecione um arquivo a ser carregado. Você pode fazer isso por qualquer meio que desejar. Depois de selecionar o arquivo que você deseja carregar:

1. Clique no diretório **myDirectory**. O painel **myDirectory** é aberto.
2. No menu na parte superior, clique em **Carregar**. O painel **Carregar arquivos** é aberto.  
    ![Uma captura de tela do painel de upload de arquivos](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Clique no ícone de pasta para abrir uma janela e procurar seus arquivos locais. 
4. Selecione um arquivo e clique em **Abrir**. 
5. Na página **Carregar arquivos**, verifique o nome do arquivo e clique em **Carregar**.
6. Quando terminar, o arquivo deverá aparecer na lista da página **myDirectory**.

#### <a name="download-a-file"></a>Baixar um arquivo
Você pode baixar uma cópia do arquivo carregado clicando com o botão direito do mouse no arquivo. Depois de clicar no botão de download, a experiência exata dependerá do sistema operacional e do navegador que você está usando.

## <a name="clean-up-resources"></a>Limpar recursos
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [O que Arquivos do Azure?](storage-files-introduction.md)

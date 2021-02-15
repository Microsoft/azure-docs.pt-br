---
title: Tutorial para copiar dados por meio de SMB no Azure Data Box | Microsoft Docs
description: Saiba como copiar dados para o Azure Data Box por SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 495b4427fb7e456c60a489b9ce3d19c2a44bd918
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680897"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb"></a>Tutorial: Copiar dados do Azure Data Box por meio do SMB

Este tutorial descreve como conectar-se e copiar dados do Data Box para um servidor local usando a IU da Web local. O dispositivo Data Box contém os dados exportados da sua conta de Armazenamento do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você realizou o pedido para o Azure Data Box.
    - Para uma ordem de importação, confira [Tutorial: Solicitar o Azure Data Box](data-box-deploy-ordered.md).
    - Para uma ordem de exportação, confira [Tutorial: Solicitar o Azure Data Box](data-box-deploy-export-ordered.md).
2. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**.
3. Você tem um computador host para o qual você deseja copiar os dados do Data Box. O computador host deve
   * Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
   * Estar conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se não houver uma conexão de 10 GbE disponível, use um link de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Conectar-se à caixa de dados

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Se estiver usando um computador host do Windows Server, siga estas etapas para conectar-se ao Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Vá para **conectar e copiar**. Selecione **Obter credenciais** para obter as credenciais de acesso dos compartilhamentos associados à sua conta de armazenamento. 

    ![Obter credenciais de compartilhamento](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. No compartilhamento de acesso e caixa de diálogo de dados de cópia, copie o **nome de usuário** e o **senha** correspondente para o compartilhamento. Selecione **OK**.
    
    ![Obter credenciais de compartilhamento, bem como acessar o compartilhamento e copiar dados](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Para acessar os compartilhamentos associados à sua conta de armazenamento (*exportbvtdataset2* no exemplo a seguir) no computador host, abra uma janela Comando. No prompt de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Dependendo do formato dos dados, os caminhos de compartilhamento serão os seguintes:
    - Blob de blocos do Azure – `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Blob de páginas do Azure – `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Arquivos do Azure – `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Quando solicitado, digite a senha do compartilhamento. A amostra a seguir mostra a conexão a um compartilhamento por meio do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. Pressione Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Selecione **OK** para abrir o Explorador de Arquivos.
    
    ![Conectar-se para compartilhar por meio do Explorador de Arquivos e inserir o IP do dispositivo](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Agora você verá os compartilhamentos como pastas.
    
    ![Conectar-se para compartilhar por meio do Explorador de Arquivos e exibir compartilhamentos](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Se estiver usando um cliente Linux, use o seguinte comando para montar o compartilhamento SMB. O parâmetro "vers" abaixo é a versão do SMB compatível com seu host do Linux. Conecte a versão adequada no comando a seguir. Para versões do SMB compatíveis com o Data Box, consulte [Sistemas de arquivo compatíveis para clientes Linux](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Copiar dados do Data Box

Quando você estiver conectado aos compartilhamentos do Data Box, a próxima etapa será copiar dados.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Depois de conectar-se ao compartilhamento SMB, comece a cópia de dados. Você pode usar qualquer ferramenta de cópia de arquivos compatível com SMB, como o Robocopy, para copiar seus dados. Vários trabalhos de cópia podem ser iniciados usando o Robocopy. 


Para mais informações sobre o comando Robocopy, vá para [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Quando a cópia estiver concluída, acesse o **Painel** e verifique o espaço usado e o espaço livre no dispositivo.

Agora você pode continuar a enviar o seu Data Box para a Microsoft.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados do Data Box

Avance para o próximo tutorial para saber como enviar o Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-export-picked-up.md)
---
title: Tutorial – compartilhar dados com clientes e parceiros usando o Azure Data Share (versão prévia)
description: Tutorial – compartilhar dados com clientes e parceiros usando o Azure Data Share (versão prévia)
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 01888f3656765b922c1b646e7ca8e07d81e799f3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838412"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Tutorial: Compartilhar seus dados usando o Azure Data Share (versão prévia)

Neste tutorial, você aprenderá a configurar um novo Azure Data Share e começará a compartilhar seus dados com clientes e parceiros fora da organização do Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Data Share.
> * Adicionar conjuntos de dados a seu Data Share.
> * Habilitar uma agenda de sincronização para seu Data Share. 
> * Adicionar destinatários a seu Data Share. 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente na permissão *Microsoft.Authorization/atribuições de função/gravação*. Essa permissão existe na função de proprietário. 
* O endereço de email de logon do Azure de seus destinatários (usar o alias de email não funcionará).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Criar uma conta do Data Share

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

1. Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

1. Pesquise *Data Share*.

1. Selecione o Data Share (versão prévia) e selecione **Criar**.

1. Preencha os detalhes básicos de seu recurso do Azure Data Share com as informações a seguir. 

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | NOME | *datashareacount* | Especifique um nome para sua conta de compartilhamento de dados. |
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que deseja usar para sua conta de compartilhamento de dados.|
    | Resource group | *test-resource-group* | Use um grupo de recursos existente ou crie um novo. |
    | Location | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
    | | |

1. Selecione **Criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos ou menos. 

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

## <a name="create-a-data-share"></a>Criar um Data Share

1. Navegue até sua página de Visão Geral do Data Share.

    ![Compartilhar seus dados](./media/share-receive-data.png "Compartilhar seus dados") 

1. Selecione **Começar a compartilhar seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes para seu Data Share. Especifique um nome, uma descrição do conteúdo de compartilhamento e os termos de uso (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Insira os detalhes de Compartilhamento") 

1. Selecione **Continuar**

1. Para adicionar Conjuntos de Dados a seu Data Share, selecione **Adicionar Conjuntos de Dados**. 

    ![Conjuntos de Dados](./media/datasets.png "Conjuntos de Dados")

1. Selecione o tipo de conjunto de dados que você deseja adicionar. 

    ![AddDatasets](./media/add-datasets.png "Adicionar Conjuntos de Dados")    

1. Navegue até o objeto que você gostaria de compartilhar e selecione "Adicionar Conjuntos de Dados". 

    ![SelectDatasets](./media/select-datasets.png "Selecionar Conjuntos de Dados")    

1. Na guia Destinatários, insira os endereços de email de seu Consumidor de Dados selecionando "+ Adicionar Destinatário". 

    ![AddRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**

1. Se você quiser que o consumidor de dados possa obter atualizações incrementais dos dados, habilite a agenda do instantâneo. 

    ![EnableSnapshots](./media/enable-snapshots.png "Habilitar instantâneos") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **Continuar**

1. Na guia Examinar + Criar, examine o Conteúdo do Pacote, Configurações, Destinatários e Configurações de Sincronização. Escolha **Criar**

O Azure Data Share agora foi criado e o destinatário de seu Compartilhamento de Dados agora está pronto para aceitar seu convite. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um Azure Data Share e convidar destinatários. Para saber mais sobre como um Consumidor de Dados pode aceitar e receber um compartilhamento de dados, prossiga para o tutorial [Aceitar e receber dados](subscribe-to-data-share.md). 
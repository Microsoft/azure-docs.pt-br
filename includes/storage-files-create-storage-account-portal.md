---
title: storage-files-create-storage-account-portal
description: Criar uma conta de armazenamento para arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03cf20e5c796a7092dc16c466934f377c945ad48
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509422"
---
Uma conta de armazenamento é um pool compartilhado de armazenamento no qual você pode implantar um compartilhamento de arquivos do Azure ou outros recursos de armazenamento como blobs ou filas. Uma conta de armazenamento pode conter uma quantidade ilimitada de compartilhamentos. Um compartilhamento pode conter uma quantidade ilimitada de arquivos, até os limites de capacidade da conta de armazenamento.

Para criar uma conta de armazenamento:

1. No menu esquerdo, selecione **+** para criar um recurso.
1. Na caixa de pesquisa, insira **conta de armazenamento**, selecione **Conta de armazenamento - blob, arquivo, tabela, fila** e selecione **Criar**.
    ![Uma captura de tela de como a entrada da conta de armazenamento se parecer na caixa de diálogo de pesquisa de recursos](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. Em **Nome**, insira *mystorageacct*, seguido por alguns números aleatórios até aparecer a marca de seleção verde indicando que é um nome exclusivo. Um nome de conta de armazenamento deve conter apenas letras minúsculas e ser globalmente exclusivo. Anote o nome da sua conta de armazenamento. Você o usará mais tarde. 
1. Em **Modelo de implantação**, deixe o valor padrão de **Gerenciador de Recursos**. Para saber mais sobre as diferenças entre o Azure Resource Manager e o modelo de implantação clássica, consulte [Entender os modelos de implantação e o estado de seus recursos](../articles/azure-resource-manager/management/deployment-models.md).
1. Em **Desempenho**, mantenha o valor padrão de **Standard**.
    
    > [!NOTE]
    > Este guia de início rápido cria um compartilhamento de arquivo padrão, mas, se você quiser usar compartilhamentos de arquivos premium, selecione **Premium**.

1. Em **Tipo de conta**, selecione **StorageV2**. Para saber mais sobre os diferentes tipos de contas de armazenamento, consulte [Noções básicas de contas de armazenamento do Azure](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

    > [!NOTE]
    > Este guia de início rápido cria uma conta de uso geral v2. Se você quiser usar compartilhamentos de arquivos premium, selecione **FileStorage**.

1. Em **Replicação**, selecione **Armazenamento com redundância local (LRS)** . 
1. Em **Transferência segura necessária**, é recomendável sempre selecionar **Habilitado**. Para saber mais sobre essa opção, consulte [Noções básicas sobre criptografia em trânsito](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
1. Em **Assinatura**, selecione a assinatura que foi usada para criar a conta de armazenamento. Se você tiver apenas uma assinatura, ela deverá ser a padrão.
1. Em **Grupo de recursos**, selecione **Criar novo**. Para o nome, insira *myResourceGroup*.
1. Em **Localização**, selecione **Leste dos EUA**.
1. Em **Redes virtuais**, deixe a opção padrão como **Desabilitado**. 
1. Selecione **Fixar no painel** para tornar o armazenamento de conta mais fácil de ser encontrado.
1. Ao terminar, selecione **Criar** para iniciar a implantação.
---
title: Tutorial sobre como devolver o Azure Data Box Heavy | Microsoft Docs
description: Saiba como enviar o Azure Data Box Heavy para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 84db33e4c7ac612353c590ac9d2904ac3bc48d38
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592387"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box Heavy e verificar o upload de dados no Azure


Este tutorial descreve como devolver o Azure Data Box Heavy e verificar os dados carregados no Azure.

Neste tutorial, você aprenderá sobre tópicos como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box Heavy para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Heavy

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

- Você concluiu o [Tutorial: Copiar dados para o Azure Data Box e verificá-los](data-box-heavy-deploy-copy-data.md).
- Trabalhos de cópia estão concluídos. A Preparação para o envio não poderá ser executada se os trabalhos de cópia estiverem em andamento.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Devolver o Data Box Heavy

1. Verifique se o dispositivo está desligado e se todos os cabos foram removidos. Enrole e coloque os quatro cabos de alimentação com segurança na bandeja que você pode acessar na parte traseira do dispositivo.
2. O dispositivo é enviado com frete LTL por FedEx nos EUA e DHL na UE

    1. Contate as [Operações do Data Box](mailto:DataBoxOps@microsoft.com) para informar sobre a retirada e obter a etiqueta de remessa de devolução.
    2. Ligue para o número local da transportadora para agendar a retirada.
    3. Certifique-se de que a etiqueta de remessa é exibida com destaque no exterior da remessa.
    4. Lembre-se de remover do dispositivo as etiquetas de remessa antigas da remessa anterior.
3. Após a retirada e o exame do Data Box Heavy pela transportadora, o status do pedido no portal será atualizado para **Retirado**. Uma ID de rastreamento também é exibida.

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e verificar o dispositivo, o status do pedido será atualizado para **Recebido**. Em seguida, o dispositivo passa por uma verificação física de danos ou sinais de violação.

Após a conclusão da verificação, o Data Box Heavy será conectado à rede no datacenter do Azure. A cópia de dados começa automaticamente. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

Depois que a cópia for concluída, o status do pedido será atualizado para **Concluído**.

Verifique se seus dados estão carregados no Azure antes de excluí-los da fonte. Seus dados podem estar em:

- Suas contas de Armazenamento do Microsoft Azure. Quando você copia os dados para o Data Box, dependendo do tipo, eles são carregados em um dos caminhos a seguir em sua conta do Armazenamento do Azure.

  - Para blobs de página e blobs de bloco: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Arquivos do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Como alternativa, você pode acessar sua conta de Armazenamento do Azure no portal do Azure e navegar de lá.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como blob de páginas e, em seguida, convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido. 

    - Se a cópia para discos gerenciados no Azure tiver êxito, você poderá acessar os **Detalhes do pedido** no portal do Azure e anotar o grupo de recursos especificado para discos gerenciados.

        ![Identificar os grupos de recursos de disco gerenciado](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Vá para o grupo de recursos anotado e localize os discos gerenciados.

        ![Disco gerenciado anexado aos grupos de recursos](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se você copiou um VHDX, ou um VHD diferencial/dinâmico, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de páginas, mas a conversão de VHD para as falhas de disco gerenciado. Acesse sua **Conta de armazenamento de preparo > Blobs** e selecione o contêiner apropriado - SSD Standard, HDD Standard ou SSD Premium. Os VHDs serão carregados como blob de páginas na conta de armazenamento temporário.

## <a name="erasure-of-data-from-data-box-heavy"></a>Eliminar dados do Data Box Heavy
 
Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Após a conclusão da eliminação, você poderá [Baixar o histórico de pedidos](data-box-portal-admin.md#download-order-history).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box Heavy para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Heavy

Avance para o artigo a seguir para saber como gerenciar o Data Box Heavy por meio da IU da Web local.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Azure Data Box](./data-box-local-web-ui-admin.md)



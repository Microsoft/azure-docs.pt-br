---
title: Tutorial sobre como devolver o Azure Data Box | Microsoft Docs
description: Saiba como enviar o Azure Data Box para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 7/08/2019
ms.author: alkohli
ms.openlocfilehash: db0f0ac3073687b7c1cd8ca60e459e4bb3aa03f4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626350"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box e verificar o upload de dados para o Azure

Este tutorial descreve como devolver o Azure Data Box e verificar os dados carregados no Azure.

Neste tutorial, você aprenderá sobre tópicos como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

- Você concluiu o [Tutorial: Copiar dados para o Azure Data Box e verificá-los](data-box-deploy-copy-data.md). 
- Trabalhos de cópia estão concluídos. A Preparação para o envio não poderá ser executada se os trabalhos de cópia estiverem em andamento.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Devolver o Data Box

Verifique se a cópia de dados no dispositivo foi concluída e se a execução de **Preparação para o envio** foi bem-sucedida. De acordo com a região para a qual você está enviando o dispositivo, o procedimento é diferente.


### <a name="ship-in-us-canada-europe"></a>Envio nos EUA, no Canadá e na Europa

Execute as etapas a seguir se estiver devolvendo o dispositivo nos EUA, no Canadá ou na Europa.

1. Verifique se o dispositivo está desligado e se os cabos foram removidos. 
2. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte posterior do dispositivo.
3. Certifique-se de que a etiqueta de remessa esteja aparecendo no papel eletrônico e agende uma retirada com a operadora. Se a etiqueta for danificada ou perdida ou não for exibida na tela do E-ink, contate o Suporte da Microsoft. Se o Suporte sugerir, acesse **Visão Geral > Baixar etiqueta de remessa** no portal do Azure. Baixe a etiqueta de remessa e afixe-a ao dispositivo. 
4. Agende uma retirada com a UPS se estiver devolvendo o dispositivo. Para agendar uma retirada:

    - Ligue para a UPS local (linha gratuita específica do país/região).
    - Em sua chamada, mencione a remessa inversa, conforme mostrado na exibição E-ink ou sua etiqueta impressa de número de controle.
    - Se o número de controle não está entre aspas, o serviço de no-break exigirá que você pague um encargo adicional durante a retirada.

    Em vez de agendar a retirada, você também pode descartar o Data Box no local mais próximo de redistribuição.
4. Após a coleta e registro do Data Box por sua operadora, o status do pedido no portal será atualizado para **Recolhido**. Uma ID de rastreamento também é exibida.

### <a name="ship-in-asia-pacific-region"></a>Envio na região do Pacífico Asiático

#### <a name="ship-in-australia"></a>Envio na Austrália

Datacenters do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as etapas a seguir para o envio na Austrália.


1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Verifique se a cópia de dados no dispositivo foi concluída e se a execução de **Preparação para o envio** foi bem-sucedida.
3. Desligue o dispositivo e remova os cabos.
4. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
5. Envie um email para a Quantium Solutions para solicitar uma retirada. Veja o número de referência do serviço especificado no portal do Azure. Use o seguinte modelo de email: – *Solicitar a etiqueta de remessa reversa com o código TAU*. Inclua os seguintes detalhes no email: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. A Quantium Solutions Australia enviará um email a você com uma etiqueta de remessa de devolução.
7. Imprima a etiqueta de devolução e afixe-a na caixa de envio.
8. Entregue o pacote para a transportadora.

Se necessário, ligue para o Suporte da Quantium Solutions ou envie um email para Azure@quantiumsolutions.com.


Caso tenha alguma pergunta sobre seu pedido por telefone:

- Envie um email para retirada primeiro.
- Forneça o nome do pedido no telefone.

#### <a name="ship-in-japan"></a>Envio no Japão 

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Desligue o dispositivo e remova os cabos.
3. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
4. Escreva as informações de nome e endereço da sua empresa na nota de consignação como suas informações de remetente.
5. Envie um email para a Quantium Solutions usando o modelo de email a seguir.

    - Se a nota de consignação do Japan Post Chakubarai não tiver sido incluída ou estiver ausente, indique isso nesse email. A Quantium Solutions Japan solicitará ao Japan Post que traga a nota de consignação após a retirada.
    - Caso você tenha vários pedidos, envie um email para garantir a retirada individual.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Receba um email de confirmação da Quantium Solutions depois de agendar uma retirada. O email de confirmação também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você poderá contatar o Suporte da Quantium Solutions (em japonês) com as seguintes informações: 

- Email：Customerservice.JP@quantiumsolutions.com 
- Telefone：03-5755-0150 


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e verificar o dispositivo, o status do pedido será atualizado para **Recebido**. Em seguida, o dispositivo passa por uma verificação física de danos ou sinais de violação.

Após a conclusão da verificação, o Data Box será conectado à rede no datacenter do Azure. A cópia de dados começa automaticamente. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

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

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

Avance para o seguinte artigo para aprender a gerenciar o Data Box por meio da interface do usuário da Web local.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Azure Data Box](./data-box-local-web-ui-admin.md)



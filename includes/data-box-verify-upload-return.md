---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/30/2019
ms.author: alkohli
ms.openlocfilehash: ca7b83d24f2416b224963559361faf5a7775cd0d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631522"
---
Quando a Microsoft receber e verificar o dispositivo, o status do pedido será atualizado para **Recebido** . Em seguida, o dispositivo passa por uma verificação física de danos ou sinais de violação.

Após a conclusão da verificação, o Data Box será conectado à rede no datacenter do Azure. A cópia de dados começa automaticamente. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

Depois que a cópia for concluída, o status do pedido será atualizado para **Concluído** .

Verifique se seus dados estão carregados no Azure antes de excluí-los da fonte. Seus dados podem estar em:

- Suas contas de Armazenamento do Microsoft Azure. Quando você copia os dados para o Data Box, eles são carregados em um dos seguintes caminhos em sua conta do Armazenamento do Azure:

  - Para blobs de página e blobs de bloco: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Arquivos do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Como alternativa, você pode acessar sua conta de Armazenamento do Azure no portal do Azure e navegar de lá.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como blob de páginas e, em seguida, convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido. 

    - Se a cópia para discos gerenciados no Azure tiver êxito, você poderá acessar os **Detalhes do pedido** no portal do Azure e anotar o grupo de recursos especificado para discos gerenciados.

        ![Identificar os grupos de recursos de disco gerenciado](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        Vá para o grupo de recursos anotado e localize os discos gerenciados.

        ![Disco gerenciado anexado aos grupos de recursos](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - Se você copiou um VHDX ou um VHD diferencial ou dinâmico, o VHDX ou VHD será carregado para a conta de armazenamento de preparo como um blob de páginas, mas a conversão do VHD para um disco gerenciado falhará. Acesse sua **Conta de armazenamento de preparo > Blobs** e selecione o contêiner apropriado – SSD Standard, HDD Standard ou SSD Premium. Os VHDs serão carregados como blobs de páginas na conta de armazenamento de preparo e acumularão encargos.


## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Prerequisites
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

Avance para o seguinte artigo para aprender a gerenciar o Data Box por meio da interface do usuário da Web local.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Azure Data Box](../articles/databox/data-box-local-web-ui-admin.md)
---
title: Exibir status de trabalhos de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como exibir o status dos trabalhos de importação/exportação do Azure e as unidades usadas. Entenda os fatores que afetam o tempo necessário para processar um trabalho.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8ef18ea663f3a77589d61ed89c50df38f5cf0d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176120"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Exibir o status de trabalhos de Importação/Exportação do Azure

Este artigo fornece informações sobre como exibir o status da unidade e do trabalho para trabalhos de Importação/Exportação do Azure. O serviço de importação/exportação do Azure é usado para transferir grandes quantidades de dados com segurança para Blobs do Azure e Arquivos do Azure. O serviço também é usado para exportar dados de armazenamento de Blobs do Azure.  

## <a name="view-job-and-drive-status"></a>Exibir o status do trabalho e da unidade
Você pode acompanhar o status de seus trabalhos de importação ou exportação na guia **importação/exportação** do portal do Azure.
1. Faça logon em https://portal.azure.com/.
2. Pesquise **trabalhos de importação/exportação**.

    ![Pesquisar em trabalhos de importação/exportação](./media/storage-import-export-view-drive-status/open-import-export-tab.png)

 3. Uma lista de seus trabalhos de importação/exportação aparece na página.

    ![Exibir o estado do trabalho](./media/storage-import-export-view-drive-status/job-state.png)

4. Selecione e clique em um trabalho para exibir os detalhes do trabalho.

   ![Exibir status detalhado do trabalho](./media/storage-import-export-view-drive-status/job-detail.png)
  
## <a name="view-job-status"></a>Exibir status do trabalho

Você verá um dos seguintes status de trabalho, dependendo de onde a unidade está no processo.

| Status do Trabalho | Descrição |
|:--- |:--- |
| Criando | Após a criação de um trabalho, seu estado será definido como **Criando**. Enquanto o trabalho está no estado **criando** , o serviço de importação/exportação assume que as unidades não foram enviadas para o Data Center. Um trabalho pode permanecer nesse estado por até duas semanas, após o qual ele é excluído automaticamente pelo serviço. |
| Remessa | Depois que você enviar seu pacote, atualize as informações de rastreamento no Portal do Azure.  Fazer isso transforma o trabalho em estado de **envio** . O trabalho permanece no estado **Enviando** por até duas semanas. 
| Recebido | Depois que todas as unidades forem recebidas no data center, o estado do trabalho será definido como **Recebido**. |
| Transferindo | Após pelo menos uma unidade começar o processamento, o estado do trabalho será definido como **Transferindo**. Para obter mais informações, consulte [Estados de unidade](#view-drive-status). |
| Empacotamento | Depois que todas as unidades tiverem concluído o processamento, o trabalho será colocado no estado **Empacotamento** até que as unidades sejam enviadas de volta para você. |
| Concluído | Depois que todas as unidades forem enviadas de volta para você, se o trabalho tiver sido concluído sem erros, ele será definido como **Concluído**. O trabalho será excluído automaticamente após 90 dias no estado **Concluído**. |
| Fechadas | Depois que todas as unidades forem enviadas de volta para você, se houver erros durante o processamento do trabalho, o trabalho será definido como **fechado**. O trabalho é excluído automaticamente após 90 dias no estado **Fechado**. |

## <a name="view-drive-status"></a>Exibir status da unidade

A tabela a seguir descreve o ciclo de vida de uma unidade individual conforme ela passa por um trabalho de importação ou exportação. O estado atual de cada unidade em um trabalho fica visível no Portal do Azure.

A tabela a seguir descreve cada estado pelo qual cada unidade em um trabalho pode passar.

| Estado da unidade | Descrição |
|:--- |:--- |
| Especificado | Para um trabalho de importação, quando o trabalho é criado no Portal do Azure, o estado inicial de uma unidade é **Especificado**. Para um trabalho de exportação, como nenhuma unidade é especificada quando o trabalho é criado, o estado inicial da unidade é **Recebido**. |
| Recebido | A unidade passa para o estado **Recebido** quando o serviço de Importação/Exportação tiver processado as unidades que foram recebidas da empresa transportadora para um trabalho de importação. Para um trabalho de exportação, o estado inicial da unidade é o estado **Recebido**. |
| Nunca recebido | A unidade passará para o estado **Nunca recebido** quando o pacote de um trabalho chegar, mas não contiver a unidade. Uma unidade também se moverá para esse Estado se o datacenter ainda não tiver recebido o pacote e o serviço tiver recebido as informações de envio pelo menos duas semanas atrás. |
| Transferindo | Uma unidade passará para o estado **Transferindo** quando o serviço começar a transferir dados da unidade para o Armazenamento do Microsoft Azure. |
| Concluído | Uma unidade passará para o estado **Concluído** quando o serviço tiver transferido com êxito todos os dados sem erros.
| Concluído mais informações | Uma unidade passa para o estado **concluído mais informações** quando o serviço apresenta problemas ao copiar dados de ou para a unidade. As informações podem incluir erros, avisos ou mensagens informativas sobre a substituição de blobs.
| Enviado de volta | Uma unidade passará para o estado **Enviado de volta** quando for enviada do data center para o endereço de retorno. |

Essa imagem no Portal do Azure exibe o estado da unidade de um trabalho de exemplo:

![Exibir estado da unidade](./media/storage-import-export-view-drive-status/drive-state.png)

A tabela a seguir descreve os estados de falha de unidade e as ações executadas para cada estado.

| Estado da unidade | Evento | Resolução/Próxima etapa |
|:--- |:--- |:--- |
| Nunca recebido | Uma unidade marcada como **nunca recebido** (porque ela não foi recebida como parte da remessa do trabalho) chega em outra remessa. | A equipe de operações move a unidade para **Recebido**. |
| N/D | Uma unidade que não faz parte de nenhum trabalho chega ao datacenter como parte de outro trabalho. | A unidade está marcada como uma unidade extra. Ele é retornado a você quando o trabalho associado ao pacote original é concluído. |

## <a name="time-to-process-job"></a>Tempo para processar o trabalho
A quantidade de tempo que leva para processar um trabalho de importação/exportação varia de acordo com uma série de fatores, como:

-  Tempo de envio
-  Carregar no datacenter
-  Tipo de trabalho e o tamanho dos dados sendo copiados
-  Número de discos em um trabalho. 

O serviço de importação/exportação não tem um SLA, mas o serviço se esforça para concluir a cópia em 7 a 10 dias depois que os discos são recebidos. Além do status Postado em portal do Azure, você pode usar APIs REST para acompanhar o progresso do trabalho. Use o parâmetro porcentagem concluída na chamada à API da operação [listar trabalhos](/previous-versions/azure/dn529083(v=azure.100)) para exibir o progresso da cópia percentual.


## <a name="next-steps"></a>Próximas etapas

* [Transferir dados com o utilitário de linha de comando AzCopy](../storage/common/storage-use-azcopy-v10.md)
* [Exemplo de API REST de importação e exportação do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
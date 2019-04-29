---
title: Recuperando informações de estado para um trabalho de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como obter informações de estado para trabalhos do serviço de Importação/Exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1a878b5a9f0502ff9acd411359895d7431fb76f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478635"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Recuperação de informações de estado para um trabalho de Importação/Exportação
Você pode chamar a operação [Get Job](/rest/api/storageimportexport/jobs) para recuperar informações sobre trabalhos de importação e de exportação. As informações retornadas incluem:

-   O estado atual do trabalho.

-   A porcentagem de conclusão aproximada de cada trabalho.

-   O estado atual de cada unidade.

-   URIs para blobs que contêm logs de erros e informações de log detalhadas (se habilitado).

As seções a seguir explicam as informações retornadas pela operação `Get Job`.

## <a name="job-states"></a>Estados do trabalho
A tabela e o diagrama de estado a seguir descrevem os estados pelos quais passam um trabalho durante seu ciclo de vida. O estado atual do trabalho pode ser determinado chamando a operação `Get Job`.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

A tabela a seguir descreve cada estado pelos quais um trabalho pode passar.

|Estado do trabalho|DESCRIÇÃO|
|---------------|-----------------|
|`Creating`|Depois de chamar a operação Put Job, um trabalho é criado e seu estado é definido como `Creating`. Enquanto o trabalho está no estado `Creating`, o serviço de importação/exportação considera que as unidades não foram enviadas para o data center. Um trabalho pode permanecer no estado `Creating` por até duas semanas; depois disso é excluído automaticamente pelo serviço.<br /><br /> Se você chamar a operação Update Job Properties enquanto o trabalho está no estado `Creating`, ele permanecerá no estado `Creating` e o intervalo limite será redefinido para mais duas semanas.|
|`Shipping`|Depois de enviar seu pacote, você deve chamar a operação Update Job Properties para atualizar o estado do trabalho para `Shipping`. O estado de envio pode ser definido somente se `DeliveryPackage` (transportadora e número de rastreamento) e as propriedades `ReturnAddress` tiverem sido definidas para o trabalho.<br /><br /> O trabalho permanecerá no estado Shipping por até duas semanas. Se duas semanas tiverem passado e as unidades não tiverem sido recebidas, os operadores do serviço de importação/exportação serão notificados.|
|`Received`|Depois que todas as unidades forem recebidas no data center, o estado do trabalho será definido como Received (Recebido).|
|`Transferring`|Depois que as unidades forem recebidas no data center e pelo menos uma unidade tiver iniciado o processamento, o estado do trabalho será definido como `Transferring`. Consulte a seção `Drive States` abaixo para saber mais.|
|`Packaging`|Depois que todas as unidades tiverem concluído o processamento, o trabalho será colocado no estado `Packaging` até que as unidades sejam enviadas para o cliente.|
|`Completed`|Depois que todas as unidades forem enviadas para o cliente, se o trabalho for concluído sem erros, será definido com o estado `Completed`. O trabalho será excluído automaticamente após 90 dias no estado `Completed`.|
|`Closed`|Depois que todas as unidades forem enviadas para o cliente, se houver erros durante o processamento do trabalho, será definido o estado `Closed`. O trabalho será excluído automaticamente após 90 dias no estado `Closed`.|

Você pode cancelar um trabalho somente em determinados estados. Um trabalho cancelado ignora a etapa de cópia de dados, mas caso contrário, ele segue as mesmas transições de estado como um trabalho que não foi cancelada.

A tabela a seguir descreve os erros que podem ocorrer para cada estado do trabalho, bem como o efeito sobre o trabalho quando ocorre um erro.

|Estado do trabalho|Evento|Resolução/Próximas etapas|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Uma ou mais unidades para um trabalho chegaram, mas o trabalho não está no estado `Shipping` ou não há registro do trabalho no serviço.|A equipe de operações do serviço de importação/exportação tentará contatar o cliente para criar ou atualizar o trabalho com as informações necessárias para prosseguir.<br /><br /> Se a equipe de operações não puder entrar em contato com o cliente dentro de duas semanas, tentará devolver as unidades.<br /><br /> Se as unidades não puderem ser retornadas e o cliente não puder ser contatado, as unidades serão destruídas com segurança em 90 dias.<br /><br /> Observe que um trabalho não pode ser processado até que seu estado seja atualizado para `Shipping`.|
|`Shipping`|O pacote de um trabalho não chegou em até duas semanas.|A equipe de operações notificará o cliente do pacote ausente. Com base na resposta do cliente, a equipe de operações estenderá o intervalo de espera pelo pacote ou cancelará o trabalho.<br /><br /> No caso de o cliente não poder ser contatado ou não responder dentro de 30 dias, a equipe de operações iniciará a ação para mover o trabalho do estado `Shipping` diretamente para o estado `Closed`.|
|`Completed/Closed`|As unidades nunca chegaram ao endereço do remetente ou foram danificadas durante o envio (aplica-se somente a um trabalho de exportação).|Se as unidades não chegarem ao endereço de retorno, o cliente deve primeiro chamar a operação Get Job ou verificar o status do trabalho no portal para garantir que as unidades foram enviadas. Se as unidades foram enviadas, o cliente deverá contatar o provedor de envio para tentar localizar as unidades.<br /><br /> Se as unidades forem danificadas durante a remessa, o cliente talvez queira solicitar outro trabalho de exportação ou baixar os blobs ausentes.|
|`Transferring/Packaging`|O trabalho tem um endereço de remetente incorreto ou ausente.|A equipe de operações tentará falar com a pessoa de contato para o trabalho a fim de obter o endereço correto.<br /><br /> No caso de o cliente não poder ser contatado, as unidades serão destruídas com segurança em 90 dias.|
|`Creating / Shipping/ Transferring`|Uma unidade que não aparece na lista de unidades a serem importadas está incluída no pacote de envio.|As unidades adicionais não serão processadas e serão retornadas ao cliente quando o trabalho for concluído.|

## <a name="drive-states"></a>Estados da unidade
A tabela e o diagrama a seguir descrevem o ciclo de vida de uma unidade individual conforme ela passa por um trabalho de importação ou exportação. Você pode obter o estado atual da unidade chamando a operação `Get Job` e analisando o elemento `State` da propriedade `DriveList`.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

A tabela a seguir descreve cada estado pelos quais uma unidade pode passar.

|Estado da unidade|DESCRIÇÃO|
|-----------------|-----------------|
|`Specified`|Para um trabalho de importação, quando o trabalho é criado com a operação Put Job, o estado inicial para uma unidade é `Specified`. Para um trabalho de exportação, como nenhuma unidade é especificada quando o trabalho é criado, o estado inicial da unidade é `Received`.|
|`Received`|A unidade passa para o estado `Received` quando o operador do serviço de importação/exportação tiver processado as unidades que foram recebidas da empresa transportadora para um trabalho de importação. Para um trabalho de exportação, o estado inicial da unidade é `Received`.|
|`NeverReceived`|A unidade passará para o estado `NeverReceived` quando o pacote de um trabalho chegar, mas não contiver a unidade. Uma unidade também pode passar para esse estado se tiver passado duas semanas desde que o serviço recebeu as informações de envio, mas o pacote ainda não foi entregue no data center.|
|`Transferring`|Uma unidade passará para o estado `Transferring` quando o serviço começar a transferir dados da unidade para o armazenamento do Windows Azure.|
|`Completed`|Uma unidade passará para o estado `Completed` quando o serviço tiver transferido com êxito todos os dados sem erros.|
|`CompletedMoreInfo`|Uma unidade passará para o estado `CompletedMoreInfo` quando o serviço encontrar alguns problemas ao copiar dados de ou para a unidade. As informações podem incluir erros, avisos ou mensagens informativas sobre a substituição de blobs.|
|`ShippedBack`|A unidade passará para o estado `ShippedBack` quando for enviada do data center para o endereço de retorno.|

A tabela a seguir descreve os estados de falha de unidade e as ações executadas para cada estado.

|Estado da unidade|Evento|Resolução/Próxima etapa|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Uma unidade que está marcada como `NeverReceived` (porque não foi recebida como parte da remessa do trabalho) chega em outra remessa.|A equipe de operações moverá a unidade para o estado `Received`.|
|`N/A`|Uma unidade que não é parte de qualquer trabalho chega no data center como parte de outro trabalho.|A unidade será marcada como uma unidade adicional e será retornada ao cliente quando o trabalho associado ao pacote original for concluído.|

## <a name="faulted-states"></a>Estados com falha
Quando um trabalho ou uma unidade não puder progredir normalmente por seu ciclo de vida esperado, o trabalho ou a unidade será movido para o estado `Faulted`. Nesse ponto, a equipe de operações entrará em contato com o cliente por email ou telefone. Quando o problema for resolvido, o trabalho ou a unidade em falha passará do estado `Faulted` para o estado apropriado.

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)

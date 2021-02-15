---
title: Restaurar um servidor removido do banco de dados do Azure para PostgreSQL
description: Este artigo descreve como restaurar um servidor Descartado no banco de dados do Azure para PostgreSQL usando o portal do Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652918"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Restaurar um servidor removido do banco de dados do Azure para PostgreSQL

Quando um servidor é removido, o backup do servidor de banco de dados pode ser mantido em até cinco dias no serviço. O backup do banco de dados pode ser acessado e restaurado somente da assinatura do Azure em que o servidor reside originalmente. As etapas recomendadas a seguir podem ser seguidas para recuperar um recurso de servidor PostgreSQL Descartado dentro de 5 dias a partir do momento da exclusão do servidor. As etapas recomendadas só funcionarão se o backup do servidor ainda estiver disponível e não for excluído do sistema. 

## <a name="pre-requisites"></a>Pré-requisitos
Para restaurar um servidor descartado do banco de dados do Azure para PostgreSQL, você precisará do seguinte:
- Nome da assinatura do Azure que hospeda o servidor original
- Local onde o servidor foi criado

## <a name="steps-to-restore"></a>Etapas para restaurar

1. Navegue até o [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Selecione o serviço **Azure monitor** e, em seguida, selecione **log de atividades**.

2. Em log de atividades, clique em **Adicionar filtro** , conforme mostrado, e defina os filtros a seguir para o seguinte

    - **Assinatura** = sua assinatura que hospeda o servidor excluído
    - **Tipo de recurso** = banco de dados do Azure para servidores PostgreSQL (Microsoft. DBforPostgreSQL/Servers)
    - **Operação** = excluir servidor PostgreSQL (Microsoft. DBforPostgreSQL/Servers/Delete)
 
    ![Log de atividades filtrado para excluir a operação do servidor PostgreSQL](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Selecione o evento **excluir servidor PostgreSQL** e, em seguida, selecione a **guia JSON**. Copie os `resourceId` `submissionTimestamp` atributos e na saída JSON. O ResourceId está no seguinte formato: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Navegue até a [página de criação da API REST do servidor](/rest/api/PostgreSQL/servers/create) PostgreSQL e selecione a guia **experimentar** realçada em verde. Faça logon usando sua conta do Azure.

 5. Forneça as Propriedades resourceGroupName **, ServerName (nome** do servidor excluído), **SubscriptionId** , com base no valor JSON do atributo ResourceId capturado na etapa 3 anterior. A propriedade API-Version é preenchida previamente e pode ser deixada como está, conforme mostrado na imagem a seguir.

    ![Criar servidor usando a API REST](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Role para baixo na seção corpo da solicitação e cole o seguinte substituindo o "local do servidor removido", "submissionTimestamp" e "ResourceId". Para "restorePointInTime", especifique um valor de "submissionTimestamp" menos **15 minutos** para garantir que o comando não faça erro.
    
    ```json
    {
      "location": "Dropped Server Location",  
      "properties": 
      {
        "restorePointInTime": "submissionTimestamp - 15 minutes",
        "createMode": "PointInTimeRestore",
        "sourceServerId": "resourceId"
      }
    }
    ```

    Por exemplo, se a hora atual for 2020-11-02T23:59:59.0000000 Z, recomendamos um mínimo de 15 minutos antes de restaurar o ponto de restauração no tempo 2020-11-02T23:44:59.0000000 Z.

    > [!Important]
    > Há um limite de tempo de cinco dias após o descarte do servidor. Após cinco dias, é esperado um erro, pois o arquivo de backup não pode ser encontrado.
    
7. Se você vir o código de resposta 201 ou 202, a solicitação de restauração será enviada com êxito. 

    A criação do servidor pode levar tempo dependendo do tamanho do banco de dados e dos recursos de computação provisionados no servidor original. O status da restauração pode ser monitorado do log de atividades por meio da filtragem de 
   - **Assinatura** = sua assinatura
   - **Tipo de recurso** = banco de dados do Azure para servidores PostgreSQL (Microsoft. DBforPostgreSQL/Servers) 
   - **Operação** = atualizar o servidor PostgreSQL criar

## <a name="next-steps"></a>Próximas etapas
- Se você estiver tentando restaurar um servidor dentro de cinco dias e ainda receber um erro depois de seguir as etapas discutidas anteriormente, abra um incidente de suporte para obter assistência. Se você estiver tentando restaurar um servidor descartado após cinco dias, será esperado um erro, pois o arquivo de backup não pode ser encontrado. Não abra um tíquete de suporte neste cenário. A equipe de suporte não poderá fornecer assistência se o backup for excluído do sistema. 
- Para evitar a exclusão acidental de servidores, é altamente recomendável usar [bloqueios de recursos](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).

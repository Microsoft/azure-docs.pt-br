---
title: Restaurar um servidor descartado do banco de dados do Azure para MySQL
description: Este artigo descreve como restaurar um servidor Descartado no banco de dados do Azure para MySQL usando o portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 34dddd8e5f3fb418fc7155630bf82a922e418402
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657083"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>Restaurar um servidor descartado do banco de dados do Azure para MySQL

Quando um servidor é removido, o backup do servidor de banco de dados pode ser mantido em até cinco dias no serviço. O backup do banco de dados pode ser acessado e restaurado somente da assinatura do Azure em que o servidor reside originalmente. As etapas recomendadas a seguir podem ser realizadas para recuperar um recurso do servidor MySQL descartado em 5 dias a partir do momento da exclusão do servidor. As etapas recomendadas só funcionarão se o backup do servidor ainda estiver disponível e não for excluído do sistema. 

## <a name="pre-requisites"></a>Pré-requisitos
Para restaurar um servidor descartado do banco de dados do Azure para MySQL, você precisará do seguinte:
- Nome da assinatura do Azure que hospeda o servidor original
- Local onde o servidor foi criado

## <a name="steps-to-restore"></a>Etapas para restaurar

1. Vá para o [log de atividades](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) da folha Monitor em portal do Azure. 

2. Em log de atividades, clique em **Adicionar filtro** , conforme mostrado e defina os filtros a seguir para o 

    - **Assinatura** = sua assinatura que hospeda o servidor excluído
    - **Tipo de recurso** = banco de dados do Azure para servidores MySQL (Microsoft. DBforMySQL/Servers) 
    - **Operação** = excluir servidor MySQL (Microsoft. DBforMySQL/Servers/Delete) 
 
     [![Log de atividades filtrado para excluir a operação do servidor MySQL](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Clique duas vezes no evento excluir servidor MySQL e clique na guia JSON e observe os atributos "ResourceId" e "submissionTimestamp" na saída JSON. O ResourceId está no seguinte formato:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Acesse a [página Criar API REST do servidor](/rest/api/mysql/servers/create) e clique na guia "Experimente" realçada em verde e faça logon com sua conta do Azure.
 
 5. Forneça o resourceGroupName, ServerName (nome do servidor excluído), SubscriptionId, derivado do atributo ResourceId capturado na etapa 3, enquanto a versão de API é preenchida previamente, conforme mostrado na imagem.
 
     [![Criar servidor usando a API REST](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
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

7. Se você vir o código de resposta 201 ou 202, a solicitação de restauração será enviada com êxito. 

8. A criação do servidor pode levar tempo dependendo do tamanho do banco de dados e dos recursos de computação provisionados no servidor original. O status da restauração pode ser monitorado do log de atividades por meio da filtragem de 
   - **Assinatura** = sua assinatura
   - **Tipo de recurso** = banco de dados do Azure para servidores MySQL (Microsoft. DBforMySQL/Servers) 
   - **Operação** = atualizar o MySQL Server CREATE

## <a name="next-steps"></a>Próximas etapas
- Se você estiver tentando restaurar um servidor dentro de cinco dias e ainda receber um erro depois de seguir as etapas discutidas anteriormente, abra um incidente de suporte para obter assistência. Se você estiver tentando restaurar um servidor descartado após cinco dias, será esperado um erro, pois o arquivo de backup não pode ser encontrado. Não abra um tíquete de suporte neste cenário. A equipe de suporte não poderá fornecer assistência se o backup for excluído do sistema. 
- Para evitar a exclusão acidental de servidores, é altamente recomendável usar [bloqueios de recursos](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).
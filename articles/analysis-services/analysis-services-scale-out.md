---
title: Escala horizontal do Azure Analysis Services | Microsoft Docs
description: Replicar os servidores do Azure Analysis Services com escala horizontal
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c226608f6c1c776463aa05c02b1d3cc04b699ec
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766817"
---
# <a name="azure-analysis-services-scale-out"></a>Escala horizontal do Azure Analysis Services

Com a escala horizontal, consultas de cliente podem ser distribuídas entre vários *réplicas de consulta* em um *pool de consulta*, reduzindo os tempos de resposta durante cargas de trabalho de consulta altas. Você também pode separar o processamento do pool de consultas, garantindo que as consultas não são afetadas negativamente pelas operações de processamento. A escala horizontal pode ser configurada no Portal do Azure ou usando a API REST do Azure Analysis Services.

A escala horizontal está disponível para servidores no tipo de preço Standard. Cada réplica de consulta é cobrada com a mesma taxa de seu servidor. Todas as réplicas de consulta são criadas na mesma região que o servidor. O número de réplicas de consulta que você pode configurar é limitado pela região em que o seu servidor está. Para obter mais informações, veja [Disponibilidade por região](analysis-services-overview.md#availability-by-region). A escala horizontal não aumenta a quantidade de memória disponível para o servidor. Para aumentar a memória, você precisa atualizar seu plano. 

## <a name="why-scale-out"></a>Por que escalar horizontalmente?

Em uma implantação típica do servidor, um servidor funciona como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em modelos em seu servidor exceder as QPUs (Unidades de Processamento de Consulta) do plano do servidor ou o processamento de modelo ocorrer ao mesmo tempo que cargas de trabalho de consulta altas, o desempenho poderá diminuir. 

Com escala horizontal, você pode criar um pool de consultas com até sete recursos de réplica de consulta adicionais (oito total, incluindo sua *primário* server). Você pode dimensionar o número de réplicas no pool de consulta para atender às demandas QPU em momentos críticos, e você pode separar um servidor de processamento do pool de consultas a qualquer momento. 

Independentemente do número de réplicas de consulta que você tem em um pool de consulta, as cargas de trabalho de processamento não são distribuídas entre as réplicas de consulta. O servidor primário serve como o servidor de processamento. Réplicas de consulta servem apenas as consultas nos bancos de dados modelo sincronizados entre o servidor primário e cada réplica no pool de consulta. 

Ao escalar horizontalmente, ele pode levar até cinco minutos para que novas réplicas de consulta incrementalmente sejam adicionados ao pool de consulta. Quando todas as novas réplicas de consulta estão funcionando e em execução, novas conexões de cliente têm carga equilibrada em todos os recursos no pool de consulta. As conexões de clientes existentes não são alteradas a partir do recurso ao qual elas estão conectadas atualmente. Ao dimensionar, quaisquer conexões de cliente existentes para um recurso de pool de consulta que está sendo removido do pool de consulta são finalizadas. Os clientes podem se reconectar a um recurso de pool de consulta restantes.

## <a name="how-it-works"></a>Como ele funciona

Ao configurar a primeira hora de escalabilidade horizontal, bancos de dados de modelo no servidor primário são *automaticamente* sincronizado com novas réplicas em um novo pool de consulta. A sincronização automática ocorre apenas uma vez. Durante a sincronização automática, arquivos de dados do servidor primário (criptografados em repouso no armazenamento de BLOBs) são copiados para um segundo local, também são criptografado em repouso no armazenamento de BLOBs. As réplicas no pool de consulta são, em seguida, *hidratada* com os dados do segundo conjunto de arquivos. 

Enquanto a sincronização automática é executada somente quando você expandir um servidor pela primeira vez, você também pode executar uma sincronização manual. Sincronizando garante que os dados nas réplicas no pool de consulta corresponde do servidor primário. Durante o processamento de modelos (Atualizar) no servidor primário, uma sincronização deve ser executada *depois de* operações de processamento são concluídas. Essa sincronização copia dados atualizados de arquivos do servidor primário no armazenamento de BLOBs para o segundo conjunto de arquivos. As réplicas no pool de consulta, em seguida, são alimentadas com dados atualizados do segundo conjunto de arquivos no armazenamento de BLOBs. 

Ao executar uma operação de expansão subsequente, por exemplo, aumentando o número de réplicas no pool de consulta de duas a cinco, as novas réplicas são alimentadas com os dados do segundo conjunto de arquivos no armazenamento de BLOBs. Não há nenhuma sincronização. Se você fosse executar uma sincronização após a expansão, as novas réplicas no pool de consulta seriam alimentado duas vezes - uma hidratação redundante. Ao executar uma operação de expansão subsequente, é importante ter em mente:

* Execute uma sincronização *antes da operação de expansão* para evitar hidratação redundante das réplicas adicionadas.

* Ao automatizar o processamento de ambos os *e* operações de escala horizontal, é importante primeiro processar dados no servidor primário, em seguida, execute uma sincronização e, em seguida, executar a operação de expansão. Essa sequência garante um impacto mínimo sobre os recursos de memória e de QPU.

* Sincronização é permitida mesmo quando não houver nenhuma réplica no pool de consulta. Se você estiver dimensionando-out de zero a um ou mais réplicas com novos dados de uma operação de processamento no servidor primário, realizar a sincronização pela primeira vez sem réplicas no pool de consulta e, em seguida, escalar horizontalmente. Sincronizando antes de escalar horizontalmente evita hidratação redundante das réplicas recém-adicionado.

* Ao excluir um banco de dados de modelo do servidor primário, ele não automaticamente é excluído do réplicas no pool de consulta. Você deve executar uma operação de sincronização usando o [AzAnalysisServicesInstance sincronização](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) comando do PowerShell que remove o arquivo/s para o banco de dados do local de armazenamento de blob compartilhado da réplica e, em seguida, exclui o modelo banco de dados nas réplicas no pool de consulta.

* Ao renomear um banco de dados no servidor primário, há uma etapa adicional necessária para garantir que o banco de dados é sincronizado corretamente para todas as réplicas. Depois de renomear, execute uma sincronização usando o [sincronização AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) comando especificando o `-Database` parâmetro com o nome antigo do banco de dados. Essa sincronização remove os arquivos com o nome antigo e o banco de dados de todas as réplicas. Em seguida, executar outra especificação de sincronização a `-Database` parâmetro com o novo nome de banco de dados. A segunda sincronização copia o banco de dados nomeado recentemente ao segundo conjunto de arquivos e recuperará todas as réplicas. Essas sincronizações não podem ser executadas usando o comando de modelo de sincronizar no portal.

### <a name="separate-processing-from-query-pool"></a>Separe o processamento do pool de consulta

Para desempenho máximo para operações de processamento e consulta, você pode optar por separar seu servidor de processamento do pool de consulta. Quando separadas, as conexões existentes e novas do cliente são atribuídas a réplicas de consulta apenas no pool de consulta. Se as operações de processamento ocuparem apenas um curto período de tempo, você poderá optar por separar seu servidor de processamento do pool de consulta apenas pelo tempo necessário para executar operações de processamento e sincronização e, em seguida, incluí-lo novamente no pool de consultas. 

## <a name="monitor-qpu-usage"></a>Monitorar o uso de QPU

Para determinar se a escala horizontal para seu servidor é necessária, monitore o servidor no Portal do Azure usando Métricas. Se a QPU for maximizada regularmente, isso significa que o número de consultas em relação aos modelos está excedendo o limite de QPU do plano. A métrica de comprimento da fila do trabalho do pool de consulta também aumenta quando o número de consultas na fila do pool de thread de consulta excede a QPU disponível. 

Outra boa métrica para assistir é médio QPU por ServerResourceType. Essa métrica compara QPU média para o servidor primário com que um pool de consulta. 

### <a name="to-configure-qpu-by-serverresourcetype"></a>Para configurar a QPU por ServerResourceType
1. Em um gráfico de linha de métricas, clique em **adicionar métrica**. 
2. Na **RESOURCE**, selecione seu servidor, em seguida, na **NAMESPACE da MÉTRICA**, selecione **métricas padrão do Analysis Services**, em seguida, na **MÉTRICA**, Selecione **QPU**e, em seguida, na **agregação**, selecione **Avg**. 
3. Clique em **aplicam-se a divisão**. 
4. Na **valores**, selecione **ServerResourceType**.  

Para obter mais informações, consulte [Monitorar métricas do servidor](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar a escala horizontal

### <a name="in-azure-portal"></a>No Portal do Azure

1. No portal, clique em **Escalar horizontalmente**. Use o controle deslizante para selecionar o número de servidores de réplica de consulta. O número de réplicas escolhido é além dos servidores existentes.

2. Em **Separar o servidor de processamento do pool de consulta**, selecione Sim para excluir o servidor de processamento dos servidores de consulta. Cliente [conexões](#connections) usando a cadeia de conexão padrão (sem `:rw`) são redirecionadas para as réplicas no pool de consulta. 

   ![Controle deslizante da escala horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **Salvar** para provisionar os novos servidores de réplica de consulta. 

Ao configurar a escala horizontal para um servidor na primeira vez, os modelos em seu servidor primário são automaticamente sincronizados com réplicas no pool de consulta. A sincronização automática ocorre apenas uma vez, quando você primeiro configura scale-out para uma ou mais réplicas. O número de réplicas no mesmo servidor as alterações subsequentes *não dispara outra sincronização automática*. A sincronização automática não ocorrerá novamente, mesmo se você definir o servidor como zero réplicas e, em seguida, novamente expansão para qualquer número de réplicas. 

## <a name="synchronize"></a>Sincronizar 

As operações de sincronização devem ser executadas manualmente ou usando a API REST.

### <a name="in-azure-portal"></a>No Portal do Azure

Em **Visão Geral** > modelo > **Sincronizar modelo**.

![Controle deslizante da escala horizontal](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Use a operação de **sincronização**.

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obter o status de sincronização  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Códigos de status de retorno:


|Código  |DESCRIÇÃO  |
|---------|---------|
|-1     |  Inválido       |
|0     | Replicando        |
|1     |  Reidratar       |
|2     |   Concluído       |
|3     |   Com falha      |
|4     |    Finalizando     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de usar o PowerShell, [instalar ou atualizar o módulo Azure PowerShell mais recente](/powershell/azure/install-az-ps). 

Para executar a sincronização, use [AzAnalysisServicesInstance sincronização](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Para definir o número de réplicas de consulta, use [AzAnalysisServicesServer conjunto](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro `-ReadonlyReplicaCount` opcional.

Para separar o servidor de processamento do pool de consulta, use [AzAnalysisServicesServer conjunto](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o valor opcional `-DefaultConnectionMode` parâmetro usar `Readonly`.

Para obter mais informações, consulte [usando uma entidade de serviço com o módulo Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>conexões

Na página Visão Geral do servidor, há dois nomes de servidor. Se você ainda não tiver configurado a escala horizontal para um servidor, os dois nomes de servidor funcionam da mesma forma. Depois de configurar a expansão para um servidor, você precisará especificar o nome do servidor adequado dependendo do tipo de conexão. 

Para conexões de cliente do usuário final como o Power BI Desktop, Excel e aplicativos personalizados, use **Nome do servidor**. 

Para SSMS, SSDT e cadeias de conexão no PowerShell, aplicativos do Azure Functions e AMO, use o **Nome do servidor de gerenciamento**. O nome do servidor de gerenciamento inclui um qualificador especial `:rw` (leitura-gravação). Todas as operações de processamento ocorrerem no servidor de gerenciamento (primário).

![nomes do servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Solução de problemas

**Problema:** usuários obtêm o erro **não é possível localizar o servidor '\<nome do servidor >' instância no modo de conexão 'ReadOnly'.**

**Solução:** Ao selecionar o **separar o servidor de processamento do pool de consulta** opção conexões de cliente usando a cadeia de caracteres de conexão padrão (sem `:rw`) são redirecionadas para réplicas de pool de consulta. Se as réplicas no pool de consulta estiverem ainda online porque a sincronização ainda não foi concluída, as conexões de cliente redirecionada podem falhar. Para evitar conexões com falha, deve haver, pelo menos, dois servidores no pool de consulta ao executar uma sincronização. Cada servidor é sincronizado individualmente, enquanto os outros permanecem online. Se você optar por não ter o servidor de processamento no pool de consulta durante o processamento, você poderá optar por removê-lo do pool para processamento e, em seguida, adicioná-lo novamente ao pool após a conclusão do processamento, mas antes da sincronização. Use as métricas Memória e QPU para monitorar o status de sincronização.

## <a name="related-information"></a>Informações relacionadas

[Monitorar métricas do servidor](analysis-services-monitor.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md) 

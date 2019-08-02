---
title: Início Rápido do Azure Sentinel – Introdução à versão prévia do Azure Sentinel | Microsoft Docs
description: Início Rápido do Azure Sentinel – Introdução ao Azure Sentinel
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 75bfdce02313ab7e799f6535f247407c239653b8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619801"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Início Rápido: Introdução à versão prévia do Azure Sentinel

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Neste guia de início rápido, você aprenderá a ser rapidamente capaz de exibir e monitorar o que está acontecendo em seu ambiente usando o Azure Sentinel. Depois de conectar suas fontes de dados ao Azure Sentinel, você obtém visualização e análise instantâneas de dados para que possa saber o que está acontecendo em todas as suas fontes de dados conectadas. O Azure Sentinela oferece painéis que oferecem toda a potência de ferramentas já disponíveis no Azure, assim como tabelas e gráficos feitos para fornecer análise para seus logs e consultas. Você pode usar painéis internos ou criar um novo painel com facilidade, seja do zero ou com base em um painel existente. 

## <a name="get-visualization"></a>Obter visualização

Para visualizar e obter uma análise do que está acontecendo em seu ambiente, primeiro, dê uma olhada no painel de visão geral para ter uma ideia da postura de segurança da organização. É possível clicar em cada elemento desses blocos para fazer busca detalhada nos dados brutos dos quais são criados. Para ajudar a reduzir o ruído e minimizar o número de alertas que você precisa revisar e investigar, o Azure Sentinel usa uma técnica de fusão para correlacionar alertas a casos. **Casos** são grupos de alertas relacionados que, juntos, criam um incidente acionável que você pode investigar e resolver.

- No portal do Microsoft Azure, selecione o Azure Sentinel e, depois, selecione o workspace que deseja monitorar.

  ![Visão geral do Azure Sentinel](./media/qs-get-visibility/overview.png)

- A barra de ferramentas na parte superior informa quantos eventos você obteve no período selecionado e compara com as 24 horas anteriores. A barra de ferramentas informa quantos alertas foram disparados com base nesses eventos (o número pequeno representa a mudança nas últimas 24 horas) e informa, para esses eventos, quantos estão abertos, em andamento e fechados. Verifique se não houve um aumento ou queda significativa no número de eventos. Se houve uma queda, é possível que uma conexão tenha parado de se reportar ao Azure Sentinel. Se houve um aumento, pode ter ocorrido algo suspeito. Verifique se há novos alertas.

   ![Funil do Azure Sentinel](./media/qs-get-visibility/funnel.png)

O corpo principal da página de visão geral fornece insight rápido sobre o status de segurança do workspace:

- **Eventos e alertas ao longo do tempo**: Lista o número de eventos e quantos alertas foram criados com base nesses eventos. Se você vir um pico incomum, deverá ver alertas para ele. Caso haja algo incomum em que houve um pico nos eventos, mas você não viu alertas, isso pode ser motivo de preocupação.

- **Eventos mal-intencionados em potencial**: Quando se detecta tráfego de fontes que são conhecidas como mal-intencionadas, o Azure Sentinel alerta no mapa. Se você vir cor de laranja, trata-se de tráfego de entrada: alguém está tentando acessar sua organização de um endereço IP mal-intencionado conhecido. Se você vir uma atividade de saída (vermelha), significará que dados da sua rede estão saindo da organização para um endereço IP mal-intencionado conhecido.

   ![Mapa do Azure Sentinel](./media/qs-get-visibility/map.png)


- **Casos recentes**: Para exibir os casos recentes, a gravidade deles e o número de alertas associados com o caso. Se você vir um pico repentino em um tipo específico de alerta, poderá significar que um ataque ativo está em execução no momento. Por exemplo, se você tiver um pico repentino de 20 eventos Pass-the-Hash do ATP do Azure, é possível que alguém esteja tentando atacá-lo no momento.

- **Anomalias na fonte de dados**: Analistas de dados da Microsoft criaram modelos que pesquisam constantemente anomalias nos dados de suas fontes de dados. Se não houver anomalias, nada será exibido. Se anomalias forem detectadas, você deverá examinar para ver o que aconteceu. Por exemplo, clique no pico na atividade do Azure. É possível clicar em **Gráfico** para ver quando o pico aconteceu e, em seguida, filtrar por atividades que ocorreram durante esse período para ver o que causou o pico.

   ![Mapa do Azure Sentinel](./media/qs-get-visibility/anomolies.png)

## Use painéis internos<a name="dashboards"></a>

Painéis internos oferecem dados integrados de suas fontes de dados conectadas para permitir que você examine os eventos gerados nesses serviços. Os painéis internos incluem a ID do Azure, eventos de atividades do Azure e locais, que podem ser dados de eventos do Windows de servidores, alertas de primeiros, de qualquer terceiro, incluindo logs de tráfego do firewall, Office 365 e protocolos inseguros com base em eventos do Windows.

1. Em **Configurações**, selecione **Painéis**. Em **Instalados**, você pode ver todos os painéis instalados. Em **Todos**, é possível ver a galeria inteira de painéis internos que estão disponíveis para instalação. 
2. Pesquise um painel específico ver a lista inteira e a descrição do que cada um oferece. 
3. Supondo que você usa o Azure AD, para começar a usar o Azure Sentinel, recomendamos que instale pelo menos os painéis a seguir:
   - **Azure AD**: Use uma ou ambas as opções a seguir:
       - **As entradas do Azure AD** analisam as entradas ao longo do tempo para ver se há anomalias. Esse painel fornece entradas com falha por aplicativos, dispositivos e locais para que você possa perceber rapidamente se acontecer algo incomum. Preste atenção a várias entradas com falha. 
       - **Os logs de auditoria do Azure AD** analisam as atividades administrativas, como alterações em usuários (adicionar, remover etc.), criação de grupo e modificações.  

   - Adicione um painel para seu firewall. Por exemplo, adicione o painel Palo Alto. O painel analisa o tráfego do firewall, fornecendo correlações entre eventos de ameaças e dados do firewall, além de realçar os eventos suspeitos em entidades. Os painéis fornecem informações sobre tendências no tráfego e permitem fazer busca detalhada e filtrar os resultados. 

      ![Painel Pal Alto](./media/qs-get-visibility/palo-alto-week-query.png)


Para personalizar os painéis, edite a consulta principal ![botão](./media/qs-get-visibility/edit-query-button.png). É possível clicar no botão ![botão](./media/qs-get-visibility/go-to-la-button.png) para acessar o [Log Analytics e editar a consulta encontrada](../azure-monitor/log-query/get-started-portal.md). Você também pode selecionar as reticências (...) e selecionar **Personalizar dados do bloco**, o que permite editar o filtro de tempo principal ou remover os blocos específicos do painel.

Para obter mais informações sobre como trabalhar com consultas, confira [Tutorial: Dados visuais no Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Adicionar um novo bloco

Se quiser adicionar um novo bloco, você poderá adicioná-lo a um painel existente, seja um criado por você ou um painel interno do Azure Sentinel. 
1. No Log Analytics, crie um bloco usando as instruções encontradas em [Tutorial: Dados visuais no Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Após a criação do bloco, em **Fixar**, selecione o painel em que deseja que o bloco apareça.

## <a name="create-new-dashboards"></a>Criar novos painéis
Você pode criar um novo painel do zero ou usar um painel interno como base para o novo painel.

1. Para criar um novo painel do zero, selecione **Painéis** e, em seguida, **+Novo painel**.
2. Selecione a assinatura em que o painel foi criado e lhe dê um nome descritivo. Cada painel é um recurso do Azure como qualquer outro. Você pode atribuir funções (RBAC) para definir e limitar quem pode acessar. 
3. Para permitir que apareça em seus painéis para fixar visualizações, você precisa compartilhá-lo. Clique em **Compartilhar** e em **Gerenciar usuários**. 
 
1. Use **Verificar acesso** e **Atribuições de função** como faria para qualquer outro recurso do Azure. Para obter mais informações, confira [Compartilhar painéis do Azure usando RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Exemplos de novos painéis

A consulta de exemplo a seguir permite que você compare as tendências de tráfego de diferentes semanas. Você pode alternar facilmente em qual fornecedor do dispositivo e fonte de dados executa a consulta. Este exemplo usa o SecurityEvent do Windows. É possível mudá-lo para ser executado no AzureActivity ou no CommonSecurityLog em qualquer outro firewall.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Talvez você queira criar uma consulta que incorpore dados de várias fontes. Você pode criar uma consulta que examina os logs de auditoria do Azure Active Directory para novos usuários que acabaram de ser criados e, em seguida, verifica os logs do Azure para ver se o usuário começou a fazer alterações na atribuição de função no prazo de 24 horas após a criação. A atividade suspeita apareceria neste painel:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Você pode criar painéis diferentes com base na função da pessoa que examina os dados e em que ela está procurando. Pode, por exemplo, criar um painel para seu administrador de rede que inclua os dados do firewall. Também pode criar painéis com base na frequência com a qual deseja observá-los, se há coisas que você deseja revisar diariamente e outros itens que deseja verificar uma vez por hora, por exemplo. É possível que queira examinar as entradas no Azure AD a cada hora em busca de anomalias. 

## <a name="create-new-detections"></a>Criar detecções

Gere detecções nas [fontes de dados que você conectou ao Azure Sentinel](connect-data-sources.md) para investigar ameaças em sua organização.

Quando você criar uma detecção, aproveite as detecções internas criadas por pesquisadores de segurança da Microsoft que são personalizadas para as fontes de dados conectadas.

1. [Na comunidade do GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Detections), acesse a pasta **Detecções** e selecione as pastas relevantes.
   ![pastas relevantes](./media/qs-get-visibility/detection-folders.png)
 
3.  Acesse a guia **Análise** e selecione **Adicionar**.
   ![criar regra no Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Copie todos os parâmetros para a regra e clique em **Criar**.
   ![criar regra de alerta](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você aprendeu como começar a usar o Azure Sentinel. Continue com o tutorial sobre [como detectar ameaças](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Detecte ameaças](tutorial-detect-threats.md) para automatizar suas respostas às ameaças.


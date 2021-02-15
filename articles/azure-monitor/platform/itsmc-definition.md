---
title: Conector de Gerenciamento de Serviços de TI em Log Analytics
description: Este artigo fornece uma visão geral de Conector de Gerenciamento de Serviços de TI (ITSMC) e informações sobre como usá-lo para monitorar e gerenciar itens de trabalho de ITSM no Log Analytics e resolver problemas rapidamente.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: ba32cfa4bc5cd0b41a210cf88fb598afc3064495
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492545"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Conectar o Azure a ferramentas de ITSM usando o Conector de Gerenciamento de Serviços de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Este artigo fornece informações sobre como configurar o Conector de Gerenciamento de Serviços de TI (ITSMC) no Log Analytics para gerenciar centralmente seus itens de trabalho do gerenciamento de serviços de ti (ITSM).

## <a name="add-it-service-management-connector"></a>Adicionar Conector de Gerenciamento de Serviços de TI

Para poder criar uma conexão, você precisa instalar o ITSMC.

1. Na portal do Azure, selecione **criar um recurso**:

   ![Captura de tela que mostra o item de menu para criar um recurso.](media/itsmc-overview/azure-add-new-resource.png)

2. Pesquise **conector de gerenciamento de serviços de ti** no Azure Marketplace. Em seguida, selecione **Criar**:

   ![Captura de tela que mostra o botão criar no Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Na seção **espaço de trabalho da la** , selecione o espaço de trabalho log Analytics onde você deseja instalar o ITSMC.
   > [!NOTE]
   > Você pode instalar o ITSMC nos espaços de trabalho Log Analytics somente nas seguintes regiões: leste dos EUA, oeste dos EUA 2, Sul EUA Central, Oeste EUA Central, US Gov Arizona, US Gov-Virgínia, Canadá central, Europa Ocidental, sul do Reino Unido, Sudeste Asiático, leste do Japão, Índia central e sudeste da Austrália.

4. Na seção **log Analytics espaço de trabalho** , selecione o grupo de recursos no qual você deseja criar o recurso ITSMC:

   ![Captura de tela que mostra a seção Log Analytics espaço de trabalho.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, os espaços de trabalho do OMS agora são chamados de *espaços de trabalho log Analytics*.

5. Selecione **OK**.

Quando o recurso ITSMC é implantado, uma notificação é exibida no canto superior direito da janela.

## <a name="create-an-itsm-connection"></a>Criar uma conexão de ITSM

Depois de instalar o ITSMC, você deve preparar sua ferramenta de ITSM para permitir a conexão de ITSMC. Com base no produto de ITSM ao qual você está se conectando, selecione um dos links a seguir para obter instruções:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Depois de ter preparado sua ferramenta de ITSM, conclua estas etapas para criar uma conexão:

1. Em **todos os recursos**, procure por **assistência técnica (*seu nome de espaço de trabalho*)**:

   ![Captura de tela que mostra os recursos recentes no portal do Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Em **fontes de dados do espaço de trabalho** no painel esquerdo, selecione **conexões de ITSM**:

   ![Captura de tela que mostra o item de menu de conexões de ITSM.](media/itsmc-overview/add-new-itsm-connection.png)

1. Selecione **Adicionar conexão**.

1. Especifique as configurações de conexão de acordo com o produto de ITSM que você está usando:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Por padrão, o ITSMC atualiza os dados de configuração da conexão uma vez a cada 24 horas. Para atualizar os dados da conexão instantaneamente para refletir as edições ou atualizações de modelo que você fizer, selecione o botão **sincronizar** no painel da sua conexão:
   >
   > ![Captura de tela que mostra o botão Sincronizar no painel da conexão.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho de ITSM desde alertas do Azure

Depois de criar sua conexão de ITSM, você pode usar o ITMC para criar itens de trabalho em sua ferramenta de ITSM com base nos alertas do Azure. Para criar os itens de trabalho, você usará a ação de ITSM em grupos de ações.

Os grupos de ação fornecem uma maneira modular e reutilizável para disparar ações para seus alertas do Azure. Você pode usar grupos de ação com alertas de métrica, alertas do log de atividades e alertas de Log Analytics no portal do Azure.

> [!NOTE]
> Depois de criar a conexão de ITSM, você precisa aguardar 30 minutos para que o processo de sincronização seja concluído.

## <a name="define-a-template"></a>Definir um modelo

Determinados tipos de item de trabalho podem usar modelos que você define na ferramenta ITSM. Usando modelos, você pode definir campos que serão preenchidos automaticamente de acordo com os valores fixos de um grupo de ação. Você pode definir qual modelo deseja usar como parte da definição de um grupo de ações.

Para criar um grupo de ação:

1. No portal do Azure, selecione  **alertas**.
2. No menu na parte superior da tela, selecione **Gerenciar ações**:

    ![Captura de tela que mostra o item de menu gerenciar ações.](media/itsmc-overview/action-groups-selection-big.png)

   A janela **Criar grupo de ações** é exibida.

3. Selecione a **assinatura** e o **grupo de recursos** em que você deseja criar o grupo de ações. Forneça valores no **nome do grupo de ações** e no **nome de exibição** do seu grupo de ação. Em seguida, selecione **Avançar: notificações**.

    ![Captura de tela que mostra a janela criar grupo de ações.](media/itsmc-overview/action-groups-details.png)

4. Na guia **notificações** , selecione **Avançar: ações**.
5. Na guia **ações** , selecione **ITSM** na lista **tipo de ação** . Para **nome**, forneça um nome para a ação. Em seguida, selecione o botão de caneta que representa **detalhes de edição**.

    ![Captura de tela que mostra seleções para criar um grupo de ações.](media/itsmc-definition/action-group-pen.png)

6. Na lista **assinatura** , selecione a assinatura que contém seu espaço de trabalho log Analytics. Na lista **conexão** , selecione o nome do conector de ITSM. Ele será seguido pelo nome do seu espaço de trabalho. Um exemplo é *MyITSMConnector (MyWorkspace)*.

7. Selecione um tipo de **item de trabalho** .

8. Se você quiser preencher os campos prontos para uso com valores fixos, selecione **usar modelo personalizado**. Caso contrário, escolha um [modelo](#define-a-template) existente na lista de **modelos** e insira os valores fixos nos campos de modelo.

9. Na última seção da interface para criar um grupo de ações de ITSM, você pode definir quantos itens de trabalho serão criados para cada alerta.

   > [!NOTE]
   > Esta seção é relevante apenas para alertas de pesquisa de log. Para todos os outros tipos de alerta, você criará um item de trabalho por alerta.

   * Se você selecionou **incidente** ou **alerta** na lista suspensa **item de trabalho** , terá a opção de criar itens de trabalho individuais para cada item de configuração.
    
     ![Captura de tela que mostra a área de tíquete I S M com o incidente selecionado como um item de trabalho.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Se você marcar a caixa de seleção **criar itens de trabalho individuais para cada item de configuração** , cada item de configuração em cada alerta criará um novo item de trabalho. Como vários alertas ocorrerão para os mesmos itens de configuração afetados, haverá mais de um item de trabalho para cada item de configuração.

       Por exemplo, um alerta com três itens de configuração criará três itens de trabalho. Um alerta que tenha um item de configuração criará um item de trabalho.
        
     * Se você desmarcar a caixa de seleção **criar itens de trabalho individuais para cada item de configuração** , o ITSMC criará um único item de trabalho para cada regra de alerta e acrescentará a ele todos os itens de configuração afetados. Um novo item de trabalho será criado se o anterior for fechado.

       >[!NOTE]
       > Nesse caso, alguns dos alertas acionados não geram novos itens de trabalho na ferramenta ITSM.

       Por exemplo, um alerta que tem três itens de configuração criará um item de trabalho. Se um alerta para a mesma regra de alerta que o exemplo anterior tiver um item de configuração, esse item de configuração será anexado à lista de itens de configuração afetados no item de trabalho criado. Um alerta para uma regra de alerta diferente que tenha um item de configuração criará um item de trabalho.

   * Se você selecionou **evento** na lista suspensa **item de trabalho** , você pode optar por criar itens de trabalho individuais para cada entrada de log ou para cada item de configuração.
    
     ![Captura de tela que mostra a área de tíquete I S M com o evento selecionado como um item de trabalho.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Se você selecionar **criar itens de trabalho individuais para cada entrada de log (o campo item de configuração não será preenchido. Pode resultar em um grande número de itens de trabalho.)**, um item de trabalho será criado para cada linha nos resultados da pesquisa da consulta de alerta de pesquisa de logs. A propriedade Description na carga do item de trabalho conterá a linha dos resultados da pesquisa.
      
     * Se você selecionar **criar itens de trabalho individuais para cada item de configuração**, cada item de configuração em cada alerta criará um novo item de trabalho. Cada item de configuração pode ter mais de um item de trabalho no sistema ITSM. Essa opção é igual à seleção da caixa de seleção que aparece depois que você seleciona **incidente** como o tipo de item de trabalho.

10. Selecione **OK**.

Quando você cria ou edita uma regra de alerta do Azure, use um grupo de ações, que tem uma ação de ITSM. Quando o alerta é disparado, o item de trabalho é criado ou atualizado na ferramenta ITSM.

> [!NOTE]
> Para obter informações sobre o preço da ação de ITSM, consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para grupos de ações.
>
> O campo Descrição curta na definição de regra de alerta é limitado a 40 caracteres quando você o envia usando a ação de ITSM.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas no ITSMC](./itsmc-resync-servicenow.md)

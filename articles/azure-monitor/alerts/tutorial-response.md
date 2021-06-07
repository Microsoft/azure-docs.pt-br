---
title: Responder a eventos com alertas do Azure Log Analytics | Microsoft Docs
description: Este tutorial ajuda-o a reconhecer alertas com Log Analytics para identificar informações importantes no workspace e notificá-lo proativamente sobre problemas ou invocar ações para tentar corrigi-los.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: 2564711fce6e8578829fb15fdbc04ae2b9d64959
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039420"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Responder a eventos com alertas do Azure Monitor
Os alertas no Azure Monitor podem identificar informações importantes no repositório do Log Analytics. Eles são criados por regras de alerta que executam pesquisas de logs automaticamente em intervalos regulares e, se os resultados da pesquisa de logs corresponderem a determinados critérios, um registro de alerta será criado e poderá ser configurado para executar uma resposta automatizada.  Este tutorial é uma continuação do tutorial [Criar e compartilhar painéis de dados do Log Analytics](../visualize/tutorial-logs-dashboards.md).   

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma regra de alerta
> * Configurar um Grupo de Ações para enviar uma notificação por email

Para concluir o exemplo neste tutorial, você deve ter uma máquina virtual existente [conectada ao espaço de trabalho do Log Analytics](../vm/quick-collect-azurevm.md).  

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Criar alertas
Alertas são criados por regras de alerta no Azure Monitor e podem executar automaticamente consultas salvas ou pesquisas de logs personalizadas em intervalos regulares.  Crie alertas com base em métricas de desempenho específicas ou quando determinados eventos são criados, na ausência de um evento ou quando um número de eventos são criados em uma janela de tempo específica.  Por exemplo, alertas podem ser utilizados para notificá-lo quando o uso médio de CPU exceder um determinado limite, quando uma atualização ausente for detectada ou quando um evento for gerado ao detectar que um daemon do Linux ou serviço Windows específico não está em execução.  Se os resultados da pesquisa de logs corresponderem a critérios específicos, um alerta será criado. Em seguida, a regra poderá executar automaticamente uma ou mais ações como notificá-lo sobre o alerta ou invocar outro processo. 

No exemplo a seguir, você criará uma regra de alerta de medição métrica com base na consulta de *VMs do Azure - Utilização do processador* salva no [tutorial Visualizar dados](../visualize/tutorial-logs-dashboards.md).  Um alerta será criado para cada máquina virtual que exceder um limite de 90%.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione o **Log Analytics**.
2. No painel esquerdo, selecione **Alertas** e, em seguida, clique em **Nova Regra de Alerta** na parte superior da página para criar um novo alerta.<br><br> ![Criar nova regra de alerta](./media/tutorial-response/alert-rule-02.png)<br>
3. Para a primeira etapa, na seção **Criar Alerta** você selecionará o espaço de trabalho do Log Analytics como o recurso, pois esse é um sinal de alerta baseado em log.  Filtre os resultados, escolhendo a **Assinatura** específica na lista suspensa, se houver mais de uma, a qual contém a VM e o espaço de trabalho do Log Analytics criado anteriormente.  Filtre o **Tipo de Recurso**, selecionando o **Log Analytics** na lista suspensa.  Por fim, selecione **Recurso** **DefaultLAWorkspace** e, em seguida, clique em **Concluído**.<br><br> ![Criar tarefa 1 da etapa de alerta](./media/tutorial-response/alert-rule-03.png)<br>
4. Na seção **Critérios de Alerta**, clique em **Adicionar Critérios** para selecionar a consulta salva e, em seguida, especifique a lógica que a regra de alerta segue.  No painel **Configurar sinal lógico**, selecione *VMs do Azure - Utilização do processador* na lista.  O painel é atualizado para apresentar as configurações do alerta.  Na parte superior, mostra o conjunto de resultados dos últimos 30 minutos do sinal selecionado e a própria consulta de pesquisa.  
5. Configure o alerta com as informações a seguir:  
   a. Na lista suspensa **Com base em**, selecione **Medição métrica**.  Uma medição métrica criará um alerta para cada objeto na consulta com um valor que exceda o limite especificado.  
   b. Para a **Condição**, selecione **Maior que** e insira **90** para o **Limite**.  
   c. Na seção Disparar Alerta com Base Em, selecione **Violações consecutivas** e na lista suspensa selecione **Maior que** e insira um valor de 3.  
   d. Em Avaliação com base na seção, modifique o valor de **Período** para **30** minutos. A regra executará a cada cinco minutos e retornará registros criados nos últimos 30 minutos em relação à hora atual.  Definir o período de tempo para uma janela maior considera uma possível latência de dados, garantindo que a consulta retorne dados para evitar um falso negativo em que o alerta nunca é disparado.  
6. Clique em **Concluído** para concluir a regra de alerta.<br><br> ![Configurar sinal de alerta](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. Agora, passando para a segunda etapa, forneça um nome de alerta no campo **Nome da regra de alerta**, como **Percentual de CPU maior que 90%**.  Especifique uma **Descrição** com detalhes específicos para o alerta e selecione **Critical(Sev 0)** para o valor de **Gravidade** das opções fornecidas.<br><br> ![Configurar detalhes de alerta](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Para ativar imediatamente a regra de alerta na criação, aceite o valor padrão para **Habilitar regra após a criação**.
9. Na terceira e última etapa, você especifica um **Grupo de Ações**, o qual garantirá que as mesmas ações sejam executadas sempre que um alerta for disparado e poderá ser utilizado para cada regra definida.  Configure um novo grupo de ações com as informações a seguir:  
   a. Selecione **Novo grupo de ações** e o painel **Adicionar grupo de ações** aparece.  
   b. Para **Nome do grupo de ações**, especifique um nome como **Operações de TI - Notificar** e um **Nome curto** como **itops-n**.  
   c. Verifique se os valores padrão para **Assinatura** e **Grupo de recursos** estão corretos. Caso contrário, selecione um correto na lista suspensa.   
   d. Na seção Ações, especifique um nome para a ação, como **Enviar Email** e em **Tipo de Ação** selecione **Email/SMS/Push/Voz** na lista suspensa. O painel de propriedades **Email/SMS/Push/Voz** será aberto à direita para fornecer informações adicionais.  
   e. No painel **Email/SMS/Push/Voz**, habilite **Email** e forneça um endereço SMTP de email válido para entregar a mensagem.  
   f. Clique em **OK** para salvar suas alterações.<br><br> 

    ![Criar novo grupo de ações](./media/tutorial-response/action-group-properties-01.png)

10. Clique em **OK** para concluir o grupo de ações. 
11. Clique em **Criar regra de alerta** para concluir a regra de alerta. Ela começa a ser executada imediatamente.<br><br> ![Concluir a criação de nova regra de alerta](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Exibir os alertas no portal do Azure
Agora que você criou um alerta, será possível exibir os alertas do Azure em um único painel e gerenciar todas as regras de alerta nas assinaturas do Azure. Ela lista todas as regras de alerta (habilitadas ou desabilitadas) e estas podem ser classificadas com base em grupos de recursos, recursos de destino, nome da regra ou status. Incluem-se uma agregação/resumo de todos os alertas disparados e o total de regras de alerta configuradas/habilitadas.<br><br> ![Página de status de Alertas do Azure](./media/tutorial-response/azure-alerts-02.png)  

Quando o alerta é disparado, a tabela reflete a condição e quantas vezes ocorreu dentro do intervalo de tempo selecionado (o padrão é as últimas seis horas).  Deverá haver um email correspondente na caixa de entrada semelhante ao exemplo a seguir, mostrando a máquina virtual incorreta e os principais resultados que corresponderam à consulta de pesquisa nesse caso.<br><br> ![Exemplo de ação de email de alerta](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como as regras de alerta podem identificar de forma proativa e responder a um problema quando executarem as pesquisas de log em intervalos agendados e correspondem a determinado critério.

Siga este link para ver amostras de script do Log Analytics pré-criadas.  

> [!div class="nextstepaction"]
> [Amostras de script do Log Analytics](../powershell-samples.md)
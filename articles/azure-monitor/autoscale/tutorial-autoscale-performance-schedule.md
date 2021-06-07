---
title: Dimensionar automaticamente os recursos do Azure com base em dados ou agenda
description: Criar uma configuração de dimensionamento automático para um plano do serviço de aplicativo usando dados de métrica e um agendamento
author: anirudhcavale
services: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: b5bcc68ddf26dd1fb8421224307c5aa20179afab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100597085"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Criar uma Configuração de Dimensionamento Automático para os recursos do Azure com base em dados de desempenho ou em um agendamento

As configurações de dimensionamento automático permitem adicionar/remover instâncias de serviço com base em condições predefinidas. Essas configurações podem ser criadas por meio do portal. Esse método fornece uma interface do usuário baseada em navegador para criar e definir uma configuração de dimensionamento automático. 

Neste tutorial, você irá 
> [!div class="checklist"]
> * Criar um Aplicativo Web e um Plano do Serviço de Aplicativo
> * Configurar regras de dimensionamento automático para expansão e redução com base no número de solicitações que um aplicativo Web recebe
> * Disparar uma ação de expansão e observar o aumento do número de instâncias
> * Disparar uma ação de expansão e observar a diminuição do número de instâncias
> * Limpar seus recursos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Criar um Aplicativo Web e um Plano do Serviço de Aplicativo
1. Clique na opção **Criar um recurso** no painel de navegação à esquerda.
2. Pesquise e selecione o item *Aplicativo Web* e clique em **Criar**.
3. Selecione um nome de aplicativo como *MyTestScaleWebApp*. Crie um grupo de recursos *myResourceGroup’ e coloque-o no grupo de recursos de sua escolha.

Em poucos minutos, os recursos devem ser provisionados. Use o Aplicativo Web e o Plano do Serviço de Aplicativo correspondente no restante deste tutorial.

   ![Criar um novo serviço de aplicativo no portal](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navegar para as configurações de Dimensionamento automático
1. No painel de navegação à esquerda, selecione a opção **Monitorar**. Depois que a página for carregada, selecione a guia **Dimensionamento Automático**.
2. Uma lista dos recursos em sua assinatura que dão suporte ao dimensionamento automático são listados aqui. Identifique o Plano do Serviço de Aplicativo criado anteriormente no tutorial e clique nele.

    ![A captura de tela mostra o portal do Azure com o Monitor e o Dimensionamento Automático selecionado.](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Na configuração de dimensionamento automático, clique no botão **Habilitar Dimensionamento Automático**.

As próximas etapas ajudarão você a preencher a tela de dimensionamento automático para que ela se pareça com a seguinte imagem:

   ![Salvar a configuração de dimensionamento automático](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Configurar o perfil padrão
1. Forneça um **Nome** para a configuração de dimensionamento automático.
2. No perfil padrão, verifique se o **Modo de dimensionamento** está definido como “Dimensionar para uma contagem de instância específica”.
3. Defina a contagem de instâncias como **1**. Essa configuração garante que, quando nenhum outro perfil está ativo ou em vigor, o perfil padrão retorna a contagem de instâncias como 1.

   ![A captura de tela mostra a página de configuração de Dimensionamento Automático com um nome inserido para a configuração.](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Criar perfil de recorrência

1. Clique no link **Adicionar uma condição de dimensionamento** no perfil padrão.

2. Edite o **Nome** desse perfil para que ele seja “Perfil de segunda a sexta-feira”.

3. Verifique se o **Modo de escala** está definido como “Dimensionar com base em uma métrica”.

4. Para **Limites de instância** defina o **Mínimo** como “1”, o **Máximo** como “2” e o **Padrão** como “1”. Essa configuração garante que esse perfil não dimensione automaticamente o plano de serviço para que ele tenha menos de 1 instância ou mais de 2 instâncias. Se o perfil não tiver dados suficientes para tomar uma decisão, ele usará o número de instâncias padrão (neste caso, 1).

5. Para **Agendamento**, selecione “Repetir dias específicos”.

6. Defina o perfil para que ele seja repetido de segunda a sexta-feira, das 9h PST às 18h PST. Essa configuração garante que esse perfil só esteja ativo e seja aplicável das 9h às 18h, de segunda a sexta-feira. Durante todos os outros horários, o perfil “Padrão” é o perfil usado pela configuração de dimensionamento automático.

## <a name="create-a-scale-out-rule"></a>Criar uma regra de escala horizontal

1. No “Perfil de segunda a sexta-feira”.

2. Clique no link **Adicionar uma regra**.

3. Defina a **Origem da métrica** como “outro recurso”. Defina o **Tipo de recurso** como “Serviços de Aplicativos” e o **Recurso** como o Aplicativo Web criado anteriormente neste tutorial.

4. Defina a **Agregação de tempo** como “Total”, o **Nome da métrica** como “Solicitações” e a **Estatística do intervalo de agregação** como “Soma”.

5. Defina o **Operador** como “Maior que”, o **Limite** como “10” e a **Duração** como “5” minutos.

6. Selecione a **Operação** como “Aumentar contagem em”, a **Contagem de instâncias** como “1” e o **Resfriamento** como “5” minutos.

7. Clique no botão **Adicionar** .

Essa regra garante que, se o Aplicativo Web receber mais de 10 solicitações em 5 minutos ou menos, uma instância adicional é adicionada ao Plano do Serviço de Aplicativo para gerenciar a carga.

   ![Criar uma regra de escala horizontal](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Criar uma regra de redução horizontal
Recomendamos que você sempre tenha uma regra de redução horizontal para acompanhar uma regra de escala horizontal. Ter as duas garante que os recursos não são superprovisionados. O superprovisionamento significa ter mais instâncias em execução do que o necessário para manipular a carga atual. 

1. No “Perfil de segunda a sexta-feira”.

2. Clique no link **Adicionar uma regra**.

3. Defina a **Origem da métrica** como “outro recurso”. Defina o **Tipo de recurso** como “Serviços de Aplicativos” e o **Recurso** como o Aplicativo Web criado anteriormente neste tutorial.

4. Defina a **Agregação de tempo** como “Total”, o **Nome da métrica** como “Solicitações” e a **Estatística do intervalo de agregação** como “Média”.

5. Defina o **Operador** como “Menor que”, o **Limite** como “5” e a **Duração** como “5” minutos.

6. Selecione a **Operação** como “Diminuir contagem em”, a **Contagem de instâncias** como “1” e o **Resfriamento** como “5” minutos.

7. Clique no botão **Adicionar** .

    ![Criar uma regra de redução horizontal](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Salvar** a configuração de dimensionamento automático.

    ![Salvar a configuração de dimensionamento automático](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Disparar a ação de escala horizontal
Para disparar a condição de escala horizontal na configuração de dimensionamento automático recém-criada, o Aplicativo Web deve ter mais de 10 solicitações em menos de 5 minutos.

1. Abra uma janela do navegador e navegue para o Aplicativo Web criado anteriormente neste tutorial. Encontre a URL do Aplicativo Web no Portal do Azure navegando para o recurso de Aplicativo Web e clicando no botão **Procurar** na guia “Visão Geral”.

2. Em sucessão rápida, recarregue a página mais de 10 vezes.

3. No painel de navegação à esquerda, selecione a opção **Monitorar**. Depois que a página for carregada, selecione a guia **Dimensionamento automático**.

4. Na lista, selecione o Plano do Serviço de Aplicativo usado durante todo este tutorial.

5. Na configuração de dimensionamento automático, clique na guia **Histórico de execuções**.

6. Você verá um gráfico que reflete a contagem de instâncias do Plano do Serviço de Aplicativo ao longo do tempo.

7. Em alguns minutos, a contagem de instâncias deve aumentar de 1 para 2.

8. Abaixo do gráfico, você verá as entradas do log de atividades para cada ação de escala usada por essa configuração de dimensionamento automático.

## <a name="trigger-scale-in-action"></a>Disparar ação de redução horizontal
A condição de redução horizontal na configuração de dimensionamento automático é disparada se há menos de 5 solicitações para o Aplicativo Web acima de um período de 10 minutos. 

1. Garanta que nenhuma solicitação está sendo enviada para o Aplicativo Web.

2. Carregue o Portal do Azure.

3. No painel de navegação à esquerda, selecione a opção **Monitorar**. Depois que a página for carregada, selecione a guia **Dimensionamento automático**.

4. Na lista, selecione o Plano do Serviço de Aplicativo usado durante todo este tutorial.

5. Na configuração de dimensionamento automático, clique na guia **Histórico de execuções**.

6. Você verá um gráfico que reflete a contagem de instâncias do Plano do Serviço de Aplicativo ao longo do tempo.

7. Em alguns minutos, a contagem de instâncias deve cair de 2 para 1. O processo leva pelo menos 100 minutos.  

8. Abaixo do gráfico, está o conjunto correspondente das entradas do log de atividades para cada ação de dimensionamento usada por essa configuração de dimensionamento automático.

    ![Exibir as ações de redução horizontal](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Limpar os recursos

1. No menu à esquerda do portal do Azure, clique em **Todos os recursos** e, em seguida, selecione o Aplicativo Web criado neste tutorial.

2. Na página de recursos, clique em **Excluir**, confirme a exclusão digitando **Sim** na caixa de texto e, depois, clique em **Excluir**.

3. Em seguida, selecione o recurso do Plano do Serviço de Aplicativo e clique em **Excluir**.

4. Confirme a exclusão digitando **Sim** na caixa de texto e, depois, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você  
> [!div class="checklist"]
> * Criou um Aplicativo Web e um Plano do Serviço de Aplicativo
> * Configurou regras de dimensionamento automático para expansão e redução com base no número de solicitações que o aplicativo Web recebeu
> * Disparou uma ação de expansão e observou o aumento do número de instâncias
> * Disparou uma ação de expansão e observou a diminuição do número de instâncias
> * Limpou seus recursos


Para saber mais sobre as configurações de dimensionamento automático, continue e vá para a [visão geral do dimensionamento automático](../autoscale/autoscale-overview.md).

> [!div class="nextstepaction"]
> [Arquivar os dados de monitoramento](../essentials/platform-logs-overview.md)


---
title: Barramento de Serviço do Azure – Atualizar automaticamente as unidades do sistema de mensagens
description: Este artigo mostra como você pode usar atualizar automaticamente as unidades de mensagens de um namespace do barramento de serviço.
ms.topic: how-to
ms.date: 03/03/2021
ms.openlocfilehash: 7fc3aca82b8f01d70dec4fc2dac7842895417ec9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177948"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Atualizar automaticamente as unidades do sistema de mensagens de um namespace do Barramento de Serviço do Azure 
O dimensionamento automático permite ter a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite adicionar recursos para lidar com os aumentos de carga e também economizar dinheiro removendo os recursos que estão ociosos. Consulte [visão geral do dimensionamento automático em Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md) para saber mais sobre o recurso de dimensionamento automático do Azure monitor. 

O Sistema de Mensagens Premium do Barramento de Serviço fornece isolamento de recursos no nível de CPU e memória, de modo que a carga de trabalho do cliente seja executada isoladamente. Esse contêiner de recursos é chamado de **unidade de mensagens**. Para saber mais sobre unidades de mensagens, confira [mensagens do barramento de serviço Premium](service-bus-premium-messaging.md). 

Usando o recurso de dimensionamento automático para namespaces Premium do barramento de serviço, você pode especificar um número mínimo e máximo de [unidades de mensagens](service-bus-premium-messaging.md) e adicionar ou remover unidades de mensagens automaticamente com base em um conjunto de regras. 

Por exemplo, você pode implementar os seguintes cenários de dimensionamento para namespaces do barramento de serviço usando o recurso de dimensionamento automático. 

- Aumente as unidades de mensagens para um namespace do barramento de serviço quando o uso da CPU do namespace ficar acima de 75%. 
- Diminua as unidades de mensagens para um namespace do barramento de serviço quando o uso da CPU do namespace ficar abaixo de 25%. 
- Use mais unidades de mensagens durante o horário comercial e menos durante o expediente. 

Este artigo mostra como você pode dimensionar automaticamente um namespace de barramento de serviço ( [unidades de mensagens](service-bus-premium-messaging.md)de atualização) no portal do Azure. 

> [!IMPORTANT]
> Este artigo aplica-se apenas à camada **premium** do Barramento de Serviço do Azure. 

## <a name="autoscale-setting-page"></a>Página de configuração de dimensionamento automático
Primeiro, siga estas etapas para navegar até a página de **configurações de autoescala** para o namespace do barramento de serviço.

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **barramento de serviço**, selecione **barramento de serviço** na lista suspensa e pressione **Enter**. 
1. Selecione o **namespace Premium** na lista de namespaces. 
1. Alterne para a página **escala** . 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Namespace do barramento de serviço – página escala":::

## <a name="manual-scale"></a>Dimensionamento manual 
Essa configuração permite que você defina um número fixo de unidades de mensagens para o namespace. 

1. Na página **configuração de dimensionamento automático** , selecione **escala manual** se ela ainda não estiver selecionada. 
1. Para configuração de **unidades de mensagens** , selecione o número de unidades de mensagens na lista suspensa.
1. Selecione **salvar** na barra de ferramentas para salvar a configuração. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Dimensionar manualmente unidades de mensagens":::       


## <a name="custom-autoscale---default-condition"></a>Autoescala personalizada-condição padrão
Você pode configurar o dimensionamento automático de unidades de mensagens usando condições. Essa condição de escala é executada quando nenhuma das outras condições de escala coincidem. Você pode definir a condição padrão de uma das seguintes maneiras:

- Dimensionar com base em uma métrica (como uso de CPU ou memória)
- Dimensionar para um número específico de unidades de mensagens

Você não pode definir um agendamento para dimensionamento automático em um intervalo de datas ou dias específicos para uma condição padrão. Essa condição de escala é executada quando nenhuma das outras condições de escala com os agendamentos são correspondentes. 

### <a name="scale-based-on-a-metric"></a>Dimensionar com base em uma métrica
O procedimento a seguir mostra como adicionar uma condição para aumentar automaticamente as unidades de mensagens (scale out) quando o uso da CPU for maior que 75% e diminuir as unidades de mensagens (Scale in) quando o uso da CPU for inferior a 25%. Os incrementos são feitos de 1 a 2, de 2 a 4, de 4 a 8 e de 8 a 16. Da mesma forma, os decrementos são feitos de 16 a 8, 8 a 4, 4 a 2 e 2 a 1. 

1. Na página **configuração de dimensionamento automático** , selecione **dimensionamento automático personalizado** para a opção **escolher como dimensionar seu recurso** . 
1. Na seção **padrão** da página, especifique um **nome** para a condição padrão. Selecione o ícone de **lápis** para editar o texto. 
1. Selecione **escala com base em uma métrica para o** **modo de escala**. 
1. Selecione **+ Adicionar uma regra**. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Padrão-escala com base em uma métrica":::    
1. Na página **regra de dimensionamento** , siga estas etapas:
    1. Selecione uma métrica na lista suspensa **nome da métrica** . Neste exemplo, é **CPU**. 
    1. Selecione um operador e valores de limite. Neste exemplo, eles são **maiores que** e **75** para o **limite de métrica para disparar a ação de escala**. 
    1. Selecione uma **operação** na seção **ação** . Neste exemplo, ele está definido para **aumentar**. 
    1. Em seguida, selecione **Adicionar**
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Padrão-escalar horizontalmente se o uso da CPU for maior que 75%":::       

        > [!NOTE]
        > O recurso de dimensionamento automático aumentará as unidades de mensagens para o namespace se o uso geral da CPU ficar acima de 75% neste exemplo. Os incrementos são feitos de 1 a 2, de 2 a 4, de 4 a 8 e de 8 a 16. 
1. Selecione **+ Adicionar uma regra** novamente e siga estas etapas na página **regra de dimensionamento** :
    1. Selecione uma métrica na lista suspensa **nome da métrica** . Neste exemplo, é **CPU**. 
    1. Selecione um operador e valores de limite. Neste exemplo, eles são **menores que** e **25** para o **limite de métrica para disparar a ação de escala**. 
    1. Selecione uma **operação** na seção **ação** . Neste exemplo, é definido como **diminuir**. 
    1. Em seguida, selecione **Adicionar** 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Padrão-Dimensionar em se o uso da CPU for inferior a 25%":::       

        > [!NOTE]
        > O recurso de dimensionamento automático diminuirá as unidades de mensagens para o namespace se o uso geral da CPU ficar abaixo de 25% neste exemplo. Os decrementos são feitos de 16 a 8, 8 a 4, 4 a 2 e 2 a 1. 
1. Defina o número **mínimo** e **máximo** e **padrão** de unidades de mensagens.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Regra padrão com base em uma métrica":::
1. Selecione **salvar** na barra de ferramentas para salvar a configuração de dimensionamento automático. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Dimensionar para um número específico de unidades de mensagens
Siga estas etapas para configurar a regra para dimensionar o namespace para usar um número específico de unidades de mensagens. Novamente, a condição padrão é aplicada quando nenhuma das outras condições de escala coincidem. 

1. Na página **configuração de dimensionamento automático** , selecione **dimensionamento automático personalizado** para a opção **escolher como dimensionar seu recurso** . 
1. Na seção **padrão** da página, especifique um **nome** para a condição padrão. 
1. Selecione **Dimensionar para unidades de mensagens específicas** para o **modo de escala**. 
1. Para **unidades de mensagens**, selecione o número de unidades de mensagens padrão. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Padrão-Dimensionar para unidades de mensagens específicas":::       

## <a name="custom-autoscale---additional-conditions"></a>Dimensionamento automático personalizado-condições adicionais
A seção anterior mostra como adicionar uma condição padrão para a configuração de dimensionamento automático. Esta seção mostra como adicionar mais condições à configuração de dimensionamento automático. Para essas condições não padrão adicionais, você pode definir uma agenda com base em dias específicos de uma semana ou um intervalo de datas. 

### <a name="scale-based-on-a-metric"></a>Dimensionar com base em uma métrica
1. Na página **configuração de dimensionamento automático** , selecione **dimensionamento automático personalizado** para a opção **escolher como dimensionar seu recurso** . 
1. Selecione **Adicionar uma condição de escala** no bloco **padrão** . 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Personalizado-adicionar um link de condição de escala":::    
1. Especifique um **nome** para a condição. 
1. Confirme se a opção **escala baseada em uma métrica** está selecionada. 
1. Selecione **+ Adicionar uma regra** para adicionar uma regra para aumentar as unidades de mensagens quando o uso geral da CPU ficar acima de 75%. Siga as etapas da seção [condição padrão](#custom-autoscale---default-condition) . 
5. Defina o número **mínimo** e **máximo** e **padrão** de unidades de mensagens.
6. Você também pode definir uma **agenda** em uma condição personalizada (mas não na condição padrão). Você pode especificar as datas de início e de término da condição (ou) selecionar dias específicos (segunda-feira, terça-feira, etc.) de uma semana. 
    1. Se você selecionar **especificar datas de início/término**, selecione o **fuso horário**, **data e hora de início** e **data e hora de término** (conforme mostrado na imagem a seguir) para que a condição esteja em vigor. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Valores mínimo, máximo e padrão para o número de unidades de mensagens":::
    1. Se você selecionar **repetir dias específicos**, selecione os dias da semana, o fuso horário, a hora de início e a hora de término em que a condição deve ser aplicada. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Repetir dias específicos":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Dimensionar para um número específico de unidades de mensagens
1. Na página **configuração de dimensionamento automático** , selecione **dimensionamento automático personalizado** para a opção **escolher como dimensionar seu recurso** . 
1. Selecione **Adicionar uma condição de escala** no bloco **padrão** . 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Personalizado-adicionar um link de condição de escala":::    
1. Especifique um **nome** para a condição. 
2. Selecione **a opção Dimensionar para unidades de mensagens específicas** para o **modo de escala**. 
1. Selecione o número de **unidades de mensagens** na lista suspensa. 
6. Para o **agendamento**, especifique as datas de início e término para a condição (ou) selecione dias específicos (segunda-feira, terça-feira, etc.) de uma semana e horas. 
    1. Se você selecionar **especificar datas de início/término**, selecione o **fuso horário**, **data e hora de início** e **data e hora de término** para que a condição esteja em vigor. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="dimensionar para unidades de mensagens específicas-datas de início e término":::        
    1. Se você selecionar **repetir dias específicos**, selecione os dias da semana, o fuso horário, a hora de início e a hora de término em que a condição deve ser aplicada.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="dimensionar para unidades de mensagens específicas – repetir dias específicos":::

    
    Para saber mais sobre como funcionam as configurações de dimensionamento automático, especialmente como ela escolhe um perfil ou condição e avalia várias regras, consulte [entender as configurações de dimensionamento automático](../azure-monitor/autoscale/autoscale-understanding-settings.md).          

    > [!NOTE]
    > - As métricas que você revisa para tomar decisões sobre o dimensionamento automático podem ser de 5-10 minutos. Quando você estiver lidando com cargas de trabalho do com picos, recomendamos que você tenha durações menores para escalar verticalmente e durações mais longas para reduzir horizontalmente (> 10 minutos) para garantir que haja unidades de mensagens suficientes para processar cargas de trabalho do com picos. 
    > 
    > - Se você encontrar falhas devido à falta de capacidade (nenhuma unidade de mensagens disponível), gere um tíquete de suporte conosco.  


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as unidades do sistema de mensagens, consulte o [Mensagens premium](service-bus-premium-messaging.md)


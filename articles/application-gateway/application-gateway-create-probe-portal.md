---
title: Criar uma investigação personalizada usando o portal
titleSuffix: Azure Application Gateway
description: Saiba como criar uma investigação personalizada para o Gateway de Aplicativo usando o portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95975948"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma investigação personalizada para o Gateway de Aplicativo usando o portal

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-probe-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell clássico do Azure](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona uma investigação de integridade personalizada a um gateway de aplicativo existente por meio do portal do Azure. Usando as investigações de integridade, Aplicativo Azure gateway monitora a integridade dos recursos no pool de back-end.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver um gateway de aplicativo, visite [Criar um Gateway de Aplicativo](./quick-create-portal.md) a fim de criar um gateway de aplicativo para trabalhar.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Criar investigação para SKU do gateway de aplicativo v2

As investigações são configuradas em um processo de duas etapas pelo portal. A primeira etapa é inserir os valores necessários para a configuração de investigação. Na segunda etapa, você testará a integridade do back-end usando essa configuração de teste e salvará a investigação. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Inserir Propriedades da investigação

1. Entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)

2. No painel Favoritos do portal do Azure, clique em Todos os recursos. Clique no gateway de aplicativo na folha Todos os recursos. Se a assinatura que você selecionou já contém vários recursos, você pode inserir partners.contoso.net na caixa Filtrar por nome... para acessar facilmente o gateway de aplicativo.

3. Selecione **investigações de integridade** e, em seguida, selecione **Adicionar** para adicionar uma nova investigação de integridade.

   ![Adicionar nova investigação][4]

4. Na página **Adicionar investigação de integridade** , preencha as informações necessárias para a investigação e, quando concluir, selecione **OK**.

   |**Configuração** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Esse valor é um nome amigável dado à investigação que é acessível no Portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo que a investigação de integridade usa. |
   |**Host**|ou seja contoso.com|Esse valor é o nome do host virtual (diferente do nome de host da VM) em execução no servidor de aplicativos. A investigação é enviada para \<protocol\> :// \<host name\> :\<port\>/\<urlPath\>|
   |**Escolha o nome do host nas configurações de HTTP de back-end**|Sim ou não|Define o cabeçalho do *host* na investigação para o nome do host nas configurações de http às quais essa investigação está associada. Especialmente necessário no caso de back-ends de vários locatários, como o serviço de aplicativo do Azure. [Saiba mais](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Escolher porta das configurações de HTTP de back-end**| Sim ou não|Define a *porta* da investigação de integridade para a porta das configurações de http às quais essa investigação está associada. Se você escolher não, poderá inserir uma porta de destino personalizada a ser usada |
   |**Porta**| 1-65535 | Porta personalizada a ser usada para as investigações de integridade | 
   |**Caminho**|/ou qualquer caminho válido|O restante da URL completa para a investigação personalizada. Um caminho válido começa com "/". Para o caminho padrão de http: \/ /contoso.com, basta usar '/' |
   |**Intervalo (segundos)**|30|Frequência com que a investigação é executada para verificar a integridade. Não é recomendável defini-la abaixo de 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a investigação aguarda antes de atingir o tempo limite. Se uma resposta válida não for recebida nesse período de tempo limite, a investigação será marcada como com falha. O intervalo de tempo limite deve ser alto o suficiente para que uma chamada http possa ser feita a fim de garantir que a página de integridade do back-end estará disponível. Observe que o valor de tempo limite não deve ser maior que o valor de ' interval ' usado nesta configuração de investigação ou o valor de ' tempo limite de solicitação ' na configuração de HTTP que será associada a essa investigação.|
   |**Limite não íntegro**|3|Número de tentativas de falha consecutivas a serem consideradas não íntegras. O limite pode ser definido como 1 ou mais.|
   |**Usar condições de correspondência de investigação**|Sim ou não|Por padrão, uma resposta HTTP(S) com código de status entre 200 e 399 é considerada íntegra. Você pode alterar o intervalo aceitável de código de resposta de back-end ou corpo de resposta de back-end. [Saiba mais](./application-gateway-probe-overview.md#probe-matching)|
   |**Configurações de HTTP**|seleção da lista suspensa|A investigação será associada às configurações de HTTP selecionadas aqui e, portanto, monitorará a integridade desse pool de back-end que está associado à configuração de HTTP selecionada. Ele usará a mesma porta para a solicitação de investigação que a que está sendo usada na configuração de HTTP selecionada. Você só pode escolher essas configurações HTTP que não estão associadas a nenhuma outra investigação personalizada. <br>Observe que apenas essas configurações HTTP estão disponíveis para associação que têm o mesmo protocolo que o protocolo escolhido nesta configuração de investigação e têm o mesmo estado para a opção *escolher nome de host do back-end de configuração de http* .|
   
   > [!IMPORTANT]
   > A investigação monitorará a integridade do back-end somente quando ele estiver associado a uma ou mais configurações de HTTP. Ele monitorará os recursos de back-end desses pools de back-end que estão associados às configurações de HTTP às quais essa investigação está associada. A solicitação de investigação será enviada como \<protocol\> :// \<hostName\> : \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Testar a integridade do back-end com a investigação

Depois de inserir as propriedades de investigação, você pode testar a integridade dos recursos de back-end para verificar se a configuração de investigação está correta e se os recursos de back-end estão funcionando conforme o esperado.

1. Selecione **testar** e observe o resultado da investigação. O gateway de aplicativo testa a integridade de todos os recursos de back-end nos pools de back-end associados às configurações de HTTP usadas para essa investigação. 

   ![Testar integridade do back-end][5]

2. Se houver recursos de back-end não íntegros, verifique a coluna **detalhes** para entender o motivo do estado não íntegro do recurso. Se o recurso tiver sido marcado como não íntegro devido a uma configuração de investigação incorreta, selecione o link voltar **ao teste de investigação** e edite a configuração da investigação. Caso contrário, se o recurso tiver sido marcado como não íntegro devido a um problema com o back-end, resolva os problemas com o recurso de back-end e, em seguida, teste o back-end novamente selecionando **voltar ao** link de investigação e selecione **testar**.

   > [!NOTE]
   > Você pode optar por salvar a investigação mesmo com recursos de back-end não íntegros, mas isso não é recomendado. Isso ocorre porque o gateway de aplicativo não encaminhará solicitações para os servidores de back-end do pool de back-end que são determinados como não íntegros pela investigação. Caso não haja recursos íntegros em um pool de back-end, você não poderá acessar seu aplicativo e receberá um erro HTTP 502.

   ![Exibir resultado da investigação][6]

3. Selecione **Adicionar** para salvar a investigação. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Criar investigação para SKU do gateway de aplicativo v1

As investigações são configuradas em um processo de duas etapas pelo portal. A primeira etapa é criar a investigação. Na segunda etapa, você adiciona a investigação às configurações de HTTP do back-end do Gateway de Aplicativo.

### <a name="create-the-probe"></a><a name="createprobe"></a>Criar a investigação

1. Entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)

2. No painel Favoritos do portal do Azure, selecione **Todos os recursos**. Selecione o gateway de aplicativo na página **todos os recursos** . Se a assinatura que você selecionou já contém vários recursos, você pode inserir partners.contoso.net na caixa Filtrar por nome... para acessar facilmente o gateway de aplicativo.

3. Selecione **investigações** e, em seguida, selecione **Adicionar** para adicionar uma investigação.

   ![Folha Adicionar Investigação com as informações preenchidas][1]

4. Na folha **Adicionar investigação de integridade** , preencha as informações necessárias para a investigação e, quando concluir, selecione **OK**.

   |**Configuração** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Esse valor é um nome amigável dado à investigação que é acessível no Portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo que a investigação de integridade usa. |
   |**Host**|ou seja contoso.com|Esse valor é o nome do host virtual (diferente do nome de host da VM) em execução no servidor de aplicativos. A investigação é enviada para (protocolo)://(nome do host):(porta de httpsetting)/urlPath.  Isso é aplicável quando o multissite é configurado no gateway de aplicativo. Se o gateway de aplicativo estiver configurado para um único site, digite ' 127.0.0.1 '.|
   |**Escolha o nome do host nas configurações de HTTP de back-end**|Sim ou não|Define o cabeçalho de *host* na investigação como o nome de host do recurso de back-end no pool de back-ends associado à configuração de http à qual essa investigação está associada. Especialmente necessário no caso de back-ends de vários locatários, como o serviço de aplicativo do Azure. [Saiba mais](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Caminho**|/ou qualquer caminho válido|O restante da URL completa para a investigação personalizada. Um caminho válido começa com "/". Para o caminho padrão de http: \/ /contoso.com, basta usar '/' |
   |**Intervalo (segundos)**|30|Frequência com que a investigação é executada para verificar a integridade. Não é recomendável defini-la abaixo de 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a investigação aguarda antes de atingir o tempo limite. Se uma resposta válida não for recebida nesse período de tempo limite, a investigação será marcada como com falha. O intervalo de tempo limite deve ser alto o suficiente para que uma chamada http possa ser feita a fim de garantir que a página de integridade do back-end estará disponível. Observe que o valor de tempo limite não deve ser maior que o valor de ' interval ' usado nesta configuração de investigação ou o valor de ' tempo limite de solicitação ' na configuração de HTTP que será associada a essa investigação.|
   |**Limite não íntegro**|3|Número de tentativas de falha consecutivas a serem consideradas não íntegras. O limite pode ser definido como 1 ou mais.|
   |**Usar condições de correspondência de investigação**|Sim ou não|Por padrão, uma resposta HTTP(S) com código de status entre 200 e 399 é considerada íntegra. Você pode alterar o intervalo aceitável de código de resposta de back-end ou corpo de resposta de back-end. [Saiba mais](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > O nome do host não é o mesmo que o nome do servidor. Esse valor é o nome do host virtual em execução no servidor de aplicativos. A investigação é enviada para \<protocol\> :// \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Adicionar a investigação ao gateway

Agora que a investigação foi criada, é hora de adicioná-la ao gateway. As configurações da investigação são definidas nas configurações de http do back-end do Gateway de Aplicativo.

1. Clique em **Configurações de HTTP** do Gateway de Aplicativo e clique nas configurações de HTTP do back-end atual listadas na janela para abrir a folha de configuração.

   ![janela de configurações de https][2]

2. Na página Configurações do **appgatewaybackendhttp** , marque a caixa de seleção **usar investigação personalizada** e escolha a investigação criada na seção [criar a investigação](#createprobe) no menu suspenso **investigação personalizada** .
   Ao concluir, clique em **Salvar** e as configurações são aplicadas.

## <a name="next-steps"></a>Próximas etapas

Exiba a integridade dos recursos de back-end conforme determinado pela investigação usando a [exibição integridade de back-end](./application-gateway-diagnostics.md#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png

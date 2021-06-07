---
title: 'Início rápido: Encaminhar eventos do Cache do Azure para Redis para o ponto de extremidade da Web com o portal do Azure'
description: Use a Grade de Eventos do Azure para assinar eventos do Cache do Azure para Redis, enviar os eventos para um webhook e processar os eventos em um aplicativo Web
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: e021f386f255f1cef61e28cbd4fd6116fc2aa727
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529307"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Início rápido: Encaminhar eventos do Cache do Azure para Redis para o ponto de extremidade da Web com o portal do Azure

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste guia de início rápido, você usará o portal do Azure para criar uma instância do Cache do Azure para Redis, assinar eventos dessa instância, disparar um evento e ver os resultados. Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No entanto, para simplificar este guia de início rápido, você enviará eventos para um aplicativo Web que coletará e exibirá as mensagens. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Quando terminar, você verá que os dados do evento foram enviados para o aplicativo Web.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Dimensionamento do Visualizador da Grade de Eventos do Azure no formato JSON.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Criar uma instância de cache do Cache do Azure para Redis 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar eventos para a instância de cache, vamos criar o ponto de extremidade para a mensagem de evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Para simplificar este guia de início rápido, você implantará um [aplicativo Web predefinido](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

1. Selecione **Implantar no Azure** no README do GitHub para implantar a solução na sua assinatura. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Botão Implantar no Azure.":::

2. Na página **Implantação personalizada**, realize as seguintes etapas: 
    1. Em **Grupo de recursos**, selecione o grupo de recursos que você criou ao criar a instância de cache. Será mais fácil fazer a limpeza após a concluir o tutorial, excluindo o grupo de recursos.  
    2. Em **Nome do Site**, insira um nome para o aplicativo Web.
    3. Em **Nome do plano de hospedagem**, insira um nome para o plano do Serviço de Aplicativo a usar para hospedar o aplicativo Web.
    4. Selecione a caixa de seleção **Concordo com os termos e condições declarados acima**. 
    5. Selecione **Comprar**. 
    
    | Configuração      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual este aplicativo Web será criado. | 
    | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
    | **Nome do Site** | Insira um nome para o aplicativo Web. | Esse valor não pode ficar em branco. | 
    | **Nome do plano de hospedagem** | Insira um nome para o Plano do Serviço de Aplicativo a ser usado para hospedar o aplicativo Web. | Esse valor não pode ficar em branco. | 

1. Selecione alertas (ícone de sino) no portal e, em seguida, selecione **Ir para o grupo de recursos**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Notificação de implantação do portal do Azure.":::

4. Na página **Grupo de recursos**, na lista de recursos, selecione o aplicativo Web que você criou. Você também verá o Plano do Serviço de Aplicativo e a instância de cache nessa lista. 

5. Na página **Serviço de Aplicativo** para seu aplicativo Web, selecione a URL para navegar até o site da Web. A URL deve estar neste formato: `https://<your-site-name>.azurewebsites.net`.

6. Confirme que você vê o site, mas que nenhum evento foi postado nele ainda.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Site vazio do Visualizador da Grade de Eventos.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Assinar a instância do Cache do Azure para Redis

Nesta etapa, você assinará um tópico para indicar à Grade de Eventos quais eventos deseja acompanhar e para o local em que os eventos serão enviados.

1. No portal, navegue até a instância de cache criada anteriormente. 
2. Na página **Cache do Azure para Redis**, selecione **Eventos** no menu à esquerda. 
3. Selecione **Webhook**. Você está enviando eventos para o seu aplicativo visualizador, usando para isso um web hook para o ponto de extremidade. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Página Eventos do portal do Azure.":::

4. Na página **Criar Assinatura de Eventos**, insira o seguinte: 

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Insira um nome para a assinatura de evento. | O valor precisa ter entre 3 e 64 caracteres. Ele só pode conter letras, números e traços. | 
    | **Tipos de Eventos** | Escolha o menu suspenso e selecione quais tipos de eventos deseja enviar por push ao destino. Para este guia de início rápido, dimensionaremos nossa instância de cache. | Aplicação de patch, dimensionamento, importação e exportação são as opções disponíveis. | 
    | **Tipo de Ponto de Extremidade** | Selecione **Webhook**. | Manipulador de eventos para receber seus eventos. | 
    | **Ponto de extremidade** | Clique em **Selecionar um ponto de extremidade**, insira a URL do aplicativo Web, adicione `api/updates` à URL da home page (por exemplo: `https://cache.azurewebsites.net/api/updates`) e escolha **Confirmar Seleção**. | Essa é a URL do aplicativo Web que você criou anteriormente. | 

5. Agora, na página **Criar Assinatura de Evento**, selecione **Criar** para criar a assinatura de evento. 

6. Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. Selecione o ícone de olho para expandir os dados de evento. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O aplicativo Web inclui o código para validar a assinatura.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Visualizador da Grade de Eventos do Azure.":::

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o ponto de extremidade

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Dimensionaremos sua instância do Cache do Azure para Redis.

1. No portal do Azure, navegue até a instância do Cache do Azure para Redis e selecione **Escala** no menu à esquerda.

1. Escolha o tipo de preço desejado na página **Escala** e clique em **Selecionar**. 

    Você pode dimensionar para um tipo de preço diferente com as restrições a seguir:
    
    * Você não pode dimensionar de uma camada de preços mais alta para uma camada de preços mais baixa.
      * Você não pode dimensionar de um cache **Premium** para um cache **Standard** ou **Básico**.
      * Você não pode dimensionar de um cache **Standard** para um cache **Básico**.
    * É possível dimensionar de um cache **Básico** para um cache **Standard**, mas não é possível alterar o tamanho simultaneamente. Se precisar de um tamanho diferente, você pode fazer uma operação de dimensionamento subsequente para o tamanho desejado.
    * Você não pode dimensionar de um cache **Básico** diretamente para um cache **Premium**. Você deve dimensionar do **Básico** para o **Standard** em uma única operação de dimensionamento e do **Standard** para o **Premium** em uma operação de dimensionamento subsequente.
    * Não é possível dimensionar de um tamanho maior para o tamanho **C0 (250 MB)** .
 
    Enquanto o cache é dimensionado para a nova camada de preços, é exibido um status **Dimensionando** na folha do **Cache Redis do Azure**. Quando o dimensionamento for concluído, o status será alterado de **Dimensionando** para **Executando**.

1. Você disparou o evento, e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. A mensagem está no formato JSON e contém uma matriz com um ou mais eventos. No exemplo a seguir, a mensagem JSON contém uma matriz com um evento. Veja seu aplicativo Web e observe que um evento **ScalingCompleted** foi recebido. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Dimensionamento do Visualizador da Grade de Eventos do Azure no formato JSON.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Caso planeje continuar trabalhando com esse evento, não limpe os recursos criados neste guia de início rápido. Caso contrário, exclua os recursos criados neste guia de início rápido.

Selecione o grupo de recursos e selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como criar tópicos e assinaturas de evento personalizados, saiba mais sobre como a Grade de Eventos pode ajudá-lo:

- [Como responder aos eventos do Cache do Azure para Redis](cache-event-grid.md)
- [Sobre a Grade de Eventos](../event-grid/overview.md)

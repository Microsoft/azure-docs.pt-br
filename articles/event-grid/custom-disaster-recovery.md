---
title: Recuperação de desastre para tópicos personalizados na Grade de Eventos do Azure
description: Este tutorial orientará você ao longo do processo de configurar sua arquitetura de eventos a ser recuperada se o serviço da Grade de Eventos passar a ser não íntegro em uma região.
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e37cb6a0679ee2e249de4ed8fa31c40d5082ea4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020136"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Criar sua própria recuperação de desastre para tópicos personalizados na Grade de Eventos
A recuperação de desastre tem como foco a recuperação após uma perda grave de funcionalidade do aplicativo. Este tutorial o orientará pelo processo de configurar sua arquitetura de eventos a ser recuperada se o serviço de Grade de Eventos se tornar não íntegro em uma região específica.

Neste tutorial, você aprenderá como criar uma arquitetura de failover ativo-passivo para tópicos personalizados na Grade de Eventos. Você realizará o failover por meio do espelhamento dos seus tópicos e assinaturas em duas regiões e, em seguida, por meio do gerenciamento de um failover quando um tópico se tornar não íntegro. A arquitetura neste tutorial faz failover de todo o tráfego novo. É importante ter cuidado com esta instalação; eventos que já estão em versão de pré-lançamento não serão recuperados até que a região comprometida esteja íntegra novamente.

> [!NOTE]
> Agora, a Grade de Eventos dá suporte à recuperação automática de desastre geográfico (GeoDR) no lado do servidor. Você ainda poderá implementar a lógica de recuperação de desastre do lado do cliente se quiser um controle maior sobre o processo de failover. Para obter detalhes sobre GeoDR automático, veja [Recuperação de desastre geográfico do lado do servidor na Grade de Eventos do Azure](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Para testar sua configuração de failover, será necessário um ponto de extremidade no qual receber seus eventos. O ponto de extremidade não faz parte de sua infraestrutura de failover, mas funcionará como o nosso manipulador de eventos para torná-lo mais fácil de testar.

Para simplificar o teste, implante um [aplicativo Web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

1. Selecione **Implantar no Azure** para implantar a solução na sua assinatura. No portal do Azure, forneça os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>

1. A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`
Anote essa URL, pois você precisará dela posteriormente.

1. Você verá o site, mas nenhum evento ainda estará publicado.

   ![Exibir novo site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Crie seus tópicos principais e secundários

Primeiro, crie dois tópicos de Grade de Eventos. Esses tópicos funcionarão como principais e secundários. Por padrão, seus eventos fluirão por meio do tópico principal. Se houver uma interrupção no serviço na região primária, a secundária assumirá.

1. Entre no [portal do Azure](https://portal.azure.com). 

1. No canto superior esquerdo do menu principal do Azure, escolha **Todos os Serviços** > pesquise **Grade de Eventos** > selecione **Tópicos de Grade de Eventos**.

   ![Menu Tópicos de Grade de Eventos](./media/custom-disaster-recovery/select-topics-menu.png)

    Selecione a estrela ao lado dos tópicos de Grade de Eventos para adicioná-los ao menu de recursos para facilitar o acesso no futuro.

1. No menu Tópicos de Grade de Eventos, selecione **+ADICIONAR** para criar o tópico principal.

   * Dê um nome lógico ao tópico e adicione “-principal” como sufixo para facilitar o acompanhamento.
   * A região desse tópico será a região primária.

     ![Caixa de diálogo principal de criação do Tópico de Grade de Eventos](./media/custom-disaster-recovery/create-primary-topic.png)

1. Após a criação do Tópico, navegue até ele e copie o **Ponto de Extremidade do Tópico**. você precisará do URI posteriormente.

    ![tópico principal de Grade de Eventos](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Obtenha a chave de acesso para o tópico, que também será necessária posteriormente. Clique em **Chaves de Acesso** no menu de recursos e copie a Chave 1.

    ![Obter a chave do tópico principal](./media/custom-disaster-recovery/get-primary-access-key.png)

1. Na folha Tópico, clique em **+Assinatura do Evento** para criar uma assinatura que conecta a assinatura do site do receptor de eventos criada nos pré-requisitos ao tutorial.

   * Dê um nome lógico à assinatura do evento e adicione “-primário” como sufixo para facilitar o acompanhamento.
   * Selecione o Web hook do tipo de ponto de extremidade.
   * Defina o ponto de extremidade como a URL do evento do receptor de eventos, que deve ter esta aparência: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Captura de tela que mostra a página "Criar Assinatura de Evento – Básica" com os valores de "Nome", "Tipo de Ponto de Extremidade" e "Ponto de Extremidade" realçados.](./media/custom-disaster-recovery/create-primary-es.png)

1. Repita o mesmo fluxo para criar seu tópico e assinatura secundários. Dessa vez, substitua o sufixo "-primário" por "-secundário" para facilitar o acompanhamento. Por fim, certifique-se de colocá-lo em uma região diferente do Azure. Embora seja possível colocá-lo em qualquer local que você desejar, é recomendável usar as [Regiões Emparelhadas do Azure](../best-practices-availability-paired-regions.md). Colocar o tópico e a assinatura secundários em uma região diferente garante que os novos eventos flutuarão mesmo se a região primária ficar inativa.

Agora você deve ter:

   * Um site do receptor de eventos para teste.
   * Um tópico principal em sua região primária.
   * Uma assinatura de eventos primários conectando seu tópico principal ao site do receptor de eventos.
   * Um tópico secundário em sua região secundária.
   * Uma assinatura de eventos secundários conectando seu tópico principal ao site do receptor de eventos.

## <a name="implement-client-side-failover"></a>Implementar o failover do lado do cliente

Agora que você tem um par de instalação de tópicos e assinaturas regionalmente redundante, você está pronto para implementar o failover no lado do cliente. Há várias maneiras de realizar isso, mas todas as implementações de failover terão um recurso em comum: se um tópico não for mais íntegro, o tráfego será redirecionado para o outro tópico.

### <a name="basic-client-side-implementation"></a>Implementação básica do lado do cliente

O código de exemplo a seguir é um publicador simples do .NET que sempre tentará ser publicado primeiro em seu tópico principal. Se não tiver êxito, ele fará o failover do tópico secundário. Em ambos os casos, ele também verifica a api de integridade do outro tópico fazendo um GET no `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`. Um tópico íntegro deve sempre responder com **200 OK** quando um GET é feito no ponto de extremidade **/api/health**.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Experimentar

Agora que você tem todos os componentes em vigor, é possível testar sua implementação de failover. Execute o exemplo acima no Visual Studio Code ou seu ambiente favorito. Substitua os quatro valores a seguir pelos pontos de extremidade e pelas chaves de seus tópicos:

   * primaryTopic – o ponto de extremidade do seu tópico principal.
   * secondaryTopic – o ponto de extremidade do seu tópico principal.
   * primaryTopicKey – a chave do seu tópico principal.
   * secondaryTopicKey – a chave do seu tópico secundário.

Experimente executar o publicador de eventos. Você deve ver os eventos de teste no visualizador da Grade de Eventos como no exemplo abaixo.

![Assinatura do Evento Principal da Grade de Eventos](./media/custom-disaster-recovery/event-grid-viewer.png)

Para garantir que o failover está funcionando, é possível alterar alguns caracteres na sua chave de tópico principal para não torná-la mais válida. Experimente executar o publicador novamente. Você ainda deve ver novos eventos serem exibidos no visualizador da Grade de Eventos; no entanto, quando você examinar seu console, você verá que eles agora estão sendo publicados por meio do tópico secundário.

### <a name="possible-extensions"></a>Extensões possíveis

Há muitas maneiras de estender este exemplo com base em suas necessidades. Para cenários de alto volume, talvez convenha verificar regularmente a api de integridade do tópico de maneira independente. Dessa forma, se um tópico ficasse inativo, não seria necessário verificá-lo em cada publicação. Após saber que um tópico não é íntegro, será possível definir a publicação do tópico secundário como padrão.

Da mesma forma, talvez convenha implementar a lógica de failback com base em suas necessidades específicas. Se a publicação no data center mais próximo for essencial para você reduzir a latência, será possível investigar periodicamente a api de integridade de um tópico que realizou o failover. Quando estiver íntegro novamente, você saberá que é mais seguro fazer o failback para o data center mais próximo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [receber eventos em um ponto de extremidade http](./receive-events.md)
- Descubra como [encaminhar eventos para Conexões Híbridas](./custom-event-to-hybrid-connection.md)
- Saiba mais sobre a [recuperação de desastre usando a DNS do Azure e o Gerenciador de Tráfego](../networking/disaster-recovery-dns-traffic-manager.md)

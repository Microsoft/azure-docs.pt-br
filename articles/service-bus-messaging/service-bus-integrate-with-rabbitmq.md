---
title: Como integrar o RabbitMQ ao barramento de serviço do Azure
description: Guia passo a passo sobre como integrar o RabbitMQ ao barramento de serviço do Azure
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91301050"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>Como integrar o RabbitMQ ao barramento de serviço do Azure

Neste guia, vamos aprender como enviar mensagens do RabbitMQ para o barramento de serviço do Azure.

Aqui estão alguns cenários em que podemos usar esses recursos:

- **Configurações de borda**: temos uma configuração de borda onde estamos enviando mensagens para RabbitMQ, mas queremos encaminhar essas mensagens para o [barramento de serviço do Azure](./service-bus-messaging-overview.md) para processamento adicional, para que possamos usar muitos dos [recursos de Big data do Azure](/azure/architecture/guide/architecture-styles/big-data).
- **Nuvem híbrida**: sua empresa acabou de adquirir terceiros que usam RabbitMQ para suas necessidades de mensagens. Eles estão em uma nuvem diferente. Enquanto eles fazem a transição para o Azure, você já pode começar a compartilhar dados por meio da ponte RabbitMQ com o barramento de serviço do Azure.
- **Integração de terceiros**: um terceiro usa o RabbitMQ como um agente e deseja enviar seus dados para nós, mas eles estão fora de nossa organização. Podemos fornecê-los com a chave SAS, concedendo a eles acesso a um conjunto limitado de filas do barramento de serviço do Azure para onde eles possam encaminhar suas mensagens.

A lista continua, mas podemos resolver a maioria desses casos de uso por meio da ponte de RabbitMQ para o Azure.

Primeiro, você precisa criar uma conta gratuita do Azure inscrevendo-se [aqui](https://azure.microsoft.com/free/)

Quando você estiver conectado à sua conta, vá para a [portal do Azure](https://portal.azure.com/) e crie um novo [namespace](./service-bus-create-namespace-portal.md)do barramento de serviço do Azure. Os namespaces são os contêineres de escopo em que nossos componentes de mensagens residirão, como filas e tópicos.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Adicionando um novo namespace do barramento de serviço do Azure

Em portal do Azure, clique no botão de adição grande para adicionar um novo recurso

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Criar recurso":::

Em seguida, selecione integração e clique em barramento de serviço do Azure para criar um namespace de mensagens:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Selecione o barramento de serviço do Azure":::

Você será solicitado a inserir as informações de namespace. Selecione a assinatura do Azure que deseja usar. Se você não tiver um [grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md), poderá criar um novo.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Criar um namespace":::

Use `rabbitmq` para `Namespace name` , mas pode ser qualquer coisa que você queira. Em seguida, defina `East US` para o local. Escolha `Basic` como o tipo de preço.

Se tudo deu certo, você deverá ver a seguinte tela de confirmação:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Criar confirmação de namespace":::

Em seguida, de volta ao portal do Azure você verá o novo `rabbitmq` namespace listado ali. Clique nele para acessar o recurso para que você possa adicionar uma fila a ele.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Lista de recursos com novo namespace":::

## <a name="creating-our-azure-service-bus-queue"></a>Criando nossa fila do barramento de serviço do Azure

Agora que você tem o namespace do barramento de serviço do Azure, clique no `Queues` botão à esquerda, em `Entities` , para que você possa adicionar uma nova fila:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Criar fila":::

O nome da fila será `from-rabbitmq` apenas como um lembrete para onde as mensagens são provenientes. Você pode deixar todas as outras opções como padrões, mas pode alterá-las de acordo com as necessidades do seu aplicativo.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>Habilitando o plug-in RabbitMQ Shovel

Para enviar mensagens do RabbitMQ para o barramento de serviço do Azure, vamos usar o [plug-in Shovel](https://www.rabbitmq.com/shovel.html) que vem empacotado com RabbitMQ. Você pode habilitar o plug-in e sua interface visual com este comando:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Talvez seja necessário executar esse comando como raiz.

Agora é hora de obter as credenciais necessárias para conectar o RabbitMQ ao Azure.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Conectando o RabbitMQ ao barramento de serviço do Azure

Você precisará criar uma SAS ( [política de acesso compartilhado](../storage/common/storage-sas-overview.md) ) para sua fila, para que o RabbitMQ possa publicar mensagens nela. Uma política SAS permite especificar o que a parte externa tem permissão para fazer com seu recurso. A ideia é que o RabbitMQ é capaz de enviar mensagens, mas não de escutar ou gerenciar a fila.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="Adicionar política SAS":::

Marque a `Send` caixa e clique `Create` para ter nossa política de SAS em vigor.

Depois que a política tiver sido criada, clique nela para ver a **cadeia de conexão primária**. Vamos usá-lo para permitir que o RabbitMQ se comunique com o barramento de serviço do Azure:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="Obter política de SAS":::

Para poder usar essa cadeia de conexão, você precisará convertê-la no formato de conexão AMQP do RabbitMQ. Portanto, vá para a [ferramenta conversor de cadeia de conexão](https://red-mushroom-0f7446a0f.azurestaticapps.net/) e cole a cadeia de conexão no formulário, clique em converter. Você obterá uma cadeia de conexão que está RabbitMQ pronta. (Esse site executa tudo local em seu navegador para que seus dados não sejam enviados pela rede). Você pode acessar seu código-fonte no [GitHub](https://github.com/videlalvaro/connstring_to_amqp).

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="Converter cadeia de conexão":::

Agora, abra o plug-in de gerenciamento RabbitMQ em nossos navegadores `http://localhost:15672/#/dynamic-shovels` e vá para `Admin -> Shovel Management` , onde você pode adicionar seu novo Shovel que cuidará do envio de mensagens de uma fila RabbitMQ para sua fila do barramento de serviço do Azure.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="Adicionar RabbitMQ Shovel":::

Aqui, chame seu Shovel `azure` e escolha `AMQP 0.9.1` como o protocolo de origem. Na captura de tela, temos `amqp://` , que é o URI padrão que nos conecta a um servidor RabbitMQ local. Certifique-se de adaptá-lo à sua implantação atual.

No lado da fila, você pode usar `azure` como o nome da sua fila. Se essa fila não existir, o RabbitMQ a criará para você. Você também pode escolher o nome de uma fila que já existe. Você pode deixar as outras opções como padrão.

Em seguida `destination` , no lado das coisas, escolha `AMQP 1.0` como o protocolo. No `URI` campo, insira a cadeia de conexão que você obteve na etapa anterior, você converteu sua cadeia de conexão do Azure para o formato RabbitMQ. Ele deverá ser parecido com:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

No `Address` campo, inseriremos o nome da sua **fila do barramento de serviço do Azure**, nesse caso, ele foi chamado `from-rabbitmq` . Clique em `Add Shovel` , e sua configuração deve estar pronta para começar a receber mensagens.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Publicando mensagens do RabbitMQ para o barramento de serviço do Azure

Na interface de gerenciamento do RabbitMQ, podemos acessar `Queues` , selecionar a `azure` fila e pesquisar o `Publish message` painel. Aparecerá um formulário que permitirá publicar mensagens diretamente em sua fila. Para nosso exemplo, vamos adicionar apenas `fist message` como `Payload` e pressionar `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Publicar primeira mensagem":::

Volte para o Azure e inspecione sua fila. Clique `Service Bus Explorer` no painel esquerdo e, em seguida, clique no botão _inspecionar_ . Se tudo deu certo, você verá que sua fila agora tem uma mensagem. Sim, parabéns!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Fila do barramento de serviço do Azure":::

Mas vamos garantir que a mensagem seja aquela que você enviou do RabbitMQ. Selecione a `Peek` guia e clique no `Peek` botão para recuperar as últimas mensagens em sua fila. Clique na mensagem para inspecionar seu conteúdo. Você deve ver algo semelhante à imagem abaixo, onde `first message` está listado.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Inspeção de fila":::

## <a name="lets-recap"></a>Vamos recapitular

Parabéns! Você alcançou muito! Você é gerenciado para obter suas mensagens do RabbitMQ para o barramento de serviço do Azure, vamos recapitular as etapas:

1. Criar um namespace do barramento de serviço do Azure
2. Adicionar uma fila ao namespace
3. Adicionar uma política SAS à sua fila
4. Obter a cadeia de conexão da fila
5. Habilitar o plug-in RabbitMQ Shovel & a interface de gerenciamento
6. Converter a cadeia de conexão do barramento de serviço do Azure no formato AMQP do RabbitMQ
7. Adicionar um novo Shovel ao RabbitMQ & conectá-lo ao barramento de serviço do Azure
8. Publicar mensagens

Seguindo as etapas anteriores, você integrou áreas de sua organização que estavam fora do Azure. O plug-in Shovel permitia que você enviasse mensagens do RabbitMQ para o barramento de serviço do Azure. Isso tem enormes vantagens, já que agora você pode permitir que terceiros confiáveis conectem seus aplicativos à sua implantação do Azure.

No final, o sistema de mensagens é sobre a habilitação de conexões e, com essa técnica, acabamos de abrir um novo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Barramento de Serviço do Azure](./service-bus-messaging-overview.md).
- Saiba mais sobre o [suporte do AMQP 1,0 no barramento de serviço](./service-bus-amqp-overview.md)

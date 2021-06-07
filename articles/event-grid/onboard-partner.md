---
title: Integrar como um parceiro de grade de eventos do Azure usando portal do Azure
description: Use portal do Azure para carregar um parceiro de grade de eventos do Azure.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98050945"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Integrar como um parceiro de grade de eventos do Azure usando o portal do Azure
Este artigo descreve como os provedores de SaaS de terceiros, também conhecidos como editores de eventos ou parceiros, são integrados à grade de eventos para poderem publicar eventos de seus serviços e como esses eventos são consumidos pelos usuários finais.

> [!IMPORTANT]
> Se você não estiver familiarizado com eventos de parceiro, consulte [visão geral de eventos de parceiro](partner-events-overview.md) para obter uma introdução detalhada dos principais conceitos que são essenciais para entender e seguir as etapas neste artigo.

## <a name="onboarding-process-for-event-publishers-partners"></a>Processo de integração para editores de eventos (parceiros)
Resumindo, habilitar os eventos do serviço a serem consumidos por usuários normalmente envolve o seguinte processo:

1. **Comunique seu interesse** em se tornar um parceiro da equipe de serviço de grade de eventos antes de prosseguir com as próximas etapas.
1. Crie um tipo de tópico de parceiro criando um **registro**. 
1. Crie um **namespace**.
1. Crie um **canal de evento** e um **tópico de parceiro** (etapa única).
1. Teste a funcionalidade de eventos de parceiros de ponta a ponta.

Para a etapa #4, você deve decidir que tipo de experiência do usuário você deseja fornecer. Você tem as seguintes opções:
- Forneça sua própria solução, normalmente uma experiência de GUI (interface gráfica do usuário) da Web, hospedada em seu domínio usando nosso SDK e/ou API REST para criar um canal de evento e seu tópico de parceiro correspondente. Com essa opção, você pode solicitar ao usuário a assinatura e o grupo de recursos sob o qual você criará um tópico de parceiro.
- Use portal do Azure ou CLI para criar o canal de evento e o tópico de parceiro associado. Com essa opção, você deve ter o Get na assinatura do Azure do usuário, de alguma forma e grupo de recursos sob o qual você criará um tópico de parceiro. 

Este artigo mostra como integrar o como um parceiro de grade de eventos do Azure usando o portal do Azure. 

> [!NOTE]
> O registro de um tipo de tópico de parceiro é uma etapa opcional. Para ajudá-lo a decidir se você deve criar um tipo de tópico de parceiro, consulte [recursos gerenciados pelo editor de eventos](partner-events-overview.md#resources-managed-by-event-publishers).

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Comunique seu interesse em se tornar um parceiro
Preencha [este formulário](https://aka.ms/gridpartnerform) e entre em contato com a equipe da grade de eventos em [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . Teremos uma conversa com você fornecendo informações detalhadas sobre casos de uso de eventos de parceiros, personas, processo de integração, funcionalidade, preços e muito mais.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir as etapas restantes, verifique se você tem:

- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- Um [locatário](../active-directory/develop/quickstart-create-new-tenant.md)do Azure.

## <a name="register-a-partner-topic-type-optional"></a>Registrar um tipo de tópico de parceiro (opcional)
1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **todos os serviços** no painel de navegação esquerdo, digite os **registros de parceiro da grade de eventos** na barra de pesquisa e selecione-o. 
1. Na página **registros do parceiro da grade de eventos** , selecione **+ Adicionar** na barra de ferramentas. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Adicionar link de registro de parceiro":::
1. Na página **criar registros do tipo de tópico de parceiro – noções básicas** , insira as seguintes informações: 
    1. Na seção **detalhes do projeto** , siga estas etapas:
        1. Selecione sua **assinatura** do Azure. 
        1. Selecione um grupo de **recursos** do Azure existente ou crie um novo grupo de recursos. 
    1. Na seção **detalhes do registro** , siga estas etapas:
        1. Para **nome de registro**, insira um nome para o registro. 
        1. Para **nome da organização**, insira o nome da sua organização. 
    1. Na seção **tipo de recurso de parceiro** , insira detalhes sobre o tipo de recurso que será exibido no **tópico de parceiro criar** página: 
        1. Para **nome do tipo de recurso de parceiro**, insira o nome para o tipo de recurso. Esse será o tipo de tópico de parceiro que será criado em sua assinatura do Azure. 
        2. Para **nome de exibição**, insira um nome de exibição amigável para o tipo de tópico (recurso) de parceiro. 
        3. Insira uma **Descrição para o tipo de recurso**. 
        4. Insira uma **Descrição para o cenário**. Ele deve explicar as maneiras ou os cenários nos quais os tópicos de parceiros para seus recursos podem ser usados.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Criar registro de parceiro":::            
1. Selecione **Avançar: serviço personalizado** na parte inferior da página. Na guia **atendimento ao cliente** da página **criar registro do parceiro** , insira as informações que os usuários do assinante usarão para entrar em contato com você no caso de um problema com a origem do evento:
    1. Insira o **número de telefone**.
    1. Insira a **extensão** para o número de telefone.
    1. Insira uma **URL** de site de suporte. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Criar registro de parceiro-atendimento ao cliente":::        
1. Selecione **Avançar: marcações** na parte inferior da página. 
1. Na página **marcas** , configure os valores a seguir. 
    1. Insira um **nome** e um **valor** para a marca que você deseja adicionar. Esta etapa é **opcional**. 
    1. Selecione **revisar + criar** na parte inferior da página para criar o registro (tipo de tópico de parceiro).

## <a name="create-a-partner-namespace"></a>Criar um namespace de parceiro

1. Na portal do Azure, selecione **todos os serviços** no menu de navegação esquerdo, digite **namespaces de parceiro de grade de eventos** na barra de pesquisa e, em seguida, selecione-o na lista. 
1. Na página **namespaces do parceiro da grade de eventos** , selecione **+ Adicionar** na barra de ferramentas. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Namespaces do parceiro-adicionar link":::
1. Na página **criar namespace do parceiro-noções básicas** , especifique as informações a seguir.
    1. Na seção **detalhes do projeto** , execute as seguintes etapas: 
        1. Selecione uma **assinatura** do Azure.
        1. Selecione um **grupo de recursos** existente ou crie um grupo de recursos. 
    1. Na seção **detalhes do namespace** , execute as seguintes etapas:
        1. Insira um **nome** para o namespace. 
        1. Selecione uma **localização** para o namespace. 
    1. Na seção **detalhes do registro** , execute as etapas a seguir para associar o namespace a um registro de parceiro. 
        1. Selecione a **assinatura** na qual o registro de parceiro existe. 
        1. Selecione o **grupo de recursos** que contém o registro do parceiro. 
        1. Selecione o **registro do parceiro** na lista suspensa.
    1. Selecione **Avançar: marcações** na parte inferior da página.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Criar namespace do parceiro-página noções básicas":::
1. Na página **marcas** , adicione marcas (opcional).
    1. Insira um **nome** e um **valor** para a marca que você deseja adicionar. Esta etapa é **opcional**.
    1. Selecione **Revisar + criar** na parte inferior da página.         
1. Na página **revisar + criar** , examine os detalhes e selecione **criar**. 

## <a name="create-an-event-channel"></a>Criar um canal de eventos
> [!IMPORTANT]
> Você precisará solicitar ao usuário uma assinatura do Azure, um grupo de recursos, um local e um nome de tópico de parceiro para criar um tópico de parceiro que seu usuário terá e gerenciar.

1. Vá para a página **visão geral** do namespace que você criou. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Namespace do parceiro – página Visão geral":::
    partner-namespace-overview.png
1. Selecione **+ canal de evento** na barra de ferramentas. 
1. Na página **criar canal de evento-noções básicas** , especifique as informações a seguir. 
    1. Na seção **detalhes do canal** , siga estas etapas:
        1. Para **nome do canal de evento**, insira um nome para o canal de evento. 
        1. Insira a **origem**. Consulte [especificações do Cloud events 1,0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) para ter uma ideia de um valor adequado para a origem. Além disso, consulte [Este exemplo de esquema de eventos de nuvem](cloud-event-schema.md#sample-event-using-cloudevents-schema).
    1. Na seção **detalhes de destino** , insira os detalhes do tópico de parceiro de destino que será criado para esse canal de evento. 
        1. Insira a **ID da assinatura** na qual o tópico de parceiro será criado. 
        1. Insira o **nome do grupo de recursos** no qual o recurso de tópico de parceiro será criado. 
        1. Insira um **nome para o tópico de parceiro**. 
    1. Selecione **Avançar: filtros** na parte inferior da página. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Criar canal de eventos-página noções básicas":::
1. Na página **filtros** , adicione filtros. execute as seguintes etapas:
    1. Filtrar os atributos de cada evento. Somente os eventos que correspondem a todos os filtros são entregues. Até 25 filtros podem ser especificados. As comparações diferenciam maiúsculas de minúsculas. As chaves válidas usadas para filtros variam de acordo com o esquema de evento. No exemplo a seguir,,, `eventid` `source` `eventtype` e `eventtypeversioin` pode ser usado para chaves. Você também pode usar propriedades personalizadas dentro da carga de dados, usando o `.` como o operador de aninhamento. Por exemplo: `data` , `data.key` , `data.key1.key2` .
    1. Selecione **Avançar: recursos adicionais** na parte inferior da página. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Criar canal de eventos-página filtros":::
        create-event-channel-filters-page.png
1. Na página **recursos adicionais** , você pode definir uma **hora de expiração** e uma **Descrição para o tópico parceiro**. 
    1. O **tempo de expiração** é a hora em que o tópico e seu canal de evento associado serão excluídos automaticamente se não forem ativados pelo cliente. Um padrão de sete dias é usado no caso de uma hora não ser fornecida. Marque a caixa de seleção para especificar seu próprio tempo de expiração. 
    1. Como este tópico é um recurso que não é criado pelo usuário, uma **Descrição** pode ajudar o usuário a entender a natureza deste tópico. Uma descrição genérica será fornecida se nenhuma for definida. Marque a caixa de seleção para definir sua própria descrição do tópico de parceiro. 
    1. Selecione **Avançar: Revisar + criar**. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Criar canal de eventos-página de recursos adicionais":::
1. Na **revisão + criar**, examine as configurações e selecione **criar** para criar o canal de evento. 

## <a name="next-steps"></a>Próximas etapas
- [Visão geral dos tópicos de parceiros](./partner-events-overview.md)
- [Formulário de integração de tópicos de parceiros](https://aka.ms/gridpartnerform)
- [Tópico de parceiro do Auth0](auth0-overview.md)
- [Como usar o tópico de parceiro do Auth0](auth0-how-to.md)
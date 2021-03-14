---
title: Criar fluxos de trabalho de visualização de aplicativos lógicos no portal do Azure
description: Crie e execute fluxos de trabalho para cenários de automação e integração com a visualização dos aplicativos lógicos do Azure no portal do Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563924"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Criar fluxos de trabalho com e sem estado no portal do Azure com o aplicativo lógico do Azure versão prévia

> [!IMPORTANT]
> Essa versão preliminar está na versão prévia pública, e é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho em produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com a [visualização dos aplicativos lógicos do Azure](logic-apps-overview-preview.md), você pode criar soluções de automação e integração entre aplicativos, dados, serviços de nuvem e sistemas criando e executando aplicativos lógicos que incluem [fluxos de trabalho com e *sem*](logic-apps-overview-preview.md#stateful-stateless) estado no portal do Azure iniciando com o novo tipo de recurso de **aplicativo lógico (versão prévia)** . Com esse novo tipo de aplicativo lógico, você pode criar vários fluxos de trabalho que são alimentados pelo tempo de execução da visualização dos aplicativos lógicos do Azure reprojetados, que fornece portabilidade, melhor desempenho e flexibilidade para implantação e execução em vários ambientes de hospedagem, não apenas no Azure, mas também em contêineres do Docker. Para saber mais sobre o novo tipo de aplicativo lógico, consulte [visão geral para visualização de aplicativos lógicos do Azure](logic-apps-overview-preview.md).

![Captura de tela que mostra a portal do Azure com o designer de fluxo de trabalho para o recurso "aplicativo lógico (visualização)".](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

No portal do Azure, você pode começar criando um novo recurso de **aplicativo lógico (versão prévia)** . Embora você também possa começar [criando um projeto no Visual Studio Code com a extensão de aplicativos lógicos do Azure (versão prévia)](create-stateful-stateless-workflows-visual-studio-code.md), ambas as abordagens fornecem a capacidade de implantar e executar seu aplicativo lógico nos mesmos tipos de ambientes de hospedagem.

Enquanto isso, você ainda pode criar o tipo de aplicativo lógico original. Embora as experiências de desenvolvimento no portal sejam diferentes entre os tipos de aplicativo lógico original e novo, sua assinatura do Azure pode incluir ambos os tipos. Você pode exibir e acessar todos os aplicativos lógicos implantados em sua assinatura do Azure, mas os aplicativos são organizados em suas próprias categorias e seções.

Este artigo mostra como criar seu aplicativo lógico e fluxo de trabalho no portal do Azure usando o tipo de recurso **aplicativo lógico (versão prévia)** e executando estas tarefas de alto nível:

* Crie o novo recurso de aplicativo lógico e adicione um fluxo de trabalho em branco.

* Adicionar um gatilho e uma ação.

* Disparar uma execução de fluxo de trabalho.

* Exibir o histórico de execução e gatilho do fluxo de trabalho.

* Habilite ou abra o Application Insights após a implantação.

* Habilite o histórico de execuções para fluxos de trabalho sem estado.

> [!NOTE]
> Para obter informações sobre os problemas atuais conhecidos, examine a [página problemas conhecidos da visualização pública de aplicativos lógicos no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma [conta de armazenamento do Azure](../storage/common/storage-account-overview.md) porque o recurso de **aplicativo lógico (versão prévia)** é alimentado por Azure Functions e tem [requisitos de armazenamento semelhantes aos aplicativos de funções](../azure-functions/storage-considerations.md). Você pode usar uma conta de armazenamento existente ou pode criar uma conta de armazenamento com antecedência ou durante a criação do aplicativo lógico.

  > [!NOTE]
  > Os [aplicativos lógicos com estado](logic-apps-overview-preview.md#stateful-stateless) executam transações de armazenamento, como o uso de filas para agendamento e armazenamento de Estados de fluxo de trabalho em tabelas e blobs. Essas transações incorrem em [encargos de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais informações sobre como os aplicativos lógicos com estado armazenam dados no armazenamento externo, consulte com [monitoração de Estado versus sem estado](logic-apps-overview-preview.md#stateful-stateless).

* Para implantar em um contêiner do Docker, você precisa de uma imagem de contêiner do Docker existente. Por exemplo, você pode criar essa imagem por meio [do registro de contêiner do Azure, do](../container-registry/container-registry-intro.md)serviço de [aplicativo](../app-service/overview.md)ou da [instância de contêiner do Azure](../container-instances/container-instances-overview.md). 

* Para criar o mesmo exemplo de aplicativo lógico neste artigo, você precisa de uma conta de email do Office 365 Outlook que usa uma conta corporativa ou de estudante da Microsoft para entrar.

  Se você optar por usar um [conector de email diferente com suporte dos aplicativos lógicos do Azure](/connectors/), como Outlook.com ou [gmail](../connectors/connectors-google-data-security-privacy-policy.md), ainda poderá seguir o exemplo, e as etapas gerais geral são as mesmas, mas a interface do usuário e as opções podem ser diferentes de algumas maneiras. Por exemplo, se você usar o conector Outlook.com, use seu conta Microsoft pessoal em vez de entrar.

* Para testar o aplicativo lógico de exemplo que você cria neste artigo, você precisa de uma ferramenta que possa enviar chamadas para o gatilho de solicitação, que é a primeira etapa no aplicativo lógico de exemplo. Se você não tiver essa ferramenta, poderá baixar, instalar e usar o [postmaster](https://www.postman.com/downloads/).

* Se você criar seu aplicativo lógico com configurações que dão suporte ao uso de [Application insights](../azure-monitor/app/app-insights-overview.md), você pode opcionalmente habilitar o log e o rastreamento de diagnóstico para seu aplicativo lógico. Você pode fazer isso ao criar seu aplicativo lógico ou após a implantação. Você precisa ter uma instância de Application Insights, mas você pode criar esse recurso com [antecedência](../azure-monitor/app/create-workspace-resource.md), ao criar seu aplicativo lógico ou após a implantação.

## <a name="create-the-logic-app-resource"></a>Criar o recurso de aplicativo lógico

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa de pesquisa portal do Azure, insira `logic app preview` e selecione **aplicativo lógico (versão prévia)**.

   ![Captura de tela que mostra a caixa de pesquisa portal do Azure com o termo de pesquisa "visualização do aplicativo lógico" e o recurso "aplicativo lógico (visualização)" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. Na página **aplicativo lógico (versão prévia)** , selecione **Adicionar**.

1. Na página **criar aplicativo lógico (versão prévia)** , na guia **noções básicas** , forneça essas informações sobre seu aplicativo lógico.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para seu aplicativo lógico. |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | O grupo de recursos do Azure em que você cria seu aplicativo lógico e os recursos relacionados. Esse nome de recurso deve ser exclusivo entre regiões e pode conter apenas letras, números, hifens ( **-** ), sublinhados (**_**), parênteses (**()**) e pontos (**.**). <p><p>Este exemplo cria um grupo de recursos denominado `Fabrikam-Workflows-RG` . |
   | **Nome do aplicativo lógico** | Sim | <*logic-app-name*> | O nome a ser usado para o aplicativo lógico. Esse nome de recurso deve ser exclusivo entre regiões e pode conter apenas letras, números, hifens ( **-** ), sublinhados (**_**), parênteses (**()**) e pontos (**.**). <p><p>Este exemplo cria um aplicativo lógico chamado `Fabrikam-Workflows` . <p><p>**Observação**: o nome do aplicativo lógico obtém automaticamente o sufixo, `.azurewebsites.net` , porque o recurso **aplicativo lógico (versão prévia)** é alimentado por Azure functions, que usa a mesma convenção de nomenclatura do aplicativo. |
   | **Publicar** | Yes | <*implantação-ambiente*> | O destino de implantação para seu aplicativo lógico. Você pode implantar no Azure selecionando **fluxo de trabalho** ou **contêiner do Docker**. <p><p>Este exemplo usa o **fluxo de trabalho**, que implanta o recurso de **aplicativo lógico (versão prévia)** no portal do Azure. <p><p>**Observação**: antes de selecionar o **contêiner do Docker**, certifique-se de criar sua imagem de contêiner do Docker. Por exemplo, você pode criar essa imagem por meio [do registro de contêiner do Azure, do](../container-registry/container-registry-intro.md)serviço de [aplicativo](../app-service/overview.md)ou da [instância de contêiner do Azure](../container-instances/container-instances-overview.md). Dessa forma, depois de selecionar o **contêiner do Docker**, você poderá [especificar o contêiner que deseja usar nas configurações do aplicativo lógico](#set-docker-container). |
   | **Região** | Sim | <*Azure-region*> | A região do Azure a ser usada ao criar o grupo de recursos e os recursos. <p><p>Este exemplo usa **Oeste dos EUA**. |
   |||||

   Aqui está um exemplo:

   ![Captura de tela que mostra a portal do Azure e a página "criar aplicativo lógico (visualização)".](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Em seguida, na guia **hospedagem** , forneça essas informações sobre a solução de armazenamento e o plano de hospedagem a serem usados para seu aplicativo lógico.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Conta de armazenamento** | Sim | <*Azure-storage-account-name*> | A [conta de armazenamento do Azure](../storage/common/storage-account-overview.md) a ser usada para transações de armazenamento. Esse nome de recurso deve ser exclusivo entre regiões e ter de 3-24 caracteres com apenas números e letras minúsculas. Selecione uma conta existente ou crie uma nova conta. <p><p>Este exemplo cria uma conta de armazenamento denominada `fabrikamstorageacct` . |
   | **Tipo de plano** | Yes | <*Azure – plano de hospedagem*> | O [plano de hospedagem](../app-service/overview-hosting-plans.md) a ser usado para implantar seu aplicativo lógico, que é o [**Functions Premium**](../azure-functions/functions-premium-plan.md) ou o [ **plano do serviço de aplicativo** (dedicado)](../azure-functions/dedicated-plan.md). Sua escolha afeta os recursos e as camadas de preço mais tarde disponíveis para você. <p><p>Este exemplo usa o **plano do serviço de aplicativo**. <p><p>**Observação**: semelhante a Azure functions, o tipo de recurso de **aplicativo lógico (versão prévia)** requer um plano de hospedagem e uma camada de preços. Os planos de consumo não têm suporte nem estão disponíveis para este tipo de recurso. Para obter mais informações, consulte estes tópicos: <p><p>- [Escala e Hospedagem de Azure Functions](../azure-functions/functions-scale.md) <br>- [Detalhes de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/) <p><p>Por exemplo, o plano Premium do Functions fornece acesso a recursos de rede, como conectar e integrar de forma privada com redes virtuais do Azure, semelhante a Azure Functions quando você cria e implanta seus aplicativos lógicos. Para obter mais informações, consulte estes tópicos: <p><p>- [Opções de rede Azure Functions](../azure-functions/functions-networking-options.md) <br>- [Aplicativos lógicos do Azure executando possibilidades de rede em qualquer lugar com os aplicativos lógicos do Azure Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Plano do Windows** | Yes | <*nome do plano*> | O nome do plano a ser usado. Selecione um plano existente ou forneça o nome para um novo plano. <p><p>Este exemplo usa o nome `Fabrikam-Service-Plan`. |
   | **SKU e tamanho** | Yes | <*preço-camada*> | O [tipo de preço](../app-service/overview-hosting-plans.md) a ser usado para hospedar seu aplicativo lógico. Suas opções são afetadas pelo tipo de plano que você escolheu anteriormente. Para alterar a camada padrão, selecione **alterar tamanho**. Em seguida, você pode selecionar outros tipos de preço, com base na carga de trabalho necessária. <p><p>Este exemplo usa o **tipo de preço F1** grátis para cargas de trabalho de **desenvolvimento/teste** . Para obter mais informações, examine os [detalhes de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/). |
   |||||

1. Em seguida, se as configurações de criação e implantação dão suporte ao uso de [Application insights](../azure-monitor/app/app-insights-overview.md), você pode opcionalmente habilitar o log e o rastreamento de diagnóstico para seu aplicativo lógico.

   1. Na guia **monitoramento** , em **Application insights**, defina **habilitar Application insights** como **Sim** , se ainda não estiver selecionado.

   1. Para a configuração de **Application insights** , selecione uma instância de Application insights existente ou, se desejar criar uma nova instância, selecione **criar nova** e forneça o nome que você deseja usar.

1. Depois que o Azure valida as configurações do aplicativo lógico, na guia **revisar + criar** , selecione **criar**.

   Por exemplo:

   ![Captura de tela que mostra a portal do Azure e novas configurações de recurso de aplicativo lógico.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Se você receber um erro de validação depois de selecionar **criar**, abra e examine os detalhes do erro. Por exemplo, se a região selecionada atingir uma cota de recursos que você está tentando criar, talvez seja necessário tentar uma região diferente.

   Depois que o Azure concluir a implantação, seu aplicativo lógico estará automaticamente ativo e em execução, mas não fará nada ainda porque não existe nenhum fluxo de trabalho.

1. Na página conclusão da implantação, selecione **ir para o recurso** para que você possa começar a criar seu fluxo de trabalho. Se você selecionou o **contêiner do Docker** para implantar seu aplicativo lógico, continue com as [etapas para fornecer informações sobre esse contêiner do Docker](#set-docker-container).

   ![Captura de tela que mostra a portal do Azure e a implantação concluída.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Especificar o contêiner do Docker para implantação

Antes de iniciar essas etapas, você precisará de uma imagem de contêiner do Docker. Por exemplo, você pode criar essa imagem por meio [do registro de contêiner do Azure, do](../container-registry/container-registry-intro.md)serviço de [aplicativo](../app-service/overview.md)ou da [instância de contêiner do Azure](../container-instances/container-instances-overview.md). Em seguida, você pode fornecer informações sobre o contêiner do Docker depois de criar seu aplicativo lógico.

1. Na portal do Azure, vá para o recurso de aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **centro de implantação**.

1. No painel **central de implantação** , siga as instruções para fornecer e gerenciar os detalhes do contêiner do Docker.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Adicionar um fluxo de trabalho em branco

1. Depois que o Azure abrir o recurso, no menu do seu aplicativo lógico, selecione **fluxos de trabalho**. Na barra de ferramentas **fluxos de trabalho** , selecione **Adicionar**.

   ![Captura de tela que mostra o menu de recursos do aplicativo lógico com "fluxos de trabalho" selecionado e, em seguida, na barra de ferramentas, "Adicionar" está selecionado.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Depois que o novo painel de **fluxo de trabalho** for aberto, forneça um nome para o fluxo de trabalho e escolha o tipo de fluxo de trabalho com [ **estado** ou **sem estado**](logic-apps-overview-preview.md#stateful-stateless) . Quando terminar, selecione **Criar**.

   Este exemplo adiciona um fluxo de trabalho com estado em branco chamado `Fabrikam-Stateful-Workflow` . Por padrão, o fluxo de trabalho é habilitado, mas não faz nada até que você adicione um gatilho e ações.

   ![Captura de tela que mostra o fluxo de trabalho com estado em branco recém-adicionado "fabrikam-stateful-Workflow".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Em seguida, abra o fluxo de trabalho em branco no designer para que você possa adicionar um gatilho e uma ação.

   1. Na lista fluxo de trabalho, selecione o fluxo de trabalho em branco.

   1. No menu fluxo de trabalho, em **desenvolvedor**, selecione **Designer**.

      Na superfície do designer, o prompt **escolher uma operação** já aparece e é selecionado por padrão para que o painel **Adicionar um gatilho** também apareça aberto.

      ![Captura de tela que mostra o designer de fluxo de trabalho aberto com "escolher uma operação" selecionada na superfície do designer.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Adicionar um gatilho e uma ação

Este exemplo cria um fluxo de trabalho que tem estas etapas:

* O [gatilho de solicitação](../connectors/connectors-native-reqres.md)interno, **quando uma solicitação HTTP é recebida**, que recebe chamadas de entrada ou solicitações e cria um ponto de extremidade que outros serviços ou aplicativos lógicos podem chamar.

* A [ação do Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), **enviar um email**.

* A [ação de resposta](../connectors/connectors-native-reqres.md)interna, que você usa para enviar uma resposta e retornar dados de volta para o chamador.

### <a name="add-the-request-trigger"></a>Adicionar o gatilho de Solicitação

Antes de adicionar um gatilho a um fluxo de trabalho em branco, verifique se o designer de fluxo de trabalho está aberto e se o prompt **escolher um operação** está selecionado na superfície do designer.

1. Ao lado da superfície do designer, no painel **Adicionar um gatilho** , na caixa de pesquisa **escolher uma operação** , verifique se a guia **interno** está selecionada. Essa guia mostra os gatilhos que são executados nativamente nos aplicativos lógicos do Azure.

1. Na caixa de pesquisa **escolher uma operação** , insira `when a http request` e selecione o gatilho de solicitação interno que é nomeado **quando uma solicitação HTTP é recebida**.

   ![Captura de tela que mostra o designer e * * adicionar um painel do gatilho * * com o gatilho "quando um pedido HTTP é recebido" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Quando o gatilho aparece no designer, o painel de detalhes do gatilho é aberto para mostrar as propriedades, as configurações e outras ações do gatilho.

   ![Captura de tela que mostra o designer com o gatilho "quando uma solicitação HTTP é recebida" está selecionado e o painel detalhes do gatilho é aberto.](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se o painel de detalhes não for exibido, verifique se o gatilho está selecionado no designer.

1. Se você precisar excluir um item do designer, [siga estas etapas para excluir itens do designer](#delete-from-designer).

1. Para salvar seu trabalho, na barra de ferramentas do designer, selecione **salvar**.

   Quando você salva um fluxo de trabalho pela primeira vez e esse fluxo de trabalho começa com um gatilho de solicitação, o serviço de aplicativos lógicos gera automaticamente uma URL para um ponto de extremidade que é criado pelo gatilho de solicitação. Posteriormente, ao testar o fluxo de trabalho, você envia uma solicitação para essa URL, que dispara o gatilho e inicia a execução do fluxo de trabalho.

### <a name="add-the-office-365-outlook-action"></a>Adicionar a ação do Outlook do Office 365

1. No designer, no gatilho que você adicionou, selecione **nova etapa**.

   O prompt **escolher uma operação** aparece no designer e o painel **Adicionar uma ação** é reaberto para que você possa selecionar a próxima ação.

   > [!NOTE]
   > Se o painel **Adicionar uma ação** mostrar a mensagem de erro ' não é possível ler a propriedade ' filtro ' de indefinido ', salve o fluxo de trabalho, recarregue a página, reabra o fluxo de trabalho e tente novamente.

1. No painel **Adicionar uma ação** , na caixa de pesquisa **escolher uma operação** , selecione **Azure**. Essa guia mostra os conectores gerenciados que estão disponíveis e implantados no Azure.

   > [!NOTE]
   > Se o painel **Adicionar uma ação** mostrar a mensagem de erro, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` salve o fluxo de trabalho, recarregue a página, reabra o fluxo de trabalho e tente adicionar a ação novamente.

   Este exemplo usa a ação do Outlook do Office 365 chamada **enviar um email (v2)**.

   ![Captura de tela que mostra o designer e o painel * * Adicionar uma ação * * com a ação "enviar um email" do Office 365 Outlook selecionada.](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. No painel de detalhes da ação, na guia **criar conexão** , selecione **entrar** para que você possa criar uma conexão com sua conta de email.

   ![Captura de tela que mostra o designer e o painel de detalhes "enviar um email (v2)" com "entrar" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. Quando for solicitado que você tenha autorização para acessar sua conta de email, entre com suas credenciais de conta.

   > [!NOTE]
   > Se você receber a mensagem de erro, `Failed with error: 'The browser is closed.'. Please sign in again` , verifique se o navegador bloqueia cookies de terceiros. Se esses cookies estiverem bloqueados, tente adicionar `https://portal.azure.com` à lista de sites que podem usar cookies. Se você estiver usando o modo Incognito, verifique se os cookies de terceiros não estão bloqueados enquanto trabalham nesse modo.
   > 
   > Se necessário, recarregue a página, abra o fluxo de trabalho, adicione a ação de email novamente e tente criar a conexão.

   Depois que o Azure cria a conexão, a ação **enviar um email** aparece no designer e é selecionada por padrão. Se a ação não estiver selecionada, selecione a ação para que seu painel de detalhes também seja aberto.

1. No painel de detalhes da ação, na guia **parâmetros** , forneça as informações necessárias para a ação, por exemplo:

   ![Captura de tela que mostra o designer e o painel de detalhes "enviar um email" com a guia "parâmetros" selecionada.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Sim | <*your-email-address*> | O destinatário do email, que pode ser seu endereço de email para fins de teste. Este exemplo usa o email fictício, `sophiaowen@fabrikam.com` . |
   | **Assunto** | Sim | `An email from your example workflow` | O assunto do email |
   | **Corpo** | Sim | `Hello from your example workflow!` | O conteúdo do corpo do email |
   ||||

   > [!NOTE]
   > Ao fazer alterações no painel de detalhes nas guias **configurações**, **resultado estático** ou **executar após** , certifique-se de selecionar **concluído** para confirmar essas alterações antes de alternar as guias ou alterar o foco para o designer. Caso contrário, o designer não manterá suas alterações.

1. Salve seu trabalho. Selecione **Salvar** na barra de ferramentas do designer.

1. Se o ambiente tiver requisitos estritos de rede ou firewalls que limitam o tráfego, você precisará configurar permissões para qualquer conexão de gatilho ou ação que exista em seu fluxo de trabalho. Para encontrar o totalmente qualificado 

   Caso contrário, para testar o fluxo de trabalho, [acione manualmente uma execução](#trigger-workflow).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Localizar nomes de domínio para acesso ao firewall

Antes de implantar seu aplicativo lógico e executar o fluxo de trabalho no portal do Azure, se o ambiente tiver requisitos de rede estritos ou firewalls que limitem o tráfego, você precisará configurar as permissões de rede ou firewall para qualquer conexão de gatilho ou ação nos fluxos de trabalho que existem em seu aplicativo lógico.

Para localizar os FQDNs (nomes de domínio totalmente qualificados) para essas conexões, siga estas etapas:

1. No menu do aplicativo lógico, em **fluxos de trabalho**, selecione **conexões**. Na guia **conexões de API** , selecione o nome do recurso da conexão, por exemplo:

   ![Captura de tela que mostra o portal do Azure e o menu do aplicativo lógico com o nome do recurso de conexão "conexões" e "offic365" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. Expanda o navegador para todo o suficiente para que, quando a **exibição JSON** aparecer no canto superior direito do navegador, selecione **modo de exibição JSON**.

   ![Captura de tela que mostra a portal do Azure e o painel de conexão de API com a "exibição JSON" selecionada.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. Localize, copie e salve o `connectionRuntimeUrl` valor da propriedade em um local seguro para que você possa configurar o firewall com essas informações.

   ![Captura de tela que mostra o valor da propriedade "connectionRuntimeUrl" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Para cada conexão, repita as etapas relevantes.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>Disparar um fluxo de trabalho

Neste exemplo, o fluxo de trabalho é executado quando o gatilho de solicitação recebe uma solicitação de entrada, que é enviada para a URL do ponto de extremidade criado pelo gatilho. Quando você salvou o fluxo de trabalho pela primeira vez, o serviço de aplicativos lógicos gerou automaticamente essa URL. Portanto, antes de poder enviar essa solicitação para disparar o fluxo de trabalho, você precisa encontrar essa URL.

1. No designer de fluxo de trabalho, selecione o gatilho de solicitação que é nomeado **quando uma solicitação HTTP é recebida**.

1. Depois que o painel de detalhes for aberto, na guia **parâmetros** , localize a propriedade **URL http post** . Para copiar a URL gerada, selecione a **URL de cópia** (ícone Copiar arquivo) e salve a URL em outro lugar por enquanto. A URL segue este formato:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Captura de tela que mostra o designer com o gatilho de solicitação e a URL do ponto de extremidade na propriedade "URL HTTP POST".](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   Para este exemplo, a URL tem esta aparência:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Você também pode encontrar a URL do ponto de extremidade no painel de **visão geral** do aplicativo lógico na propriedade **URL do fluxo de trabalho** .
   >
   > 1. No menu de recursos, selecione **visão geral**.
   > 1. No painel **visão geral** , localize a propriedade **URL do fluxo de trabalho** .
   > 1. Para copiar a URL do ponto de extremidade, mova o ponteiro do mouse sobre o final do texto da URL do ponto de extremidade e selecione **copiar para a área de transferência** (ícone Copiar arquivo).

1. Para testar a URL enviando uma solicitação, abra o [postmaster](https://www.postman.com/downloads/) ou sua ferramenta preferida para criar e enviar solicitações.

   Este exemplo continua usando o postmaster. Para obter mais informações, consulte [introdução do postmaster](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na barra de ferramentas do postmaster, selecione **novo**.

      ![Captura de tela que mostra o postmaster com o botão novo selecionado](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. No painel **criar novo** , em **blocos de construção**, selecione **solicitação**.

   1. Na janela **salvar solicitação** , em **nome da solicitação**, forneça um nome para a solicitação, por exemplo, `Test workflow trigger` .

   1. Em **selecionar uma coleção ou pasta para salvar**, selecione **criar coleção**.

   1. Em **todas as coleções**, forneça um nome para a coleção a ser criada para organizar suas solicitações, pressione Enter e selecione **salvar para <*nome* > da coleção**. Este exemplo usa `Logic Apps requests` como o nome da coleção.

      O painel de solicitação do postmaster é aberto para que você possa enviar uma solicitação à URL do ponto de extremidade para o gatilho de solicitação.

      ![Captura de tela que mostra o postmaster com o painel de solicitação aberto](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. No painel de solicitação, na caixa de endereço que está ao lado da lista de métodos, que atualmente mostra **Get** como o método de solicitação padrão, Cole a URL que você copiou anteriormente e selecione **Enviar**.

      ![Captura de tela que mostra o postmaster e a URL do ponto de extremidade na caixa endereço com o botão enviar selecionado](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Quando o gatilho é acionado, o fluxo de trabalho de exemplo é executado e envia um email que é semelhante a este exemplo:

      ![Captura de tela que mostra o email do Outlook, conforme descrito no exemplo](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Examinar o histórico de execuções

Para um fluxo de trabalho com estado, após cada execução de fluxo de trabalho, você pode exibir o histórico de execução, incluindo o status da execução geral, para o gatilho e para cada ação junto com suas entradas e saídas. Na portal do Azure, histórico de execução e históricos de gatilho aparecem no nível do fluxo de trabalho, não no nível do aplicativo lógico. Para examinar os históricos de gatilho fora do contexto de histórico de execução, consulte [examinar históricos de gatilho](#view-trigger-histories).

1. No portal do Azure, no menu do fluxo de trabalho, selecione **Monitor**.

   O painel **Monitor** mostra o histórico de execuções para esse fluxo de trabalho.

   ![Captura de tela que mostra o painel "Monitor" do fluxo de trabalho e o histórico de execução.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Se o status de execução mais recente não aparecer, na barra de ferramentas do painel **Monitor** , selecione **Atualizar**. Não acontece nenhuma execução para um gatilho que é ignorado devido a critérios não cumpridos ou não encontrar dados.

   | Status da execução | Descrição |
   |------------|-------------|
   | **Anulado** | A execução foi interrompida ou não foi concluída devido a problemas externos, por exemplo, uma interrupção do sistema ou uma assinatura do Azure enlapsada. |
   | **Cancelar** | A execução foi disparada e iniciada, mas recebeu uma solicitação de cancelamento. |
   | **Falha** | Falha em pelo menos uma ação na execução. Nenhuma ação subsequente no fluxo de trabalho foi configurada para lidar com a falha. |
   | **Executando** | A execução foi disparada e está em andamento, mas esse status também pode aparecer para uma execução que é limitada devido a [limites de ação](logic-apps-limits-and-config.md) ou ao plano de [preços atual](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Dica**: se você configurar o [log de diagnóstico](monitor-logic-apps-log-analytics.md), poderá obter informações sobre os eventos de restrição que acontecerem. |
   | **Êxito** | A execução foi bem-sucedida. Se alguma ação falhar, uma ação subsequente no fluxo de trabalho tratou dessa falha. |
   | **Tempo limite atingido** | A execução atingiu o tempo limite porque a duração atual excedeu o limite de duração da execução, que é controlado pela [configuração **retenção do histórico de execuções em dias**](logic-apps-limits-and-config.md#run-duration-retention-limits). A duração de uma execução é calculada usando a hora de início e o limite de duração da execução na hora de início. <p><p>**Observação**: se a duração da execução também exceder o *limite de retenção do histórico de execução* atual, que também é controlado pela [configuração retenção do **histórico de execução em dias**](logic-apps-limits-and-config.md#run-duration-retention-limits), a execução será desmarcada do histórico de execuções por um trabalho de limpeza diário. Se a execução atingir o tempo limite ou for concluída, o período de retenção sempre será calculado usando a hora de início e o limite de retenção *atual* da execução. Portanto, se você reduzir o limite de duração de uma execução em andamento, a execução atingirá o tempo limite. No entanto, a execução permanece ou é desmarcada do histórico de execuções com base em se a duração da execução excedeu o limite de retenção. |
   | **Aguardando** | A execução não foi iniciada ou está em pausa, por exemplo, devido a uma instância de fluxo de trabalho anterior que ainda está em execução. |
   |||

1. Para examinar o status de cada etapa em uma execução, selecione a execução que você deseja examinar.

   A exibição detalhes da execução é aberta e mostra o status de cada etapa na execução.

   ![Captura de tela que mostra a exibição de detalhes de execução com o status de cada etapa no fluxo de trabalho.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   Estes são os possíveis status que cada etapa do fluxo de trabalho pode ter:

   | Status da ação | ícone | Descrição |
   |---------------|------|-------------|
   | **Anulado** | ![Ícone do status de ação "abortado"][aborted-icon] | A ação foi interrompida ou não foi concluída devido a problemas externos, por exemplo, uma interrupção do sistema ou uma assinatura do Azure enlapsada. |
   | **Cancelar** | ![Ícone do status de ação "cancelado"][cancelled-icon] | A ação estava em execução, mas recebeu uma solicitação de cancelamento. |
   | **Falha** | ![Ícone do status da ação "falha"][failed-icon] | Falha na ação. |
   | **Executando** | ![Ícone para o status de ação "em execução"][running-icon] | A ação está em execução no momento. |
   | **Ignorado** | ![Ícone do status de ação "ignorado"][skipped-icon] | A ação foi ignorada porque a ação imediatamente anterior falhou. Uma ação tem uma `runAfter` condição que requer que a ação anterior seja concluída com êxito antes que a ação atual possa ser executada. |
   | **Êxito** | ![Ícone do status de ação "êxito"][succeeded-icon] | A ação foi bem-sucedida. |
   | **Êxito com novas tentativas** | ![Ícone para o status de ação "êxito com novas tentativas"][succeeded-with-retries-icon] | A ação foi bem-sucedida, mas somente após uma ou mais tentativas. Para examinar o histórico de repetição, na exibição detalhes do histórico de execuções, selecione essa ação para que você possa exibir as entradas e saídas. |
   | **Tempo limite atingido** | ![Ícone do status de ação "tempo limite"][timed-out-icon] | A ação foi interrompida devido ao limite de tempo limite especificado pelas configurações dessa ação. |
   | **Aguardando** | ![Ícone do status de ação "aguardando"][waiting-icon] | Aplica-se a uma ação de webhook que está aguardando uma solicitação de entrada de um chamador. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Para examinar as entradas e saídas de uma etapa específica, selecione essa etapa.

   ![Captura de tela que mostra as entradas e saídas na ação "enviar um email" selecionada.](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Para examinar melhor as entradas e saídas brutas para essa etapa, selecione **Mostrar entradas brutas** ou **Mostrar saídas brutas**.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Examinar históricos de gatilho

Para um fluxo de trabalho com estado, você pode examinar o histórico de gatilho para cada execução, incluindo o status do gatilho junto com entradas e saídas, separadamente do [contexto do histórico de execução](#view-run-history). Na portal do Azure, o histórico de gatilho e o histórico de execução aparecem no nível do fluxo de trabalho, não no nível do aplicativo lógico. Para encontrar esses dados históricos, siga estas etapas:

1. No portal do Azure, no menu do fluxo de trabalho, em **desenvolvedor**, selecione **históricos de gatilho**.

   O painel **históricos de gatilho** mostra os históricos de gatilho para as execuções do fluxo de trabalho.

1. Para examinar um histórico de gatilho específico, selecione a ID para essa execução.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Habilitar ou abrir Application Insights após a implantação

Durante a execução do fluxo de trabalho, seu aplicativo lógico emite telemetria junto com outros eventos. Você pode usar essa telemetria para obter melhor visibilidade sobre o quão bem o fluxo de trabalho é executado e como o tempo de execução dos aplicativos lógicos funciona de várias maneiras. Você pode monitorar seu fluxo de trabalho usando [Application insights](../azure-monitor/app/app-insights-overview.md), que fornece telemetria quase em tempo real (métricas ao vivo). Esse recurso pode ajudá-lo a investigar falhas e problemas de desempenho mais facilmente ao usar esses dados para diagnosticar problemas, configurar alertas e criar gráficos.

Se a criação e as configurações de implantação do aplicativo lógico dão suporte ao uso de [Application insights](../azure-monitor/app/app-insights-overview.md), você pode opcionalmente habilitar o log e o rastreamento de diagnóstico para seu aplicativo lógico. Você pode fazer isso ao criar seu aplicativo lógico no portal do Azure ou após a implantação. Você precisa ter uma instância de Application Insights, mas você pode criar esse recurso com [antecedência](../azure-monitor/app/create-workspace-resource.md), ao criar seu aplicativo lógico ou após a implantação.

Para habilitar Application Insights em um aplicativo lógico implantado ou abrir o painel de Application Insights, se já estiver habilitado, siga estas etapas:

1. Na portal do Azure, localize seu aplicativo lógico implantado.

1. No menu do aplicativo lógico, em **configurações**, selecione **Application insights**.

1. Se Application Insights não estiver habilitado, no painel **Application insights** , selecione **Ativar Application insights**. Depois que o painel for atualizado, na parte inferior, selecione **aplicar**.

   Se Application Insights estiver habilitado, no painel **Application insights** , selecione **exibir dados de Application insights**.

Depois que Application Insights for aberto, você poderá examinar várias métricas para seu aplicativo lógico. Para obter mais informações, consulte estes tópicos:

* [Aplicativos lógicos do Azure em execução em qualquer lugar-monitor com Application Insights-parte 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Aplicativos lógicos do Azure em execução em qualquer lugar-monitor com Application Insights-parte 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Habilitar o histórico de execuções para fluxos de trabalho sem estado

Para depurar um fluxo de trabalho sem estado com mais facilidade, você pode habilitar o histórico de execução para esse fluxo de trabalho e, em seguida, desabilitar o histórico de execução quando terminar. Siga estas etapas para a portal do Azure ou, se estiver trabalhando no Visual Studio Code, confira [criar fluxos de trabalho com e sem estado no Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

1. Na [portal do Azure](https://portal.azure.com), localize e abra seu recurso de **aplicativo lógico (versão prévia)** .

1. No menu do aplicativo lógico, em **configurações**, selecione **configuração**.

1. Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.

1. No painel **Adicionar/Editar configuração de aplicativo** , na caixa **nome** , digite este nome de opção de operação: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Na caixa **valor** , insira o seguinte valor: `WithStatelessRunHistory`

   Por exemplo:

   ![Captura de tela que mostra o portal do Azure e o recurso de aplicativo lógico (versão prévia) com a "configuração" > "nova configuração de aplicativo" < painel "Adicionar/Editar configuração de aplicativo" aberto e os "fluxos de trabalho. {yourWorkflowName}. Operationoptions ", opção definida como" WithStatelessRunHistory ".](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Para concluir essa tarefa, selecione **OK**. Na barra de ferramentas do painel de **configuração** , selecione **salvar**.

1. Para desabilitar o histórico de execuções quando terminar, defina a `Workflows.{yourWorkflowName}.OperationOptions` propriedade como `None` ou exclua a propriedade e seu valor.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Excluir itens do designer

Para excluir um item em seu fluxo de trabalho do designer, siga qualquer uma destas etapas:

* Selecione o item, abra o menu de atalho do item (Shift + F10) e selecione **excluir**. Para confirmar, selecione **OK**.

* Selecione o item e pressione a tecla Delete. Para confirmar, selecione **OK**.

* Selecione o item para que o painel de detalhes seja aberto para esse item. No canto superior direito do painel, abra o menu de reticências (**...**) e selecione **excluir**. Para confirmar, selecione **OK**.

  ![Captura de tela que mostra um item selecionado no designer com o painel de detalhes aberto mais o botão de reticências selecionadas e o comando "excluir".](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Se o menu de reticências não estiver visível, expanda a janela do navegador para todo o suficiente para que o painel detalhes mostre o botão de reticências (**...**) no canto superior direito.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Solucionar problemas e erros

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Novos gatilhos e ações estão ausentes no seletor de designer para fluxos de trabalho criados anteriormente

A versão prévia dos aplicativos lógicos do Azure dá suporte a ações internas para operações de Azure Function, operações de Liquid e operações XML, como **validação de XML** e **XML de transformação**. No entanto, para aplicativos lógicos criados anteriormente, essas ações podem não aparecer no designer para que você selecione se seu aplicativo lógico usa uma versão desatualizada do pacote de extensão, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Para corrigir esse problema, siga estas etapas para excluir a versão desatualizada para que o pacote de extensão possa ser atualizado automaticamente para a versão mais recente.

> [!NOTE]
> Essa solução específica se aplica somente aos recursos de **aplicativo lógico (versão prévia)** que você cria usando o portal do Azure, não os aplicativos lógicos que você cria e implanta usando Visual Studio Code e a extensão de aplicativos lógicos do Azure (versão prévia). Consulte [gatilhos e ações com suporte estão faltando no designer no Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. No portal do Azure, pare seu aplicativo lógico.

   1. No menu do aplicativo lógico, selecione **Visão geral**.

   1. Na barra de ferramentas do painel **visão geral** , selecione **parar**.

1. No menu do aplicativo lógico, em **ferramentas de desenvolvimento**, selecione **ferramentas avançadas**.

1. No painel **ferramentas avançadas** , selecione **go**, que abre o ambiente kudu para seu aplicativo lógico.

1. Na barra de ferramentas do kudu, abra o menu do **console de depuração** e selecione **cmd**. 

   Uma janela de console é aberta para que você possa navegar até a pasta do pacote usando o prompt de comando. Ou então, você pode procurar a estrutura de diretório que aparece na janela do console.

1. Navegue até a seguinte pasta, que contém pastas com controle de versão para o pacote existente:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Exclua a pasta da versão do pacote existente. Na janela do console, você pode executar este comando onde substituir `{bundle-version}` pela versão existente:

   `rm -rf {bundle-version}`

   Por exemplo: `rm -rf 1.1.3`

   > [!TIP]
   > Se você receber um erro como "permissão negada" ou "arquivo em uso", atualize a página no navegador e tente as etapas anteriores novamente até que a pasta seja excluída.

1. Na portal do Azure, retorne à página de **visão geral** do aplicativo lógico e selecione **reiniciar**.

   O portal Obtém e usa automaticamente o pacote mais recente.

## <a name="next-steps"></a>Próximas etapas

Gostaríamos de conhecer suas experiências com esta visualização pública!

* Para bugs ou problemas, [crie seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, solicitações, comentários e outros comentários, [Use este formulário de comentários](https://aka.ms/lafeedback).

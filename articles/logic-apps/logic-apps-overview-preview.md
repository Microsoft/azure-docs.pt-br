---
title: Visão geral da versão prévia dos Aplicativos Lógicos do Azure
description: A visualização dos aplicativos lógicos do Azure é uma solução de nuvem para a criação de fluxos de trabalho com estado e sem estado automatizados que integram aplicativos, dados, serviços e sistemas com o mínimo de código para cenários de nível empresarial.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 5db0214e9b985df5c5aedb1dbe9878e484af2a55
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430790"
---
# <a name="overview-azure-logic-apps-preview"></a>Visão geral: visualização de aplicativos lógicos do Azure

> [!IMPORTANT]
> Essa versão preliminar está na versão prévia pública, e é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho em produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com a visualização dos aplicativos lógicos do Azure, você pode criar soluções de automação e integração entre aplicativos, dados, serviços de nuvem e sistemas criando e executando aplicativos lógicos que incluem [fluxos de trabalho com e *sem*](#stateful-stateless) estado usando o novo tipo de recurso de **aplicativo lógico (versão prévia)** . Com esse novo tipo de aplicativo lógico, você pode criar vários fluxos de trabalho que são alimentados pelo tempo de execução da visualização dos aplicativos lógicos do Azure reprojetados, que fornece portabilidade, melhor desempenho e flexibilidade para implantação e execução em vários ambientes de hospedagem, não apenas no Azure, mas também em contêineres do Docker.

Como isso é possível? O tempo de execução reprojetado usa o [modelo de extensibilidade Azure Functions](../azure-functions/functions-bindings-register.md) e é hospedado como uma extensão no tempo de execução de Azure functions. Essa arquitetura significa que você pode executar o novo tipo de aplicativo lógico em qualquer lugar que Azure Functions é executado. Você pode hospedar o tempo de execução reprojetado em praticamente qualquer topologia de rede e escolher qualquer tamanho de computação disponível para lidar com a carga de trabalho necessária exigida pelos seus fluxos de trabalho. Para obter mais informações, consulte [Introduction to Azure Functions](../azure-functions/functions-overview.md) and [Azure Functions triggers and bindings](../azure-functions/functions-triggers-bindings.md).

Você pode criar o recurso de **aplicativo lógico (versão prévia)** [iniciando na portal do Azure](create-stateful-stateless-workflows-azure-portal.md) ou [criando um projeto no Visual Studio Code com a extensão aplicativos lógicos do Azure (versão prévia)](create-stateful-stateless-workflows-visual-studio-code.md). Além disso, no Visual Studio Code, você pode criar *e executar localmente* seus fluxos de trabalho em seu ambiente de desenvolvimento. Se você usar o portal ou Visual Studio Code, poderá implantar e executar o novo tipo de aplicativo lógico nos mesmos tipos de ambientes de hospedagem.

Esta visão geral aborda as seguintes áreas:

* [Diferenças entre a visualização dos aplicativos lógicos do Azure, o ambiente multilocatário dos aplicativos lógicos do Azure e o ambiente do serviço de integração](#preview-differences).

* [Diferenças entre fluxos de trabalho com e sem](#stateful-stateless)estado, incluindo diferenças de comportamento entre [fluxos de trabalho com estado e sem estado aninhados](#nested-behavior).

* [Recursos nesta visualização pública](#public-preview-contents).

* [Como funciona o modelo de preços](#pricing-model).

* [Recursos alterados, limitados, indisponíveis ou sem suporte](#limited-unavailable-unsupported).

* [Limites na visualização dos aplicativos lógicos do Azure](#limits).

Para obter mais informações, consulte estes outros artigos:

* [Aplicativos lógicos do Azure executando análise aprofundada em tempo de execução](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Problemas conhecidos da visualização pública de aplicativos lógicos (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Como a visualização dos aplicativos lógicos do Azure é diferente?

O tempo de execução de visualização dos aplicativos lógicos do Azure usa [Azure Functions](../azure-functions/functions-overview.md) extensibilidade e é hospedado como uma extensão no tempo de execução de Azure functions. Essa arquitetura significa que você pode executar o novo tipo de aplicativo lógico em qualquer lugar que Azure Functions é executado. Você pode hospedar o tempo de execução de visualização dos aplicativos lógicos do Azure em praticamente qualquer topologia de rede desejada e escolher qualquer tamanho de computação disponível para lidar com a carga de trabalho necessária que seu fluxo de trabalho precisa. Para obter mais informações sobre a extensibilidade de Azure Functions, consulte [SDK de trabalhos Web: Criando associações de entrada e saída personalizadas](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

Com essa nova abordagem, o tempo de execução de visualização dos aplicativos lógicos do Azure e seus fluxos de trabalho fazem parte de seu aplicativo que você pode empacotar juntos. Essa funcionalidade permite que você implante e execute seus fluxos de trabalho simplesmente copiando artefatos para o ambiente de hospedagem e iniciando seu aplicativo. Essa abordagem também fornece uma experiência mais padronizada para a criação de pipelines de DevOps em relação aos projetos de fluxo de trabalho para executar os testes e validações necessários antes de implantar alterações em ambientes de produção. Para obter mais informações, consulte [aplicativos lógicos do Azure executando aprofundamentos em tempo de execução](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

A tabela a seguir resume brevemente as diferenças no modo como os fluxos de trabalho compartilham recursos, com base no ambiente em que são executados. Para obter diferenças nos limites, consulte [limites na visualização dos aplicativos lógicos do Azure](#limits).

| Ambiente | Compartilhamento de recursos e consumo |
|-------------|----------------------------------|
| Aplicativos lógicos do Azure (multilocatário) | Os fluxos *de trabalho de clientes em vários locatários* compartilham o mesmo processamento (computação), armazenamento, rede e assim por diante. |
| Aplicativos lógicos do Azure (visualização) | Os fluxos de trabalho *no mesmo aplicativo lógico* compartilham o mesmo processamento (computação), armazenamento, rede e assim por diante. |
| Ambiente do serviço de integração (não disponível na versão prévia) | Os fluxos de trabalho no *mesmo ambiente* compartilham o mesmo processamento (computação), armazenamento, rede e assim por diante. |
||||

Enquanto isso, você ainda pode criar o tipo de aplicativo lógico original na portal do Azure e no Visual Studio Code usando a extensão original de aplicativos lógicos do Azure. Embora as experiências de desenvolvimento sejam diferentes entre os tipos de aplicativo lógico original e novo, sua assinatura do Azure pode incluir ambos os tipos. Você pode exibir e acessar todos os aplicativos lógicos implantados em sua assinatura do Azure, mas os aplicativos são organizados em suas próprias categorias e seções.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Fluxos de trabalho com e sem estado

* *Com estado*

  Crie fluxos de trabalho com estado quando precisar manter, examinar ou referenciar dados de eventos anteriores. Esses fluxos de trabalho salvam as entradas e saídas de cada ação e seus Estados no armazenamento externo, o que torna possível examinar os detalhes de execução e o histórico possível após a conclusão de cada execução. Fluxos de trabalho com estado fornecem alta resiliência se ocorrerem interrupções. Depois que os serviços e sistemas forem restaurados, você poderá reconstruir as execuções interrompidas do estado salvo e executar novamente os fluxos de trabalho até a conclusão. Fluxos de trabalho com estado podem continuar em execução por até um ano.

* *Sem estado*

  Crie fluxos de trabalho sem estado quando você não precisar salvar, examinar ou referenciar dados de eventos anteriores no armazenamento externo para revisão posterior. Esses fluxos de trabalho salvam as entradas e saídas de cada ação e seus Estados *somente na memória*, em vez de transferir esses dados para o armazenamento externo. Como resultado, fluxos de trabalho sem estado têm execuções mais curtas que normalmente não são mais de 5 minutos, desempenho mais rápido com tempos de resposta mais rápidos, maior taxa de transferência e custos de execução reduzidos porque os detalhes e o histórico de execução não são mantidos no armazenamento externo. No entanto, se ocorrerem interrupções, as execuções interrompidas não serão automaticamente restauradas, portanto, o chamador precisará reenviar manualmente as execuções interrompidas. Esses fluxos de trabalho só podem ser executados de forma síncrona.

  Para uma depuração mais fácil, você pode habilitar o histórico de execução para um fluxo de trabalho sem estado, que tem algum impacto sobre o desempenho e, em seguida, desabilitar o histórico de execução quando terminar. Para obter mais informações, consulte [criar fluxos de trabalho com e sem monitoração de estado em Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) ou [criar fluxos de trabalho com e sem estado no portal do Azure](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Atualmente, os fluxos de trabalho sem estado dão suporte apenas a *ações* para [conectores gerenciados](../connectors/apis-list.md#managed-api-connectors), que são implantados no Azure e não gatilhos. Para iniciar o fluxo de trabalho, selecione a [solicitação interna, os hubs de eventos ou o gatilho do barramento de serviço](../connectors/apis-list.md#built-ins). Esses gatilhos são executados nativamente no tempo de execução de visualização dos aplicativos lógicos do Azure. Para obter mais informações sobre gatilhos, ações e conectores limitados, indisponíveis ou sem suporte, consulte [recursos alterados, limitados, indisponíveis ou sem suporte](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Diferenças de comportamento aninhado entre fluxos de trabalho com e sem estado

Você pode [tornar um fluxo de trabalho](../logic-apps/logic-apps-http-endpoint.md) que possa ser chamado por meio de outros fluxos de trabalho que existem no mesmo recurso de **aplicativo lógico (versão prévia)** usando o [gatilho de solicitação](../connectors/connectors-native-reqres.md), o [gatilho de webhook http](../connectors/connectors-native-webhook.md)ou gatilhos de conector gerenciado que têm o [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber solicitações HTTPS.

Aqui estão os padrões de comportamento que os fluxos de trabalho aninhados podem seguir depois que um fluxo de trabalho pai chama um fluxo de trabalho filho:

* Padrão de sondagem assíncrona

  O pai não aguarda uma resposta para sua chamada inicial, mas verifica continuamente o histórico de execução do filho até que o filho termine a execução. Por padrão, os fluxos de trabalho com estado seguem esse padrão, que é ideal para fluxos de trabalho filho de longa execução que podem exceder [os limites de tempo limite de solicitação](../logic-apps/logic-apps-limits-and-config.md).

* Padrão síncrono ("disparar e esquecer")

  O filho reconhece a chamada retornando imediatamente uma `202 ACCEPTED` resposta, e o pai continua para a próxima ação sem esperar os resultados do filho. Em vez disso, o pai recebe os resultados quando o filho termina de ser executado. Fluxos de trabalho com estado filho que não incluem uma ação de resposta sempre seguem o padrão síncrono. Para fluxos de trabalho com estado filho, o histórico de execução está disponível para análise.

  Para habilitar esse comportamento, na definição JSON do fluxo de trabalho, defina a `operationOptions` propriedade como `DisableAsyncPattern` . Para obter mais informações, consulte [gatilho e tipos de ação – opções de operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Gatilho e espera

  Para um fluxo de trabalho com estado filho, o pai espera por uma resposta que retorna os resultados do filho. Esse padrão funciona de forma semelhante ao uso do [gatilho http interno ou da ação](../connectors/connectors-native-http.md) para chamar um fluxo de trabalho filho. Fluxos de trabalho sem estado filho que não incluem uma ação de resposta retornam imediatamente uma `202 ACCEPTED` resposta, mas o pai espera que o filho termine antes de continuar para a próxima ação. Esses comportamentos se aplicam somente a fluxos de trabalho sem estado filho.

Esta tabela especifica o comportamento do fluxo de trabalho filho com base em se o pai e o filho estão com estado, sem monitoração de estado ou são tipos de fluxo de trabalho mistos:

| Fluxo de trabalho pai | Fluxo de trabalho filho | Comportamento filho |
|-----------------|----------------|----------------|
| Com estado | Com estado | Assíncrono ou síncrono com `"operationOptions": "DisableAsyncPattern"` configuração |
| Com estado | Sem estado | Gatilho e espera |
| Sem estado | Com estado | Síncrono |
| Sem estado | Sem estado | Gatilho e espera |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Funcionalidades

A visualização dos aplicativos lógicos do Azure inclui muitos recursos atuais e adicionais, por exemplo:

* Crie aplicativos lógicos e seus fluxos de trabalho por meio de [conectores 390 +](/connectors/connector-reference/connector-reference-logicapps-connectors) para aplicativos de software como serviço (SaaS) e plataforma como serviço (PaaS) e serviços mais conectores para sistemas locais.

  * Alguns conectores gerenciados, como o barramento de serviço do Azure, os hubs de eventos do Azure e SQL Server executados de forma semelhante aos gatilhos e ações internos que são nativos do tempo de execução de visualização dos aplicativos lógicos do Azure, por exemplo, o gatilho de solicitação e a ação HTTP. Para obter mais informações, consulte [aplicativos lógicos do Azure que executam extensibilidade de conector interno em qualquer lugar](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Você pode usar as ações B2B para operações de Liquid e operações XML sem uma conta de integração. Para usar essas ações, você precisa ter mapas líquidos, mapas XML ou esquemas XML que você pode carregar por meio das respectivas ações no portal do Azure ou adicionar à pasta de **artefatos** do seu projeto de Visual Studio Code usando as respectivas pastas **mapas** e **esquemas** .

  * Crie e implante aplicativos lógicos que podem ser executados em qualquer lugar porque o serviço de aplicativos lógicos do Azure gera cadeias de conexão de SAS (assinatura de acesso compartilhado) que esses aplicativos lógicos podem usar para enviar solicitações ao ponto de extremidade do tempo de execução de conexão de nuvem O serviço de aplicativos lógicos salva essas cadeias de conexão com outras configurações de aplicativo para que você possa armazenar facilmente esses valores em Azure Key Vault ao implantar no Azure.

    > [!NOTE]
    > Por padrão, um recurso de **aplicativo lógico (versão prévia)** tem sua [identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md) habilitada automaticamente para autenticar conexões em tempo de execução. Essa identidade difere das credenciais de autenticação ou da cadeia de conexão que você usa ao criar uma conexão. Se você desabilitar essa identidade, as conexões não funcionarão no tempo de execução. Para exibir essa configuração, no menu do aplicativo lógico, em **configurações**, selecione **identidade**.

* Crie aplicativos lógicos com fluxos de trabalho sem estado que são executados somente na memória para que sejam concluídos mais rapidamente, respondam mais rapidamente, tenham maior taxa de transferência e custam menos para serem executados, pois os históricos de execução e os dados entre as ações não persistem no armazenamento externo. Opcionalmente, você pode habilitar o histórico de execuções para uma depuração mais fácil. Para obter mais informações, consulte [aplicativos lógicos com estado versus sem estado](#stateful-stateless).

* Execute, teste e depure localmente seus aplicativos lógicos e seus fluxos de trabalho no ambiente de desenvolvimento de Visual Studio Code.

  Antes de executar e testar seu aplicativo lógico, você pode facilitar a depuração adicionando e usando pontos de interrupção dentro do **workflow.jsno** arquivo para um fluxo de trabalho. No entanto, os pontos de interrupção têm suporte apenas para ações no momento, não gatilhos. Para obter mais informações, consulte [criar fluxos de trabalho com estado e sem estado no Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Publique ou implante diretamente aplicativos lógicos e seus fluxos de trabalho de Visual Studio Code em vários ambientes de hospedagem, como contêineres do Azure e do [Docker](/dotnet/core/docker/introduction).

* Habilite o log de diagnóstico e os recursos de rastreamento para seu aplicativo lógico usando [Application insights](../azure-monitor/app/app-insights-overview.md) quando houver suporte para suas configurações de aplicativo lógico e de assinatura do Azure.

* Regenerar chaves de acesso para conexões gerenciadas usadas por fluxos de trabalho individuais em um recurso de **aplicativo lógico (versão prévia)** . Para essa tarefa, [siga as mesmas etapas para o recurso de **aplicativos lógicos** , mas no nível de fluxo de trabalho individual](logic-apps-securing-a-logic-app.md#regenerate-access-keys), não no nível de recurso do aplicativo lógico.

> [!NOTE]
> Para obter informações sobre os problemas atuais conhecidos, examine a [página problemas conhecidos da visualização pública de aplicativos lógicos no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo de preços

Ao criar o novo tipo de aplicativo lógico no portal do Azure ou implantar do Visual Studio Code, você deve escolher um plano de hospedagem, o [serviço de aplicativo ou Premium](../azure-functions/functions-scale.md), para que seu aplicativo lógico seja usado. Este plano determina o modelo de preços que se aplica à execução do aplicativo lógico. Se você selecionar o plano do serviço de aplicativo, também deverá escolher um [tipo de preço](../app-service/overview-hosting-plans.md).

Fluxos de trabalho com *estado* usam [armazenamento externo](../azure-functions/storage-considerations.md#storage-account-requirements), portanto, o [preço do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) se aplica às transações de armazenamento que o tempo de execução de visualização do aplicativo lógico do Azure executa. Por exemplo, as filas são usadas para agendamento, enquanto tabelas e BLOBs são usados para armazenar Estados de fluxo de trabalho.

> [!NOTE]
> Durante a visualização pública, a execução de aplicativos lógicos no serviço de aplicativo não incorre em encargos *adicionais* na parte superior do plano selecionado.

Para obter mais informações sobre os modelos de preços que se aplicam a esse novo tipo de recurso, consulte estes tópicos:

* [Escala e hospedagem do Azure Functions](../azure-functions/functions-scale.md)
* [Escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure](../app-service/manage-scale-up.md)
* [Detalhes de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
* [Detalhes de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/)
* [Detalhes de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Recursos alterados, limitados, indisponíveis ou sem suporte

Na visualização dos aplicativos lógicos do Azure, esses recursos foram alterados, ou eles estão limitados, indisponíveis ou sem suporte:

* **Suporte do so**: atualmente, o designer no Visual Studio Code não funciona no sistema operacional Linux, mas você ainda pode implantar aplicativos lógicos que usam o tempo de execução de visualização dos aplicativos lógicos para máquinas virtuais baseadas em Linux. Por enquanto, você pode criar seus aplicativos lógicos no Visual Studio Code no Windows ou no macOS e, em seguida, implantar em uma máquina virtual baseada em Linux.

* **Gatilhos e ações**: alguns gatilhos internos não estão disponíveis, como janela deslizante e lote. Para iniciar o fluxo de trabalho, use a [recorrência interna, a solicitação, o http, o webhook http, os hubs de eventos ou o gatilho do barramento de serviço](../connectors/apis-list.md). Gatilhos e ações internos são executados nativamente no tempo de execução de visualização dos aplicativos lógicos do Azure, enquanto os conectores gerenciados são implantados no Azure. No designer, gatilhos e ações internas aparecem sob a guia **interna** , enquanto os gatilhos e as ações do conector gerenciado aparecem na guia **Azure** .

  > [!NOTE]
  > Para executar localmente em Visual Studio Code, gatilhos e ações baseados em webhook exigem configuração adicional. Para obter mais informações, consulte [criar fluxos de trabalho com estado e sem estado no Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * Para *fluxos de trabalho sem estado*, a guia **Azure** não aparece quando você seleciona um gatilho porque você pode selecionar apenas [ *ações* de conector gerenciado, não gatilhos](../connectors/apis-list.md#managed-api-connectors). Embora você possa habilitar os conectores gerenciados implantados pelo Azure para fluxos de trabalho sem estado, o designer não mostra nenhum gatilho de conector gerenciado para você adicionar.

  * Para *fluxos de trabalho com estado*, diferentes dos gatilhos e ações listados como não disponíveis abaixo, ambos os [gatilhos de conector gerenciados e as ações](../connectors/apis-list.md#managed-api-connectors) estão disponíveis para uso.

  * Esses gatilhos e ações foram alterados ou estão atualmente limitados, não têm suporte ou não estão disponíveis:

    * [Os *gatilhos* do gateway de dados local](../connectors/apis-list.md#on-premises-connectors) não estão disponíveis, mas as ações de gateway *estão* disponíveis.

    * Os [conectores personalizados](../connectors/apis-list.md#custom-apis-and-connectors) não estão disponíveis.

    * A ação interna, [Azure Functions escolher uma função do Azure](logic-apps-azure-functions.md) agora é **operações do Azure function – chamar uma função do Azure**. Essa ação atualmente funciona apenas para funções que são criadas a partir do modelo de **gatilho http** .

      No portal do Azure, você pode selecionar uma função de gatilho HTTP onde você tem acesso criando uma conexão por meio da experiência do usuário. Se você inspecionar a definição de JSON da ação da função na exibição de código ou na **workflow.jsno** arquivo, a ação se referirá à função usando uma `connectionName` referência. Essa versão abstrai as informações da função como uma conexão, que pode ser encontrada naconnections.jsdo seu projeto **no** arquivo, que está disponível depois que você cria uma conexão.

      > [!NOTE]
      > Na versão de visualização, a ação de função dá suporte apenas à autenticação de cadeia de caracteres de consulta. A visualização dos aplicativos lógicos do Azure Obtém a chave padrão da função ao fazer a conexão, armazena essa chave nas configurações do aplicativo e usa a chave para autenticação ao chamar a função.
      >
      > Assim como acontece com a versão original, se você renovar essa chave, por exemplo, por meio da experiência de Azure Functions no portal, a ação da função não funcionará mais devido à chave inválida. Para corrigir esse problema, você precisa recriar a conexão com a função que deseja chamar ou atualizar as configurações do aplicativo com a nova chave.

    * A ação interna, código [embutido-executar código JavaScript](logic-apps-add-run-inline-code.md) agora é **operações de código embutidas-executar JavaScript em linha**.

      * As ações de operações de código embutido não exigem mais uma conta de integração.

      * Se você usar o macOS ou Linux, **as operações de código embutido** não estarão disponíveis no momento quando você usar a extensão de aplicativos lógicos do Azure (versão prévia) no Visual Studio Code.

      * Se você fizer alterações em uma ação de operações de código embutido, será necessário reiniciar o aplicativo lógico.

      * As ações de operações de código embutido têm [limites atualizados](logic-apps-overview-preview.md#inline-code-limits).

    * Alguns [gatilhos e ações B2B internos para contas de integração](../connectors/apis-list.md#integration-account-connectors) não estão disponíveis, por exemplo, as ações codificação de **arquivo simples** e decodificação.

    * A ação interna, [aplicativos lógicos do Azure-escolher um fluxo de trabalho de aplicativo lógico](logic-apps-http-endpoint.md) agora é **operações de fluxo de trabalho – invocar um fluxo de trabalho neste aplicativo de fluxo de trabalho**.

* **Disponibilidade do plano de hospedagem**: se você criar um novo tipo de recurso de **aplicativo lógico (versão prévia)** no portal do Azure ou implantar do Visual Studio Code, você só poderá usar o plano de hospedagem do serviço de aplicativo ou Premium no Azure. Os planos de Hospedagem de consumo estão indisponíveis e não têm suporte para a implantação deste tipo de recurso. Você pode implantar de Visual Studio Code para um contêiner do Docker, mas não para um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

* **Branches paralelos**: atualmente, você não pode adicionar ramificações paralelas por meio da nova experiência de designer. No entanto, você ainda pode adicionar essas ramificações por meio da experiência de designer original e fazer com que elas apareçam no novo designer.

  1. Na parte inferior do designer, desabilite a nova experiência selecionando o **novo controle Canvas** .

  1. Adicione as ramificações paralelas ao seu fluxo de trabalho.

  1. Habilite a nova experiência selecionando o **novo controle Canvas** novamente.

* **Controle de zoom**: o controle de zoom não está disponível no designer no momento.

* **Depuração de ponto de interrupção no Visual Studio Code**: embora você possa adicionar e usar pontos de interrupção dentro do **workflow.jsno** arquivo para um fluxo de trabalho, os pontos de interrupção têm suporte apenas para ações no momento, não gatilhos. Para obter mais informações, consulte [criar fluxos de trabalho com estado e sem estado no Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

<a name="limits"></a>

## <a name="updated-limits"></a>Limites atualizados

Embora muitos limites para a visualização dos aplicativos lógicos do Azure permaneçam os mesmos que os [limites para aplicativos lógicos multilocatários do Azure](logic-apps-limits-and-config.md), esses limites foram alterados para a visualização dos aplicativos lógicos do Azure.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Limites de tempo limite de HTTP

Para uma única chamada de entrada ou chamada de saída, o tempo limite é de 230 segundos (3,9 minutos) para esses gatilhos e ações:

* Solicitação de saída: gatilho HTTP, ação HTTP
* Solicitação de entrada: gatilho de solicitação, gatilho de webhook HTTP, ação de webhook HTTP

Em comparação, aqui estão os limites de tempo limite para esses gatilhos e ações em outros ambientes em que os aplicativos lógicos e seus fluxos de trabalho são executados:

* Aplicativos lógicos multilocatários do Azure: 120 segundos (2 minutos)
* Ambiente do serviço de integração: 240 segundos (4 minutos)

Para obter mais informações, consulte [limites de http](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Conectores gerenciados

Os conectores gerenciados são limitados a 50 solicitações por minuto por conexão. Para trabalhar com problemas de limitação de conector, confira [tratar problemas de limitação (429-erro "muitas solicitações") nos aplicativos lógicos do Azure](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Operações de código embutido (executar código JavaScript)

Para uma definição de aplicativo lógico único, a ação de operações de código embutido, [**executar código JavaScript**](logic-apps-add-run-inline-code.md), tem estes limites atualizados:

* O número máximo de caracteres de código aumenta de 1.024 caracteres para 100.000 caracteres.

* A duração máxima para execução de código aumenta de cinco segundos para 15 segundos.

Para obter mais informações, consulte [limites de definição de aplicativo lógico](logic-apps-limits-and-config.md#definition-limits).

## <a name="next-steps"></a>Próximas etapas

* [Criar fluxos de trabalho com e sem monitoração de estado no portal do Azure](create-stateful-stateless-workflows-azure-portal.md)
* [Criar fluxos de trabalho com e sem estado no Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Página de problemas conhecidos da visualização pública de aplicativos lógicos no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Além disso, gostaríamos de conhecer suas experiências com a visualização dos aplicativos lógicos do Azure!

* Para bugs ou problemas, [crie seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, solicitações, comentários e outros comentários, [Use este formulário de comentários](https://aka.ms/lafeedback).

---
title: Conectar-se ao Armazenamento de Blobs do Azure
description: Criar e gerenciar BLOBs em contas de armazenamento do Azure usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677720"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Criar e gerenciar BLOBs no armazenamento de BLOBs do Azure usando o aplicativo lógico do Azure

Este artigo mostra como você pode acessar e gerenciar arquivos armazenados como blobs em sua conta de armazenamento do Azure de dentro de um aplicativo lógico com o conector do Armazenamento de Blobs do Azure. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos. Por exemplo, você pode criar aplicativos lógicos que criam, obtêm, atualizam e excluem arquivos na sua conta de armazenamento.

Suponha que você tem uma ferramenta que é atualizada em um site do Azure. que atua como o gatilho do aplicativo lógico. Quando esse evento ocorre, você pode fazer com que o aplicativo lógico atualize um arquivo em seu contêiner de armazenamento de blobs, o que é uma ação no aplicativo lógico.

Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, confira a [Referência do conector do Armazenamento de Blobs do Azure](/connectors/azureblobconnector/).

> [!IMPORTANT]
> Os aplicativos lógicos não podem acessar diretamente as contas de armazenamento que estão atrás de firewalls se estiverem na mesma região. Como alternativa, você pode ter seus aplicativos lógicos e a conta de armazenamento em regiões diferentes. Para obter mais informações sobre como habilitar o acesso de aplicativos lógicos do Azure a contas de armazenamento por trás de firewalls, consulte a seção [acessar contas de armazenamento por trás de firewalls](#storage-firewalls) mais adiante neste tópico.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limites

* Por padrão, as ações do armazenamento de BLOBs do Azure podem ler ou gravar arquivos que são *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, mas até 1024 MB, as ações do armazenamento de BLOBs do Azure dão suporte ao [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **obter conteúdo do blob** usa implicitamente o agrupamento.

* Os gatilhos do armazenamento de BLOBs do Azure não dão suporte ao agrupamento. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 50 MB ou menores. Para obter arquivos maiores que 50 MB, siga este padrão:

  * Use um gatilho de armazenamento de BLOBs do Azure que retorne Propriedades de arquivo, como **quando um blob é adicionado ou modificado (somente Propriedades)**.

  * Siga o gatilho com a ação **obter conteúdo de blob** do armazenamento de BLOBs do Azure, que lê o arquivo completo e usa implicitamente o agrupamento.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [contêiner de armazenamento padrão e uma conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)

* O aplicativo lógico onde você precisa do acesso à conta de armazenamento de blobs do Azure. Para iniciar seu aplicativo lógico com um gatilho do Armazenamento de Blobs do Azure, você precisará de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicionar gatilho de armazenamento de blobs

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico com o gatilho **quando um blob é adicionado ou modificado (somente Propriedades)** quando as propriedades de um blob são adicionadas ou atualizadas no seu contêiner de armazenamento.

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, digite "blob do azure" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa este gatilho: **quando um blob é adicionado ou modificado (somente Propriedades)**

   ![Selecionar gatilho do armazenamento de BLOBs do Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Se forem solicitados os detalhes da conexão, [crie sua conexão de armazenamento de blobs agora](#create-connection). Ou, se a conexão já existir, forneça as informações necessárias para o gatilho.

   Neste exemplo, selecione o contêiner e a pasta que você deseja monitorar.

   1. Na caixa **Contêiner**, selecione o ícone de pasta.

   2. Na lista de pastas, escolha o colchete angular direito ( **>** ) e, em seguida, navegue até encontrar e selecione a pasta desejada.

      ![Selecione a pasta de armazenamento a ser usada com o gatilho](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência com a qual o gatilho deve verificar alterações na pasta.

4. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

5. Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicionar ação de armazenamento de blobs

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico começa com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md).

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. Este exemplo usa o portal do Azure.

2. No designer do aplicativo lógico, no gatilho ou na ação, escolha **nova etapa**.

   ![Adicionar nova etapa ao fluxo de trabalho do aplicativo lógico](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Escolha o sinal de adição ( **+** ) que aparece e selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, digite "blob do azure" como filtro. Na lista de ações, selecione a ação desejada.

   Este exemplo usa esta ação: **obter conteúdo de blob**

   ![Selecionar ação de armazenamento de BLOBs do Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Se forem solicitados os detalhes da conexão, [crie sua conexão do Armazenamento de Blobs do Azure agora](#create-connection).
Ou, se a conexão já existir, forneça as informações necessárias para a ação.

   Neste exemplo, selecione o arquivo desejado.

   1. Na caixa **Blob**, selecione o ícone de pasta.
  
      ![Selecione a pasta de armazenamento a ser usada com a ação](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Localize e selecione o arquivo desejado com base no número de **ID** do blob. Você pode encontrar esse número de **ID** nos metadados do blob que são retornados pelo gatilho de armazenamento de blob descrito anteriormente.

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.
Para testar seu aplicativo lógico, verifique se a pasta selecionada contém um blob.

Este exemplo obtém apenas o conteúdo de um blob. Para exibir o conteúdo, adicione outra ação que cria um arquivo com o blob usando outro conector. Por exemplo, adicione uma ação do OneDrive que cria um arquivo com base no conteúdo do blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Conectar à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando você for solicitado a criar a conexão, forneça estas informações:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da conexão** | Sim | <*nome da conexão*> | O nome a criar para a conexão |
   | **Conta de armazenamento** | Sim | <*conta de armazenamento*> | Selecione sua conta de armazenamento na lista. |
   ||||

   Por exemplo:

   ![Criar conexão de conta de armazenamento de BLOBs do Azure](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Quando estiver pronto, selecione **criar**

1. Depois de criar a conexão, continue com a ação [Adicionar gatilho de armazenamento de BLOBs](#add-trigger) ou [Adicionar armazenamento de BLOBs](#add-action).

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/azureblobconnector/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Acessar contas de armazenamento por trás de firewalls

Você pode adicionar segurança de rede a uma conta de armazenamento do Azure restringindo o acesso com regras de firewall [e firewall](../storage/common/storage-network-security.md). No entanto, essa configuração cria um desafio para o Azure e outros serviços da Microsoft que precisam acessar a conta de armazenamento. A comunicação local no datacenter abstrai os endereços IP internos, portanto, você não pode configurar regras de firewall com restrições de IP. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](../storage/common/storage-network-security.md).

Aqui estão várias opções para acessar contas de armazenamento por trás de firewalls de aplicativos lógicos do Azure usando o conector de armazenamento de BLOBs do Azure ou outras soluções:

* Conector do blob de armazenamento do Azure

  * [Acessar contas de armazenamento em outras regiões](#access-other-regions)
  * [Acessar contas de armazenamento por meio de uma rede virtual confiável](#access-trusted-virtual-network)

* Outras soluções

  * [Acesse contas de armazenamento como um serviço confiável com identidades gerenciadas](#access-trusted-service)
  * [Acessar contas de armazenamento por meio do gerenciamento de API do Azure](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemas ao acessar contas de armazenamento na mesma região

Os aplicativos lógicos não podem acessar diretamente contas de armazenamento atrás de firewalls quando estão na mesma região. Como alternativa, coloque seus aplicativos lógicos em uma região que seja diferente da sua conta de armazenamento e forneça acesso aos [endereços IP de saída para os conectores gerenciados em sua região](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Essa solução não se aplica ao conector de armazenamento de tabelas do Azure e ao conector de armazenamento de filas do Azure. Em vez disso, para acessar o armazenamento de tabelas ou o armazenamento de filas, use o gatilho HTTP interno e as ações.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Acessar contas de armazenamento por meio de uma rede virtual confiável

Você pode colocar a conta de armazenamento em uma rede virtual do Azure que você gerencia e, em seguida, adicionar essa rede virtual à lista de redes virtuais confiáveis. Para que seu aplicativo lógico acesse a conta de armazenamento por meio de uma [rede virtual confiável](../virtual-network/virtual-networks-overview.md), você precisa implantar esse aplicativo lógico em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode se conectar a recursos em uma rede virtual. Em seguida, você pode adicionar as sub-redes no ISE à lista de confiáveis. Os conectores de armazenamento do Azure, como o conector de armazenamento de BLOBs, podem acessar diretamente o contêiner de armazenamento. Essa configuração é a mesma experiência usada para usar os pontos de extremidade de serviço de um ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Acesse contas de armazenamento como um serviço confiável com identidades gerenciadas

Para conceder aos serviços confiáveis da Microsoft acesso a uma conta de armazenamento por meio de um firewall, você pode configurar uma exceção nessa conta de armazenamento para esses serviços. Essa solução permite que os serviços do Azure que dão suporte a [identidades gerenciadas para autenticação](../active-directory/managed-identities-azure-resources/overview.md) acessem contas de armazenamento por trás de firewalls como serviços confiáveis. Especificamente, para um aplicativo lógico no Azure multilocatário global para acessar essas contas de armazenamento, você primeiro [habilita o suporte de identidade gerenciada](../logic-apps/create-managed-service-identity.md) no aplicativo lógico. Em seguida, use a ação HTTP ou o gatilho em seu aplicativo lógico e [defina seu tipo de autenticação para usar a identidade gerenciada do aplicativo lógico](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Para esse cenário, você pode usar *apenas* a ação ou o gatilho http.

Para configurar a exceção e o suporte de identidade gerenciada, siga estas etapas gerais:

1. Em sua conta de armazenamento, em **configurações**, selecione **firewalls e redes virtuais**. Em **permitir acesso de**, selecione a opção **redes selecionadas** para que as configurações relacionadas sejam exibidas.

1. Em **exceções**, selecione **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** e, em seguida, selecione **salvar**.

   ![Selecione uma exceção que permita que os serviços confiáveis da Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Nas configurações do aplicativo lógico, [habilite o suporte para a identidade gerenciada](../logic-apps/create-managed-service-identity.md).

1. No fluxo de trabalho do aplicativo lógico, adicione e configure a ação HTTP ou o gatilho para acessar a conta ou a entidade de armazenamento.

   > [!IMPORTANT]
   > Para a ação HTTP de saída ou chamadas de gatilho para contas de armazenamento do Azure, verifique se o cabeçalho da solicitação inclui a `x-ms-version` propriedade e a versão da API para a operação que você deseja executar na conta de armazenamento. Para obter mais informações, consulte [autenticar o acesso com identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) e [controle de versão para serviços de armazenamento do Azure](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Nessa ação, [Selecione a identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) a ser usada para autenticação.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Acessar contas de armazenamento por meio do gerenciamento de API do Azure

Se você usar uma camada dedicada para o [Gerenciamento de API](../api-management/api-management-key-concepts.md), poderá antecipar a API de armazenamento usando o gerenciamento de API e permitindo os endereços IP da última por meio do firewall. Basicamente, adicione a rede virtual do Azure que é usada pelo gerenciamento de API à configuração de firewall da conta de armazenamento. Em seguida, você pode usar a ação de gerenciamento de API ou a ação HTTP para chamar as APIs de armazenamento do Azure. No entanto, se você escolher essa opção, precisará manipular o processo de autenticação por conta própria. Para obter mais informações, confira [Arquitetura Enterprise Integration simples](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)